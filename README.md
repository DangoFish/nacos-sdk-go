### nacos-go
go语言版本的nacos client，支持config_client和service_client

#### client的config
- ClientConfig 客户端配置参数  
```go
constant.ClientConfig{
    TimeoutMs:      30 * 1000,
    ListenInterval: 10 * 1000,
    BeatInterval:   5 * 1000,
    NamespaceId:       "public",
    Endpoint:          ""
    CacheDir:         "/data/nacos/cache",
    LogDIr:         "/data/nacos/log",
    UpdateThreadNum:   20
    NotLoadCacheAtStart: true
    UpdateCacheWhenEmpty:true
}
```
TimeoutMs：http请求超时时间，单位毫秒
  
ListenInterval：监听间隔时间，单位毫秒（仅在ConfigClient中有效） 
 
BeatInterval：心跳间隔时间，单位毫秒（仅在ServiceClient中有效）

Endpoint：获取nacos节点ip的服务地址

NamespaceId：nacos命名空间

CacheDir：缓存目录

LogDir:日志目录

UpdateThreadNum：更新服务的线程数

NotLoadCacheAtStart: 在启动时不读取本地缓存数据，true--不读取，false--读取

UpdateCacheWhenEmpty: 当服务列表为空时是否更新本地缓存，true--更新,false--不更新


- ServerConfig nacos服务信息配置参数
```go
    constant.ServerConfig{{
		IpAddr:      "console.nacos.io",
		ContextPath: "/nacos",
		Port:        80,
	}
```
IpAddr：nacos服务的ip地址  
Port：nacos服务端口  
ContextPath：nacos服务的上下文路径，默认是“/nacos”  
<b>注：ServerConfig支持配置多个，在请求出错时，自动切换</b>


#### service_client
    注册服务实例  
    1. RegisterInstance  
    注销服务实例  
    2. DeregisterInstance  
    获取服务
    3. GetService
    获取所有的实例列表 
    4.SelectAllInstancs 
    获取实例列表
    5.SelectInstances
    获取一个健康的实例（加权轮训负载均衡）
    6.SelectOneHealthyInstance
    服务监听
    7. Subscribe
    取消服务监听  
    8. Unsubscribe

### quick start
以GetService为例：  
Step 1. 构造相关参数  
```go
    // 可以没有，采用默认值
    clientConfig := constant.ClientConfig{
    		TimeoutMs:      30 * 1000,
    		ListenInterval: 10 * 1000,
    		BeatInterval:   5 * 1000,
    	} 
    // 至少一个
    serverConfigs := []constant.ServerConfig{
    	{
    	    IpAddr:      "console1.nacos.io",
    	    ContextPath: "/nacos",
    	    Port:        80,
        },
        {
            IpAddr:      "console2.nacos.io",
            ContextPath: "/nacos",
            Port:        80,
        },
    }
```
Step 2. 构造客户端
```go
    // 如果参数设置不合法，将抛出error
    client, err := CreateNamingClient(map[string]interface{}{
    	"serverConfigs": serverConfigs,
    	"clientConfig":  clientConfig,
    })
```
Step 3. 目标操作
```go
        if err == nil {
        	// 从nacos获取服务列表
    		service, _ := client.GetService(vo.GetServiceParam{
            		ServiceName: "demo",
            		Clusters:    []string{"a"},
            	})
    	}
```

