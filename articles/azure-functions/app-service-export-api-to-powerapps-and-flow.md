---
title: Eksportowanie interfejs API hostowanymi na platformie Azure do PowerApps i przepływów Microsoft | Dokumentacja firmy Microsoft
description: Omówienie sposobu uwidacznia interfejs API hostowany w usłudze App Service w rozwiązaniu PowerApps i Flow firmy Microsoft
services: app-service
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: ef3fe5002a28c66478a10909a7e9556449cd9712
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234771"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Eksportowanie interfejs API hostowanymi na platformie Azure do PowerApps i przepływów firmy Microsoft

[Rozwiązanie PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) to usługa do tworzenia i używania aplikacji biznesowych niestandardowych, Połącz z danymi, które działają na różnych platformach. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) można łatwo automatyzować przepływy pracy i procesy biznesowe między ulubionych aplikacji i usług. Zarówno PowerApps i Microsoft Flow wyposażone w szereg łączników do źródeł danych, takich jak usługi Office 365, Dynamics 365 Salesforce i więcej. W niektórych przypadkach konstruktorów aplikacji i przepływ również chcesz się połączyć źródła danych i interfejsów API utworzony przez organizację.

Podobnie deweloperów, które mają być ujawnia swoje interfejsy API szerzej w organizacji można udostępnić ich interfejsów API do aplikacji i przepływ konstruktorów. W tym temacie przedstawiono sposób eksportowania skompilowanej za pomocą interfejsu API [usługi Azure Functions](../azure-functions/functions-overview.md) lub [usłudze Azure App Service](../app-service/app-service-web-overview.md). Staje się wyeksportowanego interfejsu API *niestandardowy łącznik*, która jest używana w rozwiązaniu PowerApps i Microsoft Flow podobnie jak wbudowanego łącznika.

