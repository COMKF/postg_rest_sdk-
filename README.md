## PostgREST-SDK

### 相关资料：
* [PostgREST官方文档](http://postgrest.org/en/stable/index.html)
* [PostgREST-API中文翻译对照](http://note.youdao.com/noteshare?id=d9ebb39ad8084901e7c04fb329ac3535)

### 使用说明
该SDK针对官方API进行了封装，简化了url后面附带的查询条件，使用者可以把更多的精力放在python代码的编写上。

### 简单查询（方法为get）
PostgREST示例url地址为：http://127.0.0.1:3000/ 
PostgREST示例数据库表为：cl

* 简单全表查询

      DBURL = 'http://127.0.0.1:3000/'
      DBNAME = 'cl'
      
      if __name__ == '__main__':
        from postg_rest_sdk import SqlRequest
        # 构建
        sr = SqlRequest(DBURL, DBNAME)
        result = sr.get()

    结果如下：
    
            [{"glbm":"4101","glbmmc":"郑州","zsdwnm":"0"}, 
        {"glbm":"4101","glbmmc":"二大队","zsdwnm":"05"}, 
        {"glbm":"4101","glbmmc":"三大队","zsdwnm":"0"}]

* 条件查询  

根据官方文档，PostgREST最大的特点是它将查询分为行查询和列查询：（以下是个人理解）

    * 行查询：通过对数值的判断，对数据进行过滤，但是必须查全表
    * 列查询：只查询某一些列，而不需要查全表，还可以进行外键关联查询，无法对数据进行条件过滤
    * 是否能混合查询，待定

根据上面这个规则，以及现有的官方文档，PostgREST-SDK制定了以下规则：  

* rtype为查询方式，在以下四个关键字中取值：default（默认）、or、and、select
* data为查询条件，data类型为None时，代表查全表。
    * 当rtype为default时，data必须为dict，示例如下：
    
          data = {'glbm': 'like 4103*', 'glbmmc': '= 洛阳市公安局交通管理支队'}
          result = sr.get(data) 

      结果如下：
            
          [{'glbm': '410300', 'glbmmc': '洛阳市公安局交通管理支队', 'zsdwnm': '0000000459016798'}]  
          
      注意：data必须是dict，它的键可以有多个，而且键必须是表中的字段；值分为两部分，第一部分是运算符，第二部分是数值，可使用的运算符见[PostgREST-API中文翻译对照](http://note.youdao.com/noteshare?id=d9ebb39ad8084901e7c04fb329ac3535)，数值可以使用*作为通配符，运算符和数值之间必须有一个空格。
    
    * 当rtype为or或者and时，data必须为dict，示例如下：
        
          data = {'glbm': 'like 4103*&like 4104*'}
          rtype = 'or'
          result = sr.get(data, rtype=rtype)
          
        结果如下：
            
            [{"glbm":"410300","glbmmc":"洛阳","zsdwnm":"0"},
            {"glbm":"4103","glbmmc":"洛阳","zsdwnm":"0"}, 
            {"glbm":"4103","glbmmc":"轻工队","zsdwnm":"0"},
            {"glbm":"410439","glbmmc":"姚队","zsdwnm":"000"}]
        
        注意：data必须是dict，当需要为一个字段做多个条件查询时，每个条件通过&连接，&连接符前后不能有空格。
    
    * 当rtype为select时，data为str、list、tuple，示例如下：
        
          data = ('glbm', 'glbmmc')
          rtype = 'select'
          result = sr.get(data, rtype=rtype)
        结果如下：
        
            [{"glbm":"4101","glbmmc":"郑州"}, 
             {"glbm":"4101","glbmmc":"二队"}, 
             {"glbm":"4101","glbmmc":"三队"}]
        
        注意：select只做列查询，至于能否执行过滤条件，需要待定。
    
* 排序  
    排序是单独的功能，只需要添加 order 参数就行，如下：
        
      data = ('glbm', 'glbmmc')
      rtype = 'select'
      order = {'glbm': 'desc', 'glbmmc': 'asc'}
      result = sr.get(data, rtype=rtype, order=order)

    结果如下：
    
        [{"glbm":"410","glbmmc":"焦作"}, 
        {"glbm":"410","glbmmc":"焦作"}, 
        {"glbm":"410","glbmmc":"焦作"}]
    
    注意：order是一个dict，键为字段，值为 desc 或 asc。


### 复杂查询

* 能否把简单查询进行混合，待定
* 支持创建视图，创建后可自动生成url访问端点
* 支持过程函数


### 插入数据（方法为post）

### 删除数据（方法为delete）

### 修改数据（方法为patch）


### 更新日志

* 2018-12-12 初版

        
        
