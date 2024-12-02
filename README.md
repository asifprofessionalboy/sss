
Select   EM.EMP_PF_EXEMPTED as EMP_PF_EXEMPTED ,EM.EMP_ESI_EXEMPTED as EMP_ESI_EXEMPTED,AttDtl.MasterID as MasterID,
sum(AttDtl.OT_hrs) as OT_hrs, Year(AttDtl.Dates) as YearWage, Month(AttDtl.Dates) as MonthWage,
AttDtl.AadharNo as AadharNo, AttDtl.VendorCode as VendorCode,EM.VendorName as VendorName,  
EM.LabourState as StateName, LM.LOcationCode as LocationCode,  LM.Location as LocationNM,   '' as SiteID, '' as WorkSite, AttDtl.WorkOrderNo,
AttDtl.WorkManSl as WorkManSl,   EM.Name as WorkManName, EM.WorkManCategory, EM.PFNo as PFNo, EM.ESINo as ESINo, 
EM.UANNo as UANNo,

(DAY(DATEADD(m, '10' + DATEDIFF(m, 0, cast('2024'as char(4))), -1))) 
as TotDaysInMonth,(DATEDIFF(ww, CAST(DATEADD(month, DATEDIFF(month, 0, '10' + '/' + '10' + '/' + '10'), 0) AS datetime) - 1, 
EOMONTH('10' + '/' + '10' + '/' + '10'))) as TotSunDays,  (case when(select(Count(ah.Hdate))  from App_HolidayMaster ah

where DATEPART(month, ah.Hdate) = '10'and Location = 'TSBSL ANGUL (TOWNSHIP)' and DATEPART(year, ah.Hdate) = '10') is null then 0 else 

(select(Count(ah.Hdate))
from App_HolidayMaster ah where DATEPART(month, ah.Hdate) = '10'and Location = 'TSBSL ANGUL (TOWNSHIP)'  and DATEPART(year, ah.Hdate) = '2024') end) 
as TotHoliDays,

