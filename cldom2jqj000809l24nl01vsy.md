# GRPC-PROTOBUF 用pb的rpc定义来建sql db表 table sql ddl Postgresql create table

以往的srv端初始构建,都是从db table开始; 即 一切先从表对象,建表开始着手;

而现如今有了protobuf这货,一切将变得更简单; 即 protobuf 完整的对象创建好, sql db 的 create table 建表语句 也能一并生成

这是我通过protobuf pb file的 定义所生成出来的

需要的话,可以留言你的email,将脚本发你试用~ 基于py3环境

```plaintext
-- // nf_sysopt
-- Migration SQL that makes the change goes here.
-- # idx000301 auto-generate on 2023-02-03 22:12:03
--GSysoptionService.EditNfSysopt=(NfSysopt)
--drop table if exists nf_sysopt;

create table if not exists nf_sysopt (

  sptId serial primary key    ,
  sptSn     varchar(55) not null default random_string_presn('SPT',12),
  sptCata text not null default ''    ,
  sptItem text not null default ''    ,
  sptKey text not null default ''    ,
  sptVal text not null default ''    ,
  sptDesc text not null default ''    ,
  crtAt    timestamptz  not null default now() ,
  crtBy text not null default ''    ,
  sort  integer not null default 0    ,
  sta    varchar(1) not null default 'T'

);
 create unique index if not exists uidx_pk_spt_snnf_sysopt on nf_sysopt (sptSn);

alter sequence if exists nf_sysopt_sptid_seq restart 1000;                            
```