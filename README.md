## This is a document with tianchi and shujia faq

### Q1:怎么生成测试数据时间序列?

代码如下:

    -- 拿到原始数据中后两个月的ds
    drop table if exists test_dates_0;
    create table test_dates_0 as select distinct(ds) from odps_tc_257100_f673506e024.mars_tianchi_user_actions where ds >20150701 and ds <=20150831;
    select * from test_dates_0 limit 20;
    
    -- 从bigint转为datetime
    drop table if exists test_dates_1;
    create table test_dates_1 as select to_date(ds,"yyyymmdd") as ds  from test_dates_0;
    
    select * from test_dates_1 limit 20;
    
    -- 增加60天
    drop table if exists test_dates_2;
    create table test_dates_2 as select dateadd(ds,61,"dd")  as ds from test_dates_1;
    
    select min(ds), max(ds) from test_dates_2;
    
    -- ds 转成字符串
    create table test_dates_3 as select cast(ds as string) as ds from test_dates_2;
    select * from test_dates_3 limit 20;
    
    
    -- 删除time部分
    drop table if exists test_dates_4;
    create table test_dates_4 as select concat(substr(substr(ds,1,10 ),1,4),substr(substr(ds,1,10 ),6,2),substr(substr(ds,1,10 ),9,2))as ds from test_dates_3;
    select * from test_dates_4 limit 20;
    
    create table test_dates_final as select ds, 'a' as join_flag from test_dates_4;
    select * from test_dates_final limit 20;
    
    -- train data ds
    create table train_dates_final as select distinct(ds), 'a' as join_flag from odps_tc_257100_f673506e024.mars_tianchi_user_actions;
    select count(*) from train_dates_final limit 20;
    
    create table all_dates_final as select * from
    (
    select * from train_dates_final
    union all
    select * from test_dates_final
    ) tmp;
    select count(*) from all_dates_final limit 20;
    
    --添加日期信息
    create table all_dates_features_final as select 
    ds, 
    weekday(to_date(ds,"yyyymmdd")) as day_of_week, 
    weekofyear(to_date(ds,"yyyymmdd")) as week_of_year,
    substr(ds,5,2)  as month,
    substr(ds,7,2)  as day,
    join_flag
    from all_dates_final;







##fork this project and commit a pr, I'll merge it as soon as possible.Some