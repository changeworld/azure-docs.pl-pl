---
title: Automatyczna aktualizacja usługi mobilności w usłudze Azure Site Recovery
description: Omówienie automatycznej aktualizacji usługi mobilności podczas replikowania maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618975"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności w replikacji platformy Azure do platformy Azure

Usługa Azure Site Recovery używa miesięcznego rytmu wydania, aby rozwiązać wszelkie problemy i ulepszyć istniejące funkcje lub dodać nowe. Aby zachować aktualną wartość usługi, należy zaplanować wdrożenie poprawek co miesiąc. Aby uniknąć narzutu związanego z każdym uaktualnieniem, można zezwolić programowi Site Recovery na zarządzanie aktualizacjami składników.

Jak wspomniano w [architekturze odzyskiwania po awarii platformy Azure do platformy Azure,](azure-to-azure-architecture.md)usługa mobilności jest instalowana na wszystkich maszynach wirtualnych platformy Azure (VM), które mają włączoną replikację z jednego regionu platformy Azure do drugiego. Gdy używasz aktualizacji automatycznych, każda nowa wersja aktualizuje rozszerzenie usługi mobilności.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak działają aktualizacje automatyczne

Korzystając z usługi Site Recovery do zarządzania aktualizacjami, wdraża ona globalny element runbook (używany przez usługi platformy Azure) za pośrednictwem konta automatyzacji, utworzonego w tej samej subskrypcji co magazyn. Każdy magazyn używa jednego konta automatyzacji. Dla każdej maszyny Wirtualnej w magazynie grupa runbook sprawdza, czy aktywne aktualizacje automatyczne. Jeśli dostępna jest nowsza wersja rozszerzenia usługi mobilności, aktualizacja jest instalowana.

Domyślny harmonogram likwidowania występuje codziennie o godzinie 12:00 w strefie czasowej lokalizacji geograficznej replikowanej maszyny Wirtualnej. Harmonogram uruchomieniu można również zmienić za pomocą konta automatyzacji.

