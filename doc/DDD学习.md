|  名称   |                    解释                     |                  用途                  |
| :-----: | :-----------------------------------------: | :------------------------------------: |
|   DSL   |                领域特定语言                 |                                        |
|   MDC   | 映射诊断上下文（Mapped Diagnostic Context） |   方便在多线程条件下的记录日志的功能   |
| TraceId |                                             | 在分布式系统重用于跟踪请求的唯一标识符 |





- api层

  - dto
    - request（入参）、response（出参）
  - api
    - xxxapi（仅暴露接口）
- facade层  【implements  api层 】
  - rest
    - xxxapi
  - rpc
    - xxxapiProvider 【调用UseCase】

```
  // Facade层是非常薄的一层，直接调用Application Service，即UseCase
        // Facade层的主要内容：
        // 1. JSR303 validation
        // 2. 统一处理异常，包括业务异常和系统异常：把底层的异常转换为对外公开的错误码和错误消息，底层只抛出异常，不处理异常

        // 把该会话通过log4j的MDC机制统一起来，对业务开发无侵入，又能在日志上把一个会话的所有日志串起来
  
```

- application层【被 facade层进行调用， 负责把外部请求，转换为领域对象】

  - service【use case层， 负责对领域服务进行编排】

    - xxxUseCase 【调用domain 层的service】

    ```
    1. 通过MapStruct进行转换：DTO 转为 Domain Model Creator
    2. 通过Creator创建Domain model
    3. 编排一个或多个领域服务，如：
    	shelvingTaskService.addTask(task);
    	stockTaskService.addTask(task)；
    4.	根据第3点的domain service的返回值来构建response
    5. 返回response
    	

- domian层【被application层进行调用】
  - service【实现spec层的 IDomainService  接口】

- spec层【规格约束层】

  - model

    ```
    1. IDomainModel  接口
    /**
     * 领域模型，对应DDD的聚合根.
     * <p>
     * <p>世界由客体组成，主体认识客体的过程也是主体改造客体的过程</p>
     * <p>{@code IDomainModel}是客体，{@code IDomainService}是主体</p>
     * <p>客体是拟物化，体现状态；主体是拟人化，体现过程</p>
     * <p>应用程序的本质是认识世界（读），和改造世界（写）的过程</p>
     * <p>不要强行充血，把主体改变客体的逻辑写到领域模型里：认清主体和客体的关系！</p>
     * <p>
     * <p>领域对象为限界上下文(BC)中受保护对象，绝对不应该将其暴露到外面！</p>
     */
     2. IDomainModelCreator 接口
     /**
     * 领域模型的契约对象，持有业务行为所需要的数据.
     * <p>
     * <p>它本身是JavaBean.</p>
     * <p>{@code Creator}模式，是为了保护领域模型：不是外部系统给模型的每个字段赋值，而是模型从{@code Creator}里挑选字段然后自行赋值.</p>
     * <p>否则，领域模型会蜕变成JavaBean，无法保护自己的状态一致性、完整性、安全性等.</p>
     */
     3. IDomainService 接口
     /**
     * 领域服务.
     * <p>
     * <p>领域服务是主体，主体认识和改造客体({@code IDomainModel})</p>
     * <p>本框架内，领域服务根据粒度的粗细分为3层：</p>
     * <pre>
     *               +--------------------+
     *               |  BaseDomainAbility |
     *      +-----------------------------|
     *      |                 IDomainStep |
     * +----------------------------------|
     * |                   IDomainService |
     * +----------------------------------+
     * </pre>
     */
    ```

    
