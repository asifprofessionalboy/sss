# sss

 Select AttDtl.VendorCode, EmpMst.v_name,  DATEPART(month, AttDtl.Dates) as Month, DATEPART(year, AttDtl.Dates) as Year, AttDtl.WorkOrderNo,  
 AttDtl.WorkManSl as WorkManSLNo, AttDtl.WorkManName WorkManName, AttDtl.Dates,  AttDtl.WorkManCategory as Category,
 (case when(select(Count(ah.Hdate)) 
 from App_HolidayMaster ah where
 DATEPART(year, ah.Hdate)= '2024' and  DATEPART(month, ah.Hdate) = '10' and Location = 'KTP' group by ah.Hdate) is null 
 then 0 
 else
 (select(Count(ah.Hdate)) from App_HolidayMaster ah where DATEPART(year, ah.Hdate)= '2024' and DATEPART(month, ah.Hdate) = '10'
 and Location = 'KTP' group by ah.Hdate) end) as TotalHoliDays,  ((DAY(DATEADD(DD, -1, DATEADD(MM, DATEDIFF(MM, -1, AttDtl.Dates), 0)))
 - DATEDIFF(ww, CAST(DATEADD(month, DATEDIFF(month, 0, AttDtl.Dates), 0) AS datetime) - 1, EOMONTH(AttDtl.Dates))) - 
 ((case when(select(Count(ah.Hdate)) from App_HolidayMaster ah where DATEPART(year, ah.Hdate)= '2024' and DATEPART(month, ah.Hdate) = '10' and
 Location = 'KTP' group by ah.Hdate) is null then 0 else (select(Count(ah.Hdate)) from App_HolidayMaster ah where DATEPART(year, ah.Hdate)= '2024'
 and DATEPART(month, ah.Hdate) = '10' and Location = 'KTP' group by ah.Hdate) end))) as TotalWorkingDays, 
 (CASE WHEN AttDtl.EngagementType = 'ManPowerSupply'
 AND AttDtl.Present = 'True' THEN 1 ELSE 0 END) TotalPresentMP, 
 (CASE WHEN AttDtl.EngagementType = 'ItemRate' AND AttDtl.Present = 'True' 
 THEN 1 ELSE 0 END) TotalPresentRate, 
 (CASE WHEN AttDtl.Present = 'True' THEN 1 ELSE 0 END) TotalPresent,
 (CASE WHEN AttDtl.Present = 'False' 
 THEN 1 ELSE 0 END) TotalAbsent   from App_AttendanceDetails AttDtl,   App_VendorMaster EmpMst   where AttDtl.VendorCode = EmpMst.v_code  
 and DATEPART(year, AttDtl.Dates)= '2024'   and DATEPART(month, AttDtl.Dates)= '10'   and AttDtl.VendorCode = '28258'  
 order by AttDtl.VendorCode, AttDtl.WorkOrderNo, AttDtl.WorkManSl, AttDtl.WorkManName, AttDtl.Dates, AttDtl.WorkManCategory
