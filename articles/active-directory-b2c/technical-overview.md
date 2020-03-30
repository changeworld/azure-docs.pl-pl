---
title: Omówienie techniczne i funkcje — usługa Azure Active Directory B2C
description: Szczegółowe wprowadzenie do funkcji i technologii w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d6b33211f6f247d9f30c0f162b388085faabe6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332544"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Omówienie techniczne i funkcje usługi Azure Active Directory B2C

Towarzysz [usługi Azure Active Directory B2C](overview.md)— ten artykuł zawiera bardziej szczegółowe wprowadzenie do usługi. Omówione w tym miejscu są podstawowe zasoby, z którymi pracujesz w usłudze, jej funkcje i jak umożliwiają one zapewnienie w pełni niestandardowe środowisko tożsamości dla klientów w aplikacjach.

## <a name="azure-ad-b2c-tenant"></a>Dzierżawa usługi Azure AD B2C

W usłudze Azure Active Directory B2C (Azure AD B2C) *dzierżawca* reprezentuje twoją organizację i jest katalogiem użytkowników. Każda dzierżawa usługi Azure AD B2C jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD B2C. Dzierżawa usługi Azure AD B2C różni się od dzierżawy usługi Azure Active Directory, którą możesz już mieć.

Podstawowe zasoby, z którymi pracujesz w dzierżawie usługi Azure AD B2C to:

* **Katalog** — katalog to *miejsce,* w którym usługa Azure AD B2C przechowuje poświadczenia użytkowników i dane profilu, a także rejestracje aplikacji.
* **Rejestracje aplikacji** — rejestrujesz aplikacje sieci web, urządzenia przenośne i natywne za pomocą usługi Azure AD B2C, aby umożliwić zarządzanie tożsamościami. Ponadto wszystkie interfejsy API, które chcesz chronić za pomocą usługi Azure AD B2C.
* **Przepływy użytkowników** i **zasady niestandardowe** — wbudowane (przepływy użytkownika) i w pełni konfigurowalne (zasady niestandardowe) środowiska tożsamości dla aplikacji.
  * Przepływy *użytkowników umożliwiają* szybką konfigurację i włączanie typowych zadań tożsamości, takich jak rejestracja, logowanie i edytowanie profilu.
  * Użyj *zasad niestandardowych,* aby umożliwić użytkownikom środowisko nie tylko dla typowych zadań tożsamości, ale także do tworzenia obsługi złożonych przepływów pracy tożsamości unikatowych dla organizacji, klientów, pracowników, partnerów i obywateli.
* **Dostawcy tożsamości** — ustawienia federacji dla:
  * Dostawcy tożsamości *społecznościowych,* tacy jak Facebook, LinkedIn lub Twitter, których chcesz wspierać w swoich aplikacjach.
  * *Zewnętrzni* dostawcy tożsamości, którzy obsługują standardowe protokoły tożsamości, takie jak OAuth 2.0, OpenID Connect i inne.
  * Konta *lokalne,* które umożliwiają użytkownikom rejestrację i logowanie się przy użyciu nazwy użytkownika (lub adresu e-mail lub innego identyfikatora) i hasła.
* **Klucze** — dodawanie kluczy szyfrowania i zarządzanie nimi do podpisywania i sprawdzania poprawności tokenów.

