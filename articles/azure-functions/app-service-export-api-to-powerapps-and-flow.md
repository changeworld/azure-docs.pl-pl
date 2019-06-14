---
title: Eksportowanie interfejsu API hostowanych na platformie Azure do usług PowerApps i Microsoft Flow | Dokumentacja firmy Microsoft
description: Omówienie sposobu uwidaczniania interfejsu API hostowanego w usłudze App Service do usługi PowerApps i Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: 9f4bbf91b09abeb917fd9f49482881e33bf788ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499638"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Eksportowanie interfejsu API hostowanych na platformie Azure do usług PowerApps i Microsoft Flow

[Usługa PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) to usługa do tworzenia i używania niestandardowych aplikacji biznesowych łączenie z danymi, które działają na różnych platformach. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) ułatwia Automatyzowanie przepływów pracy i procesów biznesowych między ulubionymi aplikacjami i usługami. Usługa PowerApps i Microsoft Flow są dostarczane z szereg wbudowanych łączników do źródeł danych, takich jak usługi Office 365, Dynamics 365, Salesforce i inne. W niektórych przypadkach twórcy aplikacji i przepływów również chcesz się połączyć z źródła danych i interfejsów API utworzonych przez jego organizację.

Podobnie deweloperów, które mają być swoje interfejsy API szerzej w organizacji można udostępnić swoje interfejsy API twórcy aplikacji i przepływów. W tym temacie pokazano, jak można wyeksportować interfejsu API utworzonych za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md) lub [usługi Azure App Service](../app-service/overview.md). Staje się wyeksportowanego interfejsu API *łącznika niestandardowego*, używany w usługach PowerApps i Microsoft Flow, podobnie jak wbudowanego łącznika.

