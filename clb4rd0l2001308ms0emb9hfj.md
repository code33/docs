---
title: "postgresql 存储过程函数 随机字符指定规则生成"
datePublished: Thu Dec 01 2022 07:31:13 GMT+0000 (Coordinated Universal Time)
cuid: clb4rd0l2001308ms0emb9hfj
slug: pg-random-function
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1669879962231/0p-AsszMz.png
tags: function, postgresql, random

---

内容比较多,可以自己逐一拨开,也可以一股脑的全部执行; 文件下载地址: [FileDownload-ClickHere](https://blserver01-generic.pkg.coding.net/amy/genpub/db-init-script.sql?version=FR022343H48)

使用方法后续给贴上

```bash
-- source: http://www.jamiebegin.com/base36-conversion-in-postgresql/
DO $$ begin raise notice '%',''||'========================== 华丽的标题分割线 ======base36_encode===================='; end; $$;

--create schema IF NOT EXISTS  activiti; comment on schema activiti is 'activiti业务模块';

--create schema IF NOT EXISTS  amycore; comment on schema amycore is 'protoPpart业务核心模块';

--create schema IF NOT EXISTS auth; comment on schema auth is '鉴权模块';

--create schema IF NOT EXISTS  betty; comment on schema betty is '佣金储值模块';

--create schema IF NOT EXISTS  promo; comment on schema promo is '推广收录模块';

-- promo
-- promo-batch  批次的增删查改
----- end-time      此批优惠码-有效截止日期
----- code-count    本批次的发行数量-优惠码的数量
----- amount    本批次的发行金额-优惠码的金额
----- create-by-usn   批次的发行人 usn
----- effc_times  可用次数
----- used_times 已用次数
-- promo-code  优惠码码基于批次的发行代金券
----- promote  邀请人/介绍人
----- invitee  受邀人/被邀人
-- promo-scanrecord 扫码记录
-- promo-writeoff 优惠码核销记录
----- contact 手机号/微信号/电子邮箱
----- order_sn 对应的订单序号
----- order_code 对应的订单编号
-- promo-send
----- target_type email phone
----- target_no   emailAddr phoneNumber
----- target_usn  usn


CREATE OR REPLACE FUNCTION base36_encode(IN digits bigint, IN min_width int = 0)
   RETURNS varchar AS $$
DECLARE
   chars char[];
   ret varchar;
   val bigint;
BEGIN
   chars := ARRAY['0','1','2','3','4','5','6','7','8','9'
      ,'A','B','C','D','E','F','G','H','I','J','K','L','M'
      ,'N','O','P','Q','R','S','T','U','V','W','X','Y','Z'];
   val := digits;
   ret := '';
   IF val < 0 THEN
      val := val * -1;
   END IF;
   WHILE val != 0 LOOP
         ret := chars[(val % 36)+1] || ret;
         val := val / 36;
      END LOOP;

   IF min_width > 0 AND char_length(ret) < min_width THEN
      ret := lpad(ret, min_width, '0');
   END IF;

   RETURN ret;

END;
$$ LANGUAGE 'plpgsql' IMMUTABLE;

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======base36_decode==================='; end; $$;


CREATE OR REPLACE FUNCTION base36_decode(IN base36 varchar)
   RETURNS bigint AS $$
DECLARE
   a char[];
   ret bigint;
   i int;
   val int;
   chars varchar;
BEGIN
   chars := '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';

   FOR i IN REVERSE char_length(base36)..1 LOOP
         a := a || substring(upper(base36) FROM i FOR 1)::char;
      END LOOP;
   i := 0;
   ret := 0;
   WHILE i < (array_length(a,1)) LOOP
         val := position(a[i+1] IN chars)-1;
         ret := ret + (val * (36 ^ i));
         i := i + 1;
      END LOOP;

   RETURN ret;

END;
$$ LANGUAGE 'plpgsql' IMMUTABLE;

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_string_32==================='; end; $$;
--select '华丽的分割线';
--  --

-- 获取总长度为re_length的字符串,其中除给定的prefix外,其他值均随机.
-- 随机范围 [0-9A-Z]
--DROP FUNCTION IF EXISTS random_string_32( INTEGER, VARCHAR );
CREATE or replace FUNCTION random_string_32(re_length INTEGER, prefix CHARACTER VARYING)
   RETURNS TEXT

AS $$
DECLARE
   char_pool  TEXT [] := '{2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,J,K,L,M,N,P,Q,R,S,T,U,V,W,X,Y,Z}';
   result     TEXT := '';
   i          INTEGER := 0;
   prefix_len INTEGER :=0;
   random_len INTEGER :=0;
   idx        INTEGER :=0;
BEGIN

   IF prefix IS NOT NULL
   THEN
      prefix_len = char_length(prefix);
      result:=prefix;
   END IF;

   IF re_length < prefix_len
   THEN
      RAISE EXCEPTION 'The length of result cannot be less than the length of prefix';
   END IF;

   -- 随机字符串的长度 = 全长 - 前缀长度
   random_len := re_length - prefix_len;

   FOR i IN 1..random_len LOOP

         idx := floor(random() * 32) + 1;

         IF idx > 32
         THEN
            idx := 32;
         END IF;

         result := result || char_pool [idx];
      END LOOP;

   RETURN result;
END;
$$ LANGUAGE 'plpgsql' IMMUTABLE;

-- ========================== 华丽的分割线 ========================== --
DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_string_36==================='; end; $$;

-- 获取总长度为re_length的字符串,其中除给定的prefix外,其他值均随机.
-- 随机范围 [0-9A-Z]
-- DROP FUNCTION IF EXISTS random_string_36( INTEGER, VARCHAR );
CREATE OR REPLACE FUNCTION random_string_36(re_length INTEGER, prefix CHARACTER VARYING)
   RETURNS TEXT
AS $$
DECLARE
   char_pool  TEXT [] := '{0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z}';
   result     TEXT := '';
   i          INTEGER := 0;
   prefix_len INTEGER :=0;
   random_len INTEGER :=0;
   idx        INTEGER :=0;
BEGIN

   IF prefix IS NOT NULL
   THEN
      prefix_len = char_length(prefix);
      result:=prefix;
   END IF;

   IF re_length < prefix_len
   THEN
      RAISE EXCEPTION 'The length of result cannot be less than the length of prefix';
   END IF;

   -- 随机字符串的长度 = 全长 - 前缀长度
   random_len := re_length - prefix_len;

   FOR i IN 1..random_len LOOP

         idx := floor(random() * 36) + 1;

         IF idx = 37
         THEN
            idx := 36;
         END IF;

         result := result || char_pool [idx];
      END LOOP;

   RETURN result;
END;
$$ LANGUAGE 'plpgsql' IMMUTABLE;
-- // First migration.
-- Migration SQL that makes the change goes here.
-- ========================== 华丽的分割线 ========================== --

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======fn_gen_glbsn==================='; end; $$;

-- // test : SELECT fn_gen_glbsn('RS',4,'seq_rs1_nfsn'); 即以RS为开头中间顺序递增字符长度4个length(且或者3)，并指定seq的名称 其中nfsn意思是info seq 为 46,655 其中 dtsn 意思是 detail seq max为1679615
-- // 转化为字符串36 进制分别为 ZZZ和 ZZZZ 字符串
-- nfsn 信息订单 非每日增量-商品信息 员工信息 配置信息 单位信息
-- dtsn 详情序号 有每日增量-订单编号 订单详情
-- lgsn 日志序号

-- drop FUNCTION if exists fn_gen_glbsn(varchar(10),text);
-- drop FUNCTION if exists fn_gen_glbsn(varchar(10),text,integer);

CREATE OR REPLACE FUNCTION fn_gen_glbsn(
   serial_type VARCHAR(10) default 'UR',
   seq_name text default 'seq_user_dtsn', ----must be with nfsn-low dtsn-mid lgsn-high
   subf_length integer default 6, -- 后面的序号长度
   seq_number integer default 0 -- 序号的数值,默认不填,若填写,则使用手动指定序号值,否则由系统生成: 高并发场景下则由外部生成seq_number传入
)
   RETURNS VARCHAR(55) AS
$$
DECLARE
   var_countseq integer :=0;
   result         VARCHAR(55);
   milli_secs     INTEGER:=0;
   yearnumber     VARCHAR(10) := '18';
   weeknumber     VARCHAR(10) := '01';
   begenNfSeq     boolean:=false; -- info seq 信息序号 不会每天产生记录的序号
   begenDtSeq     boolean:=false; -- detail 详情序号/记录序号 即每天发生业务就会产生的
   begenLgSeq     boolean:=false; -- log seq 用于日志表的序号
   randomr_length integer     := 3;
   pre_lenth      integer     := 2;
   seq_length     integer:=3;
   -- // base36 60466175 = ZZZZZ
   -- // base36 1679615 = ZZZZ 4位 一个星期16.7w
   -- // base36 46655 = ZZZ 一个星期 nf
   maxvalseq      integer:=46655;
   --  select right(('00'||DATE_PART('doy',(current_date))),3) as doy;

   --  SELECT right(to_char(now()::date, 'YYYY'), 3) ;
BEGIN
   SELECT COUNT(1) into var_countseq FROM pg_class c WHERE c.relkind = 'S' and  relname = seq_name;
   --  ZZZ     nfsn 46655    4.67w / 每周 信息设置
   --  ZZZZ    dtsn 1679615  16.8w / 每周 订单记录,操作记录,订单详情
   --  ZZZZZ   lgsn 60466175 6000w / 每周 日志记录
   select (seq_name ~ 'nfsn') into begenNfSeq;

   select (seq_name ~ 'dtsn') into begenDtSeq;
   --       lgsn 60466175
   select (seq_name ~ 'lgsn') into begenLgSeq;

   if begenNfSeq then
      seq_length = 3;
   end if;

   if begenDtSeq then
      seq_length = 4;
   end if;

   if begenLgSeq then
      seq_length = 5;
   end if;

   if var_countseq < 1 then
      --  ZZZ     nfsn 46655    4.67w / 每周 信息设置
      --  ZZZZ    dtsn 1679615  16.8w / 每周 订单记录,操作记录,订单详情
      --  ZZZZZ   lgsn 60466175 6000w / 每周 日志记录

      -- info 46655
      if begenNfSeq then
         EXECUTE ('CREATE SEQUENCE IF NOT EXISTS '|| seq_name || ' START WITH 1 MINVALUE 1 INCREMENT 1 MAXVALUE 46655 CYCLE;');
      end if;

      -- detail
      if begenDtSeq then

         --          CREATE SEQUENCE IF NOT EXISTS seq_name START WITH 1 MINVALUE 1 INCREMENT 1 MAXVALUE 1679615 CYCLE;
         -- max value ZZZZ
         EXECUTE ('CREATE SEQUENCE IF NOT EXISTS '|| seq_name || ' START WITH 1 MINVALUE 1 INCREMENT 1 MAXVALUE 1679615 CYCLE;');

      end if;

      -- log
      if begenLgSeq then
         --          CREATE SEQUENCE IF NOT EXISTS seq_name START WITH 1 MINVALUE 1 INCREMENT 1 MAXVALUE 60466175 CYCLE;
         EXECUTE ('CREATE SEQUENCE IF NOT EXISTS '|| seq_name || ' START WITH 1 MINVALUE 1 INCREMENT 1 MAXVALUE 60466175 CYCLE;');

      end if;



      if (begenNfSeq::int + begenDtSeq::int + begenLgSeq::int ) = 0 then

         raise exception 'SEQ_NAME_IS_INVALID_MUST_BE_CONTAIN_NFSN_DTSN_LGSN';

      end if;

      --
   end if;

   SELECT right(to_char(now()::date, 'YYYY'), 2) into yearnumber;

   SELECT right('0' || ((SELECT EXTRACT(WEEK FROM now()))), 2) into weeknumber;

   -- 这里的seq_length 只能是3 or 4,3位seq长度即ZZZ=46655,4位seq长度即最大ZZZZ =1679615
   randomr_length = (subf_length - seq_length) + (pre_lenth - length(serial_type));
   -- 随机字符串长度为 给定值 减去序列长度 + 前缀长度2-前缀字符数

   -- 外部调用输入的seq_number 则不走内部自增序列直接使用外部序列
   if seq_number > 0 then
      SELECT INTO result serial_type || yearnumber || weeknumber || base36_encode( (seq_number + 0), seq_length) ||
                         random_string_32(randomr_length, '');

   else
      -- 前缀字符UR2+年份2+第几周2+序号36进制4+随机字符3
      SELECT INTO result serial_type || yearnumber || weeknumber || base36_encode((nextval(seq_name) + 0), seq_length) ||
                         random_string_32(randomr_length, '');

   end if;
   RETURN result;

END;
$$
   LANGUAGE 'plpgsql' VOLATILE;

--COMMENT ON FUNCTION fn_gen_glbsn(VARCHAR, text,integer) is '全局序列号';
--COMMENT ON FUNCTION fn_gen_glbsn(VARCHAR, text,integer,integer) is '全局序列号';

-- ========================== 华丽的分割线 ========================== --
-- 你可以执行一条测试 select fn_gen_glbsn('UR','seq_usr_nfsn');


-- 获取总长度为re_length的字符串,其中除给定的prefix外,其他值均随机.
-- 随机范围 [0-9A-Z]
DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_string_custype--be-create==================='; end; $$;

CREATE or replace FUNCTION random_string_custype (pooltype integer DEFAULT 0, re_length integer DEFAULT 6, prefix VARCHAR(10) DEFAULT 'A' ) RETURNS text
   LANGUAGE plpgsql
AS $$
DECLARE
   char_pool  TEXT []:='{}';
   result     TEXT := '';
   i          INTEGER := 0;
   prefix_len INTEGER :=0;
   random_len INTEGER :=0;
   idx        INTEGER :=0;
BEGIN

   CASE
      -- 0-9+大写A到Z
      WHEN pooltype = 36 THEN
         char_pool='{0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z}'; -- 36
   -- 0-9 数字
      WHEN pooltype = 10 THEN
         char_pool='{0,1,2,3,4,5,6,7,8,9}'; --10
   -- 数字+字母去掉0,1,I,O防止歧义
      WHEN pooltype = 32 THEN
         char_pool='{2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,J,K,L,M,N,P,Q,R,S,T,U,V,W,X,Y,Z}'; -- 32
      when pooltype = 33 then
         char_pool='{2,3,4,5,6,7,8,9,1,a,b,c,d,e,f,g,h,j,k,l,m,n,p,q,r,s,t,u,v,x,y,z}'; -- 32
      when pooltype = 46 then
         char_pool = '{あ,い,う,え,お,か,き,く,け,こ,さ,し,す,せ,そ,た,ち,つ,て,と,な,に,ぬ,ね,の,は,ひ,ふ,へ,ほ,ま,み,む,め,も,や,ゆ,よ,ら,り,る,れ,ろ,わ,を,ん}';
      when pooltype = 47 then -- 多了一个 ヱ
      char_pool = '{ア,イ,ウ,エ,オ,カ,キ,ク,ケ,コ,サ,シ,ス,セ,ソ,タ,チ,ツ,テ,ト,ナ,ニ,ヌ,ネ,ノ,ハ,ヒ,フ,ヘ,ホ,マ,ミ,ム,メ,モ,ヤ,ユ,ヨ,ラ,リ,ル,レ,ロ,ワ,ヲ,ヱ,ン}';
      WHEN pooltype = 55 THEN
         char_pool='{2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,J,K,L,M,N,P,Q,R,S,T,U,V,W,X,Y,Z,a,b,c,d,e,f,g,h,j,k,m,n,p,q,r,s,t,u,v,w,x,y,z}';
      WHEN pooltype = 62 THEN
         char_pool='{0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z,a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z}';
      ELSE
         -- default is 32
         char_pool = '{2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,J,K,L,M,N,P,Q,R,S,T,U,V,W,X,Y,Z}';
      --  do nothing
      END CASE;
   if re_length < 8 then
      re_length = 8;
   end if;

   IF prefix IS NOT NULL
   THEN
      prefix_len = char_length(prefix);
      result:=prefix;
   END IF;

   IF re_length < prefix_len
   THEN
      RAISE EXCEPTION 'The length of result cannot be less than the length of prefix';
   END IF;

   -- 随机字符串的长度 = 全长 - 前缀长度
   random_len := re_length - prefix_len;

   FOR i IN 1..random_len LOOP

         idx := floor(random() * pooltype) + 1;

         IF idx = pooltype
         THEN
            idx := pooltype-1;
         END IF;

         result := result || char_pool [idx];
      END LOOP;

   RETURN result;
END;

$$;
--     LANGUAGE 'plpgsql' VOLATILE;

--     LANGUAGE 'plpgsql' VOLATILE;

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_string_custype--created==================='; end; $$;


DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_string_presn--creating==================='; end; $$;
CREATE or replace FUNCTION random_string_presn (prefix varchar(55) default 'A', re_length integer DEFAULT 8,pooltype integer DEFAULT 32 ) RETURNS text
   LANGUAGE plpgsql
AS $$
DECLARE
   char_pool  TEXT []:='{}';
   result     TEXT := '';
   i          INTEGER := 0;
   prefix_len INTEGER :=0;
   random_len INTEGER :=0;
   idx        INTEGER :=0;
   year_len   INTEGER :=3;
BEGIN

   if re_length < 15 then
      year_len=2;
   end if;

   select random_string_custype(pooltype,re_length,prefix||right(to_char(current_date, 'YYYY'), year_len)||right(('00'||DATE_PART('doy',(current_date))),3)) into result;

   RETURN result;
END;

$$;

CREATE or replace FUNCTION random_lite_presn (prefix varchar(55) default 'A', re_length integer DEFAULT 8,pooltype integer DEFAULT 32 ) RETURNS text
   LANGUAGE plpgsql
AS $$
DECLARE
   char_pool  TEXT []:='{}';
   result     TEXT := '';
   i          INTEGER := 0;
   prefix_len INTEGER :=0;
   random_len INTEGER :=0;
   idx        INTEGER :=0;
   year_len   INTEGER :=2;
BEGIN

   select random_string_custype(pooltype,re_length,prefix||right(to_char(current_date, 'YYYY'), year_len)||right(('00'||DATE_PART('doy',(current_date))),3)) into result;

   RETURN result;
END;

$$;



DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======fn_get_hour_of_year--creating==================='; end; $$;
CREATE OR REPLACE FUNCTION fn_get_hour_of_year()
   RETURNS INTEGER AS $$
DECLARE
   current_hour INTEGER;
   current_day_of_year INTEGER;
BEGIN
   SELECT EXTRACT(HOUR FROM NOW()) INTO current_hour;
   SELECT EXTRACT(DOY FROM NOW()) INTO current_day_of_year;

   RETURN (current_day_of_year - 1) * 24 + current_hour;
END;
$$ LANGUAGE plpgsql;

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======fn_convert_to_money--created==================='; end; $$;

CREATE OR REPLACE FUNCTION fn_convert_to_money(integer_value INTEGER)
   RETURNS VARCHAR AS $$
DECLARE
   decimal_value DECIMAL;
   currency_string VARCHAR;
BEGIN
   -- 将整数值除以 100，转换为带有两位小数的 decimal 值
   decimal_value := integer_value / 100.0;

   -- 使用 TO_CHAR 函数将 decimal 值格式化为金额形式的字符串
   currency_string := TO_CHAR(decimal_value, 'FM9999999999.00');

   -- 根据条件进行尾部补充
   IF POSITION('.' IN currency_string) > 0 THEN
      -- 小数点后有1位数字，尾部补充1个0
      IF LENGTH(SUBSTRING(currency_string FROM POSITION('.' IN currency_string) + 1)) = 1 THEN
         currency_string := currency_string || '0';
      END IF;
   ELSE
      -- 小数点后为空，尾部补充2个0
      currency_string := currency_string || '00';
   END IF;

   if decimal_value < 1 then
      currency_string :=  '0'||currency_string ;
   end if;
   currency_string:='¥'||currency_string;
   RETURN currency_string;
END;
$$ LANGUAGE plpgsql;

DO $$ begin raise notice '%',''||'========================== 华丽的分割线 =======random_num--created==================='; end; $$;
CREATE OR REPLACE FUNCTION random_num(length INT)
   RETURNS TEXT AS $$
DECLARE
   result TEXT := '';
   i INT;
BEGIN
   FOR i IN 1..length LOOP
         result := result || floor(random() * 10)::INT;
      END LOOP;

   RETURN result;
END;
$$ LANGUAGE plpgsql;



--     LANGUAGE 'plpgsql' VOLATILE;

--COMMENT ON FUNCTION random_string_custype(integer,integer,varchar(10)) is '随机字符串';
-- nf&cf: select random_string_custype(32,9,'S'||right(to_char(current_date, 'YYYY'), 3)||right(('00'||DATE_PART('doy',(current_date))),3)) as nf;
-- dt&rc: select random_string_custype(36,12,'S'||right(to_char(current_date, 'YYYY'), 3)||right(('00'||DATE_PART('doy',(current_date))),3)) as dt;
-- lg&hi: select random_string_custype(62,14,'S'||right(to_char(current_date, 'YYYY'), 3)||right(('00'||DATE_PART('doy',(current_date))),3)) as lg;
```