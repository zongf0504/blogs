# 初始化官方测试数据
> Kibana 官方提供了测试数据, 用于学习和使用kibana.

## 初始化脚本

```bash
#!/bin/bash
#Desc 初始化kibana 官方测试数据

# elastic web访问地址
elastic="http://172.22.12.225:9200"

# json 文件所在目录
json_dir=/home/admin/elk/kibana/demo

# 1.创建索引
echo "[1/3] 开始创建索引:"
printf "%-20s:\t" "shakespeare"
curl -XPUT $elastic/shakespeare -d '  
{
    "mappings": {
        "_default_": {
            "properties": {
                "speaker": {
                    "type": "string",
                    "index": "not_analyzed"
                },
                "play_name": {
                    "type": "string",
                    "index": "not_analyzed"
                },
                "line_id": {
                    "type": "integer"
                },
                "speech_number": {
                    "type": "integer"
                }
            }
        }
    }
}
';  

printf "\n%-20s:\t" "logstash-2015.05.18"
curl -X PUT $elastic/logstash-2015.05.18 -d ' {
    "mappings": {
        "log": {
            "properties": {
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"
                        }
                    }
                }
            }
        }
    }
}';


printf "\n%-20s:\t" "logstash-2015.05.19"
curl -XPUT $elastic/logstash-2015.05.19 -d '
{
    "mappings": {
        "log": {
            "properties": {
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"
                        }
                    }
                }
            }
        }
    }
}
';

printf "\n%-20s:\t" "logstash-2015.05.20"
curl -XPUT $elastic/logstash-2015.05.20 -d '
{
    "mappings": {
        "log": {
            "properties": {
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"
                        }
                    }
                }
            }
        }
    }
}
';

# 2. 导入数据:
echo -e "\n\n[2/3] 开始导入数据:"
echo "导入:$json_dir/accounts.json"
curl -XPOST "$elastic/bank/account/_bulk?pretty" --data-binary @$json_dir/accounts.json > /dev/null

echo -e "\n导入:$json_dir/shakespeare.json"
curl -XPOST "$elastic/shakespeare/_bulk?pretty" --data-binary @$json_dir/shakespeare.json > /dev/null

echo -e "\n导入:$json_dir/logs.jsonl"
curl -XPOST "$elastic/172.22.12.225:9200/_bulk?pretty" --data-binary @$json_dir/logs.jsonl > /dev/null

# 3. 测试
echo -e "\n[3/3] 查看索引:"
curl "$elastic/_cat/indices?v"

[admin@localhost demo]$ 

```

## 脚本执行结果
```bash
[admin@localhost demo]$ ./init_data.sh 
[1/3] 开始创建索引:
shakespeare         :   {"acknowledged":true,"shards_acknowledged":true}
logstash-2015.05.18 :   {"acknowledged":true,"shards_acknowledged":true}
logstash-2015.05.19 :   {"acknowledged":true,"shards_acknowledged":true}
logstash-2015.05.20 :   {"acknowledged":true,"shards_acknowledged":true}

[2/3] 开始导入数据:
导入:/home/admin/elk/kibana/demo/accounts.json
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  559k  100  320k  100  239k  1368k  1021k --:--:-- --:--:-- --:--:-- 1374k

导入:/home/admin/elk/kibana/demo/shakespeare.json
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 59.5M  100 35.4M  100 24.0M   446k   302k  0:01:21  0:01:21 --:--:--  465k

导入:/home/admin/elk/kibana/demo/logs.jsonl
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 55.6M  100 4868k  100 50.8M   224k  2397k  0:00:21  0:00:21 --:--:--     0

[3/3] 查看索引:
health status index               uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank                VCTbo-gYS62uLgsd__Q4XA   5   1       1000            0    640.2kb        640.2kb
yellow open   logstash-2015.05.19 B3rXiOeCRQ2F9FTNXvEAWg   5   1       4624            0     32.7mb         32.7mb
yellow open   .kibana             03-_N6n9Qi24T4x_WOs3YA   1   1          9            0     58.3kb         58.3kb
yellow open   logstash-2015.05.18 8cFWN6eaQrmt3G4KAOOSBw   5   1       4631            0     31.6mb         31.6mb
yellow open   shakespeare         hm82loVRQl-cvJf6mlbxFQ   5   1     111396            0     27.9mb         27.9mb
yellow open   logstash-2015.05.20 JLG4XkBuQUSOP_iiDKwC8A   5   1       4750            0     33.9mb         33.9mb
[admin@localhost demo]$ 

```