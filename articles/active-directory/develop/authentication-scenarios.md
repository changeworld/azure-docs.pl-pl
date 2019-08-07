---
title: Uwierzytelnianie na platformie tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o uwierzytelnianiu w usłudze Microsoft Identity platform, modelu aplikacji, interfejsie API, aprowizacji i najczęściej spotykanych scenariuszach uwierzytelniania obsługiwanych przez platformę Microsoft Identity.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7883c32e60a09c6fdfc4146c30472cfcdb57b689
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835187"
---
# <a name="what-is-authentication"></a>Co to jest uwierzytelnianie?

*Uwierzytelnianie* to wezwanie strony do podania wiarygodnych poświadczeń. Stanowi to podstawę utworzenia podmiotu zabezpieczeń używanego do obsługi tożsamości i kontroli dostępu. Mówiąc prościej, jest to proces udowadniania, że jest się tym, za kogo się podaje. Uwierzytelnianie jest czasami określane terminem AuthN.

*Autoryzacja* to przyznanie uprawnienia uwierzytelnionemu podmiotowi zabezpieczeń do wykonania jakiegoś zadania. Autoryzacja określa, do jakich danych można uzyskać dostęp oraz jakie zadania można na nich wykonać. Autoryzacja jest czasami określana terminem AuthZ.

Platforma tożsamości firmy Microsoft upraszcza uwierzytelnianie dla deweloperów aplikacji, dostarczając tożsamość jako usługę, z obsługą protokołów standardowych branżowych, takich jak OAuth 2,0 i OpenID Connect Connect, a także bibliotek typu "open source" dla różnych platform do Pomóż szybko rozpocząć tworzenie kodu.

W modelu programowania platformy tożsamości firmy Microsoft istnieją dwa podstawowe przypadki użycia:

* Podczas przepływu autoryzacji OAuth 2.0 — gdy właściciel zasobu przyznaje autoryzację do aplikacji klienckiej, co umożliwia klientowi uzyskanie dostępu do zasobów właściciela zasobu.
* Podczas dostępu do zasobu przez klienta — zgodnie z implementacją serwera zasobów, przy użyciu wartości oświadczeń obecnych w tokenie dostępu na potrzeby podejmowania na ich podstawie decyzji dotyczących kontroli dostępu.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Podstawowe informacje o uwierzytelnianiu na platformie tożsamości firmy Microsoft

Rozpatrzymy tutaj najprostszy scenariusz, w którym wymagana jest tożsamość: użytkownik w przeglądarce internetowej musi się uwierzytelnić w aplikacji internetowej. Na poniższym diagramie przedstawiono ten scenariusz:

