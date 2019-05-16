---
title: Uwierzytelnianie na platformie tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o uwierzytelnianiu platformie tożsamości firmy Microsoft, jej modelu interfejsu API, inicjowanie obsługi administracyjnej i najbardziej typowych scenariuszy uwierzytelniania tego platforma tożsamości firmy Microsoft obsługuje.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0be7a8b756ee3d1d71b15e10797176e50037a47
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540145"
---
# <a name="what-is-authentication"></a>Co to jest uwierzytelnianie?

*Uwierzytelnianie* to wezwanie strony do podania wiarygodnych poświadczeń. Stanowi to podstawę utworzenia podmiotu zabezpieczeń używanego do obsługi tożsamości i kontroli dostępu. Mówiąc prościej, jest to proces udowadniania, że jest się tym, za kogo się podaje. Uwierzytelnianie jest czasami określane terminem AuthN.

*Autoryzacja* to przyznanie uprawnienia uwierzytelnionemu podmiotowi zabezpieczeń do wykonania jakiegoś zadania. Autoryzacja określa, do jakich danych można uzyskać dostęp oraz jakie zadania można na nich wykonać. Autoryzacja jest czasami określana terminem AuthZ.

Platforma tożsamości usługi Microsoft upraszcza uwierzytelniania dla deweloperów aplikacji, zapewniając tożsamość jako usługa, za pomocą obsługi protokoły będące standardami branżowymi, takie jak OAuth 2.0 i OpenID Connect, a także bibliotek typu open source dla różnych platform, aby ułatwiają rozpoczęcie tworzenia kodu szybko.

Istnieją dwa główne przypadki użycia w modelu programowania platforma tożsamości firmy Microsoft:

* Podczas przepływu autoryzacji OAuth 2.0 — gdy właściciel zasobu przyznaje autoryzację do aplikacji klienckiej, co umożliwia klientowi uzyskanie dostępu do zasobów właściciela zasobu.
* Podczas dostępu do zasobu przez klienta — zgodnie z implementacją serwera zasobów, przy użyciu wartości oświadczeń obecnych w tokenie dostępu na potrzeby podejmowania na ich podstawie decyzji dotyczących kontroli dostępu.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Uwierzytelnianie podstawowe informacje o platformie tożsamości firmy Microsoft

Rozpatrzymy tutaj najprostszy scenariusz, w którym wymagana jest tożsamość: użytkownik w przeglądarce internetowej musi się uwierzytelnić w aplikacji internetowej. Na poniższym diagramie przedstawiono ten scenariusz:

