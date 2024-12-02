case 
    when (
        select SUM(CAST(present AS INT)) 
        from App_AttendanceDetails 
        where dates in (
            select ah.Hdate - 1 
            from App_HolidayMaster ah 
            where DATEPART(month, ah.Hdate) = '10' 
              and DATEPART(year, ah.Hdate) = '2024' 
              and Location = 'TSBSL ANGUL (TOWNSHIP)' 
              and AadharNo = AttDtl.AadharNo 
              and WorkOrderNo = AttDtl.WorkOrderNo
        )
    ) >= 1 
    or (
        select SUM(CAST(present AS INT)) 
        from App_AttendanceDetails 
        where dates in (
            select ah.Hdate + 1 
            from App_HolidayMaster ah 
            where DATEPART(month, ah.Hdate) = '10' 
              and DATEPART(year, ah.Hdate) = '2024' 
              and Location = 'TSBSL ANGUL (TOWNSHIP)' 
              and AadharNo = AttDtl.AadharNo 
              and WorkOrderNo = AttDtl.WorkOrderNo
        )
    ) >= 1 
    then (
        case 
            when ROW_NUMBER() OVER (
                PARTITION BY AttDtl.AadharNo 
                ORDER BY AttDtl.WorkOrderNo
            ) = 1 
            then (
                select COUNT(DISTINCT ch.Hdate) 
                from App_HolidayMaster ch 
                where DATEPART(month, ch.Hdate) = '10' 
                  and DATEPART(year, ch.Hdate) = '2024' 
                  and ch.Location = 'TSBSL ANGUL (TOWNSHIP)'
            ) 
            else 0 
        end
    ) 
    else 0 
end AS holiday
