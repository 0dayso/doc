## DB2数据仓库接口 ##

### 接口查询NWH.DQUT_INTER_DRAW/NPD.INTER_CFG ###

select * from NWH.DQUT_INTER_DRAW where lower(sourcetab) like '%set%value%'

可以查到接口号P08213与抽取语句

如果是数据中心与DB2数据仓库公共接口，查询数据中心对应的接口，详见<数据中心接口>相关文档



select * from npd.inter_cfg where intercode = '05007'

INORFULL+INTERCODE：接口号

TABNAME：经分NPD表




