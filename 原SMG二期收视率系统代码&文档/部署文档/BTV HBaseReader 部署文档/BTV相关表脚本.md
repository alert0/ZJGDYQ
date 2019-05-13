##### -- 1. 创建channelname表
  CREATE TABLE channelname
(
  traditionalname character varying(255) NOT NULL,
  iptvname character varying(255)
)
WITH (
  OIDS=FALSE
);
ALTER TABLE channelname
  OWNER TO postgres; 
  
####  -- 2. 向channelname表中插入数据
  insert into channelname 
values('北京卫视','BTV北京卫视');

insert into channelname
values('北京电视台文艺频道','"BTV文艺');

insert into channelname
values('北京电视台科教频道','BTV科教');

insert into channelname
values('北京电视台影视频道','BTV影视');

insert into channelname
values('北京电视台财经频道','BTV财经');

insert into channelname
values('北京电视台体育频道','BTV体育');

insert into channelname
values('北京电视台生活频道','BTV生活');

insert into channelname
values('北京电视台青年频道','BTV青年');

insert into channelname
values('北京电视台新闻频道','BTV新闻');

insert into channelname
values('北京卡酷少儿频道','BTV卡酷少儿');

insert into channelname
values('北京电视台纪实高清频道','');

####-- 3. 创建leveltype表
-- Table: leveltype

-- DROP TABLE leveltype;

CREATE TABLE leveltype
(
  name character varying NOT NULL,
  typeid integer NOT NULL,
  shortname character varying NOT NULL,
  lastdate character varying NOT NULL,
  enable boolean NOT NULL DEFAULT true,
  CONSTRAINT leveltype_pkey PRIMARY KEY (name),
  CONSTRAINT leveltype_typeid_fkey FOREIGN KEY (typeid)
      REFERENCES logtype (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
)
WITH (
  OIDS=FALSE
);
ALTER TABLE leveltype
  OWNER TO postgres;
  
####  -- 4.向leveltype表插入数据 ,在向leveltype表插入之前，首先要在logtype表中插入栏目的配置
  insert into leveltype
values('奔跑吧兄弟',6,'BPBXD','2016-03-27','t')

####  -- 5. 创建logtype表
  
   -- Table: logtype

-- DROP TABLE logtype;

CREATE TABLE logtype
(
  id serial NOT NULL,
  type character varying(255),
  name character varying(255),
  latestdate character varying(50),
  enable boolean NOT NULL DEFAULT true,
  tablesource text,
  CONSTRAINT logtype_pkey PRIMARY KEY (id)
)
WITH (
  OIDS=FALSE
);
ALTER TABLE logtype
  OWNER TO postgres;

#### -- 6. 创建logschedule表

-- Table: logschedule

-- DROP TABLE logschedule;

CREATE TABLE logschedule
(
  id serial NOT NULL,
  typeid integer,
  path character varying(255),
  cycle character varying(255),
  bulksize integer,
  count integer,
  lastprocessdate character varying(255),
  cyclestart character varying(255),
  CONSTRAINT logschedule_pkey PRIMARY KEY (id),
  CONSTRAINT logschedule_id_fkey FOREIGN KEY (id)
      REFERENCES logtype (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
)
WITH (
  OIDS=FALSE
);
ALTER TABLE logschedule
  OWNER TO postgres;

#### -- 7. 创建logsetting表

-- Table: logsetting

-- DROP TABLE logsetting;

CREATE TABLE logsetting
(
  id serial NOT NULL,
  typeid integer,
  sheet integer,
  measures character varying(255),
  positionx integer,
  positiony integer,
  rowcount integer,
  direction integer,
  CONSTRAINT logsetting_pkey PRIMARY KEY (id),
  CONSTRAINT logsetting_typeid_fkey FOREIGN KEY (typeid)
      REFERENCES logtype (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
)
WITH (
  OIDS=FALSE
);
ALTER TABLE logsetting
  OWNER TO postgres;

#### -- 8. 创建logdata表

-- Table: logdata

-- DROP TABLE logdata;

CREATE TABLE logdata
(
  id serial NOT NULL,
  typeid integer,
  title character varying(255),
  rank character varying(32),
  date character varying(255),
  measure1 character varying(255),
  measure2 character varying(255),
  measure3 character varying(255),
  measure4 character varying(255),
  measure5 character varying(255),
  measure6 character varying(255),
  measure7 character varying(255),
  measure8 character varying(255),
  startdate date,
  enddate date,
  CONSTRAINT logdata_pkey PRIMARY KEY (id),
  CONSTRAINT logdata_typeid_fkey FOREIGN KEY (typeid)
      REFERENCES logtype (id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
)
WITH (
  OIDS=FALSE
);
ALTER TABLE logdata
  OWNER TO postgres;

