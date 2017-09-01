## DB2仓库核心模型 ##

### 清单 ###

#### 转码参数表BOI.FILED_MAPPING ####

流量转码：

select * from BOI.FIELD_MAPPING where dim_num='BOSS_STD9_0031'

原始值：old_code

转码后：new_code



#### 流量清单NWH.CDR_GSM_GPRS_YYYYMMDD ####

计费流量：totalbyte/1024/1024，单位M



#### 流量业务量serv_code汇总NWH.GDS_MB_GPRS_SERVICE_YYYYMMDD/NWH.GMS_MB_GPRS_SERVICE_YYYYMM

Volte流量话单：SERVICECODE in ('1050000003','1050000004','1050000005')



####serv_code参数表NWH.SET_VALUE_JF_YYYYMM



#### 语音清单NWH.CDR_GSM_YYYYMMDD



#### V网语音清单NWH.CDR_GSM_VPMN_YYYYMM



### 业务量

####业务量汇总NWH.GDS_MB_OPER_YYYYMMDD(当日)

由7个清单模型拼成业务量汇总表

日：

NWH.GDI_MB_OPER_&TASK_ID

日累计：

select *  from NMK.GMS_MB_OPER_&TASK_ID WHERE TIME_ID=&TASK_ID UNION ALL 
select *  from NMK.GMS_MB_OPER1_&TASK_ID WHERE TIME_ID=&TASK_ID UNION ALL 
select *  from NMK.GMS_MB_OPER2_&TASK_ID WHERE TIME_ID=&TASK_ID

月：

NWH.GMS_MB_GPRS_&MTASK_ID



#####通信

通信次数：sum_times

#####流量前提条件：substr(oper_tid,1,1) in ('3')

计费流量：TOTOALBYTE/1024/1024，单位M，下同

2G：substr(oper_tid,10,1) ='G'

3G：substr(oper_tid,10,1) ='T'

4G：substr(oper_tid,10,1) ='L'

省内漫游：substr(oper_tid,3,1) in ('0')

国内漫游(省际漫游)：substr(oper_tid,3,1) in ('Q','G')

国际漫游：substr(oper_tid,3,1) not in ('0','Q','G')

#####语音前提条件：substr(oper_tid,1,1) in ('1','7')

普通语音：substr(oper_tid,1,1) in ('1')

V网语音：substr(oper_tid,1,1) in ('7')

计费时长：dura1/60，单位分钟，下同

3G计费：substr(oper_tid,18,1)='T'

主叫：substr(oper_tid,2,1) IN('O')

被叫：substr(oper_tid,2,1) IN('T')

呼转：substr(oper_tid,2,1) IN('F')

本地：substr(oper_tid,4,1) in('0','1','2','3','4')

省内长途：substr(oper_tid,4,1) in('5')

省际长途：substr(oper_tid,4,1) in('6')

国际长途(包括港澳台)：substr(oper_tid,4,1) in('7','8','9','A','B','C')

非漫游：substr(oper_tid,3,1) in ('0','1','3')

漫游：substr(oper_tid,3,1) in ('4','6','8','9','A','B','C')

省内漫游：substr(oper_tid,3,1) in ('4')

省际漫游：substr(oper_tid,3,1) in ('6')

国际漫游：substr(oper_tid,3,1) in ('8','9','A','B','C')

#####WLAN前提条件：substr(oper_tid,1,1) in ('6')



#### 业务量用户级汇总NMK.ADI_MBUSER_YYYYMMDD/NMK.AMS_MBUSER_YYYYMMDD/NMK.AMS_MBUSER_YYYYMM

通信：sum_times

计费时长：dura1/60，单位分钟，下同

主叫次数：ZJTH_TIMES

主叫计费时长：ZJTH_DURA1

被叫次数：BJTH_TIMES

被叫计费时长：BJTH_DURA1

短信：sms_times

彩信：mms_times










