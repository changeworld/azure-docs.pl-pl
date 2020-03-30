---
title: Eksportowanie interfejsu API hostowanego na platformie Azure do aplikacji PowerApps i usługi Microsoft Flow
description: Omówienie sposobu udostępnienia interfejsu API hostowanego w usłudze App Service w usługach PowerApps i usłudze Microsoft Flow
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233070"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Eksportowanie interfejsu API hostowanego na platformie Azure do aplikacji PowerApps i usługi Microsoft Flow

[Usługa PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) to usługa do tworzenia i używania niestandardowych aplikacji biznesowych, które łączą się z twoimi danymi i działają na różnych platformach. [Usługa Microsoft Flow](/learn/modules/get-started-with-flow/index) ułatwia automatyzację przepływów pracy i procesów biznesowych między ulubionymi aplikacjami i usługami. Zarówno usługi PowerApps, jak i usługa Microsoft Flow są wyposażone w różne wbudowane łączniki do źródeł danych, takich jak Office 365, Dynamics 365, Salesforce i inne. W niektórych przypadkach konstruktorzy aplikacji i przepływu również chcą połączyć się ze źródłami danych i interfejsami API utworzonymi przez ich organizację.

Podobnie deweloperzy, którzy chcą udostępnić swoje interfejsy API szerzej w organizacji, mogą udostępnić swoje interfejsy API dla konstruktorów aplikacji i przepływów. W tym temacie pokazano, jak wyeksportować interfejs API utworzony za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md) lub Usługi Azure App [Service](../app-service/overview.md). Eksportowany interfejs API staje się *łącznikiem niestandardowym,* który jest używany w programach PowerApps i Microsoft Flow, podobnie jak wbudowany łącznik.

