#Nginx 监控Zabbix
> Zabbix 可以用于监控Nginx服务器连接数,请求数,是否存活等状态,监控起来还算是比较简单的. 但是需要在nginx 所在服务器上安装Zabbix_agentd .


## 1. Nginx 开启监控状态功能
* nginx 需要开启状态查询的配置

```bash
location /status {
     stub_status on;
     access_log off;
}
```

* 重启nginx服务器, 并测试

```bash
[root@localhost ~]# nginx -s reload
[root@localhost ~]# curl http://172.22.12.100/status  
Active connections: 3 
server accepts handled requests
 454 454 6886 
Reading: 0 Writing: 1 Waiting: 2 
```

## 2. 编写zabbix 监控脚本
* 笔者zabbix_agentd 的自定义监控脚本存放在/usr/local/etc/zabbix/scripts 中
* 创建监控脚本: vim /usr/local/etc/zabbix/scripts/ngx_status.sh

```bash
#!/bin/bash
#Desc 监控nginx 状态脚本
#Auth zongf
#Date 2017/08/22
 
URL="http://172.22.12.100:80/status/"
 
# 检测nginx进程是否存在
function ping {
    /sbin/pidof nginx | wc -l 
}
# 检测nginx性能
function active {
    curl $URL 2>/dev/null| grep 'Active' | awk '{print $NF}'
}
function reading {
    curl $URL 2>/dev/null| grep 'Reading' | awk '{print $2}'
}
function writing {
    curl $URL 2>/dev/null| grep 'Writing' | awk '{print $4}'
}
function waiting {
    curl $URL 2>/dev/null| grep 'Waiting' | awk '{print $6}'
}
function accepts {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $1}'
}
function handled {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $2}'
}
function requests {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $3}'
}
# 执行function
$1
```

## 3. 测试监控脚本
* 非必须步骤, 但是建议测试一下

```bash
[root@localhost scripts]# pwd
/usr/local/etc/zabbix/scripts
[root@localhost scripts]# ./ngx_status.sh ping  
1
[root@localhost scripts]# ./ngx_status.sh accepts
550
```

## 4. 配置zabbix_agentd
* 编辑配置文件 /usr/local/etc/zabbix/zabbix_agentd.conf , 添加以下配置:

```bash
#monitor nginx
UserParameter=nginx.status[*],/usr/local/etc/zabbix/scripts/ngx_status.sh $1
```

## 5. 重启zabbix_agentd 

```bash
[root@localhost ~]# killall zabbix_agentd
[root@localhost ~]# zabbix_agentd 
```

## 6. 测试脚本
* 非必须步骤, 但是建议测试一下 

```bash
[root@localhost scripts]# zabbix_get -s 172.22.12.100 -k 'nginx.status[ping]'
1
[root@localhost scripts]# zabbix_get -s 172.22.12.100 -k 'nginx.status[accepts]'
562
```

## 7. 导入nginx 监控模板
* 如果之前导入过, 就无须再导入了, 直接进行主机管理模板即可
* 登录zabbix web, 点击 Configuration -> Templates -> import, 选择模板文件
* 模板文件内容见附录

## 8. 主机添加nginx 监控模板
* 登录zabbix, 点击Configuration -> Hosts -> 主机 -> Templates 
* 选择要关联的模板, 点击add, 点击update 即可
![](/assets/zabbix_nginx_2017-08-22_145135.png)

## 9. 创建监控Screen
* 点击Monitoring -> Screens, 新创建监控Screen 
![](/assets/zabbix_nginx_2017-08-22_145908.png)


