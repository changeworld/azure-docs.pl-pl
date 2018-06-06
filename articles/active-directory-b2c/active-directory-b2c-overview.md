---
title: Co to jest Azure Active Directory B2C? | Microsoft Docs
description: Więcej informacji dotyczących sposobu tworzenia i zarządzania logowania środowiska aplikacji przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 878d4da715308abf6dbe536ea0d1df2a68225179
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711318"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co to jest Azure Active Directory B2C?

Usługa Azure Active Directory (Azure AD) B2C jest usługą zarządzania tożsamościami, która pozwala na dostosowywanie i kontrolowanie sposobu, w jaki klienci tworzą konto, logują się i zarządzają swoimi profilami podczas korzystania z Twoich aplikacji. Obejmuje to aplikacje opracowane m.in. dla systemu iOS i Android oraz platformy .NET. Usługa Azure AD B2C umożliwia tych działań ochrony tożsamości klienta w tym samym czasie.

Można skonfigurować aplikacji zarejestrowanych w usłudze Azure AD B2C do wykonywania różnych akcji związanych z zarządzaniem tożsamościami. Przykłady to:

- Włącz klienta, aby zarejestrować i korzystać z zarejestrowaną aplikację
- Włącz klienta zarejestrowaniu zalogować się i rozpocząć korzystanie z aplikacji
- Włącz zarejestrowaniu klienta, aby edytować swój profil
- Włączanie uwierzytelniania wieloskładnikowego w aplikacji
- Włącz zarejestrować się i zaloguj się przy użyciu dostawcy tożsamości określonego klienta
- Udziel dostępu do interfejsów API, który możesz stworzyć aplikację
- Dostosowywanie wyglądu i działania obsługi rejestracji i logowania
- Zarządzanie sesjami pojedynczego logowania jednokrotnego dla aplikacji

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Co należy wziąć pod uwagę o przed rozpoczęciem korzystania z usługi Azure AD B2C

- Jak chcesz klienta do interakcji z mojej aplikacji?
- Co to jest środowisko interfejsu użytkownika, I chcesz zapewnić klientom?
- Dostawców tożsamości, które ma uświadomienie klientom, wybierz z we własnej aplikacji?
- Proces logowania wymaga dodatkowych interfejsów API do uruchomienia?

### <a name="customer-interaction"></a>Interakcji z klientem

