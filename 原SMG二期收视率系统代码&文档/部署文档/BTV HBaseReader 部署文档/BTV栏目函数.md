#### -- 栏目收视走势
-- Function: levelviewtrendfunc(character varying, character varying, character varying, character varying, integer)

-- DROP FUNCTION levelviewtrendfunc(character varying, character varying, character varying, character varying, integer);

CREATE OR REPLACE FUNCTION levelviewtrendfunc(
    IN level character varying,
    IN sd character varying,
    IN ed character varying,
    IN m character varying,
    IN ti integer)
  RETURNS TABLE(title_val character varying, startdate_val date, enddate_val date, internetcount bigint) AS
$BODY$
declare levelid int;
st date;
et date;

begin
select typeid into levelid from leveltype where name = level;
SELECT CAST(SUBSTRING(SD FROM 1 FOR 4) || '-' || SUBSTRING(SD FROM 5 FOR 2) || '-' || SUBSTRING(SD FROM 7 FOR 2)  AS DATE)
INTO ST ;
SELECT CAST( SUBSTRING(ED FROM 1 FOR 4) || '-' || SUBSTRING(ED FROM 5 FOR 2) || '-' || SUBSTRING(ED FROM 7 FOR 2) AS DATE)
INTO ET;
if(m = 'internetplaycount')
then
if(ti = 1)
then
return query 

select cast(cast(substring(curdata.measure2 from 1 for 4) || '-' || substring(curdata.measure2 from 5 for 2) || '-' || substring(curdata.measure2 from 7 for 2) as date) as varchar),
 
cast(substring(sd from 1 for 4) || '-' || substring(sd from 5 for 2) || '-' || substring(sd from 7 for 2) as date) as startdate,

cast(substring(ed from 1 for 4) || '-' || substring(ed from 5 for 2) || '-' || substring(ed from 7 for 2) as date) as enddate,
cast(curdata.measure1 - predata.measure1 as bigint) 
from (select cast(sum(cast(measure1 as bigint)) as bigint) as measure1,
measure2
from logdata 
where typeid = levelid 
and 
measure2 >= (substring(cast(st + integer '-1' as varchar) from 1 for 4) || substring(cast(st+ integer '-1' as varchar) from 6 for 2) || substring(cast(st + integer '-1' as varchar) from 9 for 2) )

and measure2 <= (substring(cast(et + integer '-1' as varchar) from 1 for 4) || substring(cast(et + integer '-1' as varchar) from 6 for 2) || substring(cast(et + integer '-1' as varchar) from 9 for 2) )

group by measure2 ) as predata,
(select cast(sum(cast (measure1 as bigint)) as bigint) as measure1, measure2 from logdata where typeid = levelid and measure2 >= sd and measure2 <= ed
group by measure2 ) as curdata
where cast(substring(curdata.measure2 from 1 for 4) || '-' || substring(curdata.measure2 from 5 for 2) || '-' || substring(curdata.measure2 from 7 for 2)  as date )  =
cast(substring(predata.measure2 from 1 for 4) || '-' || substring(predata.measure2 from 5 for 2) || '-' || substring(predata.measure2 from 7 for 2) as date  )  + integer'1' ;


end if;
else 
return query 
select measure2,cast(sd as date),cast(ed as date),
cast(null as bigint)
limit 0;
end if;
end
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION levelviewtrendfunc(character varying, character varying, character varying, character varying, integer)
  OWNER TO postgres;
  
  #### --节目收视概览
  -- Function: levelprogramsummaryfunc(character varying, character varying)

-- DROP FUNCTION levelprogramsummaryfunc(character varying, character varying);

CREATE OR REPLACE FUNCTION levelprogramsummaryfunc(
    IN level character varying,
    IN ed character varying)
  RETURNS TABLE(title_val character varying, startdate_val date, enddate_val date, iptvviewrate double precision, iptvviewshare double precision, iptvvodcount bigint, internetplaycount bigint) AS
$BODY$
declare levelid int;
begin
select typeid into levelid from leveltype where name = level;
return query 
select level as title ,cast(ed as date) ,cast(ed as date),cast(null as float) as iptvviewrate,
cast(null as float) ,
cast(null as bigint) ,
cast(sum(cast(measure1 as bigint)) as bigint)
from logdata
where typeid = levelid and measure2 = ed ;
end
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION levelprogramsummaryfunc(character varying, character varying)
  OWNER TO postgres;