## <a name="create-and-export-an-api-definition"></a>Tworzenie i eksportowanie definicji interfejsu API
Przed wyeksportowaniem interfejsu API, musi zawierać opis interfejsu API przy użyciu definicji OpenAPI (wcześniej znane jako [Swagger](http://swagger.io/) pliku). Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API. PowerApps i Microsoft Flow można tworzyć niestandardowe łączniki dla dowolnego definicji OpenAPI 2.0. Azure funkcje i usługi Azure App Service ma wbudowaną obsługę tworzenia, obsługi i zarządzania OpenAPI definicje. Aby uzyskać więcej informacji, zobacz [hosta interfejsu API RESTful z CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Można również tworzyć niestandardowe łączników w rozwiązaniu PowerApps i interfejsu użytkownika przepływ firmy Microsoft, bez korzystania z definicji OpenAPI. Aby uzyskać więcej informacji, zobacz [rejestru i użyj niestandardowego łącznika (rozwiązanie PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) i [rejestru i użyj niestandardowego łącznika (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Aby wyeksportować definicji interfejsu API, wykonaj następujące kroki:

1. W [portalu Azure](https://portal.azure.com), przejdź do funkcji Azure lub inną aplikację usługi aplikacji.

    Jeśli przy użyciu usługi Azure Functions, wybierz aplikację funkcji, wybierz **funkcji platformy**, a następnie **definicji interfejsu API**.

    ![Definicja funkcji interfejsu API platformy Azure](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Jeśli przy użyciu usługi Azure App Service, wybierz **definicji interfejsu API** na liście ustawień.

    ![Definicja interfejsu API usługi aplikacji](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **Wyeksportować do rozwiązania PowerApps + Microsoft Flow** przycisk powinien być dostępny (Jeśli nie, należy najpierw utworzyć definicję OpenAPI). Kliknij ten przycisk, aby rozpocząć proces eksportu.

    ![Eksportowanie do rozwiązania PowerApps + Microsoft Flow przycisku](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Wybierz **tryb eksportu**:

    **Express** umożliwia tworzenie niestandardowych łącznik z portalu Azure. Wymaga to jest zarejestrowany w rozwiązaniu PowerApps lub Flow firmy Microsoft i mieć uprawnienie do tworzenia łączników w środowisku docelowym. Jest to zalecane podejście spełnieniu tych dwóch wymagań. Jeśli ten tryb jest używany, wykonaj [używać funkcji eksportowania express](#express) poniższe instrukcje.

    **Ręczne** umożliwia eksportowanie definicji interfejsu API, która będzie następnie zaimportować przy użyciu portali rozwiązania PowerApps lub Flow firmy Microsoft. Jest to zalecane podejście, jeśli użytkowników platformy Azure i użytkownik z uprawnieniami do tworzenia łączników są różne osoby, lub Jeśli łącznik musi zostać utworzona w innej dzierżawie usługi Azure. Jeśli ten tryb jest używany, wykonaj [używać funkcji eksportowania ręczne](#manual) poniższe instrukcje.

    ![Tryb eksportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Niestandardowy łącznik korzysta z *kopiowania* definicji interfejsu API, więc PowerApps i Microsoft Flow nie będą od razu wiedzieć, wprowadzania zmian do aplikacji, a jego definicja interfejsu API. Jeśli wprowadzisz zmiany, powtórz kroki eksportu dla nowej wersji.

<a name="express"></a>
## <a name="use-express-export"></a>Używać funkcji eksportowania express

Aby zakończyć eksportowania w **Express** tryb, wykonaj następujące kroki:

1. Upewnij się, że zalogowano Cię do dzierżawy rozwiązania PowerApps lub Flow firmy Microsoft, do którego chcesz wyeksportować. 

2. Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Środowisko**|Wybierz środowisko, które mają być zapisywane łącznik niestandardowy. Aby uzyskać więcej informacji, zobacz [Przegląd środowisk](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę, która PowerApps i Microsoft Flow konstruktorów zostanie wyświetlony na liście łącznika.|
    |**Przygotowanie konfiguracji zabezpieczeń**|Jeśli to konieczne, podaj szczegóły konfiguracji zabezpieczeń, konieczne jest zezwolenie użytkownikom na dostęp do interfejsu API. Ten przykład przedstawia klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.|
 
    ![Express Eksport do PowerApps i Flow firmy Microsoft](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kliknij przycisk **OK**. Łącznik niestandardowy jest teraz utworzony i dodać do określonego środowiska.

Przykłady użycia **Express** tryb z usługi Azure Functions, zobacz [wywołać funkcję w rozwiązaniu PowerApps](functions-powerapps-scenario.md) i [wywoływanie funkcji z Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Używać funkcji eksportowania ręczne

Aby zakończyć eksportowania w **ręcznego** tryb, wykonaj następujące kroki:

1. Kliknij przycisk **Pobierz** i Zapisz plik, lub kliknij przycisk Kopiuj i Zapisz adres URL. Podczas importowania użyje pobierany plik lub adres URL.
 
    ![Ręczne Eksport do PowerApps i Flow firmy Microsoft](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Jeśli definicja interfejsu API zawiera żadnych definicji zabezpieczeń, są one nazywane w kroku #2. Podczas importowania PowerApps i Microsoft Flow wykrywa te i monit o podanie informacji o zabezpieczeniach. Zbierz poświadczenia związane z każdej definicji do użycia w następnej sekcji. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.

    ![Zabezpieczenia ręczne eksportu](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ten przykład przedstawia definicję klucza zabezpieczeń interfejsu API, który został uwzględniony w definicji OpenAPI.

Teraz, gdy została wyeksportowana definicja interfejsu API, zaimportuj go w celu utworzenia łącznika niestandardowego w rozwiązaniu PowerApps i Flow firmy Microsoft. Niestandardowe łączników są współużytkowane przez dwie usługi, więc musisz zaimportować definicję raz.

Aby zaimportować definicję interfejsu API do PowerApps i Flow firmy Microsoft, wykonaj następujące kroki:

1. Przejdź do strony [powerapps.com](https://web.powerapps.com) lub [flow.microsoft.com](https://flow.microsoft.com).

2. W prawym górnym rogu kliknij koło zębate ikonę, a następnie kliknij przycisk **łączniki niestandardowe**.

   ![Ikona koła zębatego w usłudze](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kliknij przycisk **Tworzenie niestandardowego łącznika**, następnie kliknij przycisk **zaimportować definicję OpenAPI**.

   ![Tworzenie łącznika niestandardowego](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Wprowadź nazwę dla niestandardowego łącznika, a następnie przejdź do definicji OpenAPI, który został wyeksportowany, a następnie kliknij przycisk **Kontynuuj**.

   ![Przekaż OpenAPI definicji](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na **ogólne** karcie, przejrzyj informacje, które pochodzą od definicji OpenAPI.

5. Na **zabezpieczeń** karcie, jeśli zostanie wyświetlony monit, aby podać szczegóły okna uwierzytelnianie, wprowadź wartości odpowiednich dla typu uwierzytelniania. Kliknij przycisk **kontynuować**.

    ![Karta Zabezpieczenia](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ten przykład przedstawia wymagane pola dla uwierzytelniania opartego na kluczu interfejsu API. Pola są różne w zależności od typu uwierzytelniania.

6. Na **definicje** , wszystkie operacje określone w pliku OpenAPI są wypełniane automatycznie. Jeśli wszystkie wymagane operacje są zdefiniowane, można przejść do następnego kroku. Jeśli nie, można dodawać i modyfikować tutaj operacji.

    ![Karta definicje](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    W tym przykładzie ma jedną operację o nazwie `CalculateCosts`. Metadane, takiej jak **opis**, wszystkie pochodzi z pliku OpenAPI.

7. Kliknij przycisk **utworzyć łącznik** w górnej części strony.

Teraz można podłączyć do niestandardowego łącznika w rozwiązaniu PowerApps i Flow firmy Microsoft. Aby uzyskać więcej informacji na temat tworzenia łączników w portalach PowerApps i Flow firmy Microsoft, zobacz [zarejestrować łącznik niestandardowy (rozwiązanie PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) i [zarejestrować łącznik niestandardowy (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Określanie typu uwierzytelniania

PowerApps i Flow firmy Microsoft obsługuje kolekcja dostawców tożsamości, zapewniające uwierzytelniania dla łączników niestandardowych. Jeśli Twój interfejs API wymaga uwierzytelnienia, upewnij się, że jest przechwytywany jako _definicji zabezpieczeń_ w dokumencie OpenAPI, jak w następującym przykładzie:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Podczas eksportowania musisz podać wartości konfiguracji, które umożliwiają PowerApps i Flow firmy Microsoft w celu uwierzytelniania użytkowników.

W tej sekcji opisano typy uwierzytelniania, które są obsługiwane w **Express** tryb: klucz interfejsu API, Azure Active Directory i rodzajowy OAuth 2.0. PowerApps i Microsoft Flow obsługują także podstawowego uwierzytelniania i protokołu OAuth 2.0 do określonych usług, takich jak Dropbox, Facebook i SalesForce.

### <a name="api-key"></a>Klucz interfejsu API
Przy użyciu klucza interfejsu API, użytkownicy łącznika są monit o podanie klucza podczas tworzenia połączenia. Należy określić nazwę klucza interfejsu API aby pomóc zrozumieć klucz, do którego jest potrzebny. W przypadku wcześniejszego przykładu używamy nazwy `API Key (contact meganb@contoso.com)` tak osób wiedzieć, gdzie można uzyskać informacje na temat klucza interfejsu API. Dla usługi Azure Functions klucz jest zwykle jeden z kluczy hosta, obejmujące kilka funkcji w funkcji aplikacji.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Podczas korzystania z usługi Azure AD, należy dwóch rejestracji aplikacji usługi Azure AD: jeden dla samego interfejsu API i drugi dla łącznika niestandardowego:

- Aby skonfigurować rejestrację dla interfejsu API, użyj [uwierzytelniania/autoryzacji dla aplikacji usługi](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funkcji.

- Aby skonfigurować rejestrację dla łącznika, postępuj zgodnie z instrukcjami [Dodawanie aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). Rejestracja musi mieć delegowane dostęp do interfejsu API i adres URL odpowiedzi `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Aby uzyskać więcej informacji, zobacz przykłady rejestracji usługi Azure AD [rozwiązania PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) i [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Te przykłady użycia usługi Azure Resource Manager jako interfejsu API; Wykonaj kroki, należy go zastąpić interfejsu API.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta łącznika usługi Azure AD rejestracji
- **Klucz tajny klienta** — klucz tajny klienta łącznika usługi Azure AD rejestracji
- **Adres URL logowania** — podstawowy adres URL dla usługi Azure AD. Na platformie Azure, jest to zazwyczaj `https://login.windows.net`.
- **Identyfikator dzierżawy** — identyfikator dzierżawcy do użycia dla danych logowania. Powinno to być "typowych" lub identyfikator dzierżawy, w której jest utworzony łącznik.
- **Adres URL zasobu** — adres URL zasobu do interfejsu API rejestracji usługi Azure AD

> [!IMPORTANT]
> Jeśli ktoś zaimportuje definicji interfejsu API do PowerApps i Microsoft Flow jako część przepływu ręczne, należy podać je z Identyfikatorem klienta i klucz tajny klienta *Rejestracja łącznika*, a także adres URL zasobu interfejsu API. Upewnij się, że tych kluczy tajnych są zarządzane w bezpieczny sposób. **Nie udostępniaj poświadczenia zabezpieczeń samego interfejsu API.**

### <a name="generic-oauth-20"></a>Ogólne uwierzytelnianie OAuth 2.0
Korzystając z ogólnym OAuth 2.0, można zintegrować z każdego dostawcy OAuth 2.0. Dzięki temu można pracować przy użyciu dostawców niestandardowych, które nie są obsługiwane natywnie.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta OAuth 2.0
- **Klucz tajny klienta** — klucz tajny klienta OAuth 2.0
- **Adres URL autoryzacji** -adresu URL autoryzacji OAuth 2.0
- **Adres URL token** — adres URL tokenu OAuth 2.0
- **Odśwież adres URL** — adres URL odświeżania OAuth 2.0


