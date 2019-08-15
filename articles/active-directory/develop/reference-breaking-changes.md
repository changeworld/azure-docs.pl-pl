---
title: Informacje o dzieleniu zmian Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej na temat zmian wprowadzonych w protokołach usługi Azure AD, które mogą mieć wpływ na aplikację.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38383685f74020f5208d42df4428f896931fbe2a
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931782"
---
# <a name="whats-new-for-authentication"></a>Co nowego w uwierzytelnianiu? 

>Otrzymuj powiadomienia o aktualizacjach na tej stronie. Po prostu Dodaj [ten adres URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) do czytnika kanału informacyjnego RSS.

System uwierzytelniania zmienia i dodaje funkcje na bieżąco w celu poprawy bezpieczeństwa i zgodności ze standardami. Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje o następujących szczegółach:

- Najnowsze funkcje
- Znane problemy
- Zmiany protokołu
- Przestarzałe funkcje

> [!TIP] 
> Ta strona jest regularnie aktualizowana, więc często pojawia się. O ile nie zaznaczono inaczej, te zmiany są wprowadzane tylko dla nowo zarejestrowanych aplikacji.  

## <a name="upcoming-changes"></a>Nadchodzące zmiany

2019 sierpnia: Wymuś semantykę POST zgodnie z regułami analizowania adresów URL — zduplikowane parametry spowodują wyzwolenie błędu, cudzysłowy między parametrami nie będą już ignorowane, a [towar](https://www.w3.org/International/questions/qa-byte-order-mark) został zignorowany.

## <a name="july-2019"></a>Lipiec 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny tylko do aplikacji dla aplikacji z jedną dzierżawą są wystawiane tylko wtedy, gdy aplikacja kliencka istnieje w dzierżawie zasobu

**Data wprowadzenia**: 26 lipca 2019

**Wpływ na punkty końcowe**: Zarówno [wersja 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) , jak i [2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Wpływ na protokół**: [Poświadczenia klienta (tokeny tylko dla aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Nastąpiła zmiana zabezpieczeń na żywo 26 lipca, która zmienia sposób, w jaki są wydawane tokeny tylko dla aplikacji (za pośrednictwem przyznanych poświadczeń klienta). Wcześniej aplikacje mogły uzyskać tokeny wywołujące dowolną inną aplikację, niezależnie od obecności w dzierżawie lub rolach, które zostały wysłane do danej aplikacji.  To zachowanie zostało zaktualizowane, tak aby w przypadku zasobów (nazywanych czasem interfejsem API sieci Web) ustawić jedną dzierżawę (domyślnie), aplikacja kliencka musi znajdować się w dzierżawie zasobów.  Należy zauważyć, że istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana, a aplikacje nadal powinny przeprowadzać własne testy autoryzacji, `roles` aby upewnić się, że występuje żądanie i zawiera oczekiwaną wartość dla interfejsu API.

Komunikat o błędzie dla tego scenariusza jest obecnie stanem: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Aby rozwiązać ten problem, użyj środowiska zgody administratora, aby utworzyć nazwę główną usługi aplikacji klienta w dzierżawie, lub utwórz ją ręcznie.  To wymaganie zapewnia, że dzierżawca udzielił uprawnienia aplikacji do działania w ramach dzierżawy.  

#### <a name="example-request"></a>Przykładowe żądanie

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`W tym przykładzie dzierżawca zasobów (Urząd) to contoso.com, aplikacja zasobów jest aplikacją jednodostępną o nazwie `gateway.contoso.com/api` dzierżawcy contoso, a `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`aplikacja kliencka.  Jeśli aplikacja kliencka ma nazwę główną usługi w ramach Contoso.com, to żądanie może być kontynuowane.  W przeciwnym razie żądanie zakończy się niepowodzeniem z błędem powyżej.  

Jeśli jednak aplikacja bramy contoso była aplikacją z wieloma dzierżawcami, żądanie będzie kontynuowane bez względu na to, czy aplikacja kliencka ma nazwę główną usługi w Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Identyfikatory URI przekierowania mogą teraz zawierać parametry ciągu zapytania

**Data wprowadzenia**: 22 lipca 2019 r.

**Wpływ na punkty końcowe**: Zarówno wersja 1.0, jak i 2.0

**Wpływ na protokół**: Wszystkie przepływy

Na [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI przekierowania (odpowiedź) przy użyciu statycznych parametrów zapytania https://contoso.com/oauth2?idp=microsoft) (na przykład w przypadku żądań OAuth 2,0).  Dynamiczne identyfikatory URI przekierowania są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do zachowywania informacji o stanie w ramach żądania uwierzytelniania — w tym celu `state` należy użyć parametru.

Statyczny parametr zapytania podlega dopasowywaniu ciągów do identyfikatorów URI przekierowania, takich jak jakakolwiek inna część identyfikatora URI przekierowania — Jeśli żaden ciąg nie jest zarejestrowany, który jest zgodny z identyfikatorem redirect_uri identyfikatora URI, żądanie zostanie odrzucone.  Jeśli identyfikator URI zostanie znaleziony w rejestracji aplikacji, cały ciąg zostanie użyty do przekierowania użytkownika, łącznie z parametrem zapytania statycznego. 

Należy pamiętać, że w tej chwili (koniec lipca 2019) środowisko rejestracji aplikacji w Azure Portal nadal blokuje parametry zapytania.  Można jednak ręcznie edytować manifest aplikacji w celu dodania parametrów zapytania i przetestowania go w aplikacji.  


## <a name="march-2019"></a>Marzec 2019

### <a name="looping-clients-will-be-interrupted"></a>Zapętlenie klientów zostanie przerwane

**Data wprowadzenia**: 25 marca 2019

**Wpływ na punkty końcowe**: Zarówno wersja 1.0, jak i 2.0

**Wpływ na protokół**: Wszystkie przepływy

Aplikacje klienckie mogą czasami niedziałać, wydając setki tego samego żądania logowania w krótkim czasie.  Te żądania mogą się nie powieść, ale wszystkie mają wpływ na słabe środowisko użytkownika i zwiększone obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i redukując dostępność dostawcy tożsamości.  Te aplikacje działają poza granicami normalnego użycia i należy je zaktualizować, aby działały prawidłowo.  

Klienci, którzy wydają zduplikowane żądania wiele razy, `invalid_grant` będą mogli `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`wysłać błąd:. 

Większość klientów nie będzie musiała zmienić zachowania, aby uniknąć tego błędu.  Ten błąd będzie miał wpływ tylko na niepoprawnie skonfigurowanych klientów (bez buforowania tokenu lub tych, które są już wyświetlane).  Klienci są śledzoni lokalnie na poszczególnych wystąpieniach (za pośrednictwem pliku cookie) na następujących czynnikach:

* Wskazówka użytkownika, jeśli istnieje

* Żądane zakresy lub zasoby

* Identyfikator klienta

* Identyfikator URI przekierowania

* Typ i tryb odpowiedzi

Aplikacje wykonujące wiele żądań (15 +) w krótkim czasie (5 minut) otrzymają `invalid_grant` komunikat o błędzie z informacją, że są zapętlenia.  Żądane tokeny mają wystarczająco długie okresy istnienia (co najmniej 10 minut, domyślnie 60 minut), więc powtarzające się żądania w tym okresie są zbędne.  

Wszystkie aplikacje powinny być `invalid_grant` obsługiwane przez wyświetlanie interakcyjnego monitu, a nie w trybie dyskretnym.  Aby uniknąć tego błędu, klienci powinni upewnić się, że prawidłowo buforują otrzymywane tokeny.


## <a name="october-2018"></a>Październik 2018 r.

### <a name="authorization-codes-can-no-longer-be-reused"></a>Nie można już ponownie używać kodów autoryzacji

**Data wprowadzenia**: 15 listopada 2018 r.

**Wpływ na punkty końcowe**: Zarówno wersja 1.0, jak i 2.0

**Wpływ na protokół**: [Przepływ kodu](v2-oauth2-auth-code-flow.md)

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Każda nowa aplikacja, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth, spowoduje wystąpienie błędu invalid_grant.

Aby uzyskać więcej informacji na temat tokenów odświeżania, zobacz [odświeżanie tokenów dostępu](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  W przypadku używania biblioteki ADAL lub MSAL jest to obsługiwane przez bibliotekę — Zastąp drugie wystąpienie elementu "AcquireTokenByAuthorizationCodeAsync" elementem "AcquireTokenSilentAsync". 

## <a name="may-2018"></a>Maj 2018 r.

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Nie można używać tokenów identyfikatora dla przepływu OBO

**Data**: 1 maja 2018

**Wpływ na punkty końcowe**: Zarówno wersja 1.0, jak i 2.0

Objęte **Protokoły**: Niejawny przepływ i [przepływ OBO](v1-oauth2-on-behalf-of-flow.md)

Od 1 maja 2018 id_tokens nie można użyć jako potwierdzenia w przepływie OBO dla nowych aplikacji. Tokeny dostępu należy zamiast tego używać w celu zabezpieczania interfejsów API, nawet między klientem a warstwą środkową tej samej aplikacji. Aplikacje zarejestrowane przed 1 maja 2018 będą nadal działać i mogą być w stanie nawiązać wymianę id_tokens z tokenem dostępu. Jednak ten wzorzec nie jest uważany za najlepsze rozwiązanie.

Aby obejść tę zmianę, można wykonać następujące czynności:

1. Utwórz internetowy interfejs API dla aplikacji z co najmniej jednym zakresem. Ten jawny punkt wejścia umożliwi dokładniejszą kontrolę i bezpieczeństwo.
1. W manifeście aplikacji, w [Azure Portal](https://portal.azure.com) lub [portalu rejestracji aplikacji](https://apps.dev.microsoft.com), upewnij się, że aplikacja może wystawiać tokeny dostępu za pośrednictwem niejawnego przepływu. Jest to kontrolowane przez `oauth2AllowImplicitFlow` klucz.
1. Gdy aplikacja kliencka żąda id_token za pośrednictwem `response_type=id_token`programu, należy również zażądać tokenu dostępu (`response_type=token`) dla internetowego interfejsu API utworzonego powyżej. W tym przypadku, podczas korzystania z punktu końcowego `scope` v 2.0, parametr powinien `api://GUID/SCOPE`wyglądać podobnie do. W punkcie końcowym `resource` v 1.0 parametr powinien być identyfikatorem URI aplikacji interfejsu API sieci Web.
1. Przekaż ten token dostępu do warstwy środkowej zamiast id_token.  
