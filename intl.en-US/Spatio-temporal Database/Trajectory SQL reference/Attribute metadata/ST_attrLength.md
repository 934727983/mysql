# ST\_attrLength {#reference_dsp_2vp_3gb .reference}

This function returns the defined length of a trajectory attribute field.

## Syntax {#section_og1_4hn_qfb .section}

```
integer ST_attrLength(trajectory traj, integer index);
integer ST_attrLength(trajectory traj, text name);
```

## Parameters {#section_cxv_qhn_qfb .section}

|Parameter|Description|
|---------|-----------|
|traj|The trajectory object.|
|i​ndex|The attribute index.|
|name|The name of the attribute field.|

## Examples {#section_lmw_qhn_qfb .section}

```
With traj AS (
  select '{"trajectory":{"version":1,"type":"STPOINT","leafcount":2,"start_time":"2010-01-01 11:30:00","end_time":"2010-01-01 12:30:00","spatial":"SRID=4326;LINESTRING(1 1,3 5)","timeline":["2010-01-01 11:30:00","2010-01-01 12:30:00"],"attributes":{"leafcount":2,"velocity":{"type":"integer","length":4,"nullable":true,"value":[1,null]},"speed":{"type":"float","length":8,"nullable":true,"value":[null,1.0]},"angle":{"type":"string","length":64,"nullable":true,"value":["test",null]}, "tngel2":{"type":"timestamp","length":8,"nullable":true,"value":["2010-01-01 12:30:00",null]},"bearing":{"type":"bool","length":1,"nullable":true,"value":[null,true]}}}}'::trajectory a)
Select st_attrLength(a, 0) from traj;
 st_attrlength 
---------------
             4
(1 row)

With traj AS (
  select '{"trajectory":{"version":1,"type":"STPOINT","leafcount":2,"start_time":"2010-01-01 11:30:00","end_time":"2010-01-01 12:30:00","spatial":"SRID=4326;LINESTRING(1 1,3 5)","timeline":["2010-01-01 11:30:00","2010-01-01 12:30:00"],"attributes":{"leafcount":2,"velocity":{"type":"integer","length":4,"nullable":true,"value":[1,null]},"speed":{"type":"float","length":8,"nullable":true,"value":[null,1.0]},"angle":{"type":"string","length":64,"nullable":true,"value":["test",null]}, "tngel2":{"type":"timestamp","length":8,"nullable":true,"value":["2010-01-01 12:30:00",null]},"bearing":{"type":"bool","length":1,"nullable":true,"value":[null,true]}}}}'::trajectory a)
Select st_attrLength(a, 'velocity') from traj;
 st_attrlength 
---------------
             4
(1 row)
```

