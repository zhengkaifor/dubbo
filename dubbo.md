

# dubbo

## 架构图

## 组件

### SPI

dubbo实现SPI 机制主要实现类为org.apache.dubbo.common.extension.ExtensionLoader

#### 总体流程

ExtensionLoader.getExtensionLoader(xxx.clss).getExtension(name);

首先通过class 的type 获取extensionLoader类，在getExtension中通过LoadingStrategy加载指定包中对应接口的拓展类，

之后通过name获取指定的拓展类，如果该接口存在wrapper的拓展类，将拓展类包装成wrapper类返回

#### adaptive拓展

dubbo中有一个adaptive 拓展类，在执行getAdaptiveExtension（）方法时，会返回一个适配拓展类

##### 代码

```java
@SuppressWarnings("unchecked")
    public T getAdaptiveExtension() {
        Object instance = cachedAdaptiveInstance.get();
        if (instance == null) {
            if (createAdaptiveInstanceError != null) {
                throw new IllegalStateException("Failed to create adaptive instance: " +
                        createAdaptiveInstanceError.toString(),
                        createAdaptiveInstanceError);
            }

            synchronized (cachedAdaptiveInstance) {
                instance = cachedAdaptiveInstance.get();
                if (instance == null) {
                    try {
                        //动态编译 生成adaptive类
                        //如果cachedAdaptiveClass不为null 则直接返回该class 对象
                        //否则通过动态编译 生成相应的xxx$Adaptive
                        //xxx$Adaptive中仅支持 方法上有@Adaptive注解的方法可以执行,否则报错
                        instance = createAdaptiveExtension();
                        cachedAdaptiveInstance.set(instance);
                    } catch (Throwable t) {
                        createAdaptiveInstanceError = t;
                        throw new IllegalStateException("Failed to create adaptive instance: " + t.toString(), t);
                    }
                }
            }
        }

        return (T) instance;
    }
```

##### 例子

###### Protocol

```java
package org.apache.dubbo.rpc;

import org.apache.dubbo.common.extension.ExtensionLoader;

public class Protocol$Adaptive implements org.apache.dubbo.rpc.Protocol {
    public java.util.List getServers() {
        throw new UnsupportedOperationException("The method public default java.util.List org.apache.dubbo.rpc.Protocol.getServers() of interface org.apache.dubbo.rpc.Protocol is not adaptive method!");
    }

    public org.apache.dubbo.rpc.Exporter export(org.apache.dubbo.rpc.Invoker arg0) throws org.apache.dubbo.rpc.RpcException {
        if (arg0 == null) throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument == null");
        if (arg0.getUrl() == null)
            throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument getUrl() == null");
        org.apache.dubbo.common.URL url = arg0.getUrl();
        String extName = (url.getProtocol() == null ? "dubbo" : url.getProtocol());
        if (extName == null)
            throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.Protocol) name from url (" + url.toString() + ") use keys([protocol])");
        org.apache.dubbo.rpc.Protocol extension = (org.apache.dubbo.rpc.Protocol) ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.Protocol.class).getExtension(extName);
        return extension.export(arg0);
    }

    public void destroy() {
        throw new UnsupportedOperationException("The method public abstract void org.apache.dubbo.rpc.Protocol.destroy() of interface org.apache.dubbo.rpc.Protocol is not adaptive method!");
    }

    public int getDefaultPort() {
        throw new UnsupportedOperationException("The method public abstract int org.apache.dubbo.rpc.Protocol.getDefaultPort() of interface org.apache.dubbo.rpc.Protocol is not adaptive method!");
    }

    public org.apache.dubbo.rpc.Invoker refer(java.lang.Class arg0, org.apache.dubbo.common.URL arg1) throws org.apache.dubbo.rpc.RpcException {
        if (arg1 == null) throw new IllegalArgumentException("url == null");
        org.apache.dubbo.common.URL url = arg1;
        String extName = (url.getProtocol() == null ? "dubbo" : url.getProtocol());
        if (extName == null)
            throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.Protocol) name from url (" + url.toString() + ") use keys([protocol])");
        org.apache.dubbo.rpc.Protocol extension = (org.apache.dubbo.rpc.Protocol) ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.Protocol.class).getExtension(extName);
        return extension.refer(arg0, arg1);
    }
}
```

