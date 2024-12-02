-- Step 1: Calculate total holidays per workman
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

-- Step 2: Assign row numbers to work orders for each workman
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

-- Step 3: Assign holiday counts only to the first work order
FinalHolidayCount AS (
    SELECT 
        AttDtl.AadharNo,
        AttDtl.WorkOrderNo,
        CASE 
            WHEN WA.RowNum = 1 THEN HC.TotalHolidays 
            ELSE 0 
        END AS HolidayCount
    FROM 
        App_AttendanceDetails AttDtl
    LEFT JOIN 
        WorkOrderAssignment WA 
        ON AttDtl.AadharNo = WA.AadharNo 
        AND AttDtl.WorkOrderNo = WA.WorkOrderNo
    LEFT JOIN 
        HolidayCount HC 
        ON AttDtl.AadharNo = HC.AadharNo
)

-- Step 4: Final query
SELECT 
    AttDtl.MasterID,
    AttDtl.AadharNo,
    AttDtl.WorkOrderNo,
    FinalHolidayCount.HolidayCount AS HolidayCount,
    EM.Name AS WorkmanName,
    SUM(CASE WHEN AttDtl.Present = 'True' THEN 1 ELSE 0 END) AS DaysWorked,
    SUM(CASE WHEN AttDtl.Present = 'False' THEN 1 ELSE 0 END) AS DaysAbsent,
    EM.VendorName,
    EM.LabourState,
    LM.LocationCode,
    LM.Location AS LocationName,
    EM.WorkManCategory,
    EM.PFNo,
    EM.ESINo,
    EM.UANNo
FROM 
    App_AttendanceDetails AttDtl
LEFT JOIN 
    App_EmployeeMaster EM 
    ON AttDtl.MasterID = EM.ID
LEFT JOIN 
    App_LocationMaster LM 
    ON AttDtl.LocationCode = LM.LocationCode
LEFT JOIN 
    FinalHolidayCount 
    ON AttDtl.AadharNo = FinalHolidayCount.AadharNo 
    AND AttDtl.WorkOrderNo = FinalHolidayCount.WorkOrderNo
WHERE 
    YEAR(AttDtl.Dates) = 2024
    AND MONTH(AttDtl.Dates) = 10
    AND AttDtl.LocationCode = 'L_49'
    AND AttDtl.VendorCode = '28291'
    AND AttDtl.WorkOrderNo IN (
        '4700022167','4700024647','4700026023','4700025409',
        '4700025624','4700025993','4700024600','4700025372',
        '4700023503','4700024976','4700025490','4700023120',
        '4700024542','4700023764','4700024579','4700024583',
        '4700025727'
    )
GROUP BY 
    AttDtl.MasterID, 
    AttDtl.AadharNo, 
    AttDtl.WorkOrderNo, 
    FinalHolidayCount.HolidayCount, 
    EM.Name,
    EM.VendorName,
    EM.LabourState,
    LM.LocationCode,
    LM.Location,
    EM.WorkManCategory,
    EM.PFNo,
    EM.ESINo,
    EM.UANNo
ORDER BY 
    EM.Name;
