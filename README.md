SELECT 
    EM.EMP_PF_EXEMPTED AS EMP_PF_EXEMPTED,
    EM.EMP_ESI_EXEMPTED AS EMP_ESI_EXEMPTED,
    AttDtl.MasterID AS MasterID,
    SUM(AttDtl.OT_hrs) AS OT_hrs,
    YEAR(AttDtl.Dates) AS YearWage,
    MONTH(AttDtl.Dates) AS MonthWage,
    AttDtl.AadharNo AS AadharNo,
    AttDtl.VendorCode AS VendorCode,
    EM.VendorName AS VendorName,
    EM.LabourState AS StateName,
    LM.LocationCode AS LocationCode,
    LM.Location AS LocationNM,
    '' AS SiteID,
    '' AS WorkSite,
    AttDtl.WorkOrderNo,
    AttDtl.WorkManSl AS WorkManSl,
    EM.Name AS WorkManName,
    EM.WorkManCategory,
    EM.PFNo AS PFNo,
    EM.ESINo AS ESINo,
    EM.UANNo AS UANNo,
    DAY(EOMONTH('2024-10-01')) AS TotDaysInMonth,
    (DATEDIFF(WEEK, DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01'), 0), DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01') + 1, 0))) AS TotSunDays,
    CASE 
        WHEN (
            SELECT SUM(CAST(present AS INT)) 
            FROM App_AttendanceDetails 
            WHERE dates IN (
                SELECT DATEADD(DAY, -1, ah.Hdate) 
                FROM App_HolidayMaster ah 
                WHERE MONTH(ah.Hdate) = 10 
                  AND YEAR(ah.Hdate) = 2024 
                  AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
                  AND AadharNo = AttDtl.AadharNo 
                  AND WorkOrderNo = AttDtl.WorkOrderNo
            )
        ) >= 1 
        OR (
            SELECT SUM(CAST(present AS INT)) 
            FROM App_AttendanceDetails 
            WHERE dates IN (
                SELECT DATEADD(DAY, 1, ah.Hdate) 
                FROM App_HolidayMaster ah 
                WHERE MONTH(ah.Hdate) = 10 
                  AND YEAR(ah.Hdate) = 2024 
                  AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
                  AND AadharNo = AttDtl.AadharNo 
                  AND WorkOrderNo = AttDtl.WorkOrderNo
            )
        ) >= 1 
        THEN (
            SELECT COUNT(DISTINCT ch.Hdate) 
            FROM App_HolidayMaster ch 
            WHERE MONTH(ch.Hdate) = 10 
              AND YEAR(ch.Hdate) = 2024 
              AND ch.Location = 'TSBSL ANGUL (TOWNSHIP)'
        )
        ELSE 0 
    END AS holiday,
    ISNULL(
        (SELECT COUNT(ah.Hdate) 
         FROM App_HolidayMaster ah 
         WHERE MONTH(ah.Hdate) = 10 
           AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
           AND YEAR(ah.Hdate) = 2024), 
        0
    ) AS TotHoliDays,
    (DAY(EOMONTH('2024-10-01')) 
      - (DATEDIFF(WEEK, DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01'), 0), DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01') + 1, 0))) 
      - ISNULL(
          (SELECT COUNT(ah.Hdate) 
           FROM App_HolidayMaster ah 
           WHERE MONTH(ah.Hdate) = 10 
             AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
             AND YEAR(ah.Hdate) = 2024), 
          0
      )) AS TotWorkingDays,
    SUM(CASE WHEN AttDtl.EngagementType = 'ManPowerSupply' AND AttDtl.Present = 'True' THEN 
             CASE WHEN AttDtl.DayDef = 'HF' THEN 0.5 ELSE 1.0 END ELSE 0.0 END) AS NoOfDaysWorkedMP,
    SUM(CASE WHEN AttDtl.EngagementType = 'ItemRate' AND AttDtl.Present = 'True' THEN 1.0 ELSE 0.0 END) AS NoOfDaysWorkedRate,
    SUM(CASE WHEN AttDtl.EngagementType = 'ManPowerSupply' AND AttDtl.Present = 'False' THEN 1.0 ELSE 0.0 END) AS NoOfDaysAbsMP
FROM 
    App_AttendanceDetails AS AttDtl
LEFT JOIN 
    App_EmployeeMaster AS EM ON AttDtl.MasterID = EM.ID
LEFT JOIN 
    App_LocationMaster AS LM ON LM.LocationCode = AttDtl.LocationCode
WHERE 
    YEAR(AttDtl.Dates) = 2024 
    AND MONTH(AttDtl.Dates) = 10
    AND AttDtl.VendorCode = '28291'
    AND AttDtl.WorkOrderNo IN ('4700022167', '4700024647', '4700026023', '4700025409', 
                               '4700025624', '4700025993', '4700024600', '4700025372', 
                               '4700023503', '4700024976', '4700025490', '4700023120', 
                               '4700024542', '4700023764', '4700024579', '4700024583', 
                               '4700025727')
GROUP BY 
    AttDtl.MasterID, 
    YEAR(AttDtl.Dates), 
    MONTH(AttDtl.Dates), 
    AttDtl.AadharNo, 
    AttDtl.VendorCode,  
    EM.VendorName, 
    EM.LabourState, 
    LM.LocationCode,
    LM.Location, 
    AttDtl.WorkOrderNo, 
    AttDtl.WorkManSl, 
    EM.Name, 
    EM.WorkManCategory, 
    EM.PFNo, 
    EM.ESINo, 
    EM.UANNo, 
    EM.EMP_PF_EXEMPTED, 
    EM.EMP_ESI_EXEMPTED
ORDER BY 
    EM.Name;