## 附:
### 1. zabbix监控nginx 模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<zabbix_export>
    <version>3.0</version>
    <date>2017-08-22T01:29:50Z</date>
    <groups>
        <group>
            <name>Templates</name>
        </group>
    </groups>
    <templates>
        <template>
            <template>Template App NGINX</template>
            <name>Template App NGINX</name>
            <description>nginx监控模板</description>
            <groups>
                <group>
                    <name>Templates</name>
                </group>
            </groups>
            <applications>
                <application>
                    <name>nginx</name>
                </application>
            </applications>
            <items>
                <item>
                    <name>nginx status connections active</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[active]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>0</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>active</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status connections reading</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[reading]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>0</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>reading</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status connections waiting</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[waiting]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>0</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>waiting</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status connections writing</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[writing]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>0</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>writing</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status PING</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[ping]</key>
                    <delay>60</delay>
                    <history>30</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>0</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>is live</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap>
                        <name>Service state</name>
                    </valuemap>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status server accepts</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[accepts]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>1</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>accepts</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status server handled</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[handled]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>1</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>handled</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
                <item>
                    <name>nginx status server requests</name>
                    <type>0</type>
                    <snmp_community/>
                    <multiplier>0</multiplier>
                    <snmp_oid/>
                    <key>nginx.status[requests]</key>
                    <delay>60</delay>
                    <history>90</history>
                    <trends>365</trends>
                    <status>0</status>
                    <value_type>3</value_type>
                    <allowed_hosts/>
                    <units/>
                    <delta>1</delta>
                    <snmpv3_contextname/>
                    <snmpv3_securityname/>
                    <snmpv3_securitylevel>0</snmpv3_securitylevel>
                    <snmpv3_authprotocol>0</snmpv3_authprotocol>
                    <snmpv3_authpassphrase/>
                    <snmpv3_privprotocol>0</snmpv3_privprotocol>
                    <snmpv3_privpassphrase/>
                    <formula>1</formula>
                    <delay_flex/>
                    <params/>
                    <ipmi_sensor/>
                    <data_type>0</data_type>
                    <authtype>0</authtype>
                    <username/>
                    <password/>
                    <publickey/>
                    <privatekey/>
                    <port/>
                    <description>requests</description>
                    <inventory_link>0</inventory_link>
                    <applications>
                        <application>
                            <name>nginx</name>
                        </application>
                    </applications>
                    <valuemap/>
                    <logtimefmt/>
                </item>
            </items>
            <discovery_rules/>
            <macros/>
            <templates/>
            <screens/>
        </template>
    </templates>
    <triggers>
        <trigger>
            <expression>{Template App NGINX:nginx.status[ping].last()}=0</expression>
            <name>nginx was down!</name>
            <url/>
            <status>0</status>
            <priority>4</priority>
            <description>NGINX进程数：0，请注意</description>
            <type>0</type>
            <dependencies/>
        </trigger>
    </triggers>
    <graphs>
        <graph>
            <name>nginx status connections</name>
            <width>900</width>
            <height>200</height>
            <yaxismin>0.0000</yaxismin>
            <yaxismax>100.0000</yaxismax>
            <show_work_period>1</show_work_period>
            <show_triggers>1</show_triggers>
            <type>0</type>
            <show_legend>1</show_legend>
            <show_3d>0</show_3d>
            <percent_left>0.0000</percent_left>
            <percent_right>0.0000</percent_right>
            <ymin_type_1>0</ymin_type_1>
            <ymax_type_1>0</ymax_type_1>
            <ymin_item_1>0</ymin_item_1>
            <ymax_item_1>0</ymax_item_1>
            <graph_items>
                <graph_item>
                    <sortorder>0</sortorder>
                    <drawtype>0</drawtype>
                    <color>00C800</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[active]</key>
                    </item>
                </graph_item>
                <graph_item>
                    <sortorder>1</sortorder>
                    <drawtype>0</drawtype>
                    <color>C80000</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[reading]</key>
                    </item>
                </graph_item>
                <graph_item>
                    <sortorder>2</sortorder>
                    <drawtype>0</drawtype>
                    <color>0000C8</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[waiting]</key>
                    </item>
                </graph_item>
                <graph_item>
                    <sortorder>3</sortorder>
                    <drawtype>0</drawtype>
                    <color>C800C8</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[writing]</key>
                    </item>
                </graph_item>
            </graph_items>
        </graph>
        <graph>
            <name>nginx status server</name>
            <width>900</width>
            <height>200</height>
            <yaxismin>0.0000</yaxismin>
            <yaxismax>100.0000</yaxismax>
            <show_work_period>1</show_work_period>
            <show_triggers>1</show_triggers>
            <type>0</type>
            <show_legend>1</show_legend>
            <show_3d>0</show_3d>
            <percent_left>0.0000</percent_left>
            <percent_right>0.0000</percent_right>
            <ymin_type_1>0</ymin_type_1>
            <ymax_type_1>0</ymax_type_1>
            <ymin_item_1>0</ymin_item_1>
            <ymax_item_1>0</ymax_item_1>
            <graph_items>
                <graph_item>
                    <sortorder>0</sortorder>
                    <drawtype>0</drawtype>
                    <color>00C800</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[accepts]</key>
                    </item>
                </graph_item>
                <graph_item>
                    <sortorder>1</sortorder>
                    <drawtype>0</drawtype>
                    <color>C80000</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[handled]</key>
                    </item>
                </graph_item>
                <graph_item>
                    <sortorder>2</sortorder>
                    <drawtype>0</drawtype>
                    <color>0000C8</color>
                    <yaxisside>0</yaxisside>
                    <calc_fnc>2</calc_fnc>
                    <type>0</type>
                    <item>
                        <host>Template App NGINX</host>
                        <key>nginx.status[requests]</key>
                    </item>
                </graph_item>
            </graph_items>
        </graph>
    </graphs>
</zabbix_export>
```





