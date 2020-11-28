##################
<# 
.DESCRIPTION 
 Script to get published application information - AppName, Delivery Group it is in, Associated Users for the app, the servers that are in the Delivery Group
 May not be 100% accruate if you are using Application Groups with Tags to launch from specific servers.
 
.NOTES 
+---------------------------------------------------------------------------------------------+ 
| Citrix Published App Information                                                                                | 
+---------------------------------------------------------------------------------------------+ 
|   DATE        : 28/11/2020 
|   AUTHOR      : rslw.xyz 
+---------------------------------------------------------------------------------------------+ 
#>
Add-PSSnapin Citrix.*

$dgs = Get-BrokerDesktopGroup

$CObj = $null
$CObj = @()

$localTime = Get-Date
$UtcTime = $localTime.ToUniversalTime()

$Date = '{0:dd-MM-yyyy HH:mm}' -f $UtcTime

ForEach($dg in $dgs){

$apps = Get-BrokerApplication -AssociatedDesktopGroupUid $dg.Uid

ForEach($app in $apps){

    [String]$AssocUN = get-BrokerApplication -Uid $app.Uid | Select-Object -ExpandProperty AssociatedUserNames

    $adgUids = @()
    $adgUids = $app.AssociatedDesktopGroupUids

    ForEach($adgUid in $adgUids){

    [String]$dgName = Get-BrokerDesktopGroup -Uid $adgUid | Select-Object -ExpandProperty Name

    $Obj = New-Object psobject

    if($AssocUN -eq ""){
    
        $DGUsers = (Get-BrokerAccessPolicyRule -AllowedConnections ViaAG -DesktopGroupUid $adgUid | Select-Object -ExpandProperty IncludedUsers).name
        $DGServers = (Get-BrokerMachine -DesktopGroupUid $adgUid).MachineName

        $Obj | Add-Member -Name ReportTime -MemberType NoteProperty -Value $Date
        $Obj | Add-Member -MemberType NoteProperty -Name AppName -value $app.ApplicationName
        $Obj | Add-Member -MemberType NoteProperty -Name PublishedName -value $app.PublishedName
        $Obj | Add-Member -MemberType NoteProperty -Name AssociatedUsers -value ($DGUsers -join ",")
        $Obj | Add-Member -MemberType NoteProperty -Name AppExe -value $app.CommandLineExecutable
        $Obj | Add-Member -MemberType NoteProperty -Name AppCmdLine -value $app.CommandLineArguments
        $Obj | Add-Member -MemberType NoteProperty -Name AppWorkDir -value $app.WorkingDirectory
        $Obj | Add-Member -MemberType NoteProperty -Name AppDeliveryGroup -value $dgName
        $Obj | Add-Member -MemberType NoteProperty -Name DeliveryGroupServers -value ($DGServers -join ",")
        $CObj += $Obj

    }
    else {

        $DGServers = (Get-BrokerMachine -DesktopGroupUid $adgUid).MachineName

        $Obj | Add-Member -Name ReportTime -MemberType NoteProperty -Value $Date
        $Obj | Add-Member -MemberType NoteProperty -Name AppName -value $app.ApplicationName
        $Obj | Add-Member -MemberType NoteProperty -Name PublishedName -value $app.PublishedName
        $Obj | Add-Member -MemberType NoteProperty -Name AssociatedUsers -value $AssocUN
        $Obj | Add-Member -MemberType NoteProperty -Name AppExe -value $app.CommandLineExecutable
        $Obj | Add-Member -MemberType NoteProperty -Name AppCmdLine -value $app.CommandLineArguments
        $Obj | Add-Member -MemberType NoteProperty -Name AppWorkDir -value $app.WorkingDirectory
        $Obj | Add-Member -MemberType NoteProperty -Name AppDeliveryGroup -value $dgName
        $Obj | Add-Member -MemberType NoteProperty -Name DeliveryGroupServers -value ($DGServers -join ",")
        $CObj += $Obj
    }

}
}
}

$CObj | Export-csv D:\citrix_applicationpermissions.csv -NoTypeInformation -Append