Usługa Azure AD B2C obsługuje [OpenID Connect](https://openid.net/connect/) dla wszystkich klientów wykonywania. W implementacji usługi Azure AD B2C OpenID Connect aplikacji inicjuje przebieg tego użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD B2C. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość klienta.

Każda aplikacja, która używa usługi Azure AD B2C musi być zarejestrowana w dzierżawie usługi Azure AD B2C, przy użyciu portalu Azure. Proces rejestracji zbiera i przypisuje wartości do tej aplikacji. Wartości te obejmują identyfikator aplikacji, który unikatowo identyfikuje i Przekierowanie URI, który może służyć do kierowania odpowiedzi z powrotem do aplikacji.

Interakcja każda aplikacja obejmuje podobnego wzorca wysokiego poziomu:

1. Aplikacja przekierowuje klienta do uruchamiania zasady.
2. Klient wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token zabezpieczający.
4. Aplikacja używa tokenu zabezpieczającego, aby podjąć próbę uzyskania dostępu do chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki mogą się nieznacznie różnić od typu aplikacji, które tworzysz.

Usługa Azure AD B2C współdziała z dostawców tożsamości, klientów i innych systemach i z katalogu lokalnego w sekwencji, aby ukończyć zadanie tożsamości. Na przykład Zaloguj się klient, zarejestrować nowego klienta lub resetowania hasła. Platforma podstawowa, która ustanawia relację zaufania wieloosobową i wykonuje następujące czynności jest nazywany Framework obsługi tożsamości. Ta struktura i zasad (nazywanych również przebieg użytkownika lub zasady zaufania Framework) jawnie definiuje uczestników, działania protokołów i sekwencji kroki do wykonania.

Usługa Azure AD B2C chroni przed typu "odmowa usługi" i hasło do ataków na aplikacji na wiele sposobów. Azure AD B2C używa wykrywanie i zapobieganie technik, takich jak pliki cookie SYN i limity szybkości i połączenia, aby chronić zasoby przed atakami typu "odmowa usługi". Środki zaradcze jest również uwzględniony ataków siłowych hasła i słownikowymi hasła.

#### <a name="built-in-policies"></a>Wbudowane zasady

Każde żądanie, które są wysyłane do usługi Azure AD B2C określa zasady. Zasada steruje zachowaniem współdziałania aplikacji z usługi Azure AD B2C. Wbudowane zasady są wstępnie zdefiniowane dla najbardziej typowych zadań tożsamości, takich jak rejestrację, logowanie i edytowanie profilu.  Na przykład zasad rejestracji umożliwia kontrolowanie zachowania, konfigurując następujące ustawienia:

- Kont społecznościowych, które odbiorca może używać zalogowania się do aplikacji
- Dane zbierane od klientów, takie jak imię lub kod pocztowy
- Uwierzytelnianie wieloskładnikowe
- Wygląd i działanie wszystkie strony
- Informacje o zwrócony do aplikacji

#### <a name="custom-policies"></a>Zasady niestandardowe 

[Zasady niestandardowe](active-directory-b2c-overview-custom.md) są pliki konfiguracyjne, które definiują zachowanie Framework obsługi tożsamości w dzierżawie usługi Azure AD B2C. Zasady niestandardowe można edytować pełni wykonywanie wielu zadań. Zasady niestandardowe jest reprezentowany jako jeden lub kilka plików w formacie XML odwołujące się do siebie nawzajem w łańcuchu hierarchicznej. 

Wiele niestandardowych zasad o różnych typach mogą być używane w dzierżawie usługi Azure AD B2C, zgodnie z potrzebami i mogą być ponownie używane w aplikacjach. Tego rodzaju elastyczności umożliwia definiowanie i modyfikowanie środowiska tożsamości klienta z minimalnym lub bez wprowadzania zmian w kodzie. Zasady mogą służyć przez dodanie parametru zapytania specjalne do żądania uwierzytelniania HTTP.

Niestandardowe zasady mogą służyć do kontrolowania podróże użytkownika w następujący sposób:

- Definiowanie interakcji z interfejsów API w celu przechwycenia dodatkowych informacji, sprawdź oświadczeń dostarczanych przez klienta, wyzwalacza lub procesów zewnętrznych.
- Zmiana działania na podstawie oświadczeń z interfejsów API lub oświadczeń w katalogu takich jak *migrationStatus*.
- Każdy przepływ pracy nie jest objęty przez wbudowane zasady. Na przykład zbieranie więcej informacji o klienta podczas logowania w usługach i autoryzację Sprawdź dostęp do zasobu.

### <a name="identity-providers"></a>Dostawcy tożsamości

Dostawca tożsamości to usługa, która służy do uwierzytelniania tożsamości klienta i wystawia tokeny zabezpieczające. W usłudze Azure AD B2C można skonfigurować w dzierżawie, takich jak konta Microsoft, Facebook lub Amazon między innymi wielu dostawców tożsamości. 

Aby skonfigurować dostawcę tożsamości w dzierżawie usługi Azure AD B2C, należy zarejestrować identyfikator aplikacji lub identyfikator klienta i klucz tajny klienta lub hasła aplikacji dostawcy tożsamości, której można utworzyć. Ten identyfikator i hasło są następnie używane do konfigurowania aplikacji.

### <a name="user-interface-experience"></a>Środowisko interfejsu użytkownika

Większość zawartości HTML i CSS, które są prezentowane klientów można sterować. Za pomocą funkcji dostosowywania interfejsu użytkownika strony, można dostosować wygląd i działanie żadnych zasad. Można również utrzymać marki i wizualne spójności między aplikacji i usługi Azure AD B2C.

Azure AD B2C kodu w przeglądarce klienta, korzysta z podejścia nowoczesnych o nazwie udostępniania zasobów między źródłami (CORS). Najpierw należy określić adres URL w zasadach z dostosowana zawartość HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartość HTML, który jest ładowany z danego adresu URL, a następnie wyświetla strony do klienta.

Parametry można wysłać do usługi Azure AD B2C w ciągu zapytania. Przez przekazanie parametru do punktu końcowego HTML, można dynamicznie zmieniać zawartość strony. Na przykład można zmienić obraz tła w witrynie usługi Azure AD B2C zapisywania się lub zaloguj się na podstawie parametru, który jest przekazywany z sieci web lub aplikacji mobilnej.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Jak rozpocząć pracę z usługą Azure AD B2C?

W usłudze Azure AD B2C dzierżawca reprezentuje organizacji i jest katalogiem użytkowników. Poszczególni dzierżawcy usługi Azure AD B2C jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD B2C. Dzierżawca zawiera informacje dotyczące klientów, którzy utworzyli konto korzystać z aplikacji. Na przykład hasła, dane profilu i uprawnień.

Musisz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, aby włączyć wszystkie funkcje i opłacać opłaty za użycie. Umożliwia klientom usługi Azure AD B2C, zaloguj się do aplikacji, należy zarejestrować aplikację w dzierżawie usługi Azure AD B2C.

Aby skonfigurować aplikację do używania usługi Azure AD B2C, należy najpierw tworzenie dzierżawy usługi Azure AD B2C i zarejestrować aplikację. Aby zarejestrować aplikację, wykonaj czynności opisane w [samouczek: Rejestrowanie aplikacji umożliwia zapisywanie się i zaloguj się przy użyciu usługi Azure AD B2C](tutorial-register-applications.md).
  
Jeśli jesteś deweloperem aplikacji sieci web ASP.NET, skonfigurować aplikację do uwierzytelniania kont, korzystając z procedury w [samouczek: Włącz aplikacji sieci web do uwierzytelniania za pomocą konta przy użyciu usługi Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Jeśli jesteś deweloperem aplikacji komputerowych, konfigurowanie aplikacji do uwierzytelniania kont, korzystając z procedury w [samouczek: Włącz aplikacja uwierzytelniania za pomocą konta przy użyciu usługi Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Jeśli jesteś deweloperem aplikacji jednej strony przy użyciu środowiska Node.js, należy skonfigurować aplikację do uwierzytelniania kont, korzystając z procedury w [samouczek: Włącz aplikacji jednej strony, do uwierzytelniania za pomocą konta przy użyciu usługi Azure AD B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij konfigurowanie aplikacji do obsługi rejestracji i logowania przy samouczka.

> [!div class="nextstepaction"]
> [Samouczek: Rejestrowanie aplikacji umożliwia zapisywanie się i zaloguj się przy użyciu usługi Azure AD B2C](tutorial-register-applications.md)
