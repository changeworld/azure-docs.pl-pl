---
title: Tworzenie puli hostów dzierżawy pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak rozwiązywać problemy z pulą dzierżawy i hosta podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371600"
---
# <a name="tenant-and-host-pool-creation"></a>Tworzenie dzierżawy i puli hosta

W tym artykule opisano problemy podczas początkowej konfiguracji dzierżawy pulpitu wirtualnego systemu Windows i powiązanej infrastruktury puli hostów sesji.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność techniczną pulpitu wirtualnego systemu Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby omówić usługę Pulpit wirtualny systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Pobieranie obrazu wielosesyjnego systemu Windows 10 Enterprise

Aby użyć obrazu wielosesyjnego systemu Windows 10 Enterprise, przejdź do portalu Azure Marketplace, wybierz pozycję **Wprowadzenie** > **do systemu Microsoft Windows 10** > i [Windows 10 Enterprise for Virtual Desktops w wersji 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Zrzut ekranu przedstawiający wybór systemu Windows 10 Enterprise for Virtual Desktops w wersji 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Tworzenie dzierżawy pulpitu wirtualnego systemu Windows

W tej sekcji opisano potencjalne problemy podczas tworzenia dzierżawy pulpitu wirtualnego systemu Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: użytkownik nie jest upoważniony do wykonywania zapytań do usługi zarządzania

![Zrzut ekranu przedstawiający okno programu PowerShell, w którym użytkownik nie jest autoryzowany do wykonywania zapytań o usługę zarządzania.](media/UserNotAuthorizedNewTenant.png)

Przykład nieprzetworzonego błędu:

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

**Przyczyna:** Użytkownik, który jest zalogowany nie został przypisany rolę TenantCreator w usłudze Azure Active Directory.

**Poprawka:** Postępuj zgodnie z instrukcjami w [Przypisywanie roli aplikacji TenantCreator do użytkownika w dzierżawie usługi Azure Active Directory.](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role) Po postępując zgodnie z instrukcjami, będziesz mieć użytkownika przypisany do roli TenantCreator.

![Zrzut ekranu przypisanej roli TenantCreator.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Tworzenie maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows

Maszyny wirtualne hosta sesji można tworzyć na kilka sposobów, ale zespół pulpitu wirtualnego systemu Windows obsługuje tylko problemy z inicjowaniem obsługi administracyjnej maszyn wirtualnych związane z ofertą [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/) Aby uzyskać więcej informacji, zobacz [Problemy z korzystaniem z pulpitu wirtualnego systemu Windows — aprowizowanie puli hostów usługi Azure Marketplace.](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemy z korzystaniem z pulpitu wirtualnego systemu Windows — aprowizuj ofertę platformy Azure Marketplace z pulą hostów

Pulpit wirtualny systemu Windows — aprowizuj szablon puli hostów jest dostępny w portalu Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Błąd: Podczas korzystania z łącza z gitHub pojawia się komunikat "Utwórz bezpłatne konto"

![Zrzut ekranu, aby utworzyć bezpłatne konto.](media/be615904ace9832754f0669de28abd94.png)

**Przyczyna 1:** Nie ma aktywnych subskrypcji na koncie używanym do logowania się na platformie Azure lub używane konto nie ma uprawnień do wyświetlania subskrypcji.

**Poprawka 1:** Zaloguj się przy za pomocą konta, które ma dostęp współautora (co najmniej) do subskrypcji, w której zostaną wdrożone maszyny wirtualne hosta sesji.

**Przyczyna 2:** Używana subskrypcja jest częścią dzierżawy dostawcy usług w chmurze firmy Microsoft (CSP).

**Poprawka 2:** Przejdź do lokalizacji Usługi GitHub, **aby utworzyć i aprowizować nową pulę hostów pulpitu wirtualnego systemu Windows** i postępuj zgodnie z poniższymi instrukcjami:

1. Kliknij prawym przyciskiem myszy **pozycję Wdrażanie** na platformie Azure i wybierz pozycję **Kopiuj adres łącza**.
2. Otwórz **Notatnik** i wklej łącze.
3. Przed znakiem # wstaw nazwę dzierżawy klienta końcowego CSP.
4. Otwórz nowe łącze w przeglądarce, a witryna Azure portal załaduje szablon.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Błąd: pojawia się błąd "wdrożenie szablonu jest nieprawidłowe"

![Zrzut ekranu przedstawiający "wdrożenie szablonu ... jest nieprawidłowy" błąd](media/troubleshooting-marketplace-validation-error-generic.png)

Przed podjęciem określonej akcji należy sprawdzić dziennik aktywności, aby wyświetlić szczegółowy błąd sprawdzania poprawności wdrożenia nie powiodło się.

Aby wyświetlić błąd w dzienniku aktywności:

1. Zamknij bieżącą ofertę wdrażania portalu Azure Marketplace.
2. Na górnym pasku wyszukiwania wyszukaj i wybierz pozycję **Dziennik aktywności**.
3. Znajdź działanie o nazwie **Sprawdzanie poprawności wdrożenia,** które ma stan **Niepowodzenie** i wybierz działanie.
   ![Zrzut ekranu przedstawiający poszczególne **Sprawdzanie poprawności wdrożenia** o stanie **Nie powiodło się**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Wybierz JSON, a następnie przewiń w dół do dołu ekranu, aż zobaczysz pole "statusMessage".
   ![Zrzut ekranu przedstawiający nieudaną aktywność z czerwonym polem wokół właściwości statusMessage tekstu JSON.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Jeśli szablon operacji przekroczy limit przydziału, można wykonać jedną z następujących czynności, aby go naprawić:

 - Uruchom portal Azure Marketplace z parametrami użytymi po raz pierwszy, ale tym razem użyj mniejszej liczby maszyn wirtualnych i rdzeni maszyn wirtualnych.
 - Otwórz łącze widoczne w polu **statusMessage** w przeglądarce, aby przesłać żądanie zwiększenia przydziału dla subskrypcji platformy Azure dla określonej jednostki SKU maszyny Wirtualnej.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Szablon usługi Azure Resource Manager i błędy konfiguracji żądanego stanu programu PowerShell (DSC)

Postępuj zgodnie z tymi instrukcjami, aby rozwiązać problemy z nieudanymi wdrożeniami szablonów usługi Azure Resource Manager i usługi PowerShell DSC.

1. Przejrzyj błędy we wdrożeniu przy użyciu [funkcji Wyświetl operacje wdrażania za pomocą usługi Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Jeśli we wdrożeniu nie ma żadnych błędów, przejrzyj błędy w dzienniku działań przy użyciu [dzienników aktywności Wyświetl, aby przeprowadzić inspekcję akcji zasobów](../azure-resource-manager/resource-group-audit.md).
3. Po zidentyfikowaniu błędu użyj komunikatu o błędzie i zasobów w [rozwiązywaniu typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager,](../azure-resource-manager/resource-manager-common-deployment-errors.md) aby rozwiązać ten problem.
4. Usuń wszystkie zasoby utworzone podczas poprzedniego wdrożenia i ponów próbę ponownego wdrożenia szablonu.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Błąd: Wdrożenie nie powiodło się....\<nazwa hosta>/joindomain

![Zrzut ekranu niepowodzenia wdrażania.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Przykład nieprzetworzonego błędu:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyna 1:** Poświadczenia dostarczone do łączenia maszyn wirtualnych z domeną są niepoprawne.

**Poprawka 1:** Zobacz błąd "Nieprawidłowe poświadczenia" dla maszyn wirtualnych nie są przyłączone do domeny w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

**Przyczyna 2:** Nazwa domeny nie rozwiązuje problemu.

**Poprawka 2:** Zobacz [Błąd: nazwa domeny nie jest rozpoznawana](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

**Przyczyna 3:** Konfiguracja DNS sieci wirtualnej (VNET) jest ustawiona na **Domyślna**.

Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Otwórz witrynę Azure Portal i przejdź do karty **Sieci wirtualne.**
2. Znajdź swoją wirtualną witrynę wirtualną, a następnie wybierz **pozycję Serwery DNS**.
3. Menu serwerów DNS powinno pojawić się po prawej stronie ekranu. W tym menu wybierz pozycję **Niestandardowe**.
4. Upewnij się, że serwery DNS wymienione w obszarze Niestandardowe są zgodne z kontrolerem domeny lub domeną usługi Active Directory. Jeśli serwer DNS nie jest widoczny, możesz go dodać, wprowadzając jego wartość w polu **Dodaj serwer DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Błąd: Wdrożenie nie powiodło się...\Nieautoryzowane

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Przyczyna:** Subskrypcja, której używasz, to typ, który nie może uzyskać dostępu do wymaganych funkcji w regionie, w którym klient próbuje wdrożyć. Na przykład subskrypcje MSDN, Bezpłatne lub Edukacyjne mogą wyświetlać ten błąd.

**Poprawka:** Zmień typ subskrypcji lub region na taki, który może uzyskać dostęp do wymaganych funkcji.

### <a name="error-vmextensionprovisioningerror"></a>Błąd: VMExtensionProvisioningError

![Zrzut ekranu przedstawiający niepowodzenie wdrożenia z nie powiodło się ze stanem inicjowania obsługi administracyjnej terminali.](media/7aaf15615309c18a984673be73ac969a.png)

**Przyczyna 1:** Błąd przejściowy w środowisku pulpitu wirtualnego systemu Windows.

**Przyczyna 2:** Błąd przejściowy z połączeniem.

**Poprawka:** Upewnij się, że środowisko pulpitu wirtualnego systemu Windows jest w dobrej kondycji, logując się przy użyciu programu PowerShell. Ręczne zakończenie rejestracji maszyny wirtualnej w [programie Utwórz pulę hostów za pomocą programu PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Błąd: określona nazwa administratora jest niedozwolona

![Zrzut ekranu wdrożenia nie powiódł się, w którym określony administrator jest niedozwolony.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Przykład nieprzetworzonego błędu:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Przyczyna:** Podane hasło zawiera zakazane podciągi (admin, administrator, katalog główny).

**Poprawka:** Zaktualizuj nazwę użytkownika lub użyj różnych użytkowników.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Błąd: maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia

![Zrzut ekranu przedstawiający operację zasobu ukończoną ze stanem inicjowania obsługi administracyjnej terminali w umieszczeniu nie powiodło się.](media/49c4a1836a55d91cd65125cf227f411f.png)

Przykład nieprzetworzonego błędu:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie było w stanie uzyskać dostępu administratora na maszynie wirtualnej.

**Poprawka:** Potwierdź nazwę użytkownika i hasło mają dostęp administracyjny na maszynie wirtualnej i ponownie uruchom szablon usługi Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Błąd: DeploymentFailed — konfiguracja programu PowerShell DSC "FirstSessionHost" została ukończona z błędami

![Zrzut ekranu przedstawiający niepowodzenie wdrażania w konfiguracji programu PowerShell DSC "FirstSessionHost" został ukończony z błędami.](media/64870370bcbe1286906f34cf0a8646ab.png)

Przykład nieprzetworzonego błędu:

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

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie było w stanie uzyskać dostępu administratora na maszynie wirtualnej.

**Poprawka:** Potwierdź nazwę użytkownika i hasło pod warunkiem mają dostęp administracyjny na maszynie wirtualnej i ponownie uruchom szablon usługi Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: DeploymentFailed — InvalidResourceReference

Przykład nieprzetworzonego błędu:

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

**Przyczyna:** Część nazwy grupy zasobów jest używana dla niektórych zasobów tworzonych przez szablon. Ze względu na nazwę pasującą do istniejących zasobów szablon może wybrać istniejący zasób z innej grupy.

**Poprawka:** Podczas uruchamiania szablonu usługi Azure Resource Manager w celu wdrożenia maszyn wirtualnych hosta sesji, należy wprowadzić dwa pierwsze znaki unikatowe dla nazwy grupy zasobów subskrypcji.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: DeploymentFailed — InvalidResourceReference

Przykład nieprzetworzonego błędu:

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

**Przyczyna:** Ten błąd jest, ponieważ karta sieciowa utworzona za pomocą szablonu usługi Azure Resource Manager ma taką samą nazwę jak inna karta sieciowa już w sieci wirtualnej.

**Poprawka:** Użyj innego prefiksu hosta.

### <a name="error-deploymentfailed--error-downloading"></a>Błąd: DeploymentFailed — błąd podczas pobierania

Przykład nieprzetworzonego błędu:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Przyczyna:** Ten błąd jest spowodowany marszrutą statyczną, regułą zapory lub siecią sieciową blokującą pobieranie pliku zip powiązanego z szablonem usługi Azure Resource Manager.

**Poprawka:** Usuń blokowanie trasy statycznej, reguły zapory lub sieciowej sieciowej. Opcjonalnie otwórz plik json szablonu usługi Azure Resource Manager w edytorze tekstu, zabierz łącze do pliku zip i pobierz zasób do dozwolonej lokalizacji.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: użytkownik nie jest upoważniony do wykonywania zapytań do usługi zarządzania

Przykład nieprzetworzonego błędu:

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

**Przyczyna:** Określony administrator dzierżawy pulpitu wirtualnego systemu Windows nie ma prawidłowego przypisania roli.

**Poprawka:** Użytkownik, który utworzył dzierżawę pulpitu wirtualnego systemu Windows, musi zalogować się do programu Windows Virtual Desktop PowerShell i przypisać użytkownikowi próbę przypisania roli. Jeśli używasz parametrów szablonu usługi GitHub Azure Resource Manager, postępuj zgodnie z tymi instrukcjami za pomocą poleceń programu PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Błąd: użytkownik wymaga uwierzytelniania wieloskładnikowego usługi Azure (MFA)

![Zrzut ekranu przedstawiający wdrożenie nie powiódł się z powodu braku uwierzytelniania wieloskładnikowego (MFA)](media/MFARequiredError.png)

Przykład nieprzetworzonego błędu:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Przyczyna:** Określony administrator dzierżawy pulpitu wirtualnego systemu Windows wymaga uwierzytelniania wieloskładnikowego platformy Azure (MFA) do zalogowania się.

**Poprawka:** Utwórz jednostkę usługi i przypisz mu rolę dla dzierżawy pulpitu wirtualnego systemu Windows, wykonując kroki opisane w [samouczku: Tworzenie podmiotów usługi i przypisań ról za pomocą programu PowerShell](create-service-principal-role-powershell.md). Po sprawdzeniu, czy można zalogować się do pulpitu wirtualnego systemu Windows za pomocą jednostki usługi, uruchom ponownie ofertę portalu Azure Marketplace lub szablon Usługi Azure Resource Manager, w zależności od używanej metody. Postępuj zgodnie z poniższymi instrukcjami, aby wprowadzić poprawne parametry dla swojej metody.

Jeśli korzystasz z oferty portalu Azure Marketplace, podaj wartości następujących parametrów, które mają być poprawnie uwierzytelnione na pulpicie wirtualnym systemu Windows:

- Właściciel dzierżawy pulpitu wirtualnego systemu Windows RDS: główny podmiot usługi
- Identyfikator aplikacji: Identyfikacja aplikacji nowego podmiotu usługi, który został utworzony
- Hasło/Potwierdzenie hasła: klucz tajny hasła wygenerowany dla jednostki usługi
- Identyfikator dzierżawy usługi Azure AD: identyfikator dzierżawy usługi Azure AD podmiotu zabezpieczeń usługi, który został utworzony

Jeśli korzystasz z szablonu Usługi Azure Resource Manager usługi GitHub, podaj wartości następujących parametrów, które mają być poprawnie uwierzytelnione na pulpicie wirtualnym systemu Windows:

- Główna nazwa użytkownika administratora dzierżawy (UPN) lub identyfikator aplikacji: Identyfikacja aplikacji nowego podmiotu usługi, który został utworzony
- Hasło administratora dzierżawy: klucz tajny hasła wygenerowany dla jednostki usługi
- IsServicePrincipal: **true**
- AadTenantId: Identyfikator dzierżawy usługi Azure AD jednostki usługi, który został utworzony

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klientów pulpitu wirtualnego systemu Windows, zobacz [Połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z klientami usług pulpitu zdalnego, zobacz [Rozwiązywanie problemów z klientem pulpitu zdalnego](troubleshoot-client.md)
- Aby rozwiązać problemy podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Pulpit wirtualny systemu Windows PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej o usłudze, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach w celu określenia błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