###### ProxyFactory

```java
package org.apache.dubbo.rpc;

import org.apache.dubbo.common.extension.ExtensionLoader;

public class ProxyFactory$Adaptive implements org.apache.dubbo.rpc.ProxyFactory {
    public java.lang.Object getProxy(org.apache.dubbo.rpc.Invoker arg0) throws org.apache.dubbo.rpc.RpcException {
        if (arg0 == null) throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument == null");
        if (arg0.getUrl() == null)
            throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument getUrl() == null");
        org.apache.dubbo.common.URL url = arg0.getUrl();
        String extName = url.getParameter("proxy", "javassist");
        if (extName == null)
            throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.ProxyFactory) name from url (" + url.toString() + ") use keys([proxy])");
        org.apache.dubbo.rpc.ProxyFactory extension = (org.apache.dubbo.rpc.ProxyFactory) ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.ProxyFactory.class).getExtension(extName);
        return extension.getProxy(arg0);
    }

    public java.lang.Object getProxy(org.apache.dubbo.rpc.Invoker arg0, boolean arg1) throws org.apache.dubbo.rpc.RpcException {
        if (arg0 == null) throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument == null");
        if (arg0.getUrl() == null)
            throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument getUrl() == null");
        org.apache.dubbo.common.URL url = arg0.getUrl();
        String extName = url.getParameter("proxy", "javassist");
        if (extName == null)
            throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.ProxyFactory) name from url (" + url.toString() + ") use keys([proxy])");
        org.apache.dubbo.rpc.ProxyFactory extension = (org.apache.dubbo.rpc.ProxyFactory) ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.ProxyFactory.class).getExtension(extName);
        return extension.getProxy(arg0, arg1);
    }

    public org.apache.dubbo.rpc.Invoker getInvoker(java.lang.Object arg0, java.lang.Class arg1, org.apache.dubbo.common.URL arg2) throws org.apache.dubbo.rpc.RpcException {
        if (arg2 == null) throw new IllegalArgumentException("url == null");
        org.apache.dubbo.common.URL url = arg2;
        String extName = url.getParameter("proxy", "javassist");
        if (extName == null)
            throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.ProxyFactory) name from url (" + url.toString() + ") use keys([proxy])");
        org.apache.dubbo.rpc.ProxyFactory extension = (org.apache.dubbo.rpc.ProxyFactory) ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.ProxyFactory.class).getExtension(extName);
        return extension.getInvoker(arg0, arg1, arg2);
    }
}
```



#### 代码

```java
@SuppressWarnings("unchecked")
//获取拓展loader类
public static <T> ExtensionLoader<T> getExtensionLoader(Class<T> type) {
    if (type == null) {
        throw new IllegalArgumentException("Extension type == null");
    }
    if (!type.isInterface()) {
        throw new IllegalArgumentException("Extension type (" + type + ") is not an interface!");
    }
    if (!withExtensionAnnotation(type)) {
        throw new IllegalArgumentException("Extension type (" + type +
                ") is not an extension, because it is NOT annotated with @" + SPI.class.getSimpleName() + "!");
    }

    ExtensionLoader<T> loader = (ExtensionLoader<T>) EXTENSION_LOADERS.get(type);
    if (loader == null) {
        //这里设置type 跟objectFactory
        EXTENSION_LOADERS.putIfAbsent(type, new ExtensionLoader<T>(type));
        loader = (ExtensionLoader<T>) EXTENSION_LOADERS.get(type);
    }
    return loader;
}
```

获取拓展类

```java
/**
 * Find the extension with the given name. If the specified name is not found, then {@link IllegalStateException}
 * will be thrown.
 */
@SuppressWarnings("unchecked")
public T getExtension(String name) {
    //获取最终适配类,默认返回wrapper对象
    return getExtension(name, true);
}

public T getExtension(String name, boolean wrap) {
    if (StringUtils.isEmpty(name)) {
        throw new IllegalArgumentException("Extension name == null");
    }
    if ("true".equals(name)) {
        return getDefaultExtension();
    }
    final Holder<Object> holder = getOrCreateHolder(name);
    Object instance = holder.get();
    if (instance == null) {
        synchronized (holder) {
            instance = holder.get();
            if (instance == null) {
                //生成拓展类
                instance = createExtension(name, wrap);
                holder.set(instance);
            }
        }
    }
    return (T) instance;
}
```

