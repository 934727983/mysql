# S​T\_BuildOverview {#reference_mqw_vc4_qfb .reference}

创建一个overview。

## 语法 {#section_og1_4hn_qfb .section}

```
raster ST_BuildOverview(cstring srcTableName, cstring srcColumnName, integer srcPyramidLevel, cstring chunkTableName);
```

## 参数 {#section_cxv_qhn_qfb .section}

|参数名称|描述|
|----|--|
|tableName|表名称。|
|columnName|raster 列名称。|
|pyramidLevel|需要创建的overview的金字塔层级。|
|chunkTableName|生成的raster对象的块表名称。|

函数支持创建一个基于表的raster overview对象。

所有指定的raster对象需要满足以下条件：

-   具有相同的波段数。
-   所有的raster对象要么都进行了地理参考，要么都不是。如果都是地理参考，则采用实地坐标镶嵌。
-   指定raster对象的像素类型可以不同。如果是实地坐标镶嵌，则SRID、像素分辨率必须一致。

## 示例 {#section_lmw_qhn_qfb .section}

```
Insert into rat_overview values(1, ST_BuildOverview('rat','rast', 0,'rbt_overview'));
```