Dzierżawa usługi Azure AD B2C jest pierwszym zasobem, który należy utworzyć, aby rozpocząć pracę z usługą Azure AD B2C. Dowiedz się, jak w [samouczku: Tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Konta w usłudze Azure AD B2C

Usługa Azure AD B2C definiuje kilka typów kont użytkowników. Usługa Azure Active Directory, usługa Azure Active Directory B2B i usługa Azure Active Directory B2C współużytkuje te typy kont.

* **Konto służbowe** — użytkownicy z kontami służbowymi mogą zarządzać zasobami w dzierżawie i z rolą administratora, mogą również zarządzać dzierżawcami. Użytkownicy z kontami służbowymi mogą tworzyć nowe konta konsumentów, resetować hasła, blokować/odblokowywać konta oraz ustawiać uprawnienia lub przypisywać konto do grupy zabezpieczeń.
* **Konto gościa** — użytkownicy zewnętrzni zapraszają do dzierżawy jako goście. Typowy scenariusz zapraszania użytkownika-gościa do dzierżawy usługi Azure AD B2C jest udostępnianie obowiązków administracyjnych.
* **Konto konsumenckie** — konta konsumenckie to konta utworzone w katalogu usługi Azure AD B2C, gdy użytkownicy wykonają podróż użytkownika rejestracji w aplikacji zarejestrowanej w dzierżawie.

![Strona zarządzania użytkownikami usługi Azure AD B2C w witrynie Azure portal](media/technical-overview/portal-01-users.png)<br/>*Rysunek: Katalog użytkowników w dzierżawie usługi Azure AD B2C w witrynie Azure portal*

### <a name="consumer-accounts"></a>Konta konsumenckie

Za pomocą konta *konsumenta* użytkownicy mogą logować się do aplikacji zabezpieczonych za pomocą usługi Azure AD B2C. Użytkownicy z kontami konsumenta nie mogą jednak uzyskać dostępu do zasobów platformy Azure, na przykład do witryny Azure portal.

Konto konsumenta może być skojarzone z tymi typami tożsamości:

* **Tożsamość lokalna** z nazwą użytkownika i hasłem przechowywanymi lokalnie w katalogu usługi Azure AD B2C. Często nazywamy te tożsamości "kontami lokalnymi".
* **Tożsamości społecznościowe** lub **korporacyjne,** w których tożsamością użytkownika zarządza dostawca tożsamości federacyjnej, taki jak Facebook, Microsoft, ADFS lub Salesforce.

Użytkownik z kontem konsumenta może zalogować się przy użyciu wielu tożsamości, na przykład nazwy użytkownika, adresu e-mail, identyfikatora pracownika, identyfikatora rządowego i innych. Jedno konto może mieć wiele tożsamości, zarówno lokalnych, jak i społecznościowych.

![Tożsamości kont konsumenckich](media/technical-overview/identities.png)<br/>*Rysunek: Jedno konto konsumenta z wieloma tożsamościami w usłudze Azure AD B2C*

Usługa Azure AD B2C umożliwia zarządzanie typowymi atrybutami profilów kont odbiorców, takimi jak nazwa wyświetlana, nazwisko, imię, miasto i inne. Można również rozszerzyć schemat usługi Azure AD do przechowywania dodatkowych informacji o użytkownikach. Na przykład ich kraj lub miejsce zamieszkania, preferowany język i preferencje, takie jak to, czy chcą subskrybować biuletyn, czy włączyć uwierzytelnianie wieloskładnikowe.

Dowiedz się więcej o typach kont użytkowników w usłudze Azure AD B2C w [50- 100 000 użytkowników w usłudze Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Zewnętrzni dostawcy tożsamości

Można skonfigurować usługę Azure AD B2C, aby umożliwić użytkownikom logowanie się do aplikacji przy użyciu poświadczeń od zewnętrznych dostawców tożsamości społecznościowych lub korporacyjnych (IdP). Usługa Azure AD B2C obsługuje zewnętrznych dostawców tożsamości, takich jak Facebook, konto Microsoft, Google, Twitter i dowolnego dostawcy tożsamości, który obsługuje protokoły OAuth 1.0, OAuth 2.0, OpenID Connect, SAML lub WS-Federation.

![Zewnętrzni dostawcy tożsamości](media/technical-overview/external-idps.png)

Dzięki zewnętrznej federacji dostawców tożsamości możesz zaoferować konsumentom możliwość logowania się przy istniejących kontach społecznościowych lub korporacyjnych bez konieczności tworzenia nowego konta tylko dla aplikacji.

Na stronie rejestracji lub logowania usługa Azure AD B2C przedstawia listę zewnętrznych dostawców tożsamości, które użytkownik może wybrać do logowania. Po wybraniu jednego z zewnętrznych dostawców tożsamości są one pobierane (przekierowywane) do witryny sieci Web wybranego dostawcy w celu ukończenia procesu logowania. Po pomyślnym zalogowaniu się użytkownika są one zwracane z powrotem do usługi Azure AD B2C w celu uwierzytelnienia konta w aplikacji.

![Przykład logowania na urządzenia mobilne za pomocą konta społecznościowego (Facebook)](media/technical-overview/external-idp.png)

Aby zobaczyć, jak dodać dostawców tożsamości w usłudze Azure AD B2C, zobacz [Samouczek: Dodawanie dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Środowisko tożsamości: przepływy użytkowników lub zasady niestandardowe

Rozszerzalna struktura zasad usługi Azure AD B2C jest jego podstawową zaletą. Zasady opisują środowisko tożsamości użytkowników, takie jak rejestracja, logowanie się i edytowanie profilu.

W usłudze Azure AD B2C istnieją dwie ścieżki podstawowe, które można podjąć, aby zapewnić te środowiska tożsamości: przepływy użytkowników i zasady niestandardowe.

* **Przepływy użytkowników** są wstępnie zdefiniowane, wbudowane, konfigurowalne zasady, które zapewniamy, dzięki czemu można utworzyć rejestrację, zaloguj się i proces edycji zasad w ciągu kilku minut.

* **Zasady niestandardowe** umożliwiają tworzenie własnych podróży użytkownika dla scenariuszy środowiska tożsamości złożonych.

Przepływy użytkownika i zasady niestandardowe są obsługiwane przez *platformę środowiska tożsamości*, aparat aranżacji zasad usługi Azure AD B2C.

### <a name="user-flow"></a>Przepływ użytkownika

Aby ułatwić szybkie konfigurowanie najczęstszych zadań tożsamości, witryna Azure Portal zawiera kilka wstępnie zdefiniowanych i konfigurowalnych zasad *nazywanych przepływami użytkowników.*

Można skonfigurować ustawienia przepływu użytkownika, takie jak te, aby kontrolować zachowania środowiska tożsamości w aplikacjach:

* Typy kont używane do logowania, takie jak konta społecznościowe, takie jak Facebook, lub konta lokalne, które używają adresu e-mail i hasła do logowania
* Atrybuty, które mają być pobrane od konsumenta, takie jak imię, kod pocztowy lub kraj zamieszkania
* Azure Multi-Factor Authentication (MFA)
* Dostosowywanie interfejsu użytkownika
* Zestaw oświadczeń w tokenie odbieranym przez aplikację po zakończeniu przepływu użytkownika
* Zarządzanie sesjami
* ... i więcej.

Najbardziej typowe scenariusze tożsamości dla większości aplikacji mobilnych, sieci web i jednostronicowych można zdefiniować i skutecznie zaimplementować za pomocą przepływów użytkowników. Zaleca się użycie wbudowanych przepływów użytkownika, chyba że masz złożone scenariusze podróży użytkownika, które wymagają pełnej elastyczności zasad niestandardowych.

Dowiedz się więcej o przepływach użytkowników w [przepływach użytkowników w usłudze Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Zasady niestandardowe

Zasady niestandardowe odblokowują dostęp do pełnej mocy aparatu aranżacji struktury środowiska tożsamości (IEF). Za pomocą zasad niestandardowych można korzystać z usługi IEF, aby utworzyć prawie każde uwierzytelnianie, rejestrację użytkowników lub edytowanie profilu, które można sobie wyobrazić.

Struktura środowiska tożsamości daje możliwość konstruowania podróży użytkownika z dowolną kombinacją kroków. Przykład:

* Sedagr z innymi dostawcami tożsamości
* Wyzwania związane z uwierzytelnianiem wieloskładnikowym pierwszej i trzeciej firmy
* Zbieranie danych wejściowych użytkownika
* Integracja z systemami zewnętrznymi za pomocą komunikacji INTERFEJSU API REST

Każda taka podróż użytkownika jest definiowana przez zasady i można utworzyć dowolną liczbę lub kilka zasad, ile potrzeba, aby włączyć najlepsze środowisko użytkownika dla twojej organizacji.

![Diagram przedstawiający przykład złożonej podróży użytkownika włączonej przez ief](media/technical-overview/custom-policy.png)

Zasady niestandardowe są definiowane przez kilka plików XML, które odwołują się do siebie w hierarchicznym łańcuchu. Elementy XML definiują schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawców oświadczeń, profile techniczne, kroki aranżacji podróży użytkownika i inne aspekty środowiska tożsamości.

Zaawansowana elastyczność zasad niestandardowych jest najbardziej odpowiednia dla, gdy trzeba utworzyć scenariusze tożsamości złożonych. Deweloperzy konfigurujący zasady niestandardowe muszą dokładnie definiować zaufane relacje, aby uwzględnić punkty końcowe metadanych, dokładne definicje wymiany oświadczeń i skonfigurować wpisy tajne, klucze i certyfikaty zgodnie z potrzebami każdego dostawcy tożsamości.

Dowiedz się więcej o zasadach niestandardowych w [obszarze Zasady niestandardowe w usłudze Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoły i tokeny

Na potrzeby podróży użytkownika usługa Azure AD B2C obsługuje [protokoły OpenID Connect i OAuth 2.0](protocols-overview.md). Podczas wdrażania protokołu OpenID Connect w usłudze Azure AD B2C aplikacja rozpoczyna podróż użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD B2C.

Wynikiem żądania skierowanego do usługi Azure AD B2C jest token zabezpieczający, taki jak [token identyfikatora lub token dostępu](tokens-overview.md). Ten token zabezpieczający definiuje tożsamość użytkownika. Tokeny są odbierane z punktów końcowych usługi `/token` `/authorize` Azure AD B2C, takich jak lub punktu końcowego. Za pomocą tych tokenów można uzyskać dostęp do oświadczeń, które mogą służyć do sprawdzania poprawności tożsamości i zezwolić na dostęp do bezpiecznych zasobów.

W przypadku tożsamości zewnętrznych usługa Azure AD B2C obsługuje federację z dowolnym dostawcą tożsamości OAuth 1.0, OAuth 2.0, OpenID Connect, SAML i WS-Fed.

![Diagram aplikacji klienckiej opartej na OIDC federating z identyfikatorem OPARTYM NA SAML](media/technical-overview/protocols.png)

Na poprzednim diagramie pokazano, jak usługa Azure AD B2C może komunikować się przy użyciu różnych protokołów w ramach tego samego przepływu uwierzytelniania:

1. Aplikacja jednostki uzależnione inicjuje żądanie autoryzacji do usługi Azure AD B2C przy użyciu OpenID Connect.
1. Gdy użytkownik aplikacji zdecyduje się zalogować przy użyciu zewnętrznego dostawcy tożsamości, który używa protokołu SAML, usługa Azure AD B2C wywołuje protokół SAML do komunikowania się z tym dostawcą tożsamości.
1. Po zakończeniu operacji logowania z zewnętrznym dostawcą tożsamości usługi Azure AD B2C następnie zwraca token do aplikacji jednostki uzależnionej przy użyciu OpenID Connect.

## <a name="application-integration"></a>Integracja aplikacji

Gdy użytkownik chce zalogować się do aplikacji, niezależnie od tego, czy jest to aplikacja internetowa, mobilna, komputerowa lub jednostronicowa (SPA), aplikacja inicjuje żądanie autoryzacji do punktu końcowego przepływu użytkownika lub niestandardowego punktu końcowego dostarczonego przez zasady. Przepływ użytkownika lub zasady niestandardowe definiuje i kontroluje środowisko użytkownika. Po zakończeniu przepływu użytkownika, na przykład przepływ *rejestracji lub logowania,* usługa Azure AD B2C generuje token, a następnie przekierowuje użytkownika z powrotem do aplikacji.

![Aplikacja mobilna ze strzałkami przedstawiającymi przepływ między stroną logowania usługi Azure AD B2C](media/technical-overview/app-integration.png)

Wiele aplikacji można użyć tego samego przepływu użytkownika lub zasady niestandardowe. Pojedyncza aplikacja może używać wielu przepływów użytkownika lub zasad niestandardowych.

Na przykład, aby zalogować się do aplikacji, aplikacja używa *rejestracji lub logowania przepływu* użytkownika. Po zalogowaniu się użytkownik może chcieć edytować swój profil, więc aplikacja inicjuje kolejne żądanie autoryzacji, tym razem przy użyciu przepływu użytkownika *edycji profilu.*

## <a name="seamless-user-experiences"></a>Bezproblemowe środowisko użytkownika

W usłudze Azure AD B2C możesz tworzyć środowisko tożsamości użytkowników, aby strony, na których są wyświetlane, płynnie łączyły się z wyglądem i działaniem marki. Masz prawie pełną kontrolę nad zawartością HTML i CSS prezentowaną użytkownikom, gdy przechodzą one przez podróże tożsamości aplikacji. Dzięki tej elastyczności można zachować spójność marki i wizualnej między aplikacją a usługą Azure AD B2C.

![Zrzuty ekranu strony logowania do rejestracji dostosowanej do marki](media/technical-overview/seamless-ux.png)

Aby uzyskać informacje na temat dostosowywania interfejsu użytkownika, zobacz [Informacje o dostosowywaniu interfejsu użytkownika w usłudze Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Lokalizacja

Dostosowywanie języka w usłudze Azure AD B2C umożliwia dostosowanie różnych języków do potrzeb klienta. Firma Microsoft udostępnia tłumaczenia dla 36 języków, ale można również dostarczyć własne tłumaczenia dla dowolnego języka. Nawet jeśli środowisko jest dostępne tylko dla jednego języka, możesz dostosować dowolny tekst na stronach.

![Trzy strony logowania do rejestracji z tekstem interfejsu użytkownika w różnych językach](media/technical-overview/localization.png)

Zobacz, jak działa lokalizacja w [dostosowywaniu języka w usłudze Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Dodawanie własnej logiki biznesowej

Jeśli zdecydujesz się użyć zasad niestandardowych, można zintegrować z interfejsu API RESTful w podróży użytkownika, aby dodać własną logikę biznesową do podróży. Na przykład usługa Azure AD B2C może wymieniać dane z usługą RESTful w celu:

* Wyświetlanie niestandardowych komunikatów o błędach przyjaznych dla użytkownika.
* Sprawdź poprawność danych wejściowych użytkownika, aby zapobiec utrwalaniu nieprawidłowo sformułowanych danych w katalogu użytkownika. Na przykład można zmodyfikować dane wprowadzone przez użytkownika, takie jak wielkie litery ich imienia, jeśli wprowadzono je we wszystkich małych literach.
* Wzbogać dane użytkowników, integrując się z aplikacją biznesową.
* Korzystając z połączeń RESTful, można wysyłać powiadomienia wypychane, aktualizować firmowe bazy danych, uruchamiać proces migracji użytkowników, zarządzać uprawnieniami, przeprowadzać inspekcje baz danych i nie tylko.

Programy lojalnościowe to inny scenariusz włączony przez obsługę usługi Azure AD B2C do wywoływania interfejsów API REST. Na przykład usługa RESTful może odbierać adres e-mail użytkownika, wysyłać zapytania do bazy danych klientów, a następnie zwracać numer lojalnościowy użytkownika do usługi Azure AD B2C. Dane zwrotne mogą być przechowywane na koncie katalogu użytkownika w usłudze Azure AD B2C, a następnie być dalej oceniane w kolejnych krokach w zasadach lub być uwzględnione w tokenie dostępu.

![Integracja biznesowa w aplikacji mobilnej](media/technical-overview/lob-integration.png)

Wywołanie interfejsu API REST można dodać na dowolnym etapie podróży użytkownika zdefiniowanego przez zasady niestandardowe. Na przykład można wywołać interfejs API REST:

* Podczas logowania tuż przed sprawdzeniem poprawności poświadczeń usługi Azure AD B2C
* Natychmiast po zalogowaniu
* Zanim usługa Azure AD B2C utworzy nowe konto w katalogu
* Po tym, jak usługa Azure AD B2C utworzy nowe konto w katalogu
* Zanim usługa Azure AD B2C wyemiuje token dostępu

Aby zobaczyć, jak używać zasad niestandardowych do integracji interfejsu API RESTful w usłudze Azure AD B2C, zobacz [Integrowanie wymiany oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="protect-customer-identities"></a>Ochrona tożsamości klientów

Usługa Azure AD B2C jest zgodna z zobowiązaniami dotyczącymi zabezpieczeń, prywatności i innych zobowiązań opisanych w [Centrum zaufania platformy Microsoft Azure.](https://www.microsoft.com/trustcenter/cloudservices/azure)

Sesje są modelowane jako zaszyfrowane dane, z kluczem odszyfrowywania znanym tylko usłudze tokenu zabezpieczającego usługi Azure AD B2C. Używany jest silny algorytm szyfrowania, AES-192. Wszystkie ścieżki komunikacji są chronione tls dla poufności i integralności. Nasza usługa tokenu zabezpieczającego używa certyfikatu rozszerzonej weryfikacji (EV) dla protokołu TLS. Ogólnie rzecz biorąc usługa tokenu zabezpieczeń zmniejsza ataki skryptów między witrynami (XSS), nie renderując niezaufanych danych wejściowych.

![Schemat bezpiecznych danych podczas przesyłania i odpoczynku](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Dostęp do danych użytkownika

Dzierżawcy usługi Azure AD B2C mają wiele cech charakterystycznych z dzierżawami usługi Azure Active Directory w przedsiębiorstwie używanymi dla pracowników i partnerów. Aspekty udostępnione obejmują mechanizmy wyświetlania ról administracyjnych, przypisywania ról i działań inspekcji.

Można przypisać role do kontrolowania, kto może wykonywać pewne akcje administracyjne w usłudze Azure AD B2C, w tym:

* Tworzenie wszystkich aspektów przepływów użytkowników i zarządzanie nimi
* Tworzenie schematu atrybutu dostępnego dla wszystkich przepływów użytkowników i zarządzanie nim
* Konfigurowanie dostawców tożsamości do użytku w bezpośredniej federacji
* Tworzenie zasad struktury zaufania i zarządzanie nimi w ramach środowiska tożsamości (zasady niestandardowe)
* Zarządzanie wpisami tajnymi dla federacji i szyfrowania w ramach środowiska tożsamości (zasady niestandardowe)

Aby uzyskać więcej informacji na temat ról usługi Azure AD, w tym obsługi roli administrowania usługami Azure AD B2C, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Uwierzytelnianie wieloskładnikowe usługi Azure AD B2C (MFA) pomaga chronić dostęp do danych i aplikacji przy zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie, oferując szereg łatwych w użyciu metod uwierzytelniania. Użytkownicy mogą lub nie mogą być kwestionowane dla usługi MFA na podstawie decyzji konfiguracyjnych, które można wykonać jako administrator.

Zobacz, jak włączyć usługę MFA w przepływach użytkowników w [obszarze Włącz uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Blokada konta inteligentnego

Aby zapobiec próbom zgadywania haseł o użyciu hasła, usługa Azure AD B2C używa zaawansowanej strategii blokowania kont na podstawie adresu IP żądania, wprowadzonych haseł i kilku innych czynników. Czas trwania blokady jest automatycznie zwiększany w zależności od ryzyka i liczby prób.

![Inteligentna blokada konta](media/technical-overview/smart-lockout1.png)

Aby uzyskać więcej informacji na temat zarządzania ustawieniami ochrony hasłem, zobacz [Zarządzanie zagrożeniami dla zasobów i danych w usłudze Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Złożoność hasła

Podczas rejestracji lub resetowania hasła użytkownicy muszą podać hasło zgodne z regułami złożoności. Domyślnie usługa Azure AD B2C wymusza zasady silnego hasła. Usługa Azure AD B2C udostępnia również opcje konfiguracji w celu określenia wymagań dotyczących złożoności haseł używanych przez klientów.

Wymagania dotyczące złożoności haseł można skonfigurować zarówno w [przepływach użytkownika,](user-flow-password-complexity.md) jak i [w zasadach niestandardowych](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Inspekcja i dzienniki

Usługa Azure AD B2C emituje dzienniki inspekcji zawierające informacje o aktywności dotyczące jego zasobów, wystawionych tokenów i dostępu administratora. Za pomocą tych dzienników inspekcji można zrozumieć aktywność platformy i zdiagnozować problemy. Wpisy dziennika inspekcji są dostępne wkrótce po wystąpieniu działania, które wygenerowało zdarzenie.

W dzienniku inspekcji, który jest dostępny dla dzierżawy usługi Azure AD B2C lub dla określonego użytkownika, można znaleźć informacje, w tym:

* Działania dotyczące autoryzacji użytkownika dostępu do zasobów B2C (na przykład administrator uzyskujący dostęp do listy zasad B2C)
* Działania związane z atrybutami katalogu pobieranymi, gdy administrator loguje się przy użyciu portalu Azure
* Tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) w aplikacjach B2C
* Operacje CRUD na kluczach przechowywanych w kontenerze kluczy B2C
* Operacje CRUD na zasobach B2C (na przykład zasady i dostawcy tożsamości)
* Sprawdzanie poprawności poświadczeń użytkownika i wystawienie tokenu

![Dziennik inspekcji poszczególnych użytkowników wyświetlany w witrynie Azure portal](media/technical-overview/audit-log.png)

Aby uzyskać dodatkowe informacje na temat dzienników inspekcji, zobacz [Uzyskiwanie dostępu do dzienników inspekcji usługi Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Szczegółowe informacje o użyciu

Usługa Azure AD B2C umożliwia odnajdowanie, kiedy użytkownicy zarejestrują się lub zalogują się do aplikacji sieci web, gdzie znajdują się użytkownicy i jakich przeglądarek i systemów operacyjnych używają. Dzięki integracji usługi Azure Application Insights z usługą Azure AD B2C przy użyciu zasad niestandardowych można uzyskać wgląd w sposób rejestrowania się, logowania się, resetowania hasła lub edytowania profilu. Dzięki takiej wiedzy możesz podejmować decyzje oparte na danych dla nadchodzących cykli rozwoju.

Dowiedz się więcej o analizie użycia w [obszarze Śledzenie zachowania użytkowników w usłudze Azure Active Directory B2C przy użyciu usługi Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz głębszy widok na funkcje i aspekty techniczne usługi Azure Active Directory B2C, rozpocznij pracę z usługą, tworząc dzierżawę B2C:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C >](tutorial-create-tenant.md)