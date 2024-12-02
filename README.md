WITH HolidayCount AS (
    SELECT 
        AttDtl.AadharNo,
        COUNT(DISTINCT ah.Hdate) AS TotalHolidays
    FROM 
        App_HolidayMaster ah
    INNER JOIN 
        App_AttendanceDetails AttDtl 
        ON ah.Location = 'TSBSL ANGUL (TOWNSHIP)' 
        AND DATEPART(month, ah.Hdate) = 10 
        AND DATEPART(year, ah.Hdate) = 2024
    GROUP BY 
        AttDtl.AadharNo
),
WorkOrderAssignment AS (
    SELECT 
        AttDtl.AadharNo,
        AttDtl.WorkOrderNo,
        ROW_NUMBER() OVER (PARTITION BY AttDtl.AadharNo ORDER BY AttDtl.WorkOrderNo) AS RowNum
    FROM 
        App_AttendanceDetails AttDtl
    WHERE 
        MONTH(AttDtl.Dates) = 10 
        AND YEAR(AttDtl.Dates) = 2024
),
HolidayDistribution AS (
    SELECT 
        AttDtl.AadharNo,
        AttDtl.WorkOrderNo,
        CASE 
            WHEN WA.RowNum = 1 THEN HC.TotalHolidays 
            ELSE 0 
        END AS HolidayCount
    FROM 
        WorkOrderAssignment WA
    LEFT JOIN 
        HolidayCount HC 
        ON WA.AadharNo = HC.AadharNo
)
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
    (DAY(EOMONTH(CAST('2024-10-01' AS DATE)))) AS TotDaysInMonth,
    (DATEDIFF(WEEK, DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01'), 0), EOMONTH('2024-10-01'))) AS TotSunDays,
    HolidayDistribution.HolidayCount AS TotHoliDays,
    ((DAY(EOMONTH('2024-10-01')) - 
    DATEDIFF(WEEK, DATEADD(MONTH, DATEDIFF(MONTH, 0, '2024-10-01'), 0), EOMONTH('2024-10-01'))) - 
    HolidayDistribution.HolidayCount) AS TotWorkingDays,
    SUM(CASE 
        WHEN AttDtl.EngagementType = 'ManPowerSupply' AND AttDtl.Present = 'True' THEN 
            CASE 
                WHEN AttDtl.DayDef = 'HF' THEN 0.5 
                ELSE 1.00 
            END 
        ELSE 0.00 
    END) AS NoOfDaysWorkedMP,
    SUM(CASE 
        WHEN AttDtl.EngagementType = 'ItemRate' AND AttDtl.Present = 'True' THEN 1.00 
        ELSE 0.00 
    END) AS NoOfDaysWorkedRate,
    SUM(CASE 
        WHEN AttDtl.EngagementType = 'ManPowerSupply' AND AttDtl.Present = 'False' THEN 1.00 
        ELSE 0.00 
    END) AS NoOfDaysAbsMP,
    -- Additional logic here, adjusted from your query...
    HolidayDistribution.HolidayCount AS Holiday
FROM 
    App_AttendanceDetails AttDtl
LEFT JOIN 
    App_EmployeeMaster EM 
    ON AttDtl.MasterID = EM.ID
LEFT JOIN 
    App_LocationMaster LM 
    ON LM.LocationCode = AttDtl.LocationCode
LEFT JOIN 
    HolidayDistribution 
    ON AttDtl.AadharNo = HolidayDistribution.AadharNo 
    AND AttDtl.WorkOrderNo = HolidayDistribution.WorkOrderNo
WHERE 
    YEAR(AttDtl.Dates) = 2024
    AND MONTH(AttDtl.Dates) = 10
    AND AttDtl.LocationCode = 'L_49'
    AND AttDtl.VendorCode = '28291'
    AND AttDtl.WorkOrderNo IN 
    (
        '4700022167','4700024647','4700026023','4700025409','4700025624',
        '4700025993','4700024600','4700025372','4700023503','4700024976',
        '4700025490','4700023120','4700024542','4700023764','4700024579',
        '4700024583','4700025727'
    )
GROUP BY 
    EM.EMP_PF_EXEMPTED,
    EM.EMP_ESI_EXEMPTED,
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
    HolidayDistribution.HolidayCount
ORDER BY 
    EM.Name;
