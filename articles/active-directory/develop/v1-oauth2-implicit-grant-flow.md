---
title: Opis protokołu OAuth2 niejawne udzielić przepływ w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat usługi Azure Active Directory implementacja protokołu oauth2 niejawne udzielić przepływ, oraz czy jest odpowiedni dla danej aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe0ee8021ae7e70654a161e37d072195bbc035f
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545263"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Opis przepływu przyznawanie niejawne protokołu OAuth2 w usłudze Azure Active Directory (AD)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Przyznawanie niejawne protokołu OAuth2 jest odpowiedzialne za są grant najdłuższy listę obawy związane z bezpieczeństwem w specyfikacji OAuth2. I jeszcze, to podejście implementowany przez ADAL JS i jeden, zaleca się podczas pisania aplikacji SPA. Co zapewnia? Jest przedmiotem wady i zalety: i jak okazuje się przyznawania niejawnego jest najlepszym rozwiązaniem, które można wykonywać w przypadku aplikacji korzystających z interfejsu API sieci Web, przy użyciu języka JavaScript w przeglądarce.

## <a name="what-is-the-oauth2-implicit-grant"></a>Przyznawanie niejawne protokołu OAuth2 co to jest?

Quintessential [przyznawania kodu autoryzacji OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) jest autoryzację, który używa dwa oddzielne punkty końcowe. Punkt końcowy autoryzacji jest używany w fazie interakcji użytkownika, co skutkuje kod autoryzacji. Punkt końcowy tokenu jest następnie używany przez klienta kodu dla tokenu dostępu, a często także tokenu odświeżania do wymiany. Aplikacje sieci Web są wymagane do przedstawienia poświadczeń własnych aplikacji do punktu końcowego tokenu, tak, aby serwer autoryzacji można uwierzytelnić klienta.

