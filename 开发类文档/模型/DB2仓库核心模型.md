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



#### 近三个月业务量NMK.MBUSER_GPRS_THREEMONTH_YYYYMM/NMK.AMS_MBUSER_THREEMONTH_YYYYMM





### 受理日志

#### 受理日志DWD_ACC_RECEPTION_DM

select serv_num, 
sum(case when connect_tid in ('bsacHal', 'bsacHalCRM') then 1 else 0 end) as bsacHal_times, 
sum(case when connect_tid in ('bsacAgt') then 1 else 0 end) as bsacAgt_times, 
sum(case when connect_tid in ('bsacWap') then 1 else 0 end) as bsacWap_times, 
sum(case when connect_tid in ('bsacNB') then 1 else 0 end) as bsacNB_times, 
sum(case when connect_tid in ('bsacKF') then 1 else 0 end) as bsacKF_times, 
sum(case when connect_tid in ('bsacSms') then 1 else 0 end) as bsacSms_times, 
sum(case when connect_tid in ('bsacAtsv') then 1 else 0 end) as bsacAtsv_times, 
sum(case when rec_tid = 'Charge' and connect_tid in ('bsacHal', 'bsacHalCRM') then 1 else 0 end) as bsacHal_charge_times, 
sum(case when rec_tid = 'Charge' and connect_tid in ('bsacAgt') then 1 else 0 end) as bsacAgt_charge_times, 
sum(case when rec_tid = 'Charge' and connect_tid in ('bsacWap') then 1 else 0 end) as bsacWap_charge_times, 
sum(case when rec_tid = 'Charge' and connect_tid in ('bsacNB') then 1 else 0 end) as bsacNB_charge_times, 
sum(case when rec_tid = 'Charge' and connect_tid in ('bsacAtsv') then 1 else 0 end) as bsacAtsv_charge_times 
from nwh.care_rec_&MTASK_ID b 
where b.roll_flag<>'1' and b.link_type in ('0','2') and b.state_tid='stcmNml' and acc_nbr is not null group by acc_nbr with ur



### 收入

#### 用户级收入NWH.MBUSER_ACCTITEM_YYYYMMDD/NWH.MBUSER_ACCTITEM_YYYYMM

出账收入：BILL_CHARE/1000，单位：元，下同

GPRS收入：GPRS_CHARGE/100



#### 近三个月收入NMK.MBUSER_ACCTITEM_THREEMONTH_YYYYMM



### 流量

#### 流量用户级宽表NMK.ADI_MBUSER_GPRS_YYYYMMDD/NMK.AMS_MBUSER_GPRS_YYYYMM

##### 字段口径

USER_FLAG：4G,3G,2G

4G：4G用户；3G：3G用户；2G：2G用户

SUB_USER_FLAG：'4G终端','3G终端'



SUB_USER_FLAG1：'4G网络','3G网络'



4G卡：RES_TYPE_ID is not null



##### 常用统计

使用4G终端的用户/使用LTE终端的用户：

select * from NMK.AMS_MBUSER_GPRS_YYYYMM where act_tid in (0,1) and USER_FLAG = '4G' and SUB_USER_FLAG = '4G终端'



使用4G流量的用户/使用LTE流量的用户：

select * from NMK.AMS_MBUSER_GPRS_YYYYMM where act_tid in (0,1) and totalbyte_lte_m>0



使用4G网络的用户/使用LTE网络的用户：

未实现



#### 流量产品订购模型NWH.MBUSER_GPRS_FEE_YYYYMMDD/NMK.MBUSER_GPRS_FEE_YYYYMM/NWH.MBUSER_LTE_FEE_YYYYMM/NWH.MBUSER_UNI_FEE_YYYYMM

##### 来源表

产品订购表：nwh.serv_function

统一套餐参数表：nwh.unify_code

手工维护参数表



4G套餐参数表：nwh.lte_fee

手工维护参数表



流量套餐参数表：nwh.gprs_fee

包含流量的套餐手工维护参数表



##### 常用统计

统一套餐订购用户(fee_status=1到达用户)

select * from NWH.MBUSER_UNI_FEE_YYYYMM where act_tid in (0,1) and fee_status=1



4G自选套餐语音订购用户

select * from NWH.MBUSER_LTE_FEE_YYYYMM where act_tid in (0,1) and fee_status=1 and type = '4G自选套餐语音'



70M以上或30元以上流量套餐订购用户数

select * from NMK.MBUSER_GPRS_FEE_YYYYMM where act_tid in (0,1) and fee_status=1 and (flow/1024>=70 or fee>=30)



包含流量的4G套餐用户数

select * from NMK.MBUSER_GPRS_FEE_YYYYMM where act_tid in (0,1) and fee_status=1 and is_4g=1






