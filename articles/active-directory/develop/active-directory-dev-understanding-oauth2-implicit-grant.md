---
title: Opis protokołu OAuth2 niejawne Przyznaj przepływu w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się więcej informacji na temat wdrożenia usługi Azure Active Directory OAuth2 niejawne Przyznaj przepływu, oraz czy jest odpowiednie dla aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263a093d5cf4b48ed1dadd4a288e548065ddf282
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Opis przepływu niejawne Przyznaj OAuth2 w usłudze Azure Active Directory (AD)
Przyznanie niejawne OAuth2 jest odpowiedzialne za jest przyznanie z najdłuższym lista problemy z zabezpieczeniami w specyfikacji protokołu OAuth2. I jeszcze, który jest podejście przez ADAL JS i które zalecamy podczas pisania aplikacji JEDNOSTRONICOWEJ. Co daje? Jest to kwestia kompromisów: i jak okaże się niejawne Przyznaj jest najlepszym podejściem może prowadzić do aplikacji, które wykorzystują interfejs API sieci Web za pośrednictwem kodu JavaScript w przeglądarce.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co to jest przyznanie niejawne OAuth2?
Quintessential [udzielania kodu autoryzacji OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) jest udzielania autoryzacji, które używa dwóch osobnych punktów końcowych. Punkt końcowy autoryzacji jest używany na etapie interakcji użytkownika, co powoduje kod autoryzacji. Token punktu końcowego jest następnie używany przez klienta do wymiany kod tokenu dostępu, a często również token odświeżania. Aplikacje sieci Web są wymagane do prezentowania własnymi poświadczeniami aplikacji do punktu końcowego tokenu, aby serwer autoryzacji może uwierzytelnić klienta.

