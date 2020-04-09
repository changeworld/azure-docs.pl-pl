---
title: Platforma tożsamości firmy Microsoft & przepływ potwierdzenia na okaziciela SAML | Azure
description: Dowiedz się, jak pobierać dane z programu Microsoft Graph bez monitowania użytkownika o poświadczenia przy użyciu przepływu potwierdzenia nośnika SAML.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886181"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Platforma tożsamości firmy Microsoft i przepływ potwierdzenia na okaziciela OAuth 2.0 SAML
Przepływ potwierdzenia na okaziciela OAuth 2.0 SAML umożliwia żądanie tokenu dostępu OAuth przy użyciu potwierdzenia SAML, gdy klient musi użyć istniejącej relacji zaufania. Podpis zastosowany do potwierdzenia SAML zapewnia uwierzytelnianie autoryzowanej aplikacji. Twierdzenie SAML jest tokenem zabezpieczającym XML wystawionym przez dostawcę tożsamości i wykorzystanym przez dostawcę usług. Usługodawca polega na swojej treści, aby zidentyfikować temat twierdzenia do celów związanych z bezpieczeństwem.

Twierdzenie SAML jest księgowane w punkcie końcowym tokenu OAuth.  Punkt końcowy przetwarza potwierdzenia i wystawia token dostępu na podstawie uprzedniej zgody aplikacji. Klient nie jest wymagane do posiadania lub przechowywania tokenu odświeżania, ani nie jest klucz tajny klienta wymagane do przekazania do punktu końcowego tokenu.

Przepływ potwierdzenia nośnika SAML jest przydatny podczas pobierania danych z interfejsów API programu Microsoft Graph (które obsługują tylko uprawnienia delegowane) bez monitowania użytkownika o poświadczenia. W tym scenariuszu udzielić poświadczeń klienta, który jest preferowany dla procesów w tle, nie działa.

W przypadku aplikacji, które wykonują interaktywne logowanie oparte na przeglądarce, aby uzyskać asercja SAML, a następnie chcesz dodać dostęp do interfejsu API chronionego przez usługę OAuth (takiego jak Microsoft Graph), można wykonać żądanie OAuth, aby uzyskać token dostępu dla interfejsu API. Gdy przeglądarka zostanie przekierowana do usługi Azure AD w celu uwierzytelnienia użytkownika, przeglądarka odbierze sesję z logowania SAML, a użytkownik nie musi wprowadzać swoich poświadczeń.

Przepływ potwierdzenia nośnika OAuth SAML jest również obsługiwany dla użytkowników uwierzytelniających się za pomocą dostawców tożsamości, takich jak Usługi federacyjne Active Directory (ADFS) sfederowane z usługą Azure Active Directory.  Twierdzenie SAML uzyskane z usługi ADFS może służyć w przepływie OAuth do uwierzytelniania użytkownika.

![Przepływ OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Wywołanie wykresu przy użyciu potwierdzenia na okaziciela SAML
Teraz zrozummy, w jaki sposób możemy rzeczywiście pobrać twierdzenie SAML programowo. To podejście jest testowane z systemem ADFS. Jednak to działa z dowolnego dostawcy tożsamości, który obsługuje zwracanie potwierdzenia SAML programowo. Podstawowy proces jest: uzyskać asercja SAML, uzyskać token dostępu i dostęp do programu Microsoft Graph.

### <a name="prerequisites"></a>Wymagania wstępne

Ustanawianie relacji zaufania między serwerem/środowiskiem autoryzacji (Microsoft 365) a dostawcą tożsamości lub wystawcą potwierdzenia na okaziciela SAML 2.0 (ADFS). Aby skonfigurować usługi ADFS do logowania jednokrotnego i jako dostawca tożsamości, można zapoznać się z [tym artykułem](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Zarejestruj aplikację w [portalu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Zaloguj się do [bloku rejestracji aplikacji portalu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Należy pamiętać, że używamy punktów końcowych w wersji 2.0 dla interfejsu API wykresu i dlatego należy zarejestrować aplikację w tym portalu. W przeciwnym razie moglibyśmy użyć rejestracji w usłudze Azure active Directory). 
1. Wybierz **pozycję Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji: 
    1. **Nazwa** — podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji.
    1. **Obsługiwane typy konta** — wybierz konta, które aplikacja ma obsługiwać.
    1. **Przekieruj identyfikator URI (opcjonalnie)** — wybierz typ aplikacji, którą budujesz, sieć Web lub klient publiczny (mobilny & pulpitu), a następnie wprowadź adres URL przekierowania (lub adres URL odpowiedzi) dla aplikacji.
    1. Po zakończeniu wybierz pozycję **Rejestruj**.
1. Zanotuj identyfikator aplikacji (klienta).
1. W lewym okienku wybierz pozycję **Certyfikaty & wpisy tajne**. Kliknij **pozycję Nowy klucz tajny klienta** w sekcji **Wpisy tajne klienta.** Skopiuj nowy klucz tajny klienta, nie będzie można pobrać po opuszczeniu bloku.
1. W lewym okienku wybierz pozycję **Uprawnienia interfejsu API,** a następnie **dodaj uprawnienie**. Wybierz pozycję **Microsoft Graph**, a następnie **uprawnienia delegowane**, a następnie wybierz **pozycję Tasks.read,** ponieważ zamierzamy używać interfejsu API wykresu programu Outlook. 

Zainstaluj [listonosza](https://www.getpostman.com/), narzędzie wymagane do testowania przykładowych żądań.  Później można przekonwertować żądania do kodu.

### <a name="get-the-saml-assertion-from-adfs"></a>Pobierz asercja SAML z usługi ADFS
Utwórz żądanie POST do punktu końcowego usługi ADFS przy użyciu otoczki SOAP do pobrania potwierdzenia SAML:

![Pobierz twierdzenie SAML](./media/v2-saml-bearer-assertion/2.png)

Wartości nagłówka:

![Wartości nagłówka](./media/v2-saml-bearer-assertion/3.png)

Treść żądania usługi ADFS:

![Treść żądania usługi ADFS](./media/v2-saml-bearer-assertion/4.png)

Po pomyślnym zaksięgowane to żądanie powinno pojawić się asercja SAML z usługi ADFS. Wymagane są tylko dane tagu **SAML:Asercja,** przekonwertować je na kodowanie base64 do użycia w dalszych żądaniach.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Pobierz token OAuth2 przy użyciu potwierdzenia SAML 
W tym kroku pobierz token OAuth2 przy użyciu odpowiedzi potwierdzenia usługi ADFS.

1. Utwórz żądanie POST, jak pokazano poniżej, z wartościami nagłówka:

    ![Post wniosek](./media/v2-saml-bearer-assertion/5.png)
1. W treści żądania zastąp **client_id**, **client_secret**i **twierdzenie** (twierdzenie kodowane przez SAML kodowane base64 uzyskało poprzedni krok):

    ![Treść żądania](./media/v2-saml-bearer-assertion/6.png)
1. Po pomyślnym żądaniu otrzymasz token dostępu z usługi Azure active directory.

### <a name="get-the-data-with-the-oauth-token"></a>Pobierz dane za pomocą tokenu Oauth

Po otrzymaniu tokenu dostępu, wywołać interfejsy API programu Graph (zadania programu Outlook w tym przykładzie). 

1. Utwórz żądanie GET z tokenem dostępu pobranym w poprzednim kroku:

    ![Wniosek GET](./media/v2-saml-bearer-assertion/7.png)

1. Po pomyślnym zażądaniu otrzymasz odpowiedź JSON.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md).