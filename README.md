[HttpPost]
[Route("Submit")]
[Authorize]
public async Task<HttpStatusCode> Submit([FromBody] List<SmeterDTO> SmeterDTO)
{
    try
    {
        var smeters = SmeterDTO.Select(smetersDTO => new SmeterDBModel
        {
            sumoid = smetersDTO.sumoid,
            notificationtype = smetersDTO.notificationtype,
            alarmcode = smetersDTO.alarmcode,
            notificationdate = smetersDTO.notificationdate ?? DateTime.Now,
            replyserviceid = smetersDTO.replyserviceid,
            supplytype = smetersDTO.supplytype,
            servicepointno = smetersDTO.servicepointno,
            meterno = smetersDTO.meterno,
            paymentcardid = smetersDTO.paymentcardid,
            jobid = smetersDTO.jobid,
            jobstatus = smetersDTO.jobstatus,
            joberrorcode = smetersDTO.joberrorcode,
            devicetime = smetersDTO.devicetime ?? DateTime.Now,
            mseid = smetersDTO.mseid,
            msetransid = smetersDTO.msetransid,
            eventcode = smetersDTO.eventcode,
            severity = smetersDTO.severity,
            transactionid = smetersDTO.transactionid,
            issuedate = smetersDTO.issuedate ?? DateTime.Now,
            transferstatus = smetersDTO.transferstatus,
            rejectionreason = smetersDTO.rejectionreason,
            transfertime = smetersDTO.transfertime ?? DateTime.Now,
        }).ToList();

        _logger.LogInformation("//Start submitting date");
        if (await _smeterDataAccess.SubmitSmeterAsync(smeters))
            return HttpStatusCode.OK;
        else
            throw new BadHttpRequestException("Unable to submit data");
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, ex.Message);
        throw new BadHttpRequestException("Unable to submit data." + ex.Message);
    }
    finally
    {
        _logger.LogInformation("//End submitting date");
    }
}
