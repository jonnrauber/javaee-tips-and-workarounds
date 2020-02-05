# Como criar níveis personalizados de log

Por padrão os níveis de log existentes no log4j são:

Level | intLevel
--- | ---:
OFF | 0
FATAL | 100
ERROR | 200
WARN | 300
INFO | 400
DEBUG | 500
TRACE | 600
ALL | Integer.MAX_VALUE

Para criar um novo nível denominado AUDIT, basta adicionar no arquivo de configurações do log4j:
```xml
<Configuration status="INFO">
  ...
  <CustomLevels>
    <CustomLevel name="AUDIT" intLevel="700" />
  </CustomLevels>
  ...
</Configuration>
```

# Fazer com que apenas um nível de log seja logado em um appender

Se configurar um log para o nível "AUDIT", o mesmo irá logar todos os intLevel <= 700 (intLevel que configurei para o AUDIT), pois os levels são **hierárquicos**.
Por exemplo, veja um appender que controle os logs que são exibidos no console padrão (sysout):

```xml
<Appenders>
  <Console name="Console" target="SYSTEM_OUT">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
  </Console>
</Appenders>
<Loggers>
  <Root level="all">
    <AppenderRef ref="Console" level="warn" />
  </Root>
</Loggers>
```

Nesse caso, o level setado para exibição é "warn". Isso significa que serão logados os WARNs, ERRORs e FATALs, e não serão logados os INFOs e levels com intLevel superior ao do WARN (300).

Então, se quiser que algum appender logue APENAS o level "audit", deve ser adicionado um filter ao appender:

```xml
<Console name="Console" target="SYSTEM_OUT">
  <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
  <Filters>
    <!-- bloqueia todos os demais níveis -->
    <ThresholdFilter level="trace" onMatch="DENY" onMismatch="NEUTRAL"/>
    <ThresholdFilter level="debug" onMatch="DENY" onMismatch="NEUTRAL"/>
    <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
    <ThresholdFilter level="warn"  onMatch="DENY" onMismatch="NEUTRAL"/>
    <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>
    <ThresholdFilter level="fatal" onMatch="DENY" onMismatch="NEUTRAL"/>

    <!-- aceita apenas o nível audit -->
    <ThresholdFilter level="audit"  onMatch="ACCEPT" onMismatch="DENY"/>
  </Filters>
</Console>
```

# Efetuar log em tabela do banco de dados com appender JDBC

Existe uma tag específica para appender chamada JDBC. O exemplo abaixo cria um appender de nome "databaseAudit", que baseado no datasource passado com o jndiName, grava as colunas definidas na tabela *tableName*:

```xml
<JDBC name="databaseAudit" tableName="NOME_DA_TABELA_DE_LOG">
  <DataSource jndiName="java:/{{ JNDI }}" />
  <Column name="user_id" pattern="%X{userId}" /> <!-- parâmetro setado pelo ThreadContext -->
  <Column name="operation" pattern="%X{operation}" /> <!-- parâmetro setado pelo ThreadContext -->
  <Column name="event_date" isEventTimestamp="true" /> <!-- coluna EVENT_DATE irá conter o timestamp do momento em que foi logado -->
  <Column name="log_level" pattern="%level" /> <!-- coluna LOG_LEVEL -->
  <Column name="logger" pattern="%logger" /> <!-- coluna LOGGER, insere o nome da classe que chamou o logger -->
  <Column name="message" pattern="%message" /> <!-- coluna MESSAGE, insere a string passada junto no momento da chamada para logger.log() -->
</JDBC>
```

# Guardar na tabela de log qual o usuário que fez a operação

Para colocar uma informação extra na tabela de log, como dizer qual o usuário que fez a operação, precisamos chamar a seguinte instrução antes de fazer o log:
```java
ThreadContext.put("userId", loggedUser);
ThreadContext.put("operation", "INSERT");
```

Assim, na coluna `USER_ID` será colocado o valor da string `loggerUser`, e na coluna `OPERATION` a string "INSERT".
