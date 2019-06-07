# Sentinel Pigeon Adapter


Sentinel Pigeon Adapter provides service invoker interceptor and provider interceptor
for [Pigeon](https://github.com/dianping/pigeon) services.

**Note: This adapter only supports Pigeon 2.9.7 and above.**

To use Sentinel Pigeon Adapter, you can simply add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-pigeon-adapter</artifactId>
    <version>x.y.z</version>
</dependency>
```

The Sentinel filters are **enabled by default**. Once you add the dependency,
the Pigeon services and methods will become protected resources in Sentinel,
which can leverage Sentinel's flow control and guard ability when rules are configured.
Demos can be found in [sentinel-pigeon-demo](https://github.com/wchswchs/sentinel-pigeon/tree/master/sentinel-pigeon-demo).

For more details of Pigeon interceptor, see [here](https://github.com/dianping/pigeon/blob/master/USER_GUIDE.md#%E5%A6%82%E4%BD%95%E5%AE%9A%E4%B9%89%E8%87%AA%E5%B7%B1%E7%9A%84%E6%8B%A6%E6%88%AA%E5%99%A8).

## Dubbo resources

The resource for Pigeon services has following granularity:

- Service method：resourceName format is `interfaceName:methodSignature`，e.g. `com.alibaba.csp.sentinel.demo.pigeon.FooService:sayHello(java.lang.String)`

## Flow control based on caller

In many circumstances, it's also significant to control traffic flow based on the **caller**.
For example, assuming that there are two services A and B, both of them initiate remote call requests to the service provider.
If we want to limit the calls from service B only, we can set the `limitApp` of flow rule as the identifier of service B (e.g. service name).

Sentinel Pigeon Adapter will automatically resolve the Pigeon invoker's *application name* as the caller's name (`origin`),
and will bring the caller's name when doing resource protection.
If `limitApp` of flow rules is not configured (`default`), flow control will take effects on all callers.
If `limitApp` of a flow rule is configured with a caller, then the corresponding flow rule will only take effect on the specific caller.

## Global fallback

Sentinel Pigeon Adapter supports global fallback configuration.
The global fallback will handle exceptions and give replacement result when blocked by
flow control, degrade or system load protection. You can implement your own `PigeonFallback` interface
and then register to `PigeonFallbackRegistry`. If no fallback is configured, Sentinel will wrap the `BlockException`
then directly throw it out.