创建拓展类对象

```java
@SuppressWarnings("unchecked")
private T createExtension(String name, boolean wrap) {
    //getExtensionClasses 默认从META-INF/dubbo/internal/   META-INF/dubbo/   这两个目录下读取对应type的拓展类
    Class<?> clazz = getExtensionClasses().get(name);
    if (clazz == null) {
        throw findException(name);
    }
    try {
        T instance = (T) EXTENSION_INSTANCES.get(clazz);
        if (instance == null) {
            EXTENSION_INSTANCES.putIfAbsent(clazz, clazz.newInstance());
            instance = (T) EXTENSION_INSTANCES.get(clazz);
        }
        //生成对象,set方法如果也有spi  同时注入
        injectExtension(instance);

        //生成wrapper
        if (wrap) {

            List<Class<?>> wrapperClassesList = new ArrayList<>();
            if (cachedWrapperClasses != null) {
                wrapperClassesList.addAll(cachedWrapperClasses);
                wrapperClassesList.sort(WrapperComparator.COMPARATOR);
                Collections.reverse(wrapperClassesList);
            }
            //多个wrapper 循环生成, 最终可能是 wrapper->wrapper->instance
            if (CollectionUtils.isNotEmpty(wrapperClassesList)) {
                for (Class<?> wrapperClass : wrapperClassesList) {
                    Wrapper wrapper = wrapperClass.getAnnotation(Wrapper.class);
                    if (wrapper == null
                            || (ArrayUtils.contains(wrapper.matches(), name) && !ArrayUtils.contains(wrapper.mismatches(), name))) {
                        instance = injectExtension((T) wrapperClass.getConstructor(type).newInstance(instance));
                    }
                }
            }
        }

        initExtension(instance);
        return instance;
    } catch (Throwable t) {
        throw new IllegalStateException("Extension instance (name: " + name + ", class: " +
                type + ") couldn't be instantiated: " + t.getMessage(), t);
    }
}
```

加载拓展类 class

