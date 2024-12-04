 Select AttDtl.VendorCode, EmpMst.v_name,  DATEPART(month, AttDtl.Dates) as Month, DATEPART(year, AttDtl.Dates) as Year, AttDtl.WorkOrderNo,  
 AttDtl.WorkManSl as WorkManSLNo, AttDtl.WorkManName WorkManName, AttDtl.Dates,  AttDtl.WorkManCategory as Category,  
 (case when
     (select(Count(ah.Hdate)) from App_HolidayMaster ah where DATEPART(year, ah.Hdate)= '2024' and  DATEPART(month, ah.Hdate) = '10' 
     and Location = 'KTP' group by ah.Hdate) is null then 0 else