![Przegląd logowania do aplikacji internetowej](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Poniżej opisano poszczególne składniki wyświetlane na diagramie:

* Platforma tożsamości firmy Microsoft jest dostawcą tożsamości. Dostawca tożsamości jest odpowiedzialny za weryfikację tożsamości użytkowników i aplikacji, które istnieją w katalogu organizacji. Wystawia on także tokeny zabezpieczające po pomyślnym uwierzytelnieniu tych użytkowników i aplikacji.
* Aplikacja, która chce oddelegowania procesów uwierzytelniania do platformą Microsoft identity musi być zarejestrowany w usłudze Azure Active Directory (Azure AD). Usługa Azure AD rejestruje i unikatowo identyfikuje aplikację w katalogu.
* Deweloperzy mogą używać bibliotek uwierzytelniania typu open source firmy Microsoft tożsamości platformy, aby ułatwić uwierzytelnianie dzięki obsłudze szczegółów protokołu dla Ciebie. Aby uzyskać więcej informacji, zobacz platforma tożsamości usługi Microsoft [bibliotek uwierzytelniania v2.0](reference-v2-libraries.md) i [bibliotek uwierzytelniania v1.0](active-directory-authentication-libraries.md).
* Po uwierzytelnieniu użytkownika aplikacja musi zweryfikować jego token zabezpieczający, aby się upewnić, że uwierzytelnianie zakończyło się pomyślnie. Procesy zachodzące w aplikacji zostały opisane w różnych przewodnikach Szybki start, samouczkach i przykładach kodu w różnych językach i strukturach.
  * Aby szybko utworzyć aplikację i dodać do niej funkcje, takie jak między innymi uzyskiwanie tokenów, odświeżanie tokenów, logowanie użytkownika i wyświetlanie informacji o użytkowniku, zapoznaj się z sekcją **Przewodniki Szybki start** w dokumentacji.
  * Aby uzyskać szczegółowe, oparte na scenariuszach procedury zadań dla deweloperów uwierzytelniania, takich jak uzyskiwanie tokenów dostępu i korzystanie z nich w wywołaniach interfejsu API programu Microsoft Graph i innych interfejsów API, implementowanie logowania firmy Microsoft przy użyciu tradycyjnej aplikacji internetowej opartej na przeglądarce za pomocą protokołu OpenID Connect i innych, zapoznaj się z sekcją **Samouczki** w dokumentacji.
  * Aby pobrać przykłady kodu, przejdź do witryny [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Przepływ żądań i odpowiedzi dotyczących procesu uwierzytelniania jest określony przez używany protokół uwierzytelniania, taki jak OAuth 2.0, OpenID Connect, WS-Federation lub SAML 2.0. Aby uzyskać więcej informacji na temat protokołów, zobacz sekcję **Pojęcia > Protokoły** w dokumentacji.

W przykładowym scenariuszu powyżej aplikacje można sklasyfikować według tych dwóch ról:

* Aplikacje, które wymagają bezpiecznego dostępu do zasobów.
* Aplikacje, które same pełnią rolę zasobu.

Teraz, po omówieniu wykonywać podstawowe zadania, Czytaj dalej, aby zapoznać się z modelem aplikacji tożsamości i interfejs API obsługuje jak inicjowanie obsługi administracyjnej działa na platformie tożsamości firmy Microsoft i linki do szczegółowych informacji o typowych scenariuszach tego platforma tożsamości usługi Microsoft.

## <a name="application-model"></a>Model aplikacji

Platforma tożsamości usługi Microsoft przedstawia aplikacje od określonego modelu, który został zaprojektowany, aby wypełnić dwa główne funkcje:

* **Identyfikowanie aplikacji zgodnie z obsługiwanymi protokołami uwierzytelniania** — obejmuje to wyliczanie wszystkich identyfikatorów, adresów URL, wpisów tajnych i powiązanych informacji, które są wymagane podczas uwierzytelniania. Oto, platformą Microsoft identity:

    * Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania.
    * Przechowuje wszystkie dane konieczne do zdecydowania, jakich zasobów może wymagać aplikacja w celu uzyskania dostępu oraz czy konkretne żądanie powinno zostać spełnione i pod jakimi warunkami.
    * Udostępnia infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji i dowolnej innej dzierżawy usługi Azure AD.

* **Obsługa zgody użytkownika podczas żądania tokenu i ułatwienia dynamicznej aprowizacji aplikacji dla dzierżaw** — w tym miejscu platformą Microsoft identity:

    * Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
    * Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

Na platformie tożsamości firmy Microsoft **obiekt aplikacji** opisuje aplikację jako abstrakcyjna jednostki. Deweloperzy pracują z aplikacjami. W czasie wdrażania platforma tożsamości firmy Microsoft używa obiektu danej aplikacji jako planu do utworzenia **nazwy głównej usługi**, która reprezentuje konkretnego wystąpienia aplikacji w katalogu lub dzierżawcy. To właśnie jednostka usługi definiuje, jakie działania może w rzeczywistości wykonywać aplikacja w konkretnym katalogu docelowym, kto może z niej korzystać, do jakich zasobów ma ona dostęp itd. Platforma tożsamości usługi Microsoft tworzy nazwę główną usługi na podstawie obiektu aplikacji za pomocą **zgody**.

Poniższy diagram przedstawia uproszczoną platformą Microsoft identity, inicjowanie obsługi administracyjnej sterowany za zgodą.  Istnieją dwa dzierżaw (A i B), gdzie dzierżawy A właścicielem aplikacji i dzierżawy B jest utworzenie wystąpienia aplikacji za pomocą nazwy głównej usługi.  

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

|   |   |
|---|---|
| 1 | Użytkownik w dzierżawie B próbuje zalogować się przy użyciu aplikacji |
| 2 | Poświadczenia użytkownika są odbierane i weryfikowane. |
| 3 | Użytkownik jest monitowany o wyrażenie zgody na przyznanie aplikacji dostępu do dzierżawy B. |
| 4 | Platforma tożsamości firmy Microsoft używa obiektu aplikacji A jako planu tworzenia usługi głównej w dzierżawie B |
| 5 | Użytkownik otrzymuje żądany token. |
|   |   |

Ten proces może być powtarzany dowolną liczbę razy dla innych dzierżaw (C, D itd.). Dzierżawy A zachowuje plan dla aplikacji (obiekt aplikacji). Użytkownicy i administratorzy wszystkich pozostałych dzierżaw, dla których aplikacja otrzymała zgodę, zachowują kontrolę nad tym, jakie działania może wykonywać aplikacja przy użyciu odpowiedniego obiektu jednostki usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [aplikacji i obiektów nazw głównych usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Oświadczenia w tokeny zabezpieczające platforma tożsamości firmy Microsoft

Tokeny zabezpieczające (dostępu i identyfikator tokenów) wydane przez platforma tożsamości usługi Microsoft zawierają oświadczenia lub potwierdzenia informacji na temat, który został uwierzytelniony. Aplikacje mogą używać oświadczeń do wykonywania różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy katalogu podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Oświadczenia obecne w dowolnym tokenie zabezpieczającym są zależne od typu tokenu, typu poświadczenia użytego do uwierzytelnienia użytkownika i konfiguracji aplikacji.

Krótki opis każdego typu oświadczenia wyemitowane przez platforma tożsamości usługi Microsoft znajduje się w poniższej tabeli. Aby uzyskać więcej informacji, zobacz [tokeny dostępu](access-tokens.md) i [tokeny Identyfikatora](id-tokens.md) wystawiony przez platforma tożsamości firmy Microsoft.

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
| Wersja | Zawiera numer wersji tokenu. |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [typy aplikacji i scenariuszy obsługiwanych na platformie tożsamości firmy Microsoft](app-types.md)
