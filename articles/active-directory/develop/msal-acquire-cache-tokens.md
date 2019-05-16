---
title: Zarządzanie tokeny (Microsoft Authentication Library) | Azure
description: Więcej informacji na temat uzyskiwania i buforowanie tokenów przy użyciu Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546090"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Pobieranie i buforowanie tokenów z zastosowaniem biblioteki MSAL
[Tokeny dostępu](access-tokens.md) umożliwić klientom bezpiecznie wywoływać interfejsy API chronionej przez platformę Azure w sieci web. Istnieje wiele sposobów, aby uzyskać token za pomocą usługi Microsoft Authentication Library (MSAL). Niektóre sposoby wymaga interakcji użytkownika za pośrednictwem przeglądarki sieci web. Niektóre nie wymagają żadnych interakcji użytkownika. Ogólnie rzecz biorąc sposób, aby uzyskać token zależy od Jeśli aplikacja jest aplikacją kliencką publiczny (komputerze lub urządzeniu przenośnym aplikacja) lub poufne klienta aplikacji (aplikacje demona aplikacja sieci Web i interfejsów API, takim jak usługa Windows).

Biblioteka MSAL buforuje token po został nabyty.  Kod aplikacji, należy spróbować uzyskać token dyskretnie (z pamięci podręcznej), najpierw przed pobierania tokenu w inny sposób.

Można także wyczyścić pamięci podręcznej tokenu, który jest osiągane poprzez usunięcie konta z pamięci podręcznej. Plik cookie sesji, który znajduje się w przeglądarce, jednak nie powoduje usunięcia.

## <a name="scopes-when-acquiring-tokens"></a>Zakresy podczas uzyskiwania tokenów
[Zakresy](v2-permissions-and-consent.md) są uprawnienia, które uwidacznia interfejs API sieci web dla aplikacji klienckich zażądać dostępu do. Aplikacje klienckie żądanie zgody użytkownika na te zakresy, podczas wprowadzania żądań uwierzytelnienia do uzyskania tokenów dostępu do interfejsów API sieci web. Biblioteka MSAL pozwala uzyskiwać tokeny dostępu do usługi Azure AD dla deweloperów (1.0) i platforma tożsamości firmy Microsoft (w wersji 2.0), interfejsów API. Protokół v2.0 używa zakresów zamiast resource w żądaniach. Aby uzyskać więcej informacji, przeczytaj [porównanie w wersji 1.0 i 2.0](active-directory-v2-compare.md). Na podstawie konfiguracji internetowego interfejsu API w wersji tokenu, który przyjmuje punktu końcowego v2.0 zwraca token dostępu do biblioteki MSAL.

Liczba MSAL uzyskania tokenu metody wymagają *zakresy* parametru. Ten parametr jest prostą listę ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Dobrze znane zakresy są [uprawnienia usługi Microsoft Graph](/graph/permissions-reference).

