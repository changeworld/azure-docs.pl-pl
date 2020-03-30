---
title: Opis niejawnego przepływu dotacji OAuth2 w usłudze Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się więcej o implementacji usługi Azure Active Directory niejawnego przepływu dotacji OAuth2 i czy jest odpowiedni dla aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154512"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Opis niejawnego przepływu dotacji OAuth2 w usłudze Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Dotację dorozumianą OAuth2 jest znany jako dotacja z najdłuższą listą obaw dotyczących bezpieczeństwa w specyfikacji OAuth2. A jednak jest to podejście zaimplementowane przez ADAL JS i to, które zalecamy podczas pisania aplikacji SPA. Co daje? To wszystko kwestia kompromisów: i jak się okazuje, niejawna dotacja jest najlepszym podejściem, które możesz realizować dla aplikacji, które zużywają web API za pośrednictwem JavaScript z przeglądarki.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co to jest dotacja dorozumiana OAuth2?

Kwintesencją [przyznania kodu autoryzacji OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) jest udzielenie autoryzacji, która używa dwóch oddzielnych punktów końcowych. Punkt końcowy autoryzacji jest używany dla fazy interakcji użytkownika, co powoduje kod autoryzacji. Punkt końcowy tokenu jest następnie używany przez klienta do wymiany kodu dla tokenu dostępu, a często token odświeżania, jak również. Aplikacje sieci Web są wymagane do przedstawienia własnych poświadczeń aplikacji do punktu końcowego tokenu, dzięki czemu serwer autoryzacji może uwierzytelnić klienta.