> [!IMPORTANT]
> Funkcja definicji interfejsu API pokazana w tym artykule jest obsługiwana tylko dla [wersji 1.x środowiska uruchomieniowego usługi Azure Functions](functions-versions.md#creating-1x-apps) i aplikacji usługi App Services. Wersja 2.x funkcji integruje się z zarządzaniem interfejsami API w celu tworzenia i obsługi definicji OpenAPI. Aby dowiedzieć się więcej, zobacz [Tworzenie definicji OpenAPI dla funkcji za pomocą usługi Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Tworzenie i eksportowanie definicji interfejsu API
Przed wyeksportowaniem interfejsu API należy opisać interfejs API przy użyciu definicji OpenAPI (wcześniej znanej jako plik [Swagger).](https://swagger.io/) Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API. Usługi PowerApps i Microsoft Flow mogą tworzyć łączniki niestandardowe dla dowolnej definicji OpenAPI 2.0. Usługi Azure Functions i usługa Azure App Service mają wbudowaną obsługę tworzenia, hostingu i zarządzania definicjami OpenAPI. Aby uzyskać więcej informacji, zobacz [Hostuj interfejs API RESTful z usługą CORS w usłudze Azure App Service.](../app-service/app-service-web-tutorial-rest-api.md)

> [!NOTE]
> Łączniki niestandardowe można również tworzyć w interfejsie użytkownika usługi PowerApps i Microsoft Flow bez użycia definicji OpenAPI. Aby uzyskać więcej informacji, zobacz [Rejestrowanie i używanie łącznika niestandardowego (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) oraz [rejestrowanie i używanie łącznika niestandardowego (Microsoft Flow)](/power-automate/developer/register-custom-api).

Aby wyeksportować definicję interfejsu API, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do usługi Azure Functions lub innej aplikacji usługi App Service.

    Jeśli korzystasz z usługi Azure Functions, wybierz aplikację funkcji, wybierz pozycję **Funkcje platformy**, a następnie **definicję interfejsu API**.

    ![Definicja interfejsu API funkcji Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Jeśli korzystasz z usługi Azure App Service, wybierz **definicję interfejsu API** z listy ustawień.

    ![Definicja interfejsu API usługi App Service](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Przycisk **Eksportuj do usługi PowerApps + Microsoft Flow** powinien być dostępny (jeśli nie, należy najpierw utworzyć definicję OpenAPI). Kliknij ten przycisk, aby rozpocząć proces eksportowania.

    ![Przycisk Eksportuj do usługi PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Wybierz **tryb eksportu:**

    **Express** umożliwia utworzenie łącznika niestandardowego z poziomu witryny Azure portal. Wymaga zalogowania się do usługi PowerApps lub Microsoft Flow i masz uprawnienia do tworzenia łączników w środowisku docelowym. Jest to zalecane podejście, jeśli te dwa wymagania mogą być spełnione. Jeśli korzystasz z tego trybu, postępuj zgodnie z poniższymi instrukcjami [eksportu użyj ekspresowego](#express) eksportu.

    **Ręcznie** umożliwia eksportowanie definicji interfejsu API, którą następnie importujesz za pomocą portali Usługi PowerApps lub Microsoft Flow. Jest to zalecane podejście, jeśli użytkownik platformy Azure i użytkownik z uprawnieniami do tworzenia łączników są różne osoby lub jeśli łącznik musi zostać utworzony w innej dzierżawie platformy Azure. Jeśli korzystasz z tego trybu, postępuj zgodnie z poniższymi instrukcjami [ręcznego eksportowania.](#manual)

    ![Tryb eksportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Łącznik niestandardowy używa *kopii* definicji interfejsu API, więc usługi PowerApps i Microsoft Flow nie będą od razu wiedzieć, czy wprowadzać zmiany w aplikacji i jej definicji interfejsu API. Jeśli wniesiesz zmiany, powtórz kroki eksportu dla nowej wersji.

<a name="express"></a>
## <a name="use-express-export"></a>Użyj eksportu ekspresowego

Aby zakończyć eksport w trybie **ekspresowym,** wykonaj następujące czynności:

1. Upewnij się, że zalogowano się do dzierżawy usługi PowerApps lub usługi Microsoft Flow, do której chcesz wyeksportować. 

2. Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Środowisko**|Wybierz środowisko, w które ma zostać zapisany łącznik niestandardowy. Aby uzyskać więcej informacji, zobacz [Environments overview](https://powerapps.microsoft.com/tutorials/environments-overview/) (Omówienie środowisk).|
    |**Niestandardowa nazwa interfejsu API**|Wprowadź nazwę, którą konstruktorzy usługi PowerApps i Microsoft Flow zobaczą na liście łączników.|
    |**Przygotowanie konfiguracji zabezpieczeń**|W razie potrzeby podaj szczegóły konfiguracji zabezpieczeń potrzebne do udzielenia użytkownikom dostępu do interfejsu API. W tym przykładzie pokazano klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [Określanie typu uwierzytelniania](#auth) poniżej.|
 
    ![Ekspresowe eksportowanie do aplikacji PowerApps i usługi Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kliknij przycisk **OK**. Łącznik niestandardowy jest teraz zbudowany i dodany do określonego środowiska.

<a name="manual"></a>
## <a name="use-manual-export"></a>Użyj eksportu ręcznego

Aby zakończyć eksport w trybie **ręcznym,** wykonaj następujące czynności:

1. Kliknij **pozycję Pobierz** i zapisz plik lub kliknij przycisk kopiuj i zapisz adres URL. Podczas importowania użyjesz pliku pobierania lub adresu URL.
 
    ![Eksportowanie ręczne do aplikacji PowerApps i usługi Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Jeśli definicja interfejsu API zawiera definicje zabezpieczeń, są one wywoływane w #2 krok. Podczas importowania usługi PowerApps i microsoft flow wykrywa je i monituje o informacje dotyczące zabezpieczeń. Zbierz poświadczenia związane z każdą definicją do użycia w następnej sekcji. Aby uzyskać więcej informacji, zobacz [Określanie typu uwierzytelniania](#auth) poniżej.

    ![Zabezpieczenia do ręcznego eksportu](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    W tym przykładzie przedstawiono definicję zabezpieczeń klucza interfejsu API, która została uwzględniona w definicji OpenAPI.

Teraz, gdy została wyeksportowana definicja interfejsu API, zaimportuj go, aby utworzyć łącznik niestandardowy w programach PowerApps i Microsoft Flow. Łączniki niestandardowe są współużytkowane między dwiema usługami, więc wystarczy zaimportować definicję tylko raz.

Aby zaimportować definicję interfejsu API do aplikacji PowerApps i usługi Microsoft Flow, wykonaj następujące czynności:

1. Przejdź do strony [powerapps.com](https://web.powerapps.com) lub [flow.microsoft.com](https://flow.microsoft.com).

2. W prawym górnym rogu kliknij ikonę koła zębatego, a następnie kliknij pozycję **Łączniki niestandardowe**.

   ![Ikona koła zębatego w usłudze](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kliknij **pozycję Utwórz łącznik niestandardowy**, a następnie kliknij pozycję **Importuj definicję OpenAPI**.

   ![Tworzenie łącznika niestandardowego](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Wprowadź nazwę łącznika niestandardowego, a następnie przejdź do wyemiczonej definicji OpenAPI i kliknij przycisk **Kontynuuj**.

   ![Przekaż definicję OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na karcie **Ogólne** przejrzyj informacje pochodzące z definicji OpenAPI.

5. Jeśli na karcie **Zabezpieczenia** zostanie wyświetlony monit o podanie szczegółów uwierzytelniania, wprowadź wartości odpowiednie dla typu uwierzytelniania. Kliknij przycisk **Kontynuuj**.

    ![Karta Zabezpieczenia](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    W tym przykładzie przedstawiono pola wymagane do uwierzytelniania za pomocą klucza interfejsu API. Pola różnią się w zależności od typu uwierzytelniania.

6. Na karcie **Definicje** wszystkie operacje zdefiniowane w pliku OpenAPI są wypełniane automatycznie. Jeśli zdefiniowane są wszystkie wymagane operacje, można przejść do następnego kroku. Jeśli nie, można dodać i zmodyfikować operacje tutaj.

    ![Karta Definicje](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    W tym przykładzie `CalculateCosts`ma jedną operację o nazwie . Metadane, takie jak **Opis,** pochodzą z pliku OpenAPI.

7. Kliknij **pozycję Utwórz łącznik** u góry strony.

Teraz można połączyć się z łącznikiem niestandardowym w programach PowerApps i Microsoft Flow. Aby uzyskać więcej informacji na temat tworzenia łączników w portalach Usługi PowerApps i Microsoft Flow, zobacz [Rejestrowanie łącznika niestandardowego (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) i [Rejestrowanie łącznika niestandardowego (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Określanie typu uwierzytelniania

Usługi PowerApps i Microsoft Flow obsługują kolekcję dostawców tożsamości, którzy zapewniają uwierzytelnianie łączników niestandardowych. Jeśli interfejs API wymaga uwierzytelniania, upewnij się, że jest przechwytywany jako _definicja zabezpieczeń_ w dokumencie OpenAPI, podobnie jak w poniższym przykładzie:

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
Podczas eksportowania podajesz wartości konfiguracji, które umożliwiają programom PowerApps i Microsoft Flow uwierzytelnianie użytkowników.

W tej sekcji opisano typy uwierzytelniania obsługiwane w trybie **ekspresowym:** klucz interfejsu API, usługa Azure Active Directory i ogólny OAuth 2.0. Usługi PowerApps i Microsoft Flow obsługują również uwierzytelnianie podstawowe oraz usługę OAuth 2.0 dla określonych usług, takich jak Dropbox, Facebook i SalesForce.

### <a name="api-key"></a>Klucz INTERFEJSU API
Podczas korzystania z klucza interfejsu API użytkownicy łącznika są monitowani o podanie klucza podczas tworzenia połączenia. Należy określić nazwę klucza interfejsu API, aby ułatwić im zrozumienie, który klucz jest potrzebny. We wcześniejszym przykładzie używamy `API Key (contact meganb@contoso.com)` nazwy, aby użytkownicy wiedzieli, gdzie można uzyskać informacje o kluczu interfejsu API. W przypadku funkcji platformy Azure klucz jest zazwyczaj jednym z kluczy hosta, obejmujące kilka funkcji w aplikacji funkcji.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Podczas korzystania z usługi Azure AD, potrzebujesz dwóch rejestracji aplikacji usługi Azure AD: jeden dla samego interfejsu API i jeden dla łącznika niestandardowego:

- Aby skonfigurować rejestrację interfejsu API, użyj funkcji [Uwierzytelnianie/autoryzacja usługi aplikacji.](../app-service/configure-authentication-provider-aad.md)

- Aby skonfigurować rejestrację łącznika, wykonaj kroki opisane w [temacie Dodawanie aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Rejestracja musi mieć delegowany dostęp do interfejsu `https://msmanaged-na.consent.azure-apim.net/redirect`API i adres URL odpowiedzi . 

Aby uzyskać więcej informacji, zobacz przykłady rejestracji usługi Azure AD dla [usług PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) i [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). W tych przykładach używaj usługi Azure Resource Manager jako interfejsu API; zastąpić interfejs API, jeśli postępujesz zgodnie z instrukcjami.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta łącznika rejestracji usługi Azure AD
- **Klucz tajny klienta** — klucz tajny klienta rejestracji usługi Azure AD łącznika
- **Adres URL logowania** — podstawowy adres URL usługi Azure AD. Na platformie Azure `https://login.windows.net`jest to zazwyczaj .
- **Identyfikator dzierżawy** — identyfikator dzierżawy, który ma być używany do logowania. Powinno to być "wspólne" lub identyfikator dzierżawy, w którym jest tworzony łącznik.
- **Adres URL zasobu** — adres URL zasobu rejestracji usługi Azure AD dla interfejsu API

> [!IMPORTANT]
> Jeśli ktoś inny zaimportuje definicję interfejsu API do aplikacji PowerApps i usługi Microsoft Flow w ramach przepływu ręcznego, należy podać im identyfikator klienta i klucz tajny klienta *rejestracji łącznika,* a także adres URL zasobu interfejsu API. Upewnij się, że te wpisy tajne są zarządzane bezpiecznie. **Nie należy udostępniać poświadczeń zabezpieczeń samego interfejsu API.**

### <a name="generic-oauth-20"></a>Ogólne uwierzytelnianie OAuth 2.0
Korzystając z ogólnego OAuth 2.0, można zintegrować z dowolnym dostawcą OAuth 2.0. Dzięki temu można pracować z dostawcami niestandardowymi, które nie są obsługiwane natywnie.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta OAuth 2.0
- **Tajemnica klienta** - tajemnica klienta OAuth 2.0
- **Adres URL autoryzacji** — adres URL autoryzacji OAuth 2.0
- **Adres URL tokenu** — adres URL tokenu OAuth 2.0
- **Odśwież adres URL** — adres URL odświeżania 2.0