Jest również możliwe MSAL dostęp do zasobów w wersji 1.0. Aby uzyskać więcej informacji, przeczytaj [zakresów dla aplikacji w wersji 1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Żądanie określonych zakresów dla internetowego interfejsu API
Gdy aplikacja musi żądać tokenów przy użyciu określonych uprawnień do zasobów interfejsu API, musisz przekazać zakresy zawierający identyfikator URI interfejsu API w poniższego formatu:  *&lt;identyfikator URI&gt; / &lt;zakresu&gt;*

Na przykład zakresy dla interfejsu API Microsoft Graph: `https://graph.microsoft.com/User.Read`

Lub, na przykład zakres dla niestandardowego interfejsu API sieci web: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Dla interfejsu API Microsoft Graph, tylko wartość zakresu `user.read` mapuje `https://graph.microsoft.com/User.Read` sformatowanie i mogą być używane zamiennie.

> [!NOTE]
> Niektóre interfejsy API, np. interfejsu API usługi Azure Resource Manager w sieci web (https://management.core.windows.net/) oczekiwać końcowe "/" w grupie odbiorców tokenu dostępu oświadczenia (aud). W takim przypadku należy przekazać zakresu jako https://management.core.windows.net//user_impersonation (Uwaga podwójny ukośnik), aby token był prawidłowy w interfejsie API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Zakresy dynamiczne żądania o zgodę przyrostowe
Podczas tworzenia aplikacji przy użyciu v1.0, trzeba było zarejestrować pełnego zestawu uprawnień (zakresy statyczne) wymagany przez aplikację dla użytkownika o zgodę podczas logowania. W wersji 2.0 można uzyskać dodatkowe uprawnienia, stosownie do potrzeb, przy użyciu parametru zakresu. Te są nazywane dynamiczne zakresy i umożliwia użytkownikowi przyrostowe zgody do zakresów.

Na przykład można początkowo loguje użytkownika i je zablokować dostępu żadnego rodzaju. Później można nadać im możliwość odczytu kalendarza użytkownika przez zażądanie zakresu kalendarza w metodach tokenu nabywania i uzyskać zgodę użytkownika.

Na przykład: `https://graph.microsoft.com/User.Read` i `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Pobieranie tokenów w trybie dyskretnym (z pamięci podręcznej)
Biblioteka MSAL zachowuje pamięć podręczną tokenu (lub dwóch pamięci podręczne dla aplikacji zawierających poufne dane klienta) i buforuje token po został pozyskany.  W wielu przypadkach próby dyskretnie uzyskać token zostanie uzyskać inny token za pomocą więcej zakresów, na podstawie tokenów w pamięci podręcznej. Istnieje również możliwość odświeżanie tokenu, gdy będzie niedługo wygaśnie (zgodnie z pamięci podręcznej tokenu zawiera także tokenu odświeżania).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Zalecane wywołanie wzorców aplikacji publicznych klienta
Kod aplikacji, należy spróbować uzyskać token w trybie dyskretnym (z pamięci podręcznej), najpierw.  Jeśli wywołanie metody zwraca błąd "Required interfejsu użytkownika" lub wyjątku, spróbuj pobierania tokenu w inny sposób. 

Istnieją dwa przepływów, przed którym zostanie **nie powinien** próba uzyskania dyskretnie tokenu:

- [Przepływ poświadczeń klienta](msal-authentication-flows.md#client-credentials), która nie korzysta z pamięci podręcznej tokenu użytkownika, ale token pamięci podręcznej aplikacji. Ta metoda zajmuje się weryfikowanie tej pamięci podręcznej do tokenu aplikacji przed wysłaniem żądania do usługi STS.
- [Przepływ kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacji sieci Web, ponieważ umarza kod, który aplikacja stało się przez użytkownika logowania i konieczności ich zgody na więcej zakresów. Ponieważ kod jest przekazywany jako parametr, a nie kontem, metoda nie może wyglądać w pamięci podręcznej przed zrealizowaniem kod, który wymaga mimo to, wywołanie usługi.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Zalecany wzorzec rozmowy w aplikacji sieci Web przy użyciu przepływu kodu autoryzacji 
Dla aplikacji sieci Web, które używają [przepływ kodu autoryzacji protokołu OpenID Connect](v2-protocols-oidc.md), jest zalecany wzorzec w kontrolerów:

- Tworzy aplikację kliencką zawierających poufne dane z pamięci podręcznej tokenu przy użyciu dostosowanych serializacji. 
- Uzyskanie tokenu przy użyciu przepływ kodu autoryzacji

## <a name="acquiring-tokens"></a>Uzyskiwanie tokenów
Ogólnie rzecz biorąc sposób pobierania tokenu zależy od tego, czy jest klientem publicznym lub poufne klienta aplikacji.

### <a name="public-client-applications"></a>Aplikacje klienckie publiczne
Dla aplikacji klienckich publiczny (komputerze lub urządzeniu przenośnym aplikacja) możesz:
- Często uzyskać tokeny interaktywnie użytkownika, zaloguj się za pośrednictwem interfejsu użytkownika lub okna podręcznego.
- Można [uzyskać token w trybie dyskretnym dla zalogowanego użytkownika](msal-authentication-flows.md#integrated-windows-authentication) przy użyciu zintegrowanego uwierzytelniania Windows (IWA/Kerberos), jeśli aplikacja jest uruchomiona na komputerze Windows przyłączone do domeny lub na platformie Azure.
- Można [pobrać tokenu przy użyciu nazwy użytkownika i hasła](msal-authentication-flows.md#usernamepassword) w klienta pulpitu programu .NET framework aplikacje, ale nie jest zalecane. Nie należy używać w aplikacjach klienckich poufne nazwy użytkownika i hasła.
- Można pobrać tokenu za pośrednictwem [przepływ kodu urządzenia](msal-authentication-flows.md#device-code) w aplikacje działające na urządzeniach, które nie zawierają przeglądarki sieci web. Użytkownik otrzymuje adres URL i kod, który następnie przechodzi do przeglądarki sieci web na innym urządzeniu i wprowadza kod i loguje się.  Następnie usługa Azure AD wysyła token z powrotem do urządzenia bez przeglądarki.

### <a name="confidential-client-applications"></a>Aplikacje poufne klienta 
Poufne klienta aplikacji (aplikacji sieci Web, interfejs API sieci Web lub demona aplikacja takim jak usługa Windows) możesz:
- Uzyskiwanie tokenów **samej aplikacji** , a nie dla użytkownika, za pomocą [przepływ poświadczeń klienta](msal-authentication-flows.md#client-credentials). Może to służyć do synchronizacji narzędzia lub narzędzia przetwarzające ogólnie rzecz biorąc użytkowników i określonego użytkownika. 
- Użyj [przepływu w imieniu z](msal-authentication-flows.md#on-behalf-of) dla internetowego interfejsu API, aby wywołać interfejs API w imieniu użytkownika. Aplikacja jest identyfikowany przy użyciu poświadczeń klienta w celu uzyskania tokenu, w oparciu o użytkownika potwierdzenia (języka SAML na przykład lub JWT token). Ten przepływ jest używany przez aplikacje, które muszą uzyskać dostęp do zasobów określonego użytkownika w wywołaniach do usługi.
- Uzyskiwanie tokenów przy użyciu [przepływ kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacjach sieci web po użytkownik loguje się za pośrednictwem autoryzację adres URL żądania. OpenID Connect aplikacji zwykle użyć to mechanizm, który informuje użytkownika, którego zalogować się przy użyciu Open ID connect, a następnie dostęp do internetowych interfejsów API w imieniu użytkownika.


## <a name="authentication-results"></a>Wyniki uwierzytelniania 
Gdy klient żąda tokenu dostępu, usługa Azure AD również zwraca wynik uwierzytelniania, który zawiera niektóre metadane dotyczące tokenu dostępu. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwia aplikacji, aby wykonać inteligentne buforowanie tokenów dostępu, bez konieczności przeanalizować tokenu dostępu, sama.  Przedstawia wynik uwierzytelniania:

- [Token dostępu](access-tokens.md) dla internetowego interfejsu API dostępu do zasobów. Jest to ciąg, zwykle JWT zakodowany w formacie base64, ale klient nigdy nie powinien wyglądać w tokenie dostępu. Format nie jest gwarantowana pozostaną niezmienione i mogą być szyfrowane dla zasobu. Osoby pisanie kodu w zależności od zawartości tokenu dostępu na komputerze klienckim jest jednym z największych źródeł błędów i podziałów logiki klienta.
- [Tokenu Identyfikacyjnego](id-tokens.md) dla użytkownika (jest to token JWT).
- Wygaśnięcie tokenu, która informuje o tym data/godzina, po wygaśnięciu ważności tokenu.
- Identyfikator dzierżawy zawiera dzierżawy, w którym użytkownik został znaleziony. Dla użytkowników-gości (scenariusze B2B usługi Azure AD) identyfikator dzierżawy jest dzierżawy gościa, a nie unikatowy dzierżawy. Gdy token jest dostarczany w imieniu użytkownika, wynik uwierzytelniania zawiera także informacje o tym użytkowniku. Poufne klienta przepływów, w których tokeny są żądane bez użytkownika (w przypadku aplikacji) te informacje użytkownika ma wartość null.
- Zakresy, dla których token został wystawiony.
- Unikatowy identyfikator dla użytkownika.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [Obsługa błędów i wyjątków](msal-handling-exceptions.md). 
