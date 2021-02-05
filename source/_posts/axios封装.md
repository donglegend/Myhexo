---
title: axios封装
date: 2021-02-04 20:47:54
categories: javascript
tags: axios
---

增强axios能力，支持自动取消、自动重试、自动缓存等能力。

<!-- more -->

## 概述
axios库本身已经很好使用了。但是具体到业务层面，会涉及到几个非常高频触发的情景需要提取处理。最常用的可能如下：
1. 取消重复的请求。(频繁操作或者state频繁更新导致组件频繁render触发的多次重复请求)
2. 失败自动发起重试。（由于网络波动或者服务器不稳定原因，重发可提高成功率的情况）
3. 自动缓存请求结果。（对于实时性要求不高的接口，可以缓存结果，避免请求）

## 思路实现
### 取消
关于axios的取消机制原理，有一篇文章分析过了，有兴趣的可以跳转查看[axios的cancelToken取消机制原理](https://segmentfault.com/a/1190000039028389)

分析：
1. 首先要统一管理多个请求，所以比较容易想到 使用一个队列来管理请求，然后考虑到每个请求的唯一性，就自然会想到Map数据结构。
2. Map请求管理数据结构添加和删除的时机，自然是利用axios提供的请求/响应拦截器来处理。
3. 然后每次开始请求和结束请求的时候，检测Map数据请求结构，如果有进行清理。
### 重试
重试retry的话，在上述基础上，在响应拦截器里监听如果 error失败，那么再次发起请求即可

### 缓存
cache功能应该是最简单的，只需要在 响应拦截器里缓存response，然后在每次开始请求的时候，检测是否有缓存的response，如果有，则直接返回即可。

## 技术实现

### 自动取消
先实现自动取消功能，根据上述分析，我们首先需要一个Map数据结构来存储请求。代码如下：
```ts
class MAxios {
  private requestMap = new Map();
}
```
然后我们需要一个对外的启动请求的接口函数，接受一些配置项参数。
在此函数里，我们需要实例化axios请求，为了方便管理，使每个请求间的配置互不干扰，我们采用一个请求一个实例的管理。
此外还需要对每个请求添加 请求、响应拦截器。
伪代码如下：
```ts
class MAxios {
  private requestMap = new Map();
  
  
  private interceptorsRequest(instance: AxiosInstance) {}
  private interceptorsResponse(instance: AxiosInstance) {}
  
  private interceptors(instance: AxiosInstance) {
    this.interceptorsRequest(instance);
    this.interceptorsResponse(instance);
  }
  
   /**
   * 实例工厂函数
   */
  private getAxiosInstance(config: IConfig) {
    const instance = axios.create();
    return instance;
  }

  
  public request(options: IConfig) {
    const config = Object.assign({}, defaultConfig, options);
    // 工厂函数获取axios实例
    const instance = this.getAxiosInstance();
    // 添加拦截器
    this.interceptors(instance);
    // 返回请求
    return instance.request(config);
  }
}
```
接下来我们着重实现 拦截器细节。对请求拦截器和响应拦截器分别分析。

- 请求拦截器
  我们需要首先去Map结构里检查是否有缓存，有的话移除，然后添加新的
- 响应拦截器
  请求成功或者失败之后，都需要移除Map缓存的请求
 
Map缓存的key，可以提供个接口给开发者自定义或者内置一个默认策略，比如根据method和url来生成。
参考代码：
```ts
 /**
   * 请求拦截器
   * @param {*} instance
   */
  private interceptorsRequest(instance: AxiosInstance) {
    instance.interceptors.request.use(
      (config) => {
        this.removeReq(config);
        this.addReq(config);
        return config;
      },
      (error) => {
        // Do something with request error
        return Promise.reject(error);
      }
    );
  }
  /**
   * 响应拦截器
   * @param {*} instance
   */
  private interceptorsResponse(instance: AxiosInstance) {
    instance.interceptors.response.use(
      (response) => {
        this.removeReq(response.config);
        return response;
      },
      (error) => {
        const config = error.config || {};
        this.removeReq(config);
        if (axios.isCancel(error)) {
          return Promise.reject(error.message);
        } else {
          return Promise.reject(error);
        }
      }
    );
  }
  
   /**
   * 生成标识请求的唯一key
   */
  getDuplicatedKey(config) {
    const { duplicatedKey = () => "" } = config;
    return duplicatedKey(config);
  }
  
 /**
   * 添加请求
   */
  addReq(config) {
    const { cancelDuplicated } = config;
    if (!cancelDuplicated) {
      return;
    }
    const key = this.getDuplicatedKey(config);
    if (!this.requestMap.has(key)) {
      config.cancelToken = new axios.CancelToken((c) => {
        this.requestMap.set(key, c);
      });
    }
  }
  /**
   * 移除请求
   */
  removeReq(config) {
    try {
      const { cancelDuplicated } = config;
      const key = this.getDuplicatedKey(config);
      if (!cancelDuplicated) {
        return;
      }
      if (!this.requestMap.has(key)) {
        return;
      }
      const cancel = this.requestMap.get(key);
      this.requestMap.delete(key);
      cancel({
        type: ERROR_TYPE.Cancel,
        message: "Request canceled ",
      });
    } catch (error) {
      console.log("removeReq: ", error);
    }
  }
```
到这里取消请求的功能就完成了。

### 自动重试
重试的逻辑非常简单，只需要在失败的时候判断一下重试次数，然后根据条件再次发起请求即可，如下：
```ts
   /**
   * 响应拦截器
   * @param {*} instance
   */
  private interceptorsResponse(instance: AxiosInstance) {
    instance.interceptors.response.use(
      (response) => {
        //balabala
      },
      (error) => {
          // retry 重试逻辑
          if (config.retry > 0) {
            return this.retry({ instance, config, error });
          }
          return Promise.reject(error);
        }
      }
    );
  }
 
  /**
   * 重试某次请求
   */
  private retry({
    instance,
    config,
    error
  }: {
    instance: AxiosInstance;
    config: IConfig;
    error: AxiosError;
  }) {
    const { retry, retryDelay, retryDelayRise } = config;
    let retryCount = config.__retryCount || 0;
    config.__retryCount = retryCount;
    // 检查是否超过重置次数
    if (retryCount >= retry!) {
      return Promise.reject(error);
    }
    // 重发计数器加1
    retryCount += 1;
    config.__retryCount = retryCount;

    if (retryCount === retry) {
      config.timeout = 15000;
    }
    // 延时重发
    let delay = 0;
    if (typeof retryDelay === 'number') {
      delay = retryDelay * (retryDelayRise ? retryCount : 1);
    } else {
      delay = retryDelay!(retryCount);
    }

    const retryTask = new Promise<void>((resolve) => {
      setTimeout(() => {
        resolve();
      }, delay);
    });
    return retryTask.then(() => {
      return instance.request(config);
    });
  }
```
OK，是不是非常简单？

### 缓存结果
缓存是最简单的功能了，直接贴代码：
```ts
  private responseCacheMap = new Map();
 /**
   * 获取response缓存
   * @param {*} config
   */
  private getResponseCache(config: IConfig) {
    const key = this.getDuplicatedKey(config);
    if (this.responseCacheMap.has(key)) {
      return this.responseCacheMap.get(key);
    }
    return null;
  }
  /**
   * 设置response缓存
   * @param {*} config
   * @param {*} response
   */
  private setResponseCache(config: IConfig, response: AxiosResponse) {
    if (!config.cache) {
      return;
    }
    const key = this.getDuplicatedKey(config);
    this.responseCacheMap.set(key, response);
  }

  public request(options: IConfig) {
    const config = Object.assign({}, defaultConfig, options);
    if (config.cache) {
      const responseCache = this.getResponseCache(config);
      if (responseCache) {
        return Promise.resolve(responseCache);
      }
    }
    // balabala
  }
```
OK,GameOver!

下面贴上完整代码：
```ts
import axios, { AxiosRequestConfig, Method, AxiosInstance, AxiosResponse, AxiosError } from 'axios';

type IRetryDelay = number | ((c: number) => number);

interface IConfig extends AxiosRequestConfig {
  cancelDuplicated?: boolean;
  duplicatedKey?: (ops: IConfig) => string;
  retry?: number;
  retryDelay?: IRetryDelay;
  retryDelayRise?: boolean;
  cache?: boolean;
  __retryCount?: number;
}

const defaultConfig: IConfig = {
  method: 'get',
  cancelDuplicated: false,
  duplicatedKey: ({ method, url }) => `${(method as Method).toLocaleLowerCase()}${url}`,
  retry: 0,
  retryDelay: 200,
  retryDelayRise: true,
  cache: false
};

const ERROR_TYPE = {
  Cancel: 'cancelDuplicated'
};

class MAxios {
  public name: string = 'MAxios';
  private requestMap = new Map();
  private responseCacheMap = new Map();

  /**
   * 生成标识请求的唯一key
   */
  private getDuplicatedKey(config: IConfig) {
    const { duplicatedKey = () => '' } = config;
    return duplicatedKey(config);
  }
  /**
   * 添加请求
   */
  private addReq(config: IConfig) {
    const { cancelDuplicated } = config;
    if (!cancelDuplicated) {
      return;
    }
    const key = this.getDuplicatedKey(config);
    if (!this.requestMap.has(key)) {
      config.cancelToken = new axios.CancelToken((c) => {
        this.requestMap.set(key, c);
      });
    }
  }
  /**
   * 移除请求
   */
  private removeReq(config: IConfig) {
    try {
      const { cancelDuplicated } = config;
      const key = this.getDuplicatedKey(config);
      if (!cancelDuplicated) {
        return;
      }
      if (!this.requestMap.has(key)) {
        return;
      }
      const cancel = this.requestMap.get(key);
      this.requestMap.delete(key);
      cancel({
        type: ERROR_TYPE.Cancel,
        message: 'Request canceled '
      });
    } catch (error) {
      console.log('removeReq: ', error);
    }
  }

  /**
   * 重试某次请求
   */
  private retry({
    instance,
    config,
    error
  }: {
    instance: AxiosInstance;
    config: IConfig;
    error: AxiosError;
  }) {
    const { retry, retryDelay, retryDelayRise } = config;
    let retryCount = config.__retryCount || 0;
    config.__retryCount = retryCount;
    // 检查是否超过重置次数
    if (retryCount >= retry!) {
      return Promise.reject(error);
    }
    // 重发计数器加1
    retryCount += 1;
    config.__retryCount = retryCount;

    if (retryCount === retry) {
      config.timeout = 15000;
    }
    // 延时重发
    let delay = 0;
    if (typeof retryDelay === 'number') {
      delay = retryDelay * (retryDelayRise ? retryCount : 1);
    } else {
      delay = retryDelay!(retryCount);
    }

    const retryTask = new Promise<void>((resolve) => {
      setTimeout(() => {
        resolve();
      }, delay);
    });
    return retryTask.then(() => {
      return instance.request(config);
    });
  }
  /**
   * 获取response缓存
   * @param {*} config
   */
  private getResponseCache(config: IConfig) {
    const key = this.getDuplicatedKey(config);
    if (this.responseCacheMap.has(key)) {
      return this.responseCacheMap.get(key);
    }
    return null;
  }
  /**
   * 设置response缓存
   * @param {*} config
   * @param {*} response
   */
  private setResponseCache(config: IConfig, response: AxiosResponse) {
    if (!config.cache) {
      return;
    }
    const key = this.getDuplicatedKey(config);
    this.responseCacheMap.set(key, response);
  }

  /**
   * 实例工厂函数
   */
  private getAxiosInstance(config: IConfig) {
    const instance = axios.create();
    return instance;
  }
  /**
   * 请求拦截器
   * @param {*} instance
   */
  private interceptorsRequest(instance: AxiosInstance) {
    instance.interceptors.request.use(
      (config) => {
        // Do something before request is sent
        this.removeReq(config);
        this.addReq(config);
        return config;
      },
      (error) => {
        // Do something with request error
        return Promise.reject(error);
      }
    );
  }
  /**
   * 响应拦截器
   * @param {*} instance
   */
  private interceptorsResponse(instance: AxiosInstance) {
    instance.interceptors.response.use(
      (response) => {
        // Do something with response data with status code 2xx
        this.removeReq(response.config);
        this.setResponseCache(response.config, response);
        return response;
      },
      (error) => {
        const config = error.config || {};
        this.removeReq(config);

        if (axios.isCancel(error)) {
          return Promise.reject(error.message);
        } else {
          // retry 重试逻辑
          if (config.retry > 0) {
            return this.retry({ instance, config, error });
          }
          return Promise.reject(error);
        }
      }
    );
  }

  private interceptors(instance: AxiosInstance) {
    this.interceptorsRequest(instance);
    this.interceptorsResponse(instance);
  }

  /**
   * public request 对外接口
   * @param {*} config 当前请求的配置参数
   */
  public request(options: IConfig) {
    const config = Object.assign({}, defaultConfig, options);

    if (config.cache) {
      const responseCache = this.getResponseCache(config);
      if (responseCache) {
        return Promise.resolve(responseCache);
      }
    }

    const instance = this.getAxiosInstance(config);
    this.interceptors(instance);
    return instance.request(config);
  }
}

export default new MAxios();

```
## 源码获取
想直接下载使用源代码的，可以跳转github仓库，包含javascript和Typescript两个版本的。
[源代码获取](https://github.com/donglegend/axios-enhance)

