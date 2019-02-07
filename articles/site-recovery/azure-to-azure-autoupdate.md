---
title: Automatyczna aktualizacja usługi mobilności, odzyskiwanie po awarii Azure – Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie automatyczna aktualizacja usługi mobilności, podczas replikowania maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 3f0f28ca22321b537ab7e8911c5cbb513a1ade81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818928"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności w replikacji Azure – Azure

Usługa Azure Site Recovery ma miesięcznego cyklu wersji, gdzie ulepszenia istniejących funkcji lub nowych pakietów są dodawane i znanych ewentualnych problemów. Oznacza to, może być aktualizowana z usługą, należy zaplanować wdrożenie tych poprawki, co miesiąc. Aby uniknąć head over skojarzone z uaktualnienia, użytkownicy zamiast tego można umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami składników. Jak wyjaśniono w [odwołanie do architektury](azure-to-azure-architecture.md) odzyskiwania po awarii Azure – Azure, usługi mobilności są instalowane na wszystkich maszynach wirtualnych platformy Azure dla których replikacja jest włączona podczas replikowania maszyn wirtualnych z jednej platformy Azure regionu do innego. Po włączeniu automatycznej aktualizacji, rozszerzenie usługi mobilności jest aktualizowany za pomocą każdej nowej wersji. W tym dokumencie szczegóły poniżej:

- Jak działa automatyczna aktualizacja
- Włącz Aktualizacje automatyczne
- Typowe problemy i rozwiązywanie problemów
 
## <a name="how-does-automatic-update-work"></a>Jak działa automatyczna aktualizacja

Gdy zezwalasz Site Recovery w celu zarządzania aktualizacjami, globalne elementu runbook (który jest używany przez usługi platformy Azure) jest wdrażany za pomocą konta usługi automation, który jest tworzony w tej samej subskrypcji co magazyn. Jedno konto usługi automation jest używany dla określonego magazynu. Element runbook sprawdza, czy dla każdej maszyny Wirtualnej w magazynie, dla których aktualizacje automatyczne są wyłączone w i inicjuje uaktualnienie rozszerzenia usługi mobilności, jeśli dostępna jest nowsza wersja. Domyślny harmonogram elementu runbook jest powtarzany codziennie o godzinie 12:00 AM zgodnie ze strefą czasową maszyny wirtualnej replikowane geograficznie. Harmonogram elementu runbook można także modyfikować za pomocą konta usługi automation przez użytkownika, jeśli to konieczne. 

> [!NOTE]
> Włączenie funkcji Aktualizacje automatyczne nie wymagają ponownego uruchomienia maszyn wirtualnych platformy Azure i nie ma wpływu na replikacji w toku.

> [!NOTE]
> Rozliczenia dla zadań, które korzystają z konta usługi automation zależy od liczby zadań razem, gdy minut w miesiącu i domyślnie 500 minut są dołączone jako bezpłatny limit jednostek dla konta usługi automation. Wykonanie przydziałów dziennych zadania z **kilka sekund, aby około minuty** i będzie **omówione w ramach bezpłatnych środków**.

BEZPŁATNY limit jednostek UWZGLĘDNIONY (MIESIĘCZNIE) ** ₹0.14 500 minut czasu uruchomione zadanie cena / minutę

## <a name="enable-automatic-updates"></a>Włącz Aktualizacje automatyczne

Można wybrać umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami w następujący sposób:-

