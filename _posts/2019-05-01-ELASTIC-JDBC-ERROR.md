---
title: ''[ELASTIC] Error at bringing DB from mysql to logstash'
date: 2019-05-01
categories: [ELASTIC]
---

![image](https://user-images.githubusercontent.com/48308562/57010232-a4c63e00-6c36-11e9-812c-5c5b2a2aae95.png)

mysql-connector을 다운받아 lib 밑에 넣어준다.

```
input {
  jdbc {
    jdbc_driver_library => "C:/elastic/logstash/lib/mysql-connector-java-8.0.16.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/youtube"
    jdbc_user => "root"
    jdbc_password => "111111"

    statement => "select title from information"
    jdbc_pool_timeout => 10
    jdbc_paging_enabled => true
    jdbc_page_size => 10000
  }
}

output {
  elasticsearch {
    "hosts" => "localhost:9200"
    "index" => "youtube_information"
    "document_type" => "title"
  }
  stdout {
    codec => rubydebug
  }
}

```

logstash에서 db와 연동시켜 줄 conf 파일을 만든다.

```
bin\logstash -f config\mysql-jdbc.conf
```

CMD에서 명령어를 실행시켜 주면 logstash에서 내 mysql에 있는 youtube DB에 존재하는 information 테이블의 title만 읽어서 elasticsearch에 자동 색인을 해 주어야 하는데 에러가 발생한다.

```
[2019-05-01T17:24:06,801][ERROR][logstash.inputs.jdbc     ] Failed to load C:/elastic/logstash/lib/mysql-connector-java-8.0.16.jar {:exception=>#<TypeError: failed to coerce jdk.internal.loader.ClassLoaders$AppClassLoader to java.net.URLClassLoader>}
[2019-05-01T17:24:06,808][ERROR][logstash.javapipeline    ] A plugin had an unrecoverable error. Will restart this plugin.
  Pipeline_id:main
  Plugin: <LogStash::Inputs::Jdbc jdbc_user=>"root", jdbc_paging_enabled=>true, jdbc_password=><password>, statement=>"select title from information", jdbc_page_size=>10000, jdbc_pool_timeout=>10, jdbc_driver_library=>"C:/elastic/logstash/lib/mysql-connector-java-8.0.16.jar", jdbc_connection_string=>"jdbc:mysql://127.0.0.1:3306/youtube", id=>"728cb1c834fec3947252279a6f7c06b7acba98d68845c04b5112179761bd03c8", jdbc_driver_class=>"com.mysql.jdbc.Driver", enable_metric=>true, codec=><LogStash::Codecs::Plain id=>"plain_d56aee00-e430-40c9-8068-1e3d44c44923", enable_metric=>true, charset=>"UTF-8">, jdbc_validate_connection=>false, jdbc_validation_timeout=>3600, sql_log_level=>"info", connection_retry_attempts=>1, connection_retry_attempts_wait_time=>0.5, parameters=>{"sql_last_value"=>1970-01-01 00:00:00 UTC}, last_run_metadata_path=>"C:\\Users\\zlslsp54/.logstash_jdbc_last_run", use_column_value=>false, tracking_column_type=>"numeric", clean_run=>false, record_last_run=>true, lowercase_column_names=>true>
  Error: com.mysql.jdbc.Driver not loaded. Are you sure you've included the correct jdbc driver in :jdbc_driver_library?
  Exception: LogStash::ConfigurationError
  Stack: C:/elastic/logstash/vendor/bundle/jruby/2.5.0/gems/logstash-input-jdbc-4.3.13/lib/logstash/plugin_mixins/jdbc/jdbc.rb:163:in `open_jdbc_connection'
C:/elastic/logstash/vendor/bundle/jruby/2.5.0/gems/logstash-input-jdbc-4.3.13/lib/logstash/plugin_mixins/jdbc/jdbc.rb:221:in `execute_statement'
C:/elastic/logstash/vendor/bundle/jruby/2.5.0/gems/logstash-input-jdbc-4.3.13/lib/logstash/inputs/jdbc.rb:277:in `execute_query'
C:/elastic/logstash/vendor/bundle/jruby/2.5.0/gems/logstash-input-jdbc-4.3.13/lib/logstash/inputs/jdbc.rb:263:in `run'
C:/elastic/logstash/logstash-core/lib/logstash/java_pipeline.rb:297:in `inputworker'
C:/elastic/logstash/logstash-core/lib/logstash/java_pipeline.rb:290:in `block in start_input'
```

위에 보이는 에러중

```
Error: com.mysql.jdbc.Driver not loaded. Are you sure you've included the correct jdbc driver in :jdbc_driver_library?
```

이 부분이 main 에러인 것 같다. 구글링 해서 찾아본 결과 lib에 설치한 `mysql-connector-java-8.0.16.jar"` 파일이 비어있을 때 발생한다고 하는데 내 파일에서 찾아본 결과 모두 정상적으로 존재했다.(아래 그림)


![image](https://user-images.githubusercontent.com/48308562/57010444-a6dccc80-6c37-11e9-87d3-5696f2ccc1f5.png)
