# Jboss 端口配置


## jboss 启动命令

```bash
$JBOSS_HOME/bin/run.sh -c $server_instance_Name -Djboss.service.binding.set=$port_name -b $ip_address &
```
* $JBOSS_HOME: jboss 安装目录
* $server_instance_name: jboss 实例名称, 不同版本所在位置应该有所不同
* $port_name: 端口号配置名称
* $ip_address: 服务器访问地址


## 默认端口
* jboss 默认配置了ports-default, ports-01, ports-02, ports-03 四个端口绑定策略,  分别对应于8080, 8180, 8280, 8380 端口, 我们可以新增端口绑定策略.

修改实例目录下配置文件夹:conf/bindingservice.beans/META-INF/bindings-jboss-beans.xml
```xml
<!--  The binding sets -->
<parameter>
   <set>
      <inject bean="PortsDefaultBindings"/>
      <inject bean="Ports01Bindings"/>
      <inject bean="Ports02Bindings"/>
      <inject bean="Ports03Bindings"/>
      <inject bean="Ports04Bindings"/>
   </set>
</parameter>
...
...
...
<!-- The ports-default bindings are obtained by taking the base bindings and adding 0 to each port value  -->
<bean name="PortsDefaultBindings"  class="org.jboss.services.binding.impl.ServiceBindingSet">
   <constructor>
      <!--  The name of the set -->
      <parameter>ports-default</parameter>
      <!-- Default host name -->
      <parameter>${jboss.bind.address}</parameter>
      <!-- The port offset -->
      <parameter>0</parameter>
      <!-- Set of bindings to which the "offset by X" approach can't be applied -->
      <parameter><null/></parameter>
   </constructor>
</bean>

<!-- The ports-01 bindings are obtained by taking the base bindings and adding 100 to each port value -->
<bean name="Ports01Bindings" class="org.jboss.services.binding.impl.ServiceBindingSet">
   <constructor>
      <!--  The name of the set -->
      <parameter>ports-01</parameter>
      <!-- Default host name -->
      <parameter>${jboss.bind.address}</parameter>
      <!-- The port offset -->
      <parameter>100</parameter>
      <!-- Set of bindings to which the "offset by X" approach can't be applied -->
      <parameter><null/></parameter>
   </constructor>
</bean>

<!-- The ports-02 bindings are obtained by taking ports-default and adding 200 to each port value -->
<bean name="Ports02Bindings" class="org.jboss.services.binding.impl.ServiceBindingSet">
   <constructor>
      <!--  The name of the set -->
      <parameter>ports-02</parameter>
      <!-- Default host name -->
      <parameter>${jboss.bind.address}</parameter>
      <!-- The port offset -->
      <parameter>200</parameter>
      <!-- Set of bindings to which the "offset by X" approach can't be applied -->
      <parameter><null/></parameter>
   </constructor> 
      <parameter><null/></parameter>
   </constructor>
</bean>
   
<!-- The ports-03 bindings are obtained by taking ports-default and adding 300 to each port value -->
<bean name="Ports03Bindings" class="org.jboss.services.binding.impl.ServiceBindingSet">
   <constructor>
      <!--  The name of the set -->
      <parameter>ports-03</parameter>
      <!-- Default host name -->
      <parameter>${jboss.bind.address}</parameter>
      <!-- The port offset -->
      <parameter>300</parameter>
      <!-- Set of bindings to which the "offset by X" approach can't be applied -->
      <parameter><null/></parameter>
   </constructor>
</bean>

<!-- The ports-04 bindings are obtained by taking ports-default and adding 300 to each port value -->
<bean name="Ports04Bindings" class="org.jboss.services.binding.impl.ServiceBindingSet">
   <constructor>
      <!--  The name of the set -->
      <parameter>ports-04</parameter>
      <!-- Default host name -->
      <parameter>${jboss.bind.address}</parameter>
      <!-- The port offset -->
      <parameter>400</parameter>
      <!-- Set of bindings to which the "offset by X" approach can't be applied -->
      <parameter><null/></parameter>
   </constructor>
</bean>
```