- [W ramach kroku Włączanie replikacji](#as-part-of-the-enable-replication-step)
- [Przełącz rozszerzenie aktualizowanie ustawień w magazynie](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>W ramach kroku Włączanie replikacji:

Po włączeniu replikacji dla maszyny wirtualnej, albo uruchamianie [z widoku maszyny wirtualnej](azure-to-azure-quickstart.md), lub [z magazynu usługi recovery services](azure-to-azure-how-to-enable-replication.md), otrzymasz możliwość albo zezwolić na odzyskiwanie lokacji Zarządzanie aktualizacjami dla rozszerzenie usługi Site Recovery lub ręcznie zarządzać takie same.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełącz rozszerzenie aktualizowanie ustawień w magazynie

1. W magazynie, przejdź do **Zarządzaj**-> **infrastruktura usługi Site Recovery**
2. W obszarze **dla usługi Azure virtual Machines**-> **ustawień aktualizacji rozszerzenia**, kliknij aby wybrać, czy zezwolić na *ASR do zarządzania aktualizacjami* lub *ręcznie zarządzać*. Kliknij pozycję **Zapisz**.

![Magazyn — przełącznik automatycznej aktualizacji](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Po wybraniu *ASR umożliwia zarządzanie*, ustawienie ma zastosowanie do wszystkich maszyn wirtualnych w odpowiedni magazyn.


> [!Note] 
> Obie opcje powiadomi konta usługi automation, który służy do zarządzania aktualizacjami. Jeśli po raz pierwszy w magazynie są włączenie tej funkcji, zostaną utworzone nowe konto usługi automation. Wszystkie replikacje kolejnych Włącz w tym samym magazynie, użyje ono utworzone wcześniej.

**Jeśli chcesz użyć na koncie usługi automation w niestandardowych, użyj poniższego skryptu:-**

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls per minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-manually"></a>Zarządzaj ręcznie

1. Jeśli nowe aktualizacje są dostępne dla usługi mobilności, zainstalowane na maszynach wirtualnych platformy Azure, zobaczysz powiadomienie o treści "dostępna jest nowa aktualizacja agenta replikacji lokacji odzyskiwania. Kliknij, aby zainstalować. "

     ![Okno replikowane elementy](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny wirtualnej.
4. Wybierz maszyny wirtualne, które chcesz uaktualnić usługę mobilności na, a następnie wybierz pozycję **OK**.

     ![Zreplikowane elementy listy maszyn wirtualnych](./media/vmware-azure-install-mobility-service/update-okpng.png)

Uruchamia zadanie Aktualizacja usługi mobilności dla każdego z wybranych maszyn wirtualnych.


## <a name="common-issues--troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli występuje problem z aktualizacji automatycznych, zostanie wyświetlone powiadomienie o tym samym, w obszarze "Problemy z konfiguracją" na pulpicie nawigacyjnym magazynu. 

W przypadku próby włączenia automatycznej aktualizacji i nie powiodło się, można znaleźć poniżej do rozwiązywania problemów.

**Błąd**: Nie masz uprawnień do utworzenia konta Uruchom jako platformy Azure (jednostki usługi) i udzielenia roli Współautor jednostce usługi. 
- Zalecana akcja: Upewnij się, że zalogowanym koncie jest przypisany "Współautor", a następnie spróbuj ponownie wykonać operację. Zapoznaj się [to](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) dokumentu, aby uzyskać więcej informacji na temat przypisywania odpowiednich uprawnień.
 
Gdy aktualizacje automatyczne są włączone, większość problemów naprawiona w usłudze Site Recovery i wymaga kliknij pozycję "**naprawy**" przycisk.

![przycisk naprawy](./media/azure-to-azure-autoupdate/repair.png)

W przypadku, gdy przycisk naprawy nie jest dostępny, zapoznaj się z komunikatem o błędzie wyświetlany w obszarze okienka ustawień rozszerzenia.

 - **Błąd**: Konto Uruchom jako nie ma uprawnień do dostępu do zasobu usługi recovery services.

    **Zalecana akcja**: Usuń i następnie [ponownie utwórz konto Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account) lub upewnij się, że aplikacja usługi Automation Uruchom jako dla konta usługi Azure Active Directory ma dostęp do zasobu usługi recovery services.

- **Błąd**: Nie można odnaleźć konta Uruchom jako. Albo jeden z nich został usunięty lub nie został utworzony — aplikacja usługi Azure Active Directory, nazwa główna usługi, rola, zasób certyfikatu usługi Automation, zasób połączenia usługi Automation — lub odcisk palca nie jest taki sam dla certyfikatu i połączenia. 

    **Zalecana akcja**: Usuń i [ponownie utwórz konto Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
