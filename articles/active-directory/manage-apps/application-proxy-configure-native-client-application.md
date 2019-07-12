---
title: Publikowanie aplikacji klienta natywnego — usługa Azure AD | Dokumentacja firmy Microsoft
description: Opisano sposób włączania aplikacje klienta natywnego nawiązać połączenia z usługą platformy Azure łącznika serwera Proxy aplikacji usługi AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji w środowisku lokalnym.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdc46ea3a45d04e6e837d0b7ad52ed8bf565cd2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702421"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak włączyć natywne aplikacje klienckie do interakcji z serwera proxy aplikacji

Serwer Proxy aplikacji usługi Azure Active Directory (Azure AD) umożliwia publikowanie aplikacji sieci web, ale również może służyć do publikowania aplikacji klienta natywnego, które są skonfigurowane przy użyciu usługi Azure AD Authentication Library (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci web, ponieważ są one zainstalowane na urządzeniu, natomiast aplikacje sieci web są dostępne za pośrednictwem przeglądarki.

Aby umożliwić obsługę aplikacji klienta natywnego, serwer Proxy aplikacji akceptuje Azure tokeny wystawione przez usługi AD, które są wysyłane w nagłówku. Usługa serwera Proxy aplikacji ma uwierzytelniania dla użytkowników. To rozwiązanie nie używa tokenów aplikacji do uwierzytelniania.

![Relacja między użytkowników końcowych, usługa Azure AD i opublikowane aplikacje](./media/application-proxy-configure-native-client-application/richclientflow.png)

Aby opublikować aplikacje natywne, użyj usługi Azure AD Authentication Library, zajmuje się uwierzytelnieniem, która obsługuje wiele środowisk klienta. Serwer Proxy aplikacji jest dopasowywana do [natywną aplikację interfejsu API sieci Web scenariusza](../develop/native-app.md).

W tym artykule przedstawiono cztery kroki publikowania aplikacji natywnej za pomocą serwera Proxy aplikacji i Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: Publikuj swoje aplikacje serwera proxy

Publikuj swoje aplikacje serwera proxy, jak w przypadku innych aplikacji i Przypisz użytkownikom uzyskiwanie dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [publikowania aplikacji za pomocą serwera Proxy aplikacji](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2: Rejestrowanie aplikacji natywnej

Teraz musisz zarejestrować aplikację w usłudze Azure AD, w następujący sposób:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). **Pulpit nawigacyjny** dla **Centrum administracyjne usługi Azure Active Directory** pojawia się.
1. Na pasku bocznym wybierz **usługi Azure Active Directory**. **Usługi Azure Active Directory** zostanie wyświetlona strona przeglądu.
1. Na pasku bocznym Omówienie usługi Azure AD wybierz **rejestracje aplikacji**. Zostanie wyświetlona lista wszystkich rejestracji aplikacji.
1. Wybierz **nowej rejestracji**. **Rejestrowania aplikacji** zostanie wyświetlona strona.

   ![Tworzenie nowej rejestracji aplikacji w witrynie Azure portal](./media/application-proxy-configure-native-client-application/create.png)

1. W **nazwa** nagłówka, określ nazwę wyświetlaną użytkownika dla aplikacji.
1. W obszarze **obsługiwane typy kont** nagłówka, wybierz poziom dostępu za pomocą następujących wytycznych:

   - Pod kątem tylko konta, które są wewnętrzne dla Twojej organizacji, wybierz **kont w tym katalogu organizacji tylko**.
   - Pod kątem tylko firmy lub klienci edukacyjnych, wybierz **kont w dowolnym katalogu organizacji**.
   - Pod kątem możliwie najszerszej zbiór tożsamości firmy Microsoft, wybierz **kont w dowolnym katalogu organizacji i osobistych kont Microsoft**.

1. W **identyfikator URI przekierowania** nagłówka, wybierz **klientem publicznym (mobilnych i klasycznych)** , a następnie wpisz identyfikator URI przekierowania dla aplikacji.
1. Wybierz, a następnie przeczytaj **zasady platformy firmy Microsoft**, a następnie wybierz pozycję **zarejestrować**. Strona przeglądu rejestrowanie nowej aplikacji jest tworzone i wyświetlane.

Aby uzyskać szczegółowe informacje dotyczące tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: Udzielanie dostępu do serwera proxy aplikacji

Skoro już zarejestrowanego w aplikacji natywnej, możesz udzielić jej dostępu do innych aplikacji w katalogu, w tym przypadku uzyskać dostęp do aplikacji serwera proxy. Aby umożliwić aplikacji natywnej uwidocznienie aplikację serwera proxy:

1. Na pasku bocznym stronie rejestracji aplikacji, wybierz **uprawnienia do interfejsu API**. **Uprawnienia do interfejsu API** strona jest wyświetlana rejestrowanie nowej aplikacji.
1. Wybierz **Dodaj uprawnienia**. **Uprawnienia do żądania interfejsu API** zostanie wyświetlona strona.
1. W obszarze **wybierz interfejs API** ustawienie, wybierz **Moja organizacja korzysta z interfejsów API**. Zostanie wyświetlona lista zawierająca aplikacji w katalogu, które interfejsy API.
1. Wpisz w polu wyszukiwania lub przewiń, aby znaleźć serwera proxy aplikacji, która została opublikowana w [krok 1: Publikuj swoje aplikacje serwera proxy](#step-1-publish-your-proxy-application), a następnie wybierz aplikację serwera proxy.
1. W **jakiego rodzaju uprawnień aplikacji wymaga?** nagłówka, wybierz typ uprawnień. Jeśli aplikacja natywna wymaga dostępu do serwera proxy aplikacji interfejsu API jako zalogowanego użytkownika, wybierz **delegowane uprawnienia**. Jeśli aplikacja natywna działa jako usługa w tle lub demon bez zalogowanego użytkownika, wybierz opcję **uprawnienia aplikacji**.
1. W **wybierz uprawnienia** nagłówka, wybierz żądane uprawnienia i wybierz **Dodaj uprawnienia**. **Uprawnienia do interfejsu API** strona aplikacji natywnej teraz przedstawiono serwer proxy aplikacji i uprawnień interfejsu API, który został dodany.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Edytuj biblioteki uwierzytelniania usługi Active Directory

Edytuj kod aplikacji macierzystej w kontekście uwierzytelniania programu Active Directory Authentication Library (ADAL) do uwzględnienia następujący tekst:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Wymagane informacje w przykładowym kodzie można znaleźć w portalu usługi Azure AD w następujący sposób:

| Wymagane informacje | Jak znaleźć go w portalu usługi Azure AD |
| --- | --- |
| \<Identyfikator dzierżawy > | **Usługa Azure Active Directory** > **właściwości** > **identyfikator katalogu** |
| \<Zewnętrzny adres Url serwera Proxy aplikacji > | **Aplikacje dla przedsiębiorstw** > *aplikację serwera proxy* > **serwera proxy aplikacji** > **zewnętrznego adresu Url** |
| \<Identyfikator aplikacji natywnej aplikacji > | **Aplikacje dla przedsiębiorstw** > *aplikacji natywnej* > **właściwości** > **Identyfikatora aplikacji** |
| \<Identyfikator URI przekierowania aplikacji natywnej > | **Usługa Azure Active Directory** > **rejestracje aplikacji** > *aplikacji natywnej* > **identyfikatory URI przekierowań** |
| \<Adres Url serwera proxy aplikacji interfejsu API > | **Usługa Azure Active Directory** > **rejestracje aplikacji** > *aplikacji natywnej* > **uprawnienia do interfejsu API**  >  **Interfejsu API / NAME uprawnień** |

Po zakończeniu edycji biblioteki ADAL, z następującymi parametrami, użytkownicy mogą uwierzytelniać natywne aplikacje klienckie nawet wtedy, gdy są one poza siecią firmową.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o przepływie aplikacja natywna, zobacz [natywne aplikacje w usłudze Azure Active Directory](../develop/native-app.md).

Dowiedz się więcej o konfigurowaniu [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