[Przyznawanie niejawne protokołu OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) jest wariant innych przydziałów autoryzacji. Umożliwia klienta w celu uzyskania tokenu dostępu (i id_token, korzystając z [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) bezpośrednio z punktu końcowego autoryzacji bez nawiązywania kontaktu z punktu końcowego tokenu, ani uwierzytelniania klienta. Ten wariant zaprojektowano dla aplikacji uruchamianych w przeglądarce sieci Web oparte na języku JavaScript: w pierwotną specyfikację OAuth2 tokeny są zwracane w fragmentu identyfikatora URI. Który udostępnia tokenu usługi bits do kodu JavaScript w kliencie, ale gwarantuje, że nie będą uwzględniane w przekierowuje do serwera. Zwracanie tokenów za pośrednictwem przeglądarki przekierowuje bezpośrednio z punktu końcowego autoryzacji. Ma tę zaletę, eliminując wszelkie wymagania dotyczące wielu wywołań pochodzenia, które są niezbędne, jeśli wymagane jest wprowadzenie do kontaktowania się z punktu końcowego tokenu aplikacji JavaScript.

Ważną cechą przyznawanie niejawne protokołu OAuth2 jest fakt, że takie przepływy tokenów odświeżania nigdy nie jest zwracana do klienta. Następna sekcja pokazuje, jak nie jest to konieczne, a w rzeczywistości wynosi problem z zabezpieczeniami.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze przyznawanie niejawne protokołu OAuth2

Specyfikację OAuth2 deklaruje, że przyznawanie niejawne został zaprojektowany na włączyć agenta użytkownika aplikacji, to znaczy aplikacji JavaScript wykonywania w przeglądarce. Definiujące cechy charakterystyczne dla takich aplikacji jest, że kod JavaScript jest używany do uzyskiwania dostępu do zasobów serwera (zwykle internetowego interfejsu API) i w związku z tym aktualizowania interfejsu użytkownika aplikacji. Reakcji aplikacji, takich jak usługi Gmail lub programu Outlook Web Access: po wybraniu wiadomości ze skrzynki odbiorczej panel wizualizacji komunikatów zmian do wyświetlenia nowe zaznaczenie w pozostałej części strony pozostaje niezmieniona. Cecha ta jest w przeciwieństwie do tradycyjnych przekierowania aplikacji opartych na sieci Web, gdzie każdy interakcja użytkownika powoduje odświeżenie strony Cała strona i renderowania pełnej stronie w odpowiedzi na nowy serwer.

Aplikacje, które podejścia języku JavaScript do jego extreme noszą nazwę jednej strony aplikacji lub aplikacji jednostronicowych. Chodzi o to, że te aplikacje tylko obsługiwać początkowej strony HTML i JavaScript skojarzona z wszystkie kolejne interakcje wykonania prowadzone przez wywołania interfejsu API sieci Web przy użyciu języka JavaScript. Jednak podejścia hybrydowych, gdzie aplikacja jest przede wszystkim, oparte na odświeżenie strony, ale wykonuje okazjonalne wywołania JS, nie są niczym niezwykłym — dyskusję na temat użycia niejawny przepływ jest odpowiedni dla osób, także.

Aplikacje oparte na przekierowaniu, zwykle bezpieczne ich żądania za pomocą plików cookie, ale które podejście nie działa także dla aplikacji JavaScript. Pliki cookie działać wyłącznie względem domeny, do których one zostały wygenerowane, gdy wywołania języka JavaScript, mogą być kierowane do innych domen. W rzeczywistości, które często jest to możliwe: reakcji aplikacji, wywoływanie interfejsu API Microsoft Graph, interfejs API pakietu Office, interfejsu API platformy Azure — wszystkie znajdujące się poza domeną, w którym aplikacja jest obsługiwany. Rosnący trend dla aplikacji JavaScript jest nie wewnętrznej bazy danych, jednostki uzależnionej 100% na innej interfejsów API sieci Web, aby zaimplementować ich funkcji biznesowych.

Obecnie preferowaną metodą ochrony wywołania internetowego interfejsu API jest użycie podejście tokenu elementu nośnego protokołu OAuth2, gdzie każde wywołanie towarzyszy token dostępu protokołu OAuth2. Interfejs API sieci Web sprawdza przychodzący token dostępu i, jeśli zostaną znalezione w nim niezbędne zakresów, udziela uprawnień dostępu do żądanej operacji. Niejawny przepływ oferuje wygodny mechanizm dla aplikacji JavaScript umożliwiające uzyskanie tokenów dostępu do internetowego interfejsu API, oferuje wiele korzyści, w odniesieniu do plików cookie:

* Tokenów można wiarygodnie uzyskać bez potrzeby wielu wywołań pochodzenia — obowiązkowe rejestracji przekierowania, którego identyfikator URI, do którego są zwracane tokeny gwarantuje, że tokeny nie jest przesunięty
* Aplikacje języka JavaScript, mogą otrzymać dowolną liczbę tokenów dostępu, zgodnie z zapotrzebowaniem, dla liczby interfejsów API sieci Web docelowych — bez żadnych ograniczeń dotyczących domen
* Funkcje języka HTML5, takie jak sesji lub lokalny magazyn Przyznaj pełną kontrolę nad buforowaniem tokena i zarządzanie okresem istnienia plików cookie zarządzania jest nieprzezroczysta dla aplikacji
* Tokeny dostępu nie są podatny na ataki, Cross-site żądania międzywitrynowego (CSRF)

Przyznawanie niejawne przepływ nie wystawia tokeny odświeżania, przede wszystkim ze względów bezpieczeństwa. Token odświeżania nie jest jak wąskiego zakresu jako tokenów dostępu, udzielanie znacznie większe możliwości, dlatego inflicting znacznie więcej szkód w przypadku, gdy jest wyciekiem. W niejawnym przepływem tokeny są dostarczane w adresie URL, więc ryzyko przechwycenia jest większa niż w przyznawania kodu autoryzacji.

Jednak aplikacji w języku JavaScript ma inny mechanizm dyspozycji odnowienia tokenów dostępu bez wielokrotnie monitowania użytkownika o poświadczenia. Aplikacja może użyć ukryty element iframe do wykonania nowego żądania tokenu względem punktu końcowego autoryzacji usługi Azure AD: tak długo, jak przeglądarka nadal ma aktywnej sesji (odczytu: ma plik cookie sesji) względem domeny usługi Azure AD można żądanie uwierzytelnienia pomyślnie wystąpić, bez konieczności interakcji z użytkownikiem.

Ten model umożliwia aplikacji JavaScript niezależnie odnowić tokenów dostępu, a nawet uzyskać nowe dla nowego interfejsu API (pod warunkiem, że użytkownik wyraził zgodę wcześniej dla nich). Umożliwia to uniknięcie dodano obciążeń, pobieranie, utrzymywania i chronienia artefaktu o wysokiej wartości, takich jak token odświeżania. Artefakt, który sprawia, że dyskretnej odnowienie jest możliwe, pliku cookie sesji usługi Azure AD jest zarządzany poza aplikacją. Inną zaletą tego podejścia jest użytkownikiem można wylogować się z usługi Azure AD przy użyciu dowolnej aplikacji zalogowali się do usługi Azure AD, uruchamiania we wszystkich kartach przeglądarki. Powoduje to usunięcie tego pliku cookie sesji usługi Azure AD i aplikacji JavaScript automatycznie spowoduje utratę możliwości odnowienia tokenów dla podpisanych się użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Przyznawanie niejawne nadaje się do mojej aplikacji?

Przyznawanie niejawne przedstawia zagrożenia więcej niż inne przyznaje i obszarów, należy zwrócić uwagę na to dobrze udokumentowane (na przykład [niewłaściwe korzystanie z tokenu dostępu do personifikacji właściciela zasobu w niejawny przepływ] [ OAuth2-Spec-Implicit-Misuse]i [Model zagrożeń protokołu OAuth 2.0 i zagadnienia dotyczące zabezpieczeń][OAuth2-Threat-Model-And-Security-Implications]). Jednak wyższy profil ryzyka jest głównie fakt, że jest przeznaczona do umożliwiają aplikacjom, które są wykonywane active kodu, obsługiwane przez zasób zdalny do przeglądarki. Jeśli planowane jest architektura SPA ma żadnych składników wewnętrznej bazy danych lub zamierzane było wywołanie interfejsu API sieci Web, przy użyciu języka JavaScript, zalecane jest użycie niejawny przepływ dla tokenu.

Jeśli aplikacja jest klient natywny, niejawny przepływ jest doskonałym rozwiązaniem. Brak pliku cookie sesji usługi Azure AD w kontekście klient natywny pozbawia aplikacji oznacza, że obsługi długotrwałych sesji. Oznacza to aplikacja wielokrotnie będzie monitował użytkownika podczas uzyskiwania tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację internetową, która obejmuje wewnętrznej bazy danych i korzystanie z interfejsu API z poziomu kodu zaplecza niejawny przepływ również nie jest dobrym rozwiązaniem. Inne przyznaje dają znacznie większe możliwości. Na przykład przyznanie poświadczenia klienta protokołu OAuth2 zapewnia możliwość uzyskiwania tokenów, które odzwierciedlają uprawnienia przypisane do aplikacji, w przeciwieństwie do delegacji użytkownika. Oznacza to, że klient ma możliwość utrzymywania programowy dostęp do zasobów, nawet gdy użytkownik nie jest aktywnie zaangażowane w sesji i tak dalej. Nie, ale takie przyznaje zapewniają wyższy gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie przesyłania za pośrednictwem przeglądarki użytkownika, nie o podwyższonym ryzyku są zapisywane w historii przeglądania i tak dalej. Aplikacja kliencka można również wykonać silne uwierzytelnianie, gdy żądania tokenu.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać pełną listę zasobów dla deweloperów dla protokołów i autoryzacji OAuth2 podejmowania przepływów przez usługę Azure AD, w tym informacje dotyczą [— przewodnik dewelopera usługi Azure AD][AAD-Developers-Guide]
* Zobacz [sposobu integrowania aplikacji z usługą Azure AD] [ ACOM-How-To-Integrate] dla głębokości dodatkowe na temat procesu integracji aplikacji.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
