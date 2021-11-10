# datasource-proxy-log-formatter
Extracts, interpolates, formats and pretty prints SQL from [datasource-proxy](https://github.com/ttddyy/datasource-proxy) json logs.

## Implementation details

- parses raw SLF4J logs using RegEx
- extracts and interpolates queries logged by [datasource-proxy](https://github.com/ttddyy/datasource-proxy) in JSON format
- formats the queries using [sql-formatter](https://github.com/zeroturnaround/sql-formatter)
- highlights the queries using [highlight.js](https://github.com/highlightjs/highlight.js)

## Screenshots

![image](https://user-images.githubusercontent.com/1070579/141092105-873e98f6-84ef-48d0-94d8-ab38778ae5a1.png)


## Configuring Datasource Proxy for Spring Boot Tests

#### pom.xml
```xml
 <dependency>
    <groupId>net.ttddyy</groupId>
    <artifactId>datasource-proxy</artifactId>
    <version>1.7</version>
    <scope>test</scope>
</dependency>
```

#### DatasourceProxyConfig.java
```java
package my.app;

// imports skipped

@TestConfiguration
class DatasourceProxyConfig implements BeanPostProcessor {

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

        if (bean instanceof DataSource) {
            return ProxyDataSourceBuilder
                .create((DataSource) bean)
                .name("dataSource-proxy")
                .logQueryBySlf4j(SLF4JLogLevel.DEBUG)
                .asJson()
                .build();
        }

        return bean;
    }
}
```

#### BaseIT.java
```java
package my.app;

// imports skipped

@SpringBootTest(classes = {
    Application.class,
    DatasourceProxyConfig.class
})
@RunWith(SpringRunner.class)
@ActiveProfiles("integration")
public abstract class BaseIT {
    // ...
}
```

#### application-integration.yml
```yaml
# ...
logging:
  level:
    # Set SLF4JQueryLoggingListener log level to "debug" to enable Datasource Proxy Query logging
    net.ttddyy.dsproxy.listener.logging.SLF4JQueryLoggingListener: off
# ...
```

For more configuration examples check [datasource-proxy-examples](https://github.com/ttddyy/datasource-proxy-examples)
