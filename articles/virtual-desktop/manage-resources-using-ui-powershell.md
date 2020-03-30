---
title: Wdrażanie narzędzia do zarządzania pulpitem wirtualnym systemu Windows przy użyciu jednostki usługi — Azure
description: Jak wdrożyć narzędzie do zarządzania dla pulpitu wirtualnego systemu Windows przy użyciu programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127795"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Wdrażanie narzędzia do zarządzania za pomocą programu PowerShell

W tym artykule pokazano, jak wdrożyć narzędzie do zarządzania przy użyciu programu PowerShell.

## <a name="important-considerations"></a>Istotne zagadnienia

Subskrypcja dzierżawy usługi Azure Active Directory (Azure AD) wymaga własnego oddzielnego wdrożenia narzędzia do zarządzania. To narzędzie nie obsługuje scenariuszy usługi Azure AD Business-to-Business (B2B). 

To narzędzie do zarządzania jest próbką. Firma Microsoft zapewni ważne aktualizacje zabezpieczeń i jakości. [Kod źródłowy jest dostępny w usłudze GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Niezależnie od tego, czy jesteś klientem, czy partnerem, zachęcamy do dostosowania narzędzia do potrzeb biznesowych.

Następujące przeglądarki są zgodne z narzędziem do zarządzania:

- Google Chrome 68 lub nowsze
- Microsoft Edge 40.15063 lub nowsze
- Mozilla Firefox 52.0 lub nowsza
- Safari 10 lub nowsze (tylko macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co jest potrzebne do wdrożenia narzędzia do zarządzania

Przed wdrożeniem narzędzia do zarządzania, trzeba użytkownika usługi Azure Active Directory (Azure AD) do utworzenia rejestracji aplikacji i wdrożenia interfejsu użytkownika zarządzania. Ten użytkownik musi:

- Mieć uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Mieć uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma wymagane uprawnienia, postępując zgodnie z instrukcjami w [Required permissions](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Aby pomyślnie wdrożyć i skonfigurować narzędzie do zarządzania, należy najpierw pobrać następujące skrypty programu PowerShell z [repozytorium GitHub szablonów usług pulpitu zdalnego](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) i zapisać je w tym samym folderze na komputerze lokalnym.

  - tworzenieWvdMgmtUxAppRegistration.ps1
  - aktualizacjaWvdMgmtUxApiUrl.ps1

Po wdrożeniu i skonfigurowaniu narzędzia do zarządzania zaleca się poprosić użytkownika o uruchomienie interfejsu użytkownika zarządzania, aby upewnić się, że wszystko działa. Użytkownik uruchamiany interfejsu użytkownika zarządzania musi mieć przypisanie roli, które umożliwia mu wyświetlanie lub edytowanie dzierżawy pulpitu wirtualnego systemu Windows.

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell

Rozpocznij, logując się do modułów Az i Azure AD PowerShell. Aby się zalogować:

1. Otwórz program PowerShell jako administrator i przejdź do katalogu, w którym zostały zapisane skrypty programu PowerShell.
2. Zaloguj się na platformę Azure przy użyciu konta, które ma uprawnienia właściciela lub współautora w ramach subskrypcji platformy Azure, której zamierzasz użyć do utworzenia narzędzia do zarządzania, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Uruchom następujące polecenie cmdlet, aby zalogować się do usługi Azure AD przy użyciu tego samego konta, które jest używane dla modułu Programu PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Następnie przejdź do folderu, w którym zostały zapisane dwa skrypty programu PowerShell z repozytorium GitHub szablonów usług PULPITU ZDALNEGO.

Zachowaj okno programu PowerShell używane do logowania się otwarte, aby uruchomić dodatkowe polecenia cmdlet programu PowerShell po zalogowaniu się.

## <a name="create-an-azure-active-directory-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure Active Directory

Uruchom następujące polecenia, aby utworzyć rejestrację aplikacji z wymaganymi uprawnieniami interfejsu API:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Po zakończeniu rejestracji aplikacji usługi Azure AD można wdrożyć narzędzie do zarządzania.

## <a name="deploy-the-management-tool"></a>Wdrażanie narzędzia do zarządzania

Uruchom następujące polecenia programu PowerShell, aby wdrożyć narzędzie do zarządzania i skojarzyć go z jednostką usługi, którą właśnie utworzono:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Po utworzeniu aplikacji sieci web należy dodać identyfikator URI przekierowania do aplikacji usługi Azure AD, aby pomyślnie zalogować się do użytkowników.

## <a name="set-the-redirect-uri"></a>Ustawianie identyfikatora URI przekierowania

Uruchom następujące polecenia programu PowerShell, aby pobrać adres URL aplikacji internetowej i ustawić go jako identyfikator URI przekierowania uwierzytelniania (nazywany także adresem URL odpowiedzi):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Po dodaniu identyfikatora URI przekierowania należy zaktualizować adres URL interfejsu API, aby narzędzie do zarządzania mogły wchodzić w interakcje z usługą zaplecza interfejsu API.

## <a name="update-the-api-url-for-the-web-application"></a>Aktualizowanie adresu URL interfejsu API dla aplikacji sieci Web

Uruchom następujący skrypt, aby zaktualizować konfigurację adresu URL interfejsu API w interfejsie aplikacji sieci web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teraz, gdy masz w pełni skonfigurowane narzędzie do zarządzania aplikacji sieci web, nadszedł czas, aby zweryfikować aplikację usługi Azure AD i wyrazić zgodę.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Weryfikowanie aplikacji usługi Azure AD i udzielanie zgody

Aby zweryfikować konfigurację aplikacji usługi Azure AD i udzielić zgody:

1. Otwórz przeglądarkę internetową i zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą konta administracyjnego.
2. Na pasku wyszukiwania u góry witryny Azure portal wyszukaj **rejestracje aplikacji** i wybierz element w obszarze **Usługi**.
3. Wybierz **wszystkie aplikacje** i wyszukaj unikatową nazwę aplikacji podana dla skryptu programu PowerShell w [obszarze Tworzenie rejestracji aplikacji usługi Azure Active Directory](#create-an-azure-active-directory-app-registration).
4. W panelu po lewej stronie przeglądarki wybierz **pozycję Uwierzytelnianie** i upewnij się, że identyfikator URI przekierowania jest taki sam jak adres URL aplikacji sieci web dla narzędzia do zarządzania, jak pokazano na poniższej ilustracji.
   
   [![Strona uwierzytelniania z wprowadzonym](media/management-ui-redirect-uri-inline.png) identyfikatorem URI przekierowania](media/management-ui-redirect-uri-expanded.png#lightbox)

5. W lewym panelu wybierz **uprawnienia interfejsu API,** aby potwierdzić, że uprawnienia zostały dodane. Jeśli jesteś administratorem globalnym, wybierz przycisk **Udziel zgody `tenantname` administratora** i postępuj zgodnie z monitami o okno dialogowe, aby udzielić zgody administratora w organizacji.
    
    [![Strona](media/management-ui-permissions-inline.png) Uprawnień interfejsu API](media/management-ui-permissions-expanded.png#lightbox)

Teraz można rozpocząć korzystanie z narzędzia do zarządzania.

## <a name="use-the-management-tool"></a>Korzystanie z narzędzia do zarządzania

Teraz, gdy masz skonfigurowane narzędzie do zarządzania w dowolnym momencie, możesz go uruchomić w dowolnym miejscu i czasie. Oto jak uruchomić narzędzie:

1. Otwórz adres URL aplikacji internetowej w przeglądarce internetowej. Jeśli nie pamiętasz adresu URL, możesz zalogować się na platformie Azure, znaleźć usługę aplikacji wdrożoną dla narzędzia do zarządzania, a następnie wybrać adres URL.
2. Zaloguj się przy użyciu poświadczeń pulpitu wirtualnego systemu Windows.
   
   > [!NOTE]
   > Jeśli podczas konfigurowania narzędzia do zarządzania nie udzielono zgody administratora, każdy użytkownik, który się zaloguje, będzie musiał wyrazić zgodę użytkownika, aby móc korzystać z tego narzędzia.

3. Po wyświetleniu monitu o wybranie grupy dzierżawy wybierz z listy rozwijanej pozycję **Domyślna grupa dzierżawców.**
4. Po wybraniu **domyślnej grupy dzierżawy**po lewej stronie okna powinno pojawić się menu. W tym menu znajdź nazwę grupy dzierżawy i wybierz ją.
   
   > [!NOTE]
   > Jeśli masz niestandardową grupę dzierżawy, wprowadź ją ręcznie, zamiast wybierać ją z listy rozwijanej.

## <a name="report-issues"></a>Zgłaszanie problemów

Jeśli natkniesz się na jakiekolwiek problemy z narzędziem do zarządzania lub innymi narzędziami pulpitu wirtualnego systemu Windows, postępuj zgodnie ze [wskazówkami w szablonach usługi Azure Resource Manager dla usług pulpitu zdalnego,](https://github.com/Azure/RDS-Templates/blob/master/README.md) aby zgłosić je w usłudze GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak wdrożyć i połączyć się z narzędziem do zarządzania, możesz dowiedzieć się, jak używać usługi Azure Service Health do monitorowania problemów z usługą i poradatoriów kondycji. Aby dowiedzieć się więcej, zobacz nasz [samouczek Konfigurowanie alertów usługi](./set-up-service-alerts.md).