((DAY(DATEADD(DD, -1, DATEADD(MM, DATEDIFF(MM, -1, '10' + '/' + '10' + '/' + '10'), 0))) - 
DATEDIFF(ww, CAST(DATEADD(month, DATEDIFF(month, 0, '10' + '/' + '10' + '/' + '10'), 0)  AS datetime) - 1,
EOMONTH('10' + '/' + '10' + '/' + '10'))) - ((case when(select(Count(ah.Hdate)) from App_HolidayMaster ah 

where DATEPART(month, ah.Hdate) = '10'and Location = 'TSBSL ANGUL (TOWNSHIP)' and DATEPART(year, ah.Hdate) = '10') is null then 0 else 
(select(Count(ah.Hdate))  from App_HolidayMaster ah  where DATEPART(month, ah.Hdate) = '10'



and Location = 'TSBSL ANGUL (TOWNSHIP)' and DATEPART(year, ah.Hdate) = '10') end)))  as TotWorkingDays,SUM(CASE WHEN AttDtl.EngagementType = 'ManPowerSupply'
AND AttDtl.Present = 'True' THEN  case when AttDtl.DayDef = 'HF' then 0.5 else 1.00 end ELSE 0.00 END) NoOfDaysWorkedMP ,
SUM(CASE WHEN AttDtl.EngagementType = 'ItemRate' AND AttDtl.Present = 'True' THEN 1.00 ELSE 0.00 END) NoOfDaysWorkedRate, 
SUM(CASE WHEN AttDtl.EngagementType = 'ManPowerSupply' AND AttDtl.Present = 'False' THEN 1.00 ELSE 0.00 END) NoOfDaysAbsMP,
(select x.Approve from App_WagesDetailsJharkhand x where x.LocationCode=LM.LocationCode and x.MasterID = AttDtl.MasterID 
and x.WorkOrderNo = AttDtl.WorkOrderNo and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates) 
and x.YearWage = Year(AttDtl.Dates)) Approve, (select case when x.MonthWage is null then 0 else 1 end from App_WagesDetailsJharkhand x 
where  x.LocationCode=AttDtl.LocationCode and  x.MasterID = AttDtl.MasterID and x.WorkOrderNo = AttDtl.WorkOrderNo 
and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates) and x.YearWage = Year(AttDtl.Dates)) Flag,
case when((select top 1 Basic_rate from App_EmployeeMaster where VendorCode = '28291' and AadharCard = AttDtl.AadharNo 
and ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc ) > isnull(WM.Basic,0)) 
then(select top 1 Basic_rate from App_EmployeeMaster where VendorCode = '28291' and AadharCard = AttDtl.AadharNo   and ApprvStatus = 'Approve' 
and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc   )
else isnull(WM.Basic,0) end as BasicRate,case when((select top 1 Da_rate from App_EmployeeMaster 
where VendorCode = '28291' and AadharCard = AttDtl.AadharNo  and ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory 
order by CreatedOn desc  ) > isnull(WM.DA,0)) then(select top 1 Da_rate from App_EmployeeMaster where VendorCode = '28291' and AadharCard = AttDtl.AadharNo
and ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc  ) 
else isnull(WM.DA,0) end as DARate,(select case when x.PieceRate is null then 0 else x.PieceRate end from App_WagesDetailsJharkhand x 
where x.MasterID = AttDtl.MasterID and x.WorkOrderNo = AttDtl.WorkOrderNo and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates)
and x.YearWage = Year(AttDtl.Dates)  and x.LocationCode=AttDtl.LocationCode) PieceRate,(select x.OverTimeAmt 
from App_WagesDetailsJharkhand x where x.MasterID = AttDtl.MasterID and x.WorkOrderNo = AttDtl.WorkOrderNo 
and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates)  and x.YearWage = Year(AttDtl.Dates) 
and x.LocationCode=AttDtl.LocationCode) OverTimeAmt,(select x.CashPaymentAmt from App_WagesDetailsJharkhand x 
where x.MasterID = AttDtl.MasterID and x.WorkOrderNo = AttDtl.WorkOrderNo and x.WorkManSl = AttDtl.WorkManSl
and x.MonthWage = Month(AttDtl.Dates) and x.YearWage = Year(AttDtl.Dates) and x.LocationCode=AttDtl.LocationCode ) 
CashPaymentAmt,(select x.OtherDeduAmt from App_WagesDetailsJharkhand x where x.MasterID = AttDtl.MasterID 
and x.WorkOrderNo = AttDtl.WorkOrderNo and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates) 
and x.YearWage = Year(AttDtl.Dates) and x.LocationCode=AttDtl.LocationCode) OtherDeduAmt,
Case when((select top 1 OtherAllow from App_EmployeeMaster where VendorCode= '28291' and AadharCard = AttDtl.AadharNo  
and ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc   )> isnull(WM.Allowance,0)) 
then(select top 1 OtherAllow from App_EmployeeMaster where VendorCode = '28291' and AadharCard = AttDtl.AadharNo   and ApprvStatus = 'Approve'
and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc  ) else isnull(WM.Allowance,0) 
end as OtherAllow,Case when((select top 1 HRA from App_EmployeeMaster where VendorCode= '28291' and AadharCard = AttDtl.AadharNo and 
ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc  )>isnull( WM.HRA,0))
then(select top 1 HRA from App_EmployeeMaster where VendorCode = '28291' and AadharCard = AttDtl.AadharNo  
and ApprvStatus = 'Approve' and WorkManSl=AttDtl.WorkManSl and WorkManCategory=EM.WorkManCategory order by CreatedOn desc ) else isnull(WM.HRA,0) 
end as HRA ,case when(select sum(CAST(present AS INT)) from App_AttendanceDetails where dates in (select ah.Hdate - 1 from App_HolidayMaster ah 
where DATEPART(month, ah.Hdate) = '10'and DATEPART(year, ah.Hdate) = '2024'and  Location = 'TSBSL ANGUL (TOWNSHIP)' and AadharNo = AttDtl.AadharNo 
and WorkOrderNo = AttDtl.WorkOrderNo))>= 1 or(select sum(CAST(present AS INT)) from App_AttendanceDetails where dates in 
(select ah.Hdate + 1 from App_HolidayMaster ah where DATEPART(month, ah.Hdate) = '10'and DATEPART(year, ah.Hdate) = '2024'
and Location = 'TSBSL ANGUL (TOWNSHIP)' and AadharNo = AttDtl.AadharNo and WorkOrderNo = AttDtl.WorkOrderNo) )>= 1 then 
(select count(distinct ch.Hdate) from App_HolidayMaster ch where  DATEPART(month, ch.Hdate) = '10'
and DATEPART(year, ch.Hdate) = '2024'and ch.Location = 'TSBSL ANGUL (TOWNSHIP)' )      else 0 end holiday,
(select x.WeeklyAllowance from App_WagesDetailsJharkhand x where x.MasterID = AttDtl.MasterID and x.WorkOrderNo = AttDtl.WorkOrderNo 
and x.WorkManSl = AttDtl.WorkManSl and x.MonthWage = Month(AttDtl.Dates)  and x.YearWage = Year(AttDtl.Dates)
and x.LocationCode=AttDtl.LocationCode) WeeklyAllowance                     from App_AttendanceDetails as AttDtl  
left join App_EmployeeMaster as EM  on AttDtl.MasterID = EM.ID left join App_LocationMaster as LM on LM.LocationCode = AttDtl.LocationCode 
left join App_SiteMaster as SM on SM.ID = AttDtl.SiteID  left join App_WageMaster as WM on WM.Category = EM.WorkManCategory 
and WM.Juridiction = 'State'and WM.State = '9' and WM.LawFor = 'Other than Mines'and wm.MinesLaw = 'Ground'
and wm.LocationCode = 'L_49'  and wm.EffectiveDate='8/1/2024 12:00:00 AM' where year(AttDtl.Dates) = '2024'
and AttDtl.LocationCode = 'L_49' and month(AttDtl.Dates) = '10'and AttDtl.VendorCode = '28291'
and AttDtl.WorkOrderNo IN 
('4700022167','4700024647','4700026023','4700025409','4700025624','4700025993','4700024600','4700025372','4700023503','4700024976',
'4700025490','4700023120','4700024542','4700023764','4700024579','4700024583','4700025727')
group by AttDtl.MasterID,Year(AttDtl.Dates) , Month(AttDtl.Dates), AttDtl.AadharNo, AttDtl.VendorCode,  EM.VendorName ,EM.LabourState, LM.LOcationCode,

LM.Location, AttDtl.WorkOrderNo, AttDtl.WorkManSl, EM.Name,EM.WorkManCategory, 

EM.PFNo, EM.ESINo, EM.UANNo, WM.Basic, WM.DA, WM.Allowance, WM.HRA, EM.EMP_PF_EXEMPTED,EM.EMP_ESI_EXEMPTED,AttDtl.LocationCode order by EM.Name  


