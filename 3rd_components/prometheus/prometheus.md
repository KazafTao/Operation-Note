# Prometheus 笔记
----
 - **自定义python探针**
1. 导入prometheus_client包
2. 将要采集的指标设置成prometheus四种指标类型之一
3. 执行相应操作获取指标的值，将指标的值set进去
4. 将探针向prometheus server注册，数据会自动上传到prometheus server