[Dotację do dorozumianą OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) jest wariantem innych dotacji autoryzacji. Umożliwia klientowi uzyskanie tokenu dostępu (i id_token, podczas korzystania z [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) bezpośrednio z punktu końcowego autoryzacji, bez kontaktowania się z punktem końcowym tokenu ani uwierzytelniania klienta. Ten wariant został zaprojektowany dla aplikacji opartych na języku JavaScript uruchomionych w przeglądarce sieci Web: w oryginalnej specyfikacji OAuth2 tokeny są zwracane w fragmencie identyfikatora URI. To sprawia, że bity tokenu są dostępne dla kodu JavaScript w kliencie, ale gwarantuje, że nie zostaną uwzględnione w przekierowaniach do serwera. W OAuth2 niejawne udzielić, punkt końcowy autoryzacji wystawia tokeny dostępu bezpośrednio do klienta przy użyciu identyfikatora URI przekierowania, który został wcześniej dostarczony. Ma również tę zaletę, że eliminuje wszelkie wymagania dotyczące wywołań między źródłami, które są niezbędne, jeśli aplikacja JavaScript jest wymagana do skontaktowania się z punktem końcowym tokenu.

Ważną cechą dotacji niejawnej OAuth2 jest fakt, że takie przepływy nigdy nie zwracają tokenów odświeżania do klienta. W następnej sekcji pokazano, jak nie jest to konieczne i w rzeczywistości będzie to problem z zabezpieczeniami.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze dla dotacji dorozumianej OAuth2

Specyfikacja OAuth2 oświadcza, że niejawna dotacja została opracowana w celu umożliwienia aplikacji agenta użytkownika — to znaczy aplikacji JavaScript wykonywanych w przeglądarce. Cechą definiującą takich aplikacji jest to, że kod JavaScript jest używany do uzyskiwania dostępu do zasobów serwera (zazwyczaj interfejsu API sieci Web) i do odpowiedniego aktualizowania środowiska użytkownika aplikacji. Pomyśl o aplikacjach takich jak Gmail lub Outlook Web Access: po wybraniu wiadomości ze skrzynki odbiorczej tylko panel wizualizacji wiadomości zmienia się, aby wyświetlić nowe zaznaczenie, podczas gdy reszta strony pozostaje niezmieniona. Ta cecha jest w przeciwieństwie do tradycyjnych aplikacji sieci Web opartych na przekierowaniu, gdzie każda interakcja użytkownika powoduje pełny powrót strony i renderowanie całej strony nowej odpowiedzi serwera.

Aplikacje, które przyjmują podejście oparte na języku JavaScript do jego skrajności, są nazywane aplikacjami jednostronicowymi lub umowami SPA. Chodzi o to, że te aplikacje obsługują tylko początkową stronę HTML i powiązany javascript, a wszystkie późniejsze interakcje są napędzane przez wywołania interfejsu API sieci Web wykonywane za pośrednictwem języka JavaScript. Jednak podejścia hybrydowe, gdzie aplikacja jest głównie postback-driven, ale wykonuje sporadyczne wywołania JS, nie są rzadkością — dyskusja na temat użycia przepływu niejawnego jest istotne dla tych, jak również.

Aplikacje oparte na przekierowaniach zazwyczaj zabezpieczają swoje żądania za pomocą plików cookie, jednak takie podejście nie działa równie dobrze w przypadku aplikacji JavaScript. Pliki cookie działają tylko na domenę, dla których zostały wygenerowane, podczas gdy połączenia JavaScript mogą być kierowane do innych domen. W rzeczywistości często tak będzie: pomyśl o aplikacjach wywołujących interfejs API programu Microsoft Graph, interfejs API pakietu Office, interfejs API platformy Azure — wszystkie przebywające poza domeną, z której jest obsługiwana aplikacja. Rosnącą tendencją dla aplikacji JavaScript jest brak zaplecza w ogóle, opierając się w 100% na interfejsach API sieci Web innych firm, aby zaimplementować swoją funkcję biznesową.

Obecnie preferowaną metodą ochrony wywołań do interfejsu API sieci Web jest użycie podejścia tokenu nośnika OAuth2, gdzie każde wywołanie jest towarzyszy token dostępu OAuth2. Interfejs API sieci Web sprawdza token dostępu przychodzącego i, jeśli znajdzie w nim niezbędne zakresy, udziela dostępu do żądanej operacji. Niejawny przepływ zapewnia wygodny mechanizm dla aplikacji JavaScript w celu uzyskania tokenów dostępu dla interfejsu API sieci Web, oferując liczne korzyści w odniesieniu do plików cookie:

* Tokeny można niezawodnie uzyskać bez konieczności wykonywania połączeń między źródłami – obowiązkowa rejestracja identyfikatora URI przekierowania, do którego tokeny są gwarancjami zwrotu, że tokeny nie są przemieszczane
* Aplikacje JavaScript mogą uzyskać tyle tokenów dostępu, ile potrzebują, dla tylu interfejsów API sieci Web, na które są kierowane — bez ograniczeń dotyczących domen
* Funkcje HTML5, takie jak sesja lub pamięć lokalna, zapewniają pełną kontrolę nad buforowaniem tokenów i zarządzaniem okresem istnienia, podczas gdy zarządzanie plikami cookie jest nieprzezroczyste dla aplikacji
* Tokeny dostępu nie są podatne na ataki fałszerstwa żądań między witrynami (CSRF)

Niejawny przepływ dotacji nie wystawia tokenów odświeżania, głównie ze względów bezpieczeństwa. Token odświeżania nie jest tak wąsko objęty jak tokeny dostępu, co daje znacznie więcej mocy, co powoduje znacznie więcej obrażeń w przypadku wycieku. W przepływie niejawnym tokeny są dostarczane w adresie URL, w związku z tym ryzyko przechwycenia jest wyższa niż w grant kodu autoryzacji.

Jednak aplikacja JavaScript ma inny mechanizm do dyspozycji do odnawiania tokenów dostępu bez wielokrotnego monitowania użytkownika o poświadczenia. Aplikacja może używać ukrytego elementu iframe do wykonywania nowych żądań tokenu względem punktu końcowego autoryzacji usługi Azure AD: tak długo, jak przeglądarka nadal ma aktywną sesję (przeczytaj: ma plik cookie sesji) względem domeny usługi Azure AD, żądanie uwierzytelniania może z powodzeniem występują bez konieczności interakcji z użytkownikiem.

Ten model daje aplikacji JavaScript możliwość niezależnego odnawiania tokenów dostępu, a nawet nabywania nowych dla nowego interfejsu API (pod warunkiem, że użytkownik wcześniej wyraził na nie zgodę). Pozwala to uniknąć dodatkowego obciążenia nabycia, konserwacji i ochrony artefaktu o wysokiej wartości, takiego jak token odświeżania. Artefakt, który umożliwia ciche odnawianie, plik cookie sesji usługi Azure AD, jest zarządzany poza aplikacją. Inną zaletą tego podejścia jest użytkownik może wylogować się z usługi Azure AD, przy użyciu dowolnej aplikacji zalogowanych do usługi Azure AD, uruchomionych na dowolnej karcie przeglądarki. Powoduje to usunięcie pliku cookie sesji usługi Azure AD, a aplikacja JavaScript automatycznie utraci możliwość odnawiania tokenów dla wylogowanego użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Czy dotacja niejawna jest odpowiednia dla mojej aplikacji?

Dotacja dorozumiana stanowi więcej ryzyka niż inne dotacje, a obszary, na które należy zwrócić uwagę, są dobrze udokumentowane (na przykład [Niewłaściwe użycie tokenu dostępu w celu personifikacji właściciela zasobu w przepływie niejawnym][OAuth2-Spec-Implicit-Misuse] i [oauth 2.0 Threat Model i zagadnienia dotyczące zabezpieczeń][OAuth2-Threat-Model-And-Security-Implications]). Jednak wyższy profil ryzyka jest w dużej mierze ze względu na fakt, że jest przeznaczony do włączenia aplikacji, które wykonują aktywny kod, obsługiwane przez zdalnego zasobu do przeglądarki. Jeśli planujesz architekturę SPA, nie masz składników wewnętrznej bazy danych lub zamierzasz wywołać interfejs API sieci Web za pośrednictwem języka JavaScript, zaleca się użycie niejawnego przepływu do pozyskiwania tokenów.

Jeśli aplikacja jest klientem macierzystym, niejawny przepływ nie jest doskonałym rozwiązaniem. Brak pliku cookie sesji usługi Azure AD w kontekście klienta macierzystego pozbawia aplikacji ze środków utrzymania długotrwałej sesji. Oznacza to, że aplikacja będzie wielokrotnie monitować użytkownika podczas uzyskiwania tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację sieci Web, która zawiera wewnętrznej bazy danych i korzystanie z interfejsu API z jego kodu wewnętrznej bazy danych, niejawny przepływ również nie jest dobrym rozwiązaniem. Inne dotacje dają znacznie więcej mocy. Na przykład poświadczeń klienta OAuth2 udzielić zapewnia możliwość uzyskania tokenów, które odzwierciedlają uprawnienia przypisane do samej aplikacji, w przeciwieństwie do delegowania użytkowników. Oznacza to, że klient ma możliwość utrzymania programowego dostępu do zasobów, nawet jeśli użytkownik nie jest aktywnie zaangażowany w sesję i tak dalej. Nie tylko to, ale takie dotacje dają wyższe gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie są przesyłane przez przeglądarkę użytkownika, nie ryzykują zapisania ich w historii przeglądarki i tak dalej. Aplikacja kliencka może również wykonywać silne uwierzytelnianie podczas żądania tokenu.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Jak zintegrować aplikację z usługą Azure AD, aby][ACOM-How-To-Integrate] uzyskać dodatkową szczegółową głębię procesu integracji aplikacji.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
