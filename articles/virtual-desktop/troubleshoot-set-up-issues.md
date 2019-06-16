---
title: Pulpit wirtualny Windows dzierżawy i hosta tworzenia puli - Azure
description: Jak do rozwiązywania oraz usuwania puli dzierżawy i hosta problemy występujące podczas instalacji środowiska pulpitu wirtualnego Windows dzierżawy.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523328"
---
# <a name="tenant-and-host-pool-creation"></a>Tworzenie dzierżawy i puli hosta

W tym artykule opisano problemy podczas początkowej konfiguracji dzierżawy pulpitu wirtualnego Windows i infrastruktury puli hosta sesji powiązanych.

## <a name="provide-feedback"></a>Przekazywanie opinii

Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Pobieranie obrazu wielu sesji systemu Windows 10 Enterprise

Aby użyć obrazu wielu sesji systemu Windows 10 Enterprise, przejdź do portalu Azure Marketplace wybierz pozycję **wprowadzenie** > **Microsoft Windows 10** > i [Windows 10 Enterprise dla Pulpitów wirtualnych (wersja zapoznawcza), wersja 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Zrzut ekranu przedstawiający Wybieranie systemu Windows 10 Enterprise podglądu pulpitów wirtualnych, 1809 wersji.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Tworzenie wirtualnego pulpitu Windows dzierżawy

W tej części omówiono potencjalnych problemów podczas tworzenia dzierżawy Windows pulpitu wirtualnego.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Użytkownik nie ma uprawnień do zapytań do usługi zarządzania

![Okno zrzut ekranu programu PowerShell, w którym użytkownik nie ma uprawnień do zapytań do usługi zarządzania.](media/UserNotAuthorizedNewTenant.png)

Przykład surowa wersja błędu:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Przyczyna:** Użytkownik, który jest zalogowany, nie przypisano roli TenantCreator ich usługi Azure Active Directory.

**Poprawka:** Postępuj zgodnie z instrukcjami w [przypisanie roli aplikacji TenantCreator do użytkownika w dzierżawie usługi Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Po wykonaniu instrukcji, będziesz mieć przypisane do roli TenantCreator użytkownika.

![Zrzut ekranu TenantCreator przypisanej roli.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Tworzenie maszyn wirtualnych z hosta sesji pulpitu wirtualnego Windows

Host sesji maszyny wirtualne można tworzyć na kilka sposobów, ale problemy związane z szablonu usługi Azure Resource Manager do aprowizacji maszyn wirtualnych obsługują tylko zespoły zdalnego pulpitu wirtualnego usług/Windows Desktop. Szablon usługi Azure Resource Manager jest dostępny w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) i [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemy przy użyciu Windows pulpitu wirtualnego — Aprowizowanie ofertę hosta puli portalu Azure Marketplace

Windows Desktop wirtualnych — Aprowizowanie szablonu puli host jest dostępny w witrynie Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Błąd: Korzystając z linku z serwisu GitHub, komunikat "Utwórz bezpłatne konto" pojawia się

![Zrzut ekranu, aby utworzyć bezpłatne konto.](media/be615904ace9832754f0669de28abd94.png)

**Przyczyny 1:** Nie istnieją aktywne subskrypcje w ramach konta używane na potrzeby logowania do platformy Azure lub używane konto nie ma uprawnień do wyświetlania subskrypcji.

**Napraw 1:** Zaloguj się przy użyciu konta które ma prawa dostępu współautora (co najmniej) do subskrypcji, gdzie hosta sesji maszyn wirtualnych do wdrożenia.

**Przyczyny 2:** Używana subskrypcja jest częścią dzierżawy dostawcy usług firmy Microsoft w chmurze (CSP).

**Napraw 2:** Przejdź do lokalizacji usługi GitHub dla **tworzenie i aprowizowanie nowej puli hosta wirtualnego pulpitu Windows** i postępuj zgodnie z tymi instrukcjami:

1. Kliknij prawym przyciskiem myszy **Wdróż na platformie Azure** i wybierz **adres łącza kopiowania**.
2. Otwórz **Notatnik** i Wklej link.
3. Przed znakiem # Wstaw nazwę dzierżawy klienta punktu końcowego programu CSP.
4. Otwórz nowy link w przeglądarce i witryny Azure portal zostanie załadowany szablon.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Szablon usługi Azure Resource Manager i błędy programu PowerShell Desired State Configuration (DSC)

Wykonaj te instrukcje, rozwiązywać problemy z niepowodzeniem wdrożenia szablony usługi Azure Resource Manager i programu PowerShell DSC.

1. Przejrzyj błędy w wdrożenia, używając [wyświetlanie operacji wdrażania przy użyciu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Jeśli nie ma żadnych błędów w ramach wdrożenia, przejrzyj błędy w dzienniku aktywności przy użyciu [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Po zidentyfikowaniu błąd Użyj komunikat o błędzie i zasobów w [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) rozwiązania problemu.
4. Usuń wszystkie zasoby utworzone podczas poprzedniego wdrożenia i ponów próbę, ponownie wdrożenie szablonu.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Błąd: Wdrożenie nie powiodło się...<hostname>/joindomain

![Twoje wdrożenie nie powiodło się zrzut ekranu.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Przykład surowa wersja błędu:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyny 1:** Poświadczenia podane dla przyłączanie maszyn wirtualnych do domeny są nieprawidłowe.

**Napraw 1:** Wyświetlony błąd "Nieprawidłowe poświadczenia" dla maszyn wirtualnych nie są przyłączone do domeny w [konfigurację maszyny Wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

**Przyczyny 2:** Nazwa domeny nie jest rozpoznawane.

**Napraw 2:** Można znaleźć błąd "nazwa domeny nie rozwiąże to" maszyny wirtualne nie są przyłączone do domeny w [konfigurację maszyny Wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Błąd: VMExtensionProvisioningError

![Zrzut ekranu z Your wdrożenie nie powiodło się ze stanem inicjowania obsługi terminalu nie powiodło się.](media/7aaf15615309c18a984673be73ac969a.png)

**Przyczyny 1:** Błąd przejściowy ze środowiskiem pulpitu wirtualnego Windows.

**Przyczyny 2:** Przejściowy błąd połączenia.

**Poprawka:** Upewnij się, że środowisko pulpitu wirtualnego Windows jest w dobrej kondycji, logując się przy użyciu programu PowerShell. Zakończ rejestrację maszynę Wirtualną ręcznie w [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Błąd: Określona nazwa użytkownika administratora nie jest dozwolone.

![Zrzut ekranu przedstawiający wdrożenie nie powiodło się nie jest dozwolona przez administratora określone.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Przykład surowa wersja błędu:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Przyczyna:** Podane hasło zawiera zakazane podciągów (admin, administrator, główny).

**Poprawka:** Aktualizowanie nazwy użytkownika lub użyj różnych użytkowników.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Błąd: Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia

![Zrzut ekranu przedstawiający operacja dotycząca zasobu zakończyła ze stanem inicjowania obsługi terminalu, w tym wdrażanie nie powiodło się.](media/49c4a1836a55d91cd65125cf227f411f.png)

Przykład surowa wersja błędu:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie był w stanie uzyskać dostęp administratora na maszynie Wirtualnej.

**Poprawka:** Upewnij się, nazwę użytkownika i hasło mają dostęp administracyjny na maszynie wirtualnej i ponownie uruchom szablon usługi Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Błąd: Niepowodzenia wdrożenia — konfiguracji DSC programu PowerShell "FirstSessionHost" została ukończona z błędami

![Zrzut ekranu przedstawiający wdrożenie się nie powieść się przy użyciu konfiguracji DSC programu PowerShell "FirstSessionHost" została ukończona z błędami.](media/64870370bcbe1286906f34cf0a8646ab.png)

Przykład surowa wersja błędu:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie był w stanie uzyskać dostęp administratora na maszynie Wirtualnej.

**Poprawka:** Upewnij się, że nazwa użytkownika i hasło podane dostęp administratora na maszynie wirtualnej, a następnie ponownie uruchom szablon usługi Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: Niepowodzenia wdrożenia — InvalidResourceReference

Przykład surowa wersja błędu:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyna:** Część nazwy grupy zasobów jest używana do niektórych zasobów tworzonego przez szablon. Ze względu na nazwę dopasowania istniejących zasobów szablon może wybrać istniejący zasób z innej grupy.

**Poprawka:** Podczas uruchamiania szablonu usługi Azure Resource Manager do wdrożenia hosta sesji maszyn wirtualnych, unikatowość dwa pierwsze znaki nazwy grupy zasobów z subskrypcji.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: Niepowodzenia wdrożenia — InvalidResourceReference

Przykład surowa wersja błędu:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Przyczyna:** Ten błąd jest, ponieważ karta sieciowa, utworzony przy użyciu szablonu usługi Azure Resource Manager ma taką samą nazwę jak karta sieciowa już w sieci Wirtualnej.

**Poprawka:** Użyj prefiksu innego hosta.

### <a name="error-deploymentfailed--error-downloading"></a>Błąd: Niepowodzenia wdrożenia — błąd podczas pobierania

Przykład surowa wersja błędu:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Przyczyna:** Przyczyną tego błędu jest trasę statyczną, reguły zapory lub sieciowej grupy zabezpieczeń blokuje pobieranie pliku zip, powiązane z szablonem usługi Azure Resource Manager.

**Poprawka:** Usuń blokuje trasę statyczną, reguły zapory i sieciowej grupy zabezpieczeń. Opcjonalnie Otwórz plik json szablonu usługi Azure Resource Manager w edytorze tekstów, wykonać link do pliku zip i Pobierz zasób do dozwolonych lokalizacji.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Użytkownik nie ma uprawnień do zapytań do usługi zarządzania

Przykład surowa wersja błędu:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Przyczyna:** Określony Administrator dzierżawy pulpitu wirtualnego Windows nie ma prawidłową rolą.

**Poprawka:** Użytkownik, który utworzył dzierżawy Windows pulpitu wirtualnego musi zalogować się do programu PowerShell Windows wirtualnego pulpitu i przypisz tego użytkownika Próba przypisania roli. Jeśli korzystasz z usługi GitHub usługi Azure Resource Manager parametrów szablonu, wykonaj te instrukcje, za pomocą poleceń programu PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Błąd: Użytkownik wymaga usługi Azure Multi-Factor Authentication (MFA)

![Zrzut ekranu przedstawiający wdrożenie nie powiodło się ze względu na brak uwierzytelniania Multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Przykład surowa wersja błędu:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Przyczyna:** Określony Administrator dzierżawy pulpitu wirtualnego Windows wymaga usługi Azure Multi-Factor Authentication (MFA) do logowania.

**Poprawka:** Tworzenie jednostki usługi i przypisz go rolę w dzierżawie pulpitu wirtualnego Windows wykonując kroki opisane w [samouczka: Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Po sprawdzeniu, czy możesz zalogować się do Windows pulpitu wirtualnego przy użyciu jednostki usługi, należy ponownie uruchomić oferty w portalu Azure Marketplace lub szablonu usługi GitHub usługi Azure Resource Manager, w zależności od metody, których używasz. Postępuj zgodnie z poniższymi instrukcjami, aby wprowadzić poprawne parametry swoje metody.

Jeśli korzystasz z oferty w portalu Azure Marketplace, podaj wartości następujących parametrów do prawidłowo uwierzytelniania Windows pulpitu wirtualnego:

- Pulpit wirtualny Windows dzierżawy właściciela pulpitu zdalnego: Nazwa główna usługi
- Identyfikator aplikacji: Utworzony identyfikator aplikacji nową nazwę główną usługi
- Hasło/Potwierdź hasło: Hasła, który został wygenerowany dla jednostki usługi
- Identyfikator dzierżawy usługi Azure AD: Utworzony identyfikator dzierżawy usługi Azure AD jednostki usługi

Jeśli korzystasz z szablonów usługi GitHub usługi Azure Resource Manager, podaj wartości następujących parametrów do prawidłowo uwierzytelniania Windows pulpitu wirtualnego:

- Główna nazwa użytkownika administratora dzierżawy (UPN) lub identyfikator aplikacji: Utworzony identyfikator aplikacji nową nazwę główną usługi
- Hasło administratora dzierżawy: Hasła, który został wygenerowany dla jednostki usługi
- IsServicePrincipal: **true**
- AadTenantId: Utworzony identyfikator dzierżawy usługi Azure AD jednostki usługi

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne na temat rozwiązywania problemów ścieżki eskalacji i pulpitu wirtualnego Windows zobacz [Rozwiązywanie problemów — omówienie, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązywać problemy podczas konfigurowania maszyny wirtualnej (VM) w Windows pulpitu wirtualnego, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązywać problemy z połączeniami klienta pulpitu wirtualnego Windows, zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md).
- Aby rozwiązywać problemy podczas korzystania z programu PowerShell z usługą Windows pulpitu wirtualnego, zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).