> [!NOTE]
> Począwszy od [pakietu zbiorczego aktualizacji 35,](site-recovery-whats-new.md#updates-march-2019)można wybrać istniejące konto automatyzacji do użycia w przypadku aktualizacji. Przed aktualizacją pakietu zbiorczego 35 usługa Site Recovery domyślnie utworzyła konto automatyzacji. Tę opcję można wybrać tylko po włączeniu replikacji dla maszyny Wirtualnej. Nie jest dostępna dla maszyny Wirtualnej, która ma już włączoną replikację. Wybrane ustawienie ma zastosowanie do wszystkich maszyn wirtualnych platformy Azure chronionych w tym samym magazynie.

Włączenie aktualizacji automatycznych nie wymaga ponownego uruchomienia maszyn wirtualnych platformy Azure ani nie wpływa na trwającą replikację.

Rozliczenia zadań na koncie automatyzacji są oparte na liczbie minut wykonywania zadania używanych w miesiącu. Wykonanie zadania trwa od kilku sekund do około minuty każdego dnia i jest objęte jako wolne jednostki. Domyślnie 500 minut są uwzględniane jako wolne jednostki dla konta automatyzacji, jak pokazano w poniższej tabeli:

| Bezpłatne jednostki wliczone w cenę (co miesiąc) | Price |
|---|---|
| Czas wykonywania zadania 500 minut | 0,14/minutę

## <a name="enable-automatic-updates"></a>Włączanie aktualizacji automatycznych

Istnieje kilka sposobów zarządzania aktualizacjami rozszerzenia przez funkcję Site Recovery:

- [Zarządzanie w ramach kroku włączania replikacji](#manage-as-part-of-the-enable-replication-step)
- [Przełączanie ustawień aktualizacji rozszerzenia wewnątrz przechowalni](#toggle-the-extension-update-settings-inside-the-vault)
- [Ręczne zarządzanie aktualizacjami](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Zarządzanie w ramach kroku włączania replikacji

Po włączeniu replikacji dla maszyny Wirtualnej, zaczynając [od widoku maszyny Wirtualnej](azure-to-azure-quickstart.md) lub [z magazynu usług odzyskiwania,](azure-to-azure-how-to-enable-replication.md)można zezwolić użytkownikowi Site Recovery na zarządzanie aktualizacjami rozszerzenia usługi Site Recovery lub ręczne zarządzanie nimi.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Ustawienia rozszerzenia":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełączanie ustawień aktualizacji rozszerzenia wewnątrz przechowalni

1. W przechowalni usług odzyskiwania przejdź do **witryny odzyskiwania** > **infrastruktury**.
1. W obszarze**Ustawienia** > aktualizacji rozszerzenia **dla maszyn wirtualnych** > platformy Azure**zezwalaj na zarządzanie programem Site Recovery**, wybierz pozycję **Włączone**.

   Aby ręcznie zarządzać rozszerzeniem, wybierz opcję **Wył.**

1. Wybierz **pozycję Zapisz**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Ustawienia aktualizacji rozszerzenia":::

> [!IMPORTANT]
> Po wybraniu opcji **Zezwalaj na odzyskiwanie witryny do zarządzania**ustawienie jest stosowane do wszystkich maszyn wirtualnych w przechowalni.

> [!NOTE]
> Każda z tych opcji powiadamia o koncie automatyzacji używanym do zarządzania aktualizacjami. Jeśli używasz tej funkcji w repozytorium po raz pierwszy, domyślnie tworzone jest nowe konto automatyzacji. Alternatywnie można dostosować ustawienie i wybrać istniejące konto automatyzacji. Wszystkie kolejne taks, aby włączyć replikację w tym samym magazynie użyje wcześniej utworzonego konta automatyzacji. Obecnie menu rozwijane będzie wymieniać tylko konta automatyzacji, które znajdują się w tej samej grupie zasobów co magazyn.

> [!IMPORTANT]
> Poniższy skrypt musi zostać uruchomiony w kontekście konta automatyzacji.
W przypadku niestandardowego konta automatyzacji użyj następującego skryptu:

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
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
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

            # Rate controlling the get calls to maximum 120 calls each minute.
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

### <a name="manage-updates-manually"></a>Ręczne zarządzanie aktualizacjami

1. Jeśli na maszynach wirtualnych są zainstalowane nowe aktualizacje usługi mobilności, zostanie wyświetlone następujące powiadomienie: **Dostępna jest aktualizacja agenta replikacji odzyskiwania witryny. Kliknij, aby zainstalować.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno Elementy replikowane":::

1. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny Wirtualnej.
1. Wybierz maszyny wirtualne, które chcesz uaktualnić, a następnie wybierz przycisk **OK**. Usługa Aktualizuj mobilność zostanie uruchomina dla każdej wybranej maszyny Wirtualnej.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista elementów replikowanych":::

## <a name="common-issues-and-troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli występuje problem z aktualizacjami automatycznymi, zostanie wyświetlone powiadomienie o błędzie w obszarze **Problemy z konfiguracją** na pulpicie nawigacyjnym magazynu.

Jeśli nie możesz włączyć aktualizacji automatycznych, zobacz następujące typowe błędy i zalecane akcje:

- **Błąd:** Nie masz uprawnień do tworzenia konta usługi Azure Uruchom jako (jednostki usługi) i przyznania roli współautora podmiotowi usługi.

  **Zalecane działanie:** Upewnij się, że konto zalogowane jest przypisane jako współautor i spróbuj ponownie. Aby uzyskać więcej informacji na temat przypisywania uprawnień, zobacz sekcję Wymagane uprawnienia [jak: Użyj portalu do utworzenia aplikacji i jednostki usługi Azure AD, która może uzyskiwać dostęp do zasobów](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Aby rozwiązać większość problemów po włączeniu aktualizacji automatycznych, wybierz pozycję **Napraw .** Jeśli przycisk naprawy nie jest dostępny, zobacz komunikat o błędzie wyświetlany w okienku ustawień aktualizacji rozszerzenia.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Przycisk naprawy usługi odzyskiwania witryny w ustawieniach aktualizacji rozszerzenia":::

- **Błąd:** Konto Uruchom jako nie ma uprawnień dostępu do zasobu usług odzyskiwania.

  **Zalecane działanie**: Usuń, a następnie [ponownie utwórz konto Uruchom jako](/azure/automation/automation-create-runas-account). Lub upewnij się, że aplikacja Usługi Azure Active Directory usługi Azure na koncie Automation uruchom jako może uzyskać dostęp do zasobu usług odzyskiwania.

- **Błąd:** Nie znaleziono konta Uruchom jako. Jeden z nich został usunięty lub nie został utworzony — aplikacja usługi Azure Active Directory, podmiot zabezpieczeń usługi, rola, zasób certyfikatu automatyzacji, zasób połączenia automatyzacji — lub odcisk palca nie jest identyczny między certyfikatem a połączeniem.

  **Zalecane działanie**: Usuń, a następnie [ponownie utwórz konto Uruchom jako](/azure/automation/automation-create-runas-account).

- **Błąd:** Usługa Azure Run jako certyfikat używany przez konto automatyzacji wkrótce wygaśnie.

  Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa po upływie jednego roku od daty utworzenia. Można go odnowić w dowolnym momencie przed wygaśnięciem jego ważności. Jeśli zarejestrowałeś się w celu otrzymywania powiadomień e-mail, otrzymasz również e-maile, gdy wymagane jest działanie z Twojej strony. Ten błąd zostanie wyświetlony na dwa miesiące przed datą wygaśnięcia i zmieni się na błąd krytyczny, jeśli certyfikat wygasł. Po wygaśnięciu certyfikatu automatyczna aktualizacja nie będzie działać, dopóki nie odnowisz tego samego.

  **Zalecane działanie:** Aby rozwiązać ten problem, wybierz pozycję **Napraw,** a następnie **odnów certyfikat**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="odnawianie-certyfikat":::

  > [!NOTE]
  > Po odnowieniu certyfikatu odśwież stronę, aby wyświetlić bieżący stan.
