SELECT 
    ER.ID,
    ER.UPDATEDBY,
    ER.UPDATEDAT,
    ER.EMP_TYPE,
    ER.RATE,
    ER.FROM_DATE,
    ER.TO_DATE,
    ER.REMARKS,
    S.STATE_NAME AS STATE_NAME, -- From State table
    C.COVERAGE_AREA AS COVERAGE_AREA, -- From Coverage Area table
    EC.EMP_CATEGORY AS EMP_CATEGORY, -- From Employee Category table
    ER.LOCATION
FROM 
    EMP_RATES ER
LEFT JOIN 
    STATE_TBL S -- Replace STATE_TBL with the actual state table name
ON 
    ER.STATE_NAME_ID = S.STATE_ID -- Replace STATE_NAME_ID and STATE_ID with the actual column names

LEFT JOIN 
    COVERAGE_AREA_TBL C -- Replace COVERAGE_AREA_TBL with the actual coverage area table name
ON 
    ER.COVERAGE_AREA_ID = C.COVERAGE_ID -- Replace COVERAGE_AREA_ID and COVERAGE_ID with the actual column names

LEFT JOIN 
    EMP_CATEGORY_TBL EC -- Replace EMP_CATEGORY_TBL with the actual employee category table name
ON 
    ER.EMP_CATEGORY_ID = EC.CATEGORY_ID; -- Replace EMP_CATEGORY_ID and CATEGORY_ID with the actual column names
