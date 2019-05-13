 #### --频道全媒体收视排名
 -- Function: channelallmediaratingrankingfunc(date)

-- DROP FUNCTION channelallmediaratingrankingfunc(date);

CREATE OR REPLACE FUNCTION channelallmediaratingrankingfunc(IN ed date)
  RETURNS TABLE(name character varying, startdate_ date, enddate_ date, rank integer, share double precision, growth integer) AS
$BODY$
BEGIN
RETURN query 
SELECT a.title,a.startdate ,a.enddate ,
CAST (a.rank AS INT),CAST (ROUND(a.measure1::numeric,2) AS FLOAT),
CASE
WHEN CAST (a.rank AS INT) - CAST (b.rank AS INT) = 0 THEN 0
WHEN CAST (a.rank AS INT) - CAST (b.rank AS INT) < 0 THEN - 1
ELSE 1
END AS growth
FROM logdata a
LEFT JOIN logdata b ON a.typeid = b.typeid
AND a.title = b.title
AND b.enddate = a.enddate - INTERVAL '1 days'
WHERE a.enddate = ed
AND a.typeid = 3
ORDER BY CAST (a.rank AS INT) ;
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION channelallmediaratingrankingfunc(date)
  OWNER TO postgres;

#### --传统市场频道收视考核
-- Function: channelratingassessmentfunc(date, integer)

-- DROP FUNCTION channelratingassessmentfunc(date, integer);

CREATE OR REPLACE FUNCTION channelratingassessmentfunc(
    IN ed date,
    IN metrictype integer)
  RETURNS TABLE(name character varying, startdate_ date, enddate_ date, target double precision, value double precision) AS
$BODY$
BEGIN
RETURN query 
SELECT title, startdate ,enddate,
CASE 
WHEN metricType=0 THEN
CAST( ROUND(measure1::numeric,2) AS double precision)
ELSE  
CAST( ROUND(measure3::numeric,2) AS double precision)
END AS target,
CASE 
WHEN metricType=0 THEN
CAST( ROUND(measure2::numeric,2) AS double precision)
ELSE  
CAST( ROUND(measure4::numeric,2) AS double precision)
END AS value
FROM logdata
WHERE typeid = 1
AND startdate <= ed AND enddate >= ed;
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION channelratingassessmentfunc(date, integer)
  OWNER TO postgres;

#### --本地市场概况
-- Function: localmarketoverview_channelfunc(date)

-- DROP FUNCTION localmarketoverview_channelfunc(date);

CREATE OR REPLACE FUNCTION localmarketoverview_channelfunc(IN ed date)
  RETURNS TABLE(name character varying, startdate_ date, enddate_ date, rank integer, value double precision) AS
$BODY$
BEGIN
RETURN query 
SELECT title, startdate ,enddate,CAST(a.rank AS INT),CAST(ROUND(measure1::numeric*100,2) AS double precision)
FROM logdata a
WHERE typeid = 5
AND enddate = ed
ORDER BY CAST(a.rank AS INT);
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION localmarketoverview_channelfunc(date)
  OWNER TO postgres;


-- Function: localmarketoverview_channelgroupfunc(date)

-- DROP FUNCTION localmarketoverview_channelgroupfunc(date);

CREATE OR REPLACE FUNCTION localmarketoverview_channelgroupfunc(IN ed date)
  RETURNS TABLE(name character varying, startdate_ date, enddate_ date, value double precision) AS
$BODY$
BEGIN
RETURN query 
SELECT title, startdate ,enddate,CAST(ROUND(measure1::numeric,2) AS double precision)
FROM logdata 
WHERE typeid = 2
AND enddate = ed;
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION localmarketoverview_channelgroupfunc(date)
  OWNER TO postgres;
  
  

#### --卫视频道全媒体收视情况
-- Function: sachannelallmediaratingoverviewfunc(date)

-- DROP FUNCTION sachannelallmediaratingoverviewfunc(date);

CREATE OR REPLACE FUNCTION sachannelallmediaratingoverviewfunc(IN ed date)
  RETURNS TABLE(date_ date, startdate_ date, enddate_ date, share double precision, rating double precision) AS
$BODY$
BEGIN
RETURN query 
SELECT ed,ed,ed,a.share,b.rating 
FROM
(SELECT CAST(ROUND(measure1::numeric,2) AS FLOAT) AS share,enddate
FROM logdata   
WHERE enddate=ed
AND typeid = 4 and btrim(title,'')='全国市场份额%') a,
(SELECT CAST(ROUND(measure1::numeric,2) AS FLOAT) AS rating,enddate
FROM logdata   
WHERE enddate=ed
AND typeid = 4 and btrim(title,'')='全国收视率%') b
WHERE a.enddate=b.enddate;
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION sachannelallmediaratingoverviewfunc(date)
  OWNER TO postgres;
  
  #### --卫视频道全媒体收视走势
  -- Function: sachannelallmediaratingtrendfunc(date, date)

-- DROP FUNCTION sachannelallmediaratingtrendfunc(date, date);

CREATE OR REPLACE FUNCTION sachannelallmediaratingtrendfunc(
    IN sd date,
    IN ed date)
  RETURNS TABLE(date_ date, startdate_ date, enddate_ date, rank integer, share double precision) AS
$BODY$
BEGIN
RETURN query 
SELECT datelist.date_val ,sd ,ed,
CAST ( logdata.rank AS INT),CAST (ROUND( logdata.measure1::numeric,2) AS FLOAT)
FROM (select * from testexecutesql(sd,ed)  order by date_val ) as datelist 
left join logdata
 on ( datelist.date_val = logdata.startdate   and  logdata.typeid = 3 and btrim(logdata.title,'')='北京卫视' )
ORDER BY datelist.date_val;

END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION sachannelallmediaratingtrendfunc(date, date)
  OWNER TO postgres;


