

```sql
--简单Case函数
CASE sex
WHEN '1' THEN '男'
WHEN '2' THEN '女'
ELSE '其他' END

--Case搜索函数
CASE WHEN sex = '1' THEN '男'
WHEN sex = '2' THEN '女'
ELSE '其他' END  
```




docker run -d --name kibana -e ELASTICSEARCH_URL=http://192.168.10.143:9200 -p 5601:5601 -d docker.elastic.co/kibana/kibana:7.3.1

 sudo docker exec -it kibana /bin/bash  

 /usr/share/kibana/config/kibana.yml