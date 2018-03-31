---
title: Uwierzytelniania za pomocą interfejsów API REST usługi Mobile Engagement
description: Opisuje sposób uwierzytelniania za pomocą interfejsów API REST usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Uwierzytelniania za pomocą interfejsów API REST usługi Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

## <a name="overview"></a>Przegląd

Ten dokument zawiera opis sposobu pobrać tokenu uwierzytelniania za pomocą interfejsów API REST Mobile Engagement prawidłowy OAuth usługi Azure Active Directory (Azure AD).

W tej procedurze założono, że masz ważnej subskrypcji platformy Azure i utworzono aplikację usługi Mobile Engagement przy użyciu jednej z [samouczki developer](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

Token OAuth na podstawie Microsoft Azure Active Directory jest używany do uwierzytelniania. 

W celu uwierzytelnienia żądania interfejsu API nagłówek autoryzacji musi można dodać do każdego żądania. Nagłówek autoryzacji ma następujący format:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory tokeny wygaśnie w ciągu godziny.
> 
> 

Istnieje kilka sposobów w celu pobrania tokenu. Ponieważ interfejsy API są nazywane z usługi w chmurze, chcesz użyć klucza interfejsu API. Klucz interfejsu API w terminologii Azure jest nazywany hasła nazwy głównej usługi. W poniższej procedurze opisano jeden ze sposobów skonfigurować ją ręcznie.

### <a name="one-time-setup-using-a-script"></a>Jednorazowej konfiguracji (za pomocą skryptu)

Aby przeprowadzić instalację za pomocą skryptu programu PowerShell, należy wykonać czynności w poniższych instrukcjach. Skrypt programu PowerShell wymaga co najmniej ilość czasu instalacji, ale używa najbardziej dopuszczalnej wartości domyślne. 

Opcjonalnie można również wykonać instrukcje w [instalacji ręcznej](mobile-engagement-api-authentication-manual.md) dla bezpośrednio w ten sposób w portalu Azure. Podczas konfigurowania z portalu Azure, możesz to zrobić bardziej szczegółową konfigurację.

1. Pobierz najnowszą wersję programu Azure PowerShell przez [ją pobrać](http://aka.ms/webpi-azps). Aby uzyskać więcej informacji na temat instrukcje pobierania, zobacz [ten przegląd](/powershell/azure/overview).

2. Po zainstalowaniu programu PowerShell, użyj następujących poleceń, aby upewnić się, że masz **modułu Azure** zainstalowane:

    a. Upewnij się, że moduł Azure PowerShell jest dostępny na liście dostępnych modułów.

        Get-Module –ListAvailable

    ![Dostępne moduły Azure][1]

    b. Jeśli nie możesz znaleźć modułu Azure PowerShell na poprzedniej liście, należy uruchomić:

        Import-Module Azure
3. Zaloguj się do usługi Azure Resource Manager z programu PowerShell, uruchamiając następujące polecenie. Podaj nazwę użytkownika i hasło dla konta platformy Azure: 

        Login-AzureRmAccount
4. Jeśli masz wiele subskrypcji, należy wykonać następujące czynności:

    a. Pobranie listy wszystkich subskrypcji. Następnie skopiuj **SubscriptionId** subskrypcji, która ma być używany. Upewnij się, że ta subskrypcja ma aplikację usługi Mobile Engagement. Zamierzasz korzystać z tej aplikacji na interakcję z interfejsów API. 

        Get-AzureRmSubscription

    b. Uruchom następujące polecenie. Podaj **SubscriptionId** do konfigurowania subskrypcji, która ma być używana:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Skopiuj tekst [AzureRmServicePrincipalOwner.ps1 nowy](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) skryptu na komputerze lokalnym. Następnie zapisz go jako polecenia cmdlet programu PowerShell (na przykład `APIAuth.ps1`), a następnie uruchom je.

         `.\APIAuth.ps1`.

6. Skrypt zostanie wyświetlona prośba o wprowadzenie danych dla **principalName**. Podaj odpowiednią nazwę, która ma być używany dla aplikacji usługi Active Directory (na przykład APIAuth). 

7. Po zakończeniu skrypt wyświetla następujące cztery wartości. Pamiętaj skopiować je, ponieważ potrzebne do uwierzytelnienia programowo z usługą Active Directory: 

   - **TenantId**
   - **Identyfikator subskrypcji**
   - **Identyfikator aplikacji**
   - **Wpis tajny**

   Użyj identyfikatora dzierżawcy jako `{TENANT_ID}`, identyfikator aplikacji jako `{CLIENT_ID}` i tajne jako `{CLIENT_SECRET}`.

   > [!NOTE]
   > Domyślne zasady zabezpieczeń może spowodować zablokowanie dostępu z uruchomionych skryptów PowerShell. Jeśli tak, użyj następującego polecenia, aby tymczasowo skonfigurować zasady wykonywania, aby pozwolić na wykonanie skryptu:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Oto, jak wygląda zestaw poleceń cmdlet programu PowerShell.
    ![Polecenia cmdlet programu PowerShell][3]
9. W portalu Azure, przejdź do usługi Active Directory, wybierz **rejestracji aplikacji**, a następnie wyszukaj aplikację, aby zapewnić, że istnieje.
    ![Wyszukaj aplikację][4]

### <a name="steps-to-get-a-valid-token"></a>Kroki, aby pobrać prawidłowego tokenu

1. Wywołanie interfejsu API z następującymi parametrami. Upewnij się zastąpić **dzierżawy\_identyfikator**, **klienta\_identyfikator**, i **klienta\_klucz TAJNY**:
   
   * **Adres URL żądania** jako `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **Nagłówek HTTP Content-Type** jako `application/x-www-form-urlencoded`
   
   * **Treść żądania HTTP** jako `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Poniżej zamieszczono przykładowe żądanie:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Poniżej przedstawiono przykład odpowiedzi:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     W tym przykładzie dołączono kodowania adresów URL parametrów POST, w którym `resource` wartość jest rzeczywiście `https://management.core.windows.net/`. Należy zachować ostrożność również kodowania adresu URL `{CLIENT_SECRET}`, ponieważ może ona zawierać znaków specjalnych.

     > [!NOTE]
     > Do testowania, możesz użyć narzędzia klienta HTTP takich jak [Fiddler](http://www.telerik.com/fiddler) lub [Chrome Postman rozszerzenia](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. W każdym wywołaniu interfejsu API zawierają teraz nagłówek żądania autoryzacji:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Jeśli żądanie zwróci kod stanu 401, sprawdź treść odpowiedzi. Go można stwierdzić, że token utracił ważność. W takim przypadku należy uzyskać nowy token.

## <a name="use-the-apis"></a>Korzystanie z interfejsów API
Teraz, gdy masz prawidłowy token, można przystąpić do wykonywania wywołań interfejsu API.

1. W poszczególnych żądań interfejsu API musisz przekazać token prawidłowych, niewygasłych. Użytkownik uzyskał token niewygasłe w poprzedniej sekcji.

2. Dołącz niektórych parametrów na żądanie identyfikatora URI, który identyfikuje aplikację. Żądanie identyfikatora URI wygląda podobnie do następującego kodu:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Aby uzyskać parametry, wybierz nazwę aplikacji. Następnie wybierz **pulpitu nawigacyjnego**. Strona z wszystkie trzy parametry, zostanie wyświetlona w następujący sposób:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** nazwy Twojej grupy zasobów ma być **MobileEngagement** chyba że zostaną utworzone nowe. 

> [!NOTE]
> Ignoruj adres głównego interfejsu API powodu dla poprzedniego interfejsów API.
> 
> Jeśli aplikacja została utworzona przy użyciu portalu Azure, należy użyć nazwy zasobu usługi Application, który jest inny niż nazwa aplikacji. Jeśli utworzono aplikację w portalu Azure, należy używać nazwy aplikacji. (Brak rozróżnienia między nazwa zasobów aplikacji i nazwy aplikacji dla aplikacji, które są tworzone w nowego portalu.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