## <a name="create-and-export-an-api-definition"></a>Tworzenie i eksportowanie definicji interfejsu API
Przed wyeksportowaniem interfejsu API, należy opisać interfejs API za pomocą definicji interfejsu OpenAPI (wcześniej znanej jako [Swagger](https://swagger.io/) pliku). Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API. Usługi PowerApps i Microsoft Flow można tworzyć łączniki niestandardowe dla dowolnego definicji interfejsu OpenAPI 2.0. Usługa Azure Functions i Azure App Service ma wbudowaną obsługę tworzenia, obsługi i zarządzania definicji interfejsu OpenAPI. Aby uzyskać więcej informacji, zobacz [hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Możesz także tworzyć łączniki niestandardowe w usłudze PowerApps i Microsoft Flow UI, bez korzystania z definicji interfejsu OpenAPI. Aby uzyskać więcej informacji, zobacz [rejestrowanie i używanie łącznika niestandardowego (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) i [rejestrowanie i używanie łącznika niestandardowego (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Aby wyeksportować definicję interfejsu API, wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do usługi Azure Functions lub inną aplikację usługi App Service.

    Jeśli używasz usługi Azure Functions, wybierz aplikację funkcji, wybierz pozycję **funkcje platformy**, a następnie **definicji interfejsu API**.

    ![Usługa Azure definicji interfejsu API funkcji](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    W przypadku korzystania z usługi Azure App Service, wybrać **definicji interfejsu API** na liście ustawień.

    ![Definicja interfejsu API usługi App Service](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **Eksportowanie do usługi PowerApps i Microsoft Flow** przycisk powinien być dostępny (Jeśli nie, musisz najpierw utworzyć definicję interfejsu OpenAPI). Kliknij ten przycisk, aby rozpocząć proces eksportowania.

    ![Eksportowanie do usługi PowerApps i Microsoft Flow przycisku](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Wybierz **tryb eksportu**:

    **Express** umożliwia tworzenie łącznika niestandardowego w witrynie Azure Portal. Wymaga zalogowano się do usługi PowerApps lub Microsoft Flow i masz uprawnienia do utworzenia łączników w środowisku docelowym. Jest to zalecane podejście, spełnieniu tych dwóch wymagań. Jeśli ten tryb jest używany, postępuj zgodnie z [stosowania express eksportu](#express) poniższymi instrukcjami.

    **Ręczne** umożliwia eksportowanie definicji interfejsu API, które można następnie zaimportować przy użyciu portali usługi PowerApps lub Microsoft Flow. Przedstawia zalecane podejście, czy użytkownika platformy Azure i użytkownika z uprawnieniami do tworzenia łączników są różne osoby, czy łącznik musi zostać utworzona w innej dzierżawie platformy Azure. Jeśli ten tryb jest używany, postępuj zgodnie z [wyeksportuj ręcznie](#manual) poniższymi instrukcjami.

    ![Tryb eksportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Łącznik niestandardowy używa *kopiowania* definicji interfejsu API, dzięki czemu usług PowerApps i Microsoft Flow nie będzie od razu wiedzieć, możesz wprowadzić zmiany do aplikacji, a jego definicja interfejsu API. W przypadku wprowadzenia zmian, powtórz kroki eksportowania dla nowej wersji.

<a name="express"></a>
## <a name="use-express-export"></a>Użyj express eksportu

Aby zakończyć eksportowanie w **Express** tryb, wykonaj następujące kroki:

1. Upewnij się, że zalogowano Cię do dzierżawy usługi PowerApps lub Microsoft Flow, do którego chcesz wyeksportować. 

2. Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Środowisko**|Wybierz środowisko, w którym łącznik niestandardowy powinien zostać zapisany. Aby uzyskać więcej informacji, zobacz [Przegląd środowisk](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę usługi PowerApps i Microsoft Flow, które Konstruktorzy zostanie wyświetlony na liście łącznika.|
    |**Przygotowywanie konfiguracji zabezpieczeń**|W razie potrzeby udostępnienia szczegółów konfiguracji zabezpieczeń, które są potrzebne, aby zezwolić użytkownikom na dostęp do interfejsu API. Ten przykład przedstawia klucza interfejsu API. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.|
 
    ![Express eksportu do usług PowerApps i Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kliknij przycisk **OK**. Łącznik niestandardowy jest teraz utworzone i dodane do środowiska, wskazana.

Aby uzyskać przykłady użycia **Express** trybu przy użyciu usługi Azure Functions, zobacz [wywoływanie funkcji z usługi PowerApps](functions-powerapps-scenario.md) i [wywoływanie funkcji z Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Wyeksportuj ręcznie

Aby zakończyć eksportowanie w **ręczne** tryb, wykonaj następujące kroki:

1. Kliknij przycisk **Pobierz** i Zapisz plik, lub kliknij przycisk kopiowania i Zapisz adres URL. Pobrany plik lub adres URL będzie używany podczas importowania.
 
    ![Ręcznie eksportować dane do usług PowerApps i Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Jeśli definicja interfejsu API zawiera wszystkie definicje zabezpieczeń, są one nazywane w kroku #2. Podczas importowania PowerApps i Microsoft Flow wykrywa te i monituje o podanie informacji o zabezpieczeniach. Zbierz poświadczenia związane z każda definicja do użycia w następnej sekcji. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.

    ![Zabezpieczenia ręcznie eksportować dane](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ten przykład przedstawia definicję zabezpieczeń klucza interfejsu API, która została uwzględniona w definicji interfejsu OpenAPI.

Teraz, gdy zostały wyeksportowane definicji interfejsu API, możesz zaimportować go, aby utworzyć łącznik niestandardowy w usłudze PowerApps i Microsoft Flow. Łączniki niestandardowe są udostępniane między dwoma usługami, więc trzeba zaimportować definicję jeden raz.

Aby zaimportować definicję interfejsu API do usług PowerApps i Microsoft Flow, wykonaj następujące kroki:

1. Przejdź do strony [powerapps.com](https://web.powerapps.com) lub [flow.microsoft.com](https://flow.microsoft.com).

2. W prawym górnym rogu kliknij ikonę koła zębatego, a następnie kliknij przycisk **łączniki niestandardowe**.

   ![Ikona koła zębatego w usłudze](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kliknij przycisk **Utwórz łącznik niestandardowy**, następnie kliknij przycisk **Importowanie definicji interfejsu OpenAPI**.

   ![Tworzenie łącznika niestandardowego](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Wprowadź nazwę łącznika niestandardowego, a następnie przejdź do definicji interfejsu OpenAPI, który został wyeksportowany, a następnie kliknij przycisk **Kontynuuj**.

   ![Przekazywanie definicji interfejsu OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na **ogólne** kartę, zapoznaj się z informacjami, który pochodzi z definicji interfejsu OpenAPI.

5. Na **zabezpieczeń** kartę, jeśli zostanie wyświetlony monit o podanie szczegółów uwierzytelniania, wprowadź wartości odpowiednich dla typu uwierzytelniania. Kliknij pozycję **Kontynuuj**.

    ![Karta Zabezpieczenia](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ten przykład przedstawia wymagane pola dla uwierzytelniania kluczem interfejsu API. Pola różnią się w zależności od typu uwierzytelniania.

6. Na **definicje** kartę, wszystkie operacje zdefiniowane w pliku OpenAPI są wypełniane automatycznie. Jeśli wszystkie wymagane operacje są zdefiniowane, możesz przejść do następnego kroku. W przeciwnym razie możesz dodawać i modyfikować operacji w tym miejscu.

    ![Karta definicje](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    W tym przykładzie ma jedną operację, o nazwie `CalculateCosts`. Metadane, takiej jak **opis**, wszystkie pochodzą z pliku OpenAPI.

7. Kliknij przycisk **utworzyć łącznik** w górnej części strony.

Zapewnia teraz możliwość połączenia z łącznikiem niestandardowym w usłudze PowerApps i Microsoft Flow. Aby uzyskać więcej informacji na temat tworzenia łączników w portalach usługi PowerApps i Microsoft Flow, zobacz [zarejestrować łącznik niestandardowy (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) i [zarejestrować łącznik niestandardowy (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Określanie typu uwierzytelniania

Usługa PowerApps i Microsoft Flow obsługuje kolekcję dostawców tożsamości, które zapewniają uwierzytelniania dla łączników niestandardowych. Jeśli Twój interfejs API wymaga uwierzytelniania, upewnij się, że jest przechwytywany jako _definicji zabezpieczeń_ w dokumencie interfejsu OpenAPI, jak w poniższym przykładzie:

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
Podczas eksportowania możesz podać wartości konfiguracji, umożliwiające usług PowerApps i Microsoft Flow w celu uwierzytelniania użytkowników.

W tej sekcji omówiono typy uwierzytelniania, które są obsługiwane przez **Express** trybu: Klucz interfejsu API usługi Azure Active Directory i ogólna OAuth 2.0. Usługa PowerApps i Microsoft Flow obsługują także uwierzytelnianie podstawowe i OAuth 2.0 dla konkretnych usług, takich jak Dropbox, Facebook i SalesForce.

### <a name="api-key"></a>Klucz interfejsu API
Przy użyciu klucza interfejsu API, łącznika monit o podanie klucza, podczas tworzenia połączenia. Określasz nazwa klucza interfejsu API, aby pomóc im zrozumieć, jest potrzebny. We wcześniejszym przykładzie używamy nazwy `API Key (contact meganb@contoso.com)` aby użytkownicy wiedzieli, gdzie można uzyskać informacje na temat klucza interfejsu API. Dla usługi Azure Functions klucz jest zwykle jeden z kluczy hosta, obejmującego kilka funkcji w ramach aplikacji funkcji.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Korzystając z usługi Azure AD, potrzebujesz dwóch rejestracje aplikacji usługi Azure AD: jeden dla sam interfejs API i jeden dla łącznika niestandardowego:

- Aby skonfigurować rejestrację dla interfejsu API, należy użyć [uwierzytelniania/autoryzacji dla aplikacji usługi](../app-service/configure-authentication-provider-aad.md) funkcji.

- Aby skonfigurować rejestrację dla łącznika, wykonaj kroki opisane w [dodawania aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Rejestracja musi mieć delegowany dostęp do interfejsu API i adresu URL odpowiedzi `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Aby uzyskać więcej informacji, zobacz przykłady rejestracji usługi Azure AD [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) i [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Te przykłady użycia usługi Azure Resource Manager jako interfejs API; Postępuj zgodnie z instrukcjami, należy go zastąpić interfejsu API.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta łącznika rejestracji w usłudze Azure AD
- **Klucz tajny klienta** — klucz tajny klienta łącznika rejestracji w usłudze Azure AD
- **Adres URL logowania** — podstawowy adres URL dla usługi Azure AD. Na platformie Azure, zazwyczaj jest to `https://login.windows.net`.
- **Identyfikator dzierżawy** — identyfikator dzierżawy, który ma być używany dla nazwy logowania. Powinno to być "typowych" lub identyfikator dzierżawy, w którym zostanie utworzona łącznika.
- **Adres URL zasobu** — adres URL zasobu rejestracji usługi Azure AD dla interfejsu API

> [!IMPORTANT]
> Jeśli ktoś inny zostaną zaimportowane definicji interfejsu API do usług PowerApps i Microsoft Flow jako część przepływu ręcznie, należy podać je z Identyfikatorem klienta i wpis tajny klienta *rejestrację łącznika*, a także adres URL zasobu interfejsu API. Upewnij się, że tych kluczy tajnych są bezpiecznie zarządzane. **Nie należy udostępniać poświadczeń zabezpieczeń sam interfejs API.**

### <a name="generic-oauth-20"></a>Ogólne uwierzytelnianie OAuth 2.0
Korzystając z ogólnego protokołu OAuth 2.0, można zintegrować z dowolnego dostawcy uwierzytelniania OAuth 2.0. Dzięki temu można pracować z dostawców niestandardowych, które nie są obsługiwane natywnie.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta OAuth 2.0
- **Klucz tajny klienta** — klucz tajny klienta OAuth 2.0
- **Adres URL autoryzacji** — adres URL autoryzacji OAuth 2.0
- **Adres URL tokenu** — adres URL tokenu OAuth 2.0
- **Odśwież adres URL** — adres URL owświeżania OAuth 2.0