![image-20210526135551517](https://gitee.com/zk94/oss/raw/master/uPic/image-20210526135551517.png)

```java
/**
 * synchronized in getExtensionClasses
 */
private Map<String, Class<?>> loadExtensionClasses() {
    //通过spi 注解 获取 spi.value ,设置cachedDefaultName , 在getDefaultExtension中使用
    cacheDefaultExtensionName();

    Map<String, Class<?>> extensionClasses = new HashMap<>();
    //加载该接口的所有拓展类,同时如果类上有@adaptive注解,cachedAdaptiveClass=该类
    //同一个接口的拓展类同时只能有一个类上有@adaptive,否则根据配置报错或者覆盖
    //同时如果类的构造函数参数包含该接口,将被添加到cachedWrapperClasses中
    for (LoadingStrategy strategy : strategies) {
        loadDirectory(extensionClasses, strategy.directory(), type.getName(), strategy.preferExtensionClassLoader(), strategy.overridden(), strategy.excludedPackages());
        loadDirectory(extensionClasses, strategy.directory(), type.getName().replace("org.apache", "com.alibaba"), strategy.preferExtensionClassLoader(), strategy.overridden(), strategy.excludedPackages());
    }

    return extensionClasses;
}
```

### Protocol

协议接口，该接口主要提供了两个能力，export,refer.

其中export负责暴露本地服务到注册中心

refer负责从注册中心拉取服务到本地

###### export

​	ServiceConfig->解析bean,组装暴露url->RegistryProtocol.export();	

###### refer

​	ReferenceConfig->解析bean->ReferenceConfig.get->ReferenceConfig.createProxy

->RegistryProtocol.refer->getInvoker中获取DynamicDirectory->Zookeeper.register 注册到consumer中

->DynamicDirectory.subscribe获取provider，category，configurators，routers 目录下的url信息->增加ZK节点监听

->RegistryDirectory.refreshInvoker 刷新本地invoker

###### invoker

​	Protocol会根据url头上的协议不同生成相应的invoker，如果是register则生成clusterInvoker，dubbo则生成dubboInvoker等

### registry

注册中心，提供将接口信息注册到注册中心的抽象接口，目前官方提供的有zookeeper,redis,nacos等

registry还提供了订阅功能，即由registry处理订阅节点发生变化的情况

当前实现为(以zookeeper为例)

RegistryProtocol->export->registryFactory.getRegistry->ZookeeperRegistryFactory.createRegistry

->ZookeeperRegistry.ZookeeperTransporter.connect

###### 监听

在作为provider时：

注册中心会订阅configurators节点

作为consumer时：

注册中心会订阅configurators，providers，routers三个节点

利用该特性，我们可以通过dubboAdmin动态修改运行中配置。该特性实际为刷新Directory中的invoker。

dubbo中监听节点发生变化时，需要注册中心下发全量数据进行处理，即每次变更后都会下发全量的节点数据到客户端

### Directory

#### 概念

动态注册信息，consumer会将从zk上取回来的providers信息存放在Directory中，其中Directory.url为RegistryUrl，

Directory.consumerUrl为注册到zookeeper上的url（这里有个bug，具体看问题章节）。

每当zookeeper监听到configurators，providers，routers三个节点的任一变化后，都会触发org.apache.dubbo.registry.integration.RegistryDirectory#notify，在该接口中，会重新刷新，改写invoker（列如provider下线，动态修改配置等都会触发该接口）

#### 代码

```java
@Override
    public synchronized void notify(List<URL> urls) {
        Map<String, List<URL>> categoryUrls = urls.stream()
                .filter(Objects::nonNull)
                .filter(this::isValidCategory)
                .filter(this::isNotCompatibleFor26x)
                .collect(Collectors.groupingBy(this::judgeCategory));

        List<URL> configuratorURLs = categoryUrls.getOrDefault(CONFIGURATORS_CATEGORY, Collections.emptyList());
        this.configurators = Configurator.toConfigurators(configuratorURLs).orElse(this.configurators);

        List<URL> routerURLs = categoryUrls.getOrDefault(ROUTERS_CATEGORY, Collections.emptyList());
        toRouters(routerURLs).ifPresent(this::addRouters);

        // providers
        List<URL> providerURLs = categoryUrls.getOrDefault(PROVIDERS_CATEGORY, Collections.emptyList());
        /**
         * 3.x added for extend URL address
         */
        ExtensionLoader<AddressListener> addressListenerExtensionLoader = ExtensionLoader.getExtensionLoader(AddressListener.class);
        List<AddressListener> supportedListeners = addressListenerExtensionLoader.getActivateExtension(getUrl(), (String[]) null);
        if (supportedListeners != null && !supportedListeners.isEmpty()) {
            for (AddressListener addressListener : supportedListeners) {
                providerURLs = addressListener.notify(providerURLs, getConsumerUrl(),this);
            }
        }
        refreshOverrideAndInvoker(providerURLs);
    }
```

### Cluster

dubbo集群实现类，也可以说是invoker的工厂类。通过

```java
protected abstract <T> AbstractClusterInvoker<T> doJoin(Directory<T> directory) throws RpcException;
```

这个方法生成相应的invoker，也是dubbo的拓展点。可通过配置url上的cluster值来指定

常用的有以下几种：

1.FailoverCluster--若无配置，默认为该类型。 失败跳过

2.FailbackCluster--失败即返回，且记录失败请求，在后台自动重试

3.FailfastCluster--仅执行一次，失败即返回

4.FailsafeCluster--失败后返回空对象，不返回错误。

5.ForkingCluster--同时调用多个invoker

6.BroadcastCluster--遍历调用全部invoker

### Invoker

有两种。

ClusterInvoker:我们最终所使用的invoker，一个ClusterInvoker持有多个普通的invoker（provider），其中多个普通的provider保存在Directory中

dubboInvoker：被ClusterInvoker持有，由ClusterInvoker选出需要执行方法的dubboInvoker 执行方法。

### Filter

在ProtocolFilterWrapper中进行构建 在生成普通invoker时

```java
@Override
public <T> Exporter<T> export(Invoker<T> invoker) throws RpcException {
    if (UrlUtils.isRegistry(invoker.getUrl())) {
        return protocol.export(invoker);
    }
    return protocol.export(buildInvokerChain(invoker, SERVICE_FILTER_KEY, CommonConstants.PROVIDER));
}

@Override
public <T> Invoker<T> refer(Class<T> type, URL url) throws RpcException {
    if (UrlUtils.isRegistry(url)) {
        return protocol.refer(type, url);
    }
    return buildInvokerChain(protocol.refer(type, url), REFERENCE_FILTER_KEY, CommonConstants.CONSUMER);
}
```

会构建invoker的调用链，filter会在真实invoker被调用前进行一些处理，比较常见的比如封装异常等

### 问题

###### group不可用

现象：

当provider，consumer都配置dubbo.registry.group=xx时，理论上应该可以调用。但是实际上会提示no provider

原因：

provider如果配置了dubbo.registry.group，会在zk上注册为xx/Interface，其中url为dubbo://xxx。具体注册节点如下

![image-20210603153756762](https://gitee.com/zk94/oss/raw/master/uPic/image-20210603153756762.png)

但是consumer在处理中，其中consumerUrl带上了group标签

```java
public AbstractDirectory(URL url, RouterChain<T> routerChain) {
    if (url == null) {
        throw new IllegalArgumentException("url == null");
    }

    queryMap = StringUtils.parseQueryString(url.getParameterAndDecoded(REFER_KEY));
    String path = queryMap.get(PATH_KEY);
    this.consumedProtocol = this.queryMap.get(PROTOCOL_KEY) == null ? DUBBO : this.queryMap.get(PROTOCOL_KEY);
    this.url = url.removeParameter(REFER_KEY).removeParameter(MONITOR_KEY);
    //url为注册url, 这里直接变更了注册url的协议,并且加上了自身的配置信息,在 url里有group信息的情况下,consumerUrl也带上了group标签
    this.consumerUrl = this.url.setProtocol(consumedProtocol).setPath(path == null ? queryMap.get(INTERFACE_KEY) : path).addParameters(queryMap)
            .removeParameter(MONITOR_KEY);

    setRouterChain(routerChain);
}
```

在生成invoker时，会导致consumerUrl跟providerUrl无法匹配上，导致无法正确生成invoker

```java
public static boolean isMatch(URL consumerUrl, URL providerUrl) {
    String consumerInterface = consumerUrl.getServiceInterface();
    String providerInterface = providerUrl.getServiceInterface();
    //FIXME accept providerUrl with '*' as interface name, after carefully thought about all possible scenarios I think it's ok to add this condition.
    if (!(ANY_VALUE.equals(consumerInterface)
            || ANY_VALUE.equals(providerInterface)
            || StringUtils.isEquals(consumerInterface, providerInterface))) {
        return false;
    }

    if (!isMatchCategory(providerUrl.getParameter(CATEGORY_KEY, DEFAULT_CATEGORY),
            consumerUrl.getParameter(CATEGORY_KEY, DEFAULT_CATEGORY))) {
        return false;
    }
    if (!providerUrl.getParameter(ENABLED_KEY, true)
            && !ANY_VALUE.equals(consumerUrl.getParameter(ENABLED_KEY))) {
        return false;
    }
    //在这里会将consumerUrl 跟providerUrl做match
    String consumerGroup = consumerUrl.getParameter(GROUP_KEY);
    String consumerVersion = consumerUrl.getParameter(VERSION_KEY);
    String consumerClassifier = consumerUrl.getParameter(CLASSIFIER_KEY, ANY_VALUE);

    String providerGroup = providerUrl.getParameter(GROUP_KEY);
    String providerVersion = providerUrl.getParameter(VERSION_KEY);
    String providerClassifier = providerUrl.getParameter(CLASSIFIER_KEY, ANY_VALUE);
    return (ANY_VALUE.equals(consumerGroup) || StringUtils.isEquals(consumerGroup, providerGroup) || StringUtils.isContains(consumerGroup, providerGroup))
            && (ANY_VALUE.equals(consumerVersion) || StringUtils.isEquals(consumerVersion, providerVersion))
            && (consumerClassifier == null || ANY_VALUE.equals(consumerClassifier) || StringUtils.isEquals(consumerClassifier, providerClassifier));
}
```