[OAuth2 niejawne Przyznaj](https://tools.ietf.org/html/rfc6749#section-1.3.2) jest wariant innych przyznaje autoryzacji. Umożliwia klientowi Uzyskaj token dostępu (i żądaniu, korzystając z [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) bezpośrednio z punktu końcowego autoryzacji bez kontaktowania się z punktem końcowym tokenów ani uwierzytelniania klienta. Ten wariant zostało zaprojektowane specjalnie dla aplikacji działających w przeglądarce sieci Web oparte na języku JavaScript: w oryginalnym specyfikację OAuth2, tokeny są zwracane w fragmentu identyfikatora URI. Dzięki temu tokenu usługi bits dostępne z kodu JavaScript w kliencie, ale gwarantuje, że nie będą uwzględniane w przekierowuje do serwera. Zwracanie tokeny za pośrednictwem przeglądarki przekierowuje bezpośrednio z punktu końcowego autoryzacji. Ma również zaletą wyeliminowanie wszelkie wymagania dotyczące wielu wywołań źródła, które są niezbędne, jeśli aplikacja JavaScript jest wymagany do kontaktowania się z punktu końcowego tokena.

Ważną cechą grant niejawne OAuth2 jest fakt, że takie przepływa tokenów odświeżania nigdy nie zwracany do klienta. Jak zostanie wyświetlone w następnej sekcji, która nie jest naprawdę konieczne i faktycznie jest problem z zabezpieczeniami.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenariusze odpowiedniego grant niejawne OAuth2
Jak specyfikację OAuth2 sam deklaruje, niejawne Przyznaj została opracowana na potrzeby aplikacji, agenta użytkownika — to znaczy aplikacji JavaScript wykonywania w przeglądarce. Definiowanie cech takich aplikacji jest, że kod JavaScript jest używana do uzyskiwania dostępu do zasobów serwera (zwykle interfejs API sieci Web) i w związku z tym aktualizowania aplikacji środowiska użytkownika. Pomyśl o aplikacjach, takich jak usługi Gmail lub Outlook Web Access: po wybraniu wiadomości ze skrzynki odbiorczej panelu wizualizacji komunikat zmiany do wyświetlenia nowego zaznaczenia w pozostałej części strony pozostaje niezmieniona. W przeciwieństwie do tradycyjnych przekierowania aplikacji opartych na sieci Web, to, gdzie każdej interakcji użytkownika powoduje odświeżenie strony całą stronę i renderowania stron pełnej odpowiedzi na nowy serwer.

Aplikacje, które przyjmują podejście oparty na języku JavaScript do jego extreme są nazywane aplikacje jednostronicowe lub źródła: chodzi o to, że te aplikacje tylko obsługiwać początkowej strony HTML i JavaScript skojarzone z wszystkich kolejnych interakcjach prowadzone przez interfejs API sieci Web wywołania są wykonywane za pośrednictwem kodu JavaScript. Jednak hybrydowego podejścia, gdzie aplikacja jest przeważnie oparte na odświeżenie strony, ale wykonuje okazjonalne wywołania JS, nie są rzadko — omówienie użycia niejawnego przepływu dotyczy tych również.

Aplikacje oparte na przekierowaniu zwykle bezpieczne ich żądania za pomocą plików cookie, jednak, które podejście nie działa także dla aplikacji JavaScript. Pliki cookie są prawidłowe tylko w domenie, do których one zostały wygenerowane, gdy wywołania języka JavaScript mogą być kierowane do innych domen. W rzeczywistości, który często jest to możliwe: reakcji aplikacji wywoływanie interfejsu API programu Graph firmy Microsoft, interfejsu API usługi Office, interfejsu API Azure — wszystkie znajdującej się poza domeną, w którym aplikacja jest obsługiwana. Rosnącą dla aplikacji JavaScript jest nie wewnętrznej bazy danych na wszystkich jednostki uzależnionej 100% na 3 strona interfejsów API sieci Web do zaimplementowania ich funkcję biznesową.

Obecnie preferowaną metodą ochrony wywołania interfejsu API sieci Web jest użycie podejście tokenu elementu nośnego OAuth2, w których każdego wywołania towarzyszy token dostępu OAuth2. Interfejs API sieci Web sprawdza przychodzący token dostępu i, jeśli zostaną znalezione w nim niezbędne zakresy, jego udziela dostępu do żądanej operacji. Przepływu niejawnego oferuje wygodny mechanizm aplikacji JavaScript umożliwiające uzyskanie tokenów dostępu do interfejsu API sieci Web oferuje wiele korzyści w odniesieniu do plików cookie:

* Tokeny niezawodnie uzyskuje się bez konieczności wielu wywołań pochodzenia — obowiązkowe rejestracji przekierowania, który identyfikatora URI, do którego są zwracane tokeny gwarantuje, że nie są przemieszczeniu tokenów
* Aplikacji JavaScript można uzyskać tyle tokenów dostępu potrzebują, na tyle interfejsów API sieci Web ich elementami docelowymi — bez żadnych ograniczeń w domenach
* Funkcje HTML5, takie jak sesji lub lokalnego magazynu przyznać pełną kontrolę nad buforowaniem tokena i zarządzanie okresem istnienia plików cookie zarządzania jest nieprzezroczysta dla aplikacji
* Tokeny dostępu nie są podatne na Cross-site (CSRF) fałszerstwie żądania

Przepływ niejawne Przyznaj nie wystawia tokeny odświeżania, przede wszystkim ze względów bezpieczeństwa. Token odświeżania nie jest w ściśle zakresu jako tokenów dostępu, udzielanie znacznie więcej mocy dlatego inflicting znacznie więcej szkód w przypadku, gdy jest wyciekiem. W procesie niejawne tokeny są dostarczane w adresie URL, dlatego ryzyko przechwycenia wyższe niż w udzielania kodu autoryzacji.

Jednak pamiętaj, że aplikacja JavaScript ma inny mechanizm dyspozycji do odnawiania tokenów dostępu bez wielokrotnie monitowania użytkownika o poświadczenia. Aplikacji za pomocą ukryte iframe można wykonywać nowe żądania tokenu względem punktu końcowego autoryzacji usługi Azure AD: tak długo, jak przeglądarka jest nadal aktywna sesja (do odczytu: ma plik cookie sesji) w domenie usługi Azure AD można żądanie uwierzytelnienia pomyślnie wystąpić bez konieczności interakcji z użytkownikiem.

Ten model umożliwia aplikacji JavaScript niezależnie odnowić tokenów dostępu, a nawet uzyskać nowe nowego interfejsu API (pod warunkiem, że użytkownik wcześniej zgodę dla nich. Dzięki temu można uniknąć dodano konieczności pobierania, utrzymywaniem i ochrona artefaktów wysokiej wartości, takie jak token odświeżania. Artefaktu, co umożliwia dyskretnej odnowienia, pliku cookie sesji usługi Azure AD, odbywa się poza aplikacji. Zaletą tej metody jest użytkownika można wylogować się z usługą Azure AD przy użyciu aplikacji rejestracji w usłudze Azure AD w tych kartach przeglądarki. Spowoduje to usunięcie pliku cookie sesji usługi Azure AD i aplikacji JavaScript automatycznie utracisz możliwość odnowić tokeny podpisane się użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Niejawne Przyznaj nadaje się do mojej aplikacji?
Niejawne Przyznaj niesie ze sobą ryzyka więcej niż inne przyznaje i należy zwrócić uwagę na obszary są dobrze udokumentowane. Na przykład [nieprawidłowe użycie elementu Token dostępu do personifikacji właściciela zasobu w przepływu niejawnego] [ OAuth2-Spec-Implicit-Misuse] i [modelu zagrożeń OAuth 2.0 i zagadnienia dotyczące zabezpieczeń] [ OAuth2-Threat-Model-And-Security-Implications]). Wyższy profil ryzyka jest jednak przede wszystkim fakt, że jest przeznaczona na potrzeby aplikacji, których wykonanie kodu active, obsługiwanych przez zasób zdalny do przeglądarki. Jeśli planujesz architekturę SPA nie składniki wewnętrznej bazy danych lub zamierzane było wywołanie interfejsu API sieci Web, za pośrednictwem kodu JavaScript, zalecane jest użycie przepływu niejawnego uzyskania tokenu.

Jeśli aplikacja jest klientami, niejawnego przepływu nie stanowi doskonałe dopasowanie. Brak pliku cookie sesji usługi Azure AD w kontekście klientami pozbawia aplikacji w sposób obsługi długotrwałe sesji. Co oznacza aplikacji wielokrotnie będzie Monituj użytkownika podczas uzyskiwania tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację sieci Web, w tym wewnętrznej bazie danych i korzystanie z interfejsu API z jego kod zaplecza przepływu niejawnego również nie jest odpowiedni. Inne przyznaje nadaj znacznie więcej możliwości. Na przykład przyznania poświadczeń klienta OAuth2 zapewnia możliwości uzyskiwania tokenów odzwierciedlające uprawnienia przypisane do aplikacji, w przeciwieństwie do delegacji użytkownika. Oznacza to, że klient ma możliwość obsługi programowy dostęp do zasobów, nawet gdy użytkownik nie jest są aktywnie zaangażowane w sesji i tak dalej. Nie tylko który, ale takie przyznaje nadać wyższy gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie przesyłania za pośrednictwem przeglądarki użytkownika, nie ryzyka są zapisywane w historii przeglądania i tak dalej. Aplikacja kliencka można również wykonywać silnego uwierzytelniania podczas żądania tokenu.

## <a name="next-steps"></a>Kolejne kroki
* Aby zapoznać się z pełną listą zasoby dla deweloperów, protokołów i autoryzacji OAuth2 udzielić pomocy technicznej przepływów przez usługę Azure AD, takich jak informacje dotyczą [przewodnik dewelopera usługi Azure AD][AAD-Developers-Guide]
* Zobacz [Integrowanie aplikacji z usługą Azure AD] [ ACOM-How-To-Integrate] dla dodatkowych głębokość na proces integracji aplikacji.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