![Przegląd logowania do aplikacji internetowej](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Poniżej opisano poszczególne składniki wyświetlane na diagramie:

* Platforma tożsamości firmy Microsoft jest dostawcą tożsamości. Dostawca tożsamości jest odpowiedzialny za weryfikację tożsamości użytkowników i aplikacji, które istnieją w katalogu organizacji. Wystawia on także tokeny zabezpieczające po pomyślnym uwierzytelnieniu tych użytkowników i aplikacji.
* Aplikacja, która chce przeprowadzić uwierzytelnianie w usłudze Microsoft Identity platform, musi być zarejestrowana w Azure Active Directory (Azure AD). Usługa Azure AD rejestruje i unikatowo identyfikuje aplikację w katalogu.
* Deweloperzy mogą korzystać z bibliotek uwierzytelniania platformy Microsoft Identity platform, aby ułatwić sobie uwierzytelnianie dzięki obsłudze szczegółowych informacji o protokole. Aby uzyskać więcej informacji, zobacz [biblioteki uwierzytelniania](reference-v2-libraries.md) programu Microsoft Identity platform v 2.0 i [biblioteki uwierzytelniania w wersji 1.0](active-directory-authentication-libraries.md).
* Po uwierzytelnieniu użytkownika aplikacja musi zweryfikować jego token zabezpieczający, aby się upewnić, że uwierzytelnianie zakończyło się pomyślnie. Procesy zachodzące w aplikacji zostały opisane w różnych przewodnikach Szybki start, samouczkach i przykładach kodu w różnych językach i strukturach.
  * Aby szybko utworzyć aplikację i dodać do niej funkcje, takie jak między innymi uzyskiwanie tokenów, odświeżanie tokenów, logowanie użytkownika i wyświetlanie informacji o użytkowniku, zapoznaj się z sekcją **Przewodniki Szybki start** w dokumentacji.
  * Aby uzyskać szczegółowe, oparte na scenariuszach procedury zadań dla deweloperów uwierzytelniania, takich jak uzyskiwanie tokenów dostępu i korzystanie z nich w wywołaniach interfejsu API programu Microsoft Graph i innych interfejsów API, implementowanie logowania firmy Microsoft przy użyciu tradycyjnej aplikacji internetowej opartej na przeglądarce za pomocą protokołu OpenID Connect i innych, zapoznaj się z sekcją **Samouczki** w dokumentacji.
  * Aby pobrać przykłady kodu, przejdź do witryny [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Przepływ żądań i odpowiedzi dotyczących procesu uwierzytelniania jest określony przez używany protokół uwierzytelniania, taki jak OAuth 2.0, OpenID Connect, WS-Federation lub SAML 2.0. Aby uzyskać więcej informacji na temat protokołów, zobacz sekcję **Pojęcia > Protokoły** w dokumentacji.

W przykładowym scenariuszu powyżej aplikacje można sklasyfikować według tych dwóch ról:

* Aplikacje, które wymagają bezpiecznego dostępu do zasobów.
* Aplikacje, które same pełnią rolę zasobu.

Teraz, gdy znasz już podstawowe informacje, Przeczytaj, jak poznać model i interfejs API aplikacji tożsamości, sposób działania aprowizacji na platformie tożsamości firmy Microsoft oraz linki do szczegółowych informacji o typowych scenariuszach obsługiwanych przez platformę tożsamości firmy Microsoft.

## <a name="application-model"></a>Model aplikacji

Platforma tożsamości firmy Microsoft reprezentuje aplikacje, które są przeznaczone do realizacji dwóch głównych funkcji:

* **Identyfikowanie aplikacji zgodnie z obsługiwanymi protokołami uwierzytelniania** — obejmuje to wyliczanie wszystkich identyfikatorów, adresów URL, wpisów tajnych i powiązanych informacji, które są wymagane podczas uwierzytelniania. W tym miejscu platforma tożsamości firmy Microsoft:

    * Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania.
    * Przechowuje wszystkie dane konieczne do zdecydowania, jakich zasobów może wymagać aplikacja w celu uzyskania dostępu oraz czy konkretne żądanie powinno zostać spełnione i pod jakimi warunkami.
    * Udostępnia infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji i dowolnej innej dzierżawy usługi Azure AD.

* **Obsługuj zgodę użytkownika podczas żądania tokenu i Ułatwiaj dynamiczną obsługę administracyjną aplikacji w ramach dzierżawców** — w tym miejscu — platformy tożsamości firmy Microsoft:

    * Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
    * Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

W programie Microsoft Identity platform **obiekt aplikacji** opisuje aplikację jako jednostkę abstrakcyjną. Deweloperzy pracują z aplikacjami. W czasie wdrażania platforma Microsoft Identity platform używa danego obiektu aplikacji jako planu do utworzenia jednostki **usługi**, która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. To właśnie jednostka usługi definiuje, jakie działania może w rzeczywistości wykonywać aplikacja w konkretnym katalogu docelowym, kto może z niej korzystać, do jakich zasobów ma ona dostęp itd. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji za pomocą **zgody**.

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizacji platformy tożsamości firmy Microsoft, który jest oparty na wyrażaniu zgody.  W tym przypadku istnieją dwie dzierżawy (a i B), w których dzierżawca jest właścicielem aplikacji, a dzierżawca B tworzy wystąpienie aplikacji za pośrednictwem jednostki usługi.  

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się przy użyciu aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są uzyskiwane i weryfikowane pod kątem uwierzytelniania
1. Użytkownik otrzymuje monit o wyrażenie zgody aplikacji na uzyskanie dostępu do dzierżawy B
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako strategii tworzenia jednostki usługi w dzierżawie B
1. Użytkownik otrzymuje żądany token.

Ten proces może być powtarzany dowolną liczbę razy dla innych dzierżaw (C, D itd.). Dzierżawca A zachowuje plan dla aplikacji (obiektu aplikacji). Użytkownicy i administratorzy wszystkich pozostałych dzierżaw, dla których aplikacja otrzymała zgodę, zachowują kontrolę nad tym, jakie działania może wykonywać aplikacja przy użyciu odpowiedniego obiektu jednostki usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Oświadczenia w tokenach zabezpieczeń platformy tożsamości firmy Microsoft

Tokeny zabezpieczające (tokeny dostępu i identyfikatorów) wystawiane przez platformę tożsamości firmy Microsoft zawierają oświadczenia lub potwierdzenia informacji dotyczących podmiotu, który został uwierzytelniony. Aplikacje mogą używać oświadczeń do wykonywania różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy katalogu podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Oświadczenia obecne w dowolnym tokenie zabezpieczającym są zależne od typu tokenu, typu poświadczenia użytego do uwierzytelnienia użytkownika i konfiguracji aplikacji.

W poniższej tabeli przedstawiono krótki opis każdego typu roszczeń emitowanych przez platformę tożsamości firmy Microsoft. Aby uzyskać bardziej szczegółowe informacje, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md) wystawione przez platformę tożsamości firmy Microsoft.

| Oświadczenie | Opis |
| --- | --- |
| Identyfikator aplikacji | Określa aplikację, która korzysta z tokenu. |
| Odbiorcy | Identyfikuje zasób odbiorcy, dla którego jest przeznaczony token. |
| Application Authentication Context Class Reference | Wskazuje, jak klient został uwierzytelniony (klient publiczny lub klient poufny). |
| Błyskawiczne uwierzytelnianie | Rejestruje datę i godzinę wystąpienia uwierzytelniania. |
| Metoda uwierzytelniania | Wskazuje, jak podmiot tokenu został uwierzytelniony (hasło, certyfikat itp.). |
| Imię | Udostępnia imię użytkownika w postaci, w jakiej jest ono ustawione w usłudze Azure AD. |
| Grupy | Zawiera identyfikatory obiektów grup usługi Azure AD, których członkiem jest użytkownik. |
| Dostawca tożsamości | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. |
| Wystawiony o | Rejestruje godzinę, o której został wystawiony token (często używane w celu odświeżenia tokenu). |
| Wystawca | Identyfikuje usługę STS, która emitowała token, a także dzierżawę usługi Azure AD. |
| Nazwisko | Udostępnia nazwisko użytkownika w postaci, w jakiej jest ono ustawione w usłudze Azure AD. |
| Name (Nazwa) | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. |
| Identyfikator obiektu | Zawiera niezmienny, unikatowy identyfikator podmiotu w usłudze Azure AD. |
| Role | Zawiera przyjazne nazwy ról aplikacji usługi Azure AD, które udzielono użytkownikowi. |
| Scope | Wskazuje uprawnienia przyznane aplikacji klienckiej. |
| Podmiot | Określa podmiot zabezpieczeń, dla którego token określa informacje. |
| Identyfikator dzierżawy | Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token. |
| Czas życia tokenu | Definiuje przedział czasu, przez który token jest prawidłowy. |
| Nazwa główna użytkownika | Zawiera nazwę główną użytkownika podmiotu. |
| Version | Zawiera numer wersji tokenu. |

## <a name="next-steps"></a>Następne kroki

* Poznaj [typy aplikacji i scenariusze obsługiwane przez platformę tożsamości firmy Microsoft](app-types.md)
