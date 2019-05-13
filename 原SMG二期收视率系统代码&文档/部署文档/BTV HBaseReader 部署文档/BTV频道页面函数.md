 #### -- 频道概览
 -- Function: channelsummaryfunc(character varying, date)

-- DROP FUNCTION channelsummaryfunc(character varying, date);

CREATE OR REPLACE FUNCTION channelsummaryfunc(
    IN channel character varying,
    IN ed date)
  RETURNS TABLE(title_val character varying, startdate_val date, enddate_val date, viewshare double precision, iptvviewrate double precision, iptvshare double precision) AS
$BODY$
begin
return query
select title,startdate,enddate,cast(measure1 as float) * 100 as viewshare,cast(null as float) as iptvviewrate,cast(null as float) as iptvshare
from logdata 
where typeid = 5 and startdate = ed and enddate = ed and title = channel ;

end
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION channelsummaryfunc(character varying, date)
  OWNER TO postgres;
  
  #### --频道收视走势
  -- Function: channelviewtrendfunc(character varying, date, date, character varying, integer)

-- DROP FUNCTION channelviewtrendfunc(character varying, date, date, character varying, integer);

CREATE OR REPLACE FUNCTION channelviewtrendfunc(
    IN channel character varying,
    IN sd date,
    IN ed date,
    IN m character varying,
    IN ti integer)
  RETURNS TABLE(title_val character varying, startdate_val date, enddate_val date, metric double precision) AS
$BODY$
begin
if(m = 'viewshare')
then 
if(ti = 1)
then
return query
select cast (startdate as varchar) ,sd,ed,CAST (ROUND((cast(measure1 as float) * 100) :: numeric,2) AS FLOAT) as metric
from logdata 
where typeid = 5 and startdate >= sd and enddate <= ed and title = channel 
order by logdata.startdate ;
else
return query 
select cast(null as varchar) as title,cast(null as date) as startdate,cast(null as date) as enddate,cast(null as float) as measure1
limit 0;
end if;
else
return query 
select cast(null as varchar) as title,cast(null as date) as startdate,cast(null as date) as enddate,cast(null as float) as measure1
limit 0;
end if;
end
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION channelviewtrendfunc(character varying, date, date, character varying, integer)
  OWNER TO postgres;
  
  #### -- 所有频道
  -- Function: channelnamesfunc()

-- DROP FUNCTION channelnamesfunc();

CREATE OR REPLACE FUNCTION channelnamesfunc()
  RETURNS TABLE(traditionalname_ character varying, iptvnane_ character varying) AS
$BODY$
BEGIN 
	RETURN query SELECT
		traditionalname,
		iptvname
	FROM
		channelname;
END
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
  ROWS 1000;
ALTER FUNCTION channelnamesfunc()
  OWNER TO postgres;


