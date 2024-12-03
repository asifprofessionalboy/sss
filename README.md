CASE 
    WHEN AttDtl.WorkOrderNo = (
        SELECT TOP 1 WorkOrderNo
        FROM App_AttendanceDetails subAtt
        WHERE subAtt.AadharNo = AttDtl.AadharNo
          AND YEAR(subAtt.Dates) = YEAR(AttDtl.Dates)
          AND MONTH(subAtt.Dates) = MONTH(AttDtl.Dates)
        ORDER BY subAtt.WorkOrderNo
    )
    THEN 
        CASE 
            WHEN (
                SELECT SUM(CAST(present AS INT)) 
                FROM App_AttendanceDetails 
                WHERE dates IN (
                    SELECT DATEADD(DAY, -1, ah.Hdate) 
                    FROM App_HolidayMaster ah 
                    WHERE DATEPART(month, ah.Hdate) = 10 
                      AND DATEPART(year, ah.Hdate) = 2024 
                      AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
                      AND AadharNo = AttDtl.AadharNo
                )
            ) >= 1 
            OR (
                SELECT SUM(CAST(present AS INT)) 
                FROM App_AttendanceDetails 
                WHERE dates IN (
                    SELECT DATEADD(DAY, 1, ah.Hdate) 
                    FROM App_HolidayMaster ah 
                    WHERE DATEPART(month, ah.Hdate) = 10 
                      AND DATEPART(year, ah.Hdate) = 2024 
                      AND Location = 'TSBSL ANGUL (TOWNSHIP)' 
                      AND AadharNo = AttDtl.AadharNo
                )
            ) >= 1 
            THEN (
                SELECT COUNT(DISTINCT ch.Hdate) 
                FROM App_HolidayMaster ch 
                WHERE DATEPART(month, ch.Hdate) = 10 
                  AND DATEPART(year, ch.Hdate) = 2024 
                  AND ch.Location = 'TSBSL ANGUL (TOWNSHIP)'
            )
            ELSE 0 
        END
    ELSE 0
END AS Holiday
