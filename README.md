
--case when


--(select sum(CAST(present AS INT)) from App_AttendanceDetails where dates in 

--(select ah.Hdate - 1 from App_HolidayMaster ah 
--where DATEPART(month, ah.Hdate) = '10'and DATEPART(year, ah.Hdate) = '2024'and  Location = 'TSBSL ANGUL (TOWNSHIP)' and AadharNo = AttDtl.AadharNo 
--and WorkOrderNo = AttDtl.WorkOrderNo))>= 1 

--or

--(select sum(CAST(present AS INT)) from App_AttendanceDetails where dates in 



--(select ah.Hdate + 1 from App_HolidayMaster ah where DATEPART(month, ah.Hdate) = '10'and DATEPART(year, ah.Hdate) = '2024'
--and Location = 'TSBSL ANGUL (TOWNSHIP)' and AadharNo = AttDtl.AadharNo and WorkOrderNo = AttDtl.WorkOrderNo) )>= 1 

--then 


--(select count(distinct ch.Hdate) from App_HolidayMaster ch where  DATEPART(month, ch.Hdate) = '10'
--and DATEPART(year, ch.Hdate) = '2024'and ch.Location = 'TSBSL ANGUL (TOWNSHIP)' ) 

--else 0 end holiday,
