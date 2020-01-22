---
title: Uwierzytelnianie na platformie tożsamości firmy Microsoft | Azure
description: Poznaj podstawowe informacje o uwierzytelnianiu w usłudze Microsoft Identity platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff9af6a6f51ca89fa7e852609ceed1ee02c6e23f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294358"
---
# <a name="authentication-basics"></a>Podstawowe informacje o uwierzytelnianiu

## <a name="what-is-authentication"></a>Co to jest uwierzytelnianie

W tym artykule omówiono wiele pojęć związanych z uwierzytelnianiem, które należy zrozumieć w celu utworzenia chronionych aplikacji sieci Web, interfejsów API sieci Web lub aplikacji wywołujących chronione interfejsy API sieci Web.

**Uwierzytelnianie** to proces potwierdzania użytkownika. Uwierzytelnianie jest czasami określane terminem AuthN.

**Autoryzacja** to czynność udzielenia uprawnienia uwierzytelnionej stronie. Określa dane, do których można uzyskać dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami określana terminem AuthZ.

Zamiast tworzyć aplikacje, które zachowują własne informacje o nazwie użytkownika i hasła, co wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach, aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Azure Active Directory (Azure AD) to scentralizowany dostawca tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji pozwala na takie scenariusze, jak zasady dostępu warunkowego, które wymagają, aby użytkownik znajdował się w określonej lokalizacji, korzystać z uwierzytelniania wieloskładnikowego, a także umożliwiać użytkownikowi logowanie się raz, a następnie automatyczne zalogowano się do wszystkich aplikacji sieci Web, które współużytkują ten sam katalog scentralizowany. Ta funkcja jest określana jako logowanie jednokrotne (SSO).

Scentralizowany dostawca tożsamości jest jeszcze ważniejszy dla aplikacji, które znajdują się na całym świecie, niekoniecznie logują się z sieci przedsiębiorstwa. Usługa Azure AD uwierzytelnia użytkowników i zapewnia tokeny dostępu. Token dostępu jest tokenem zabezpieczającym wystawianym przez serwer autoryzacji. Zawiera informacje o użytkowniku i aplikacji, dla których jest przeznaczony token, który może służyć do uzyskiwania dostępu do interfejsów API sieci Web i innych chronionych zasobów.

Platforma tożsamości firmy Microsoft upraszcza uwierzytelnianie dla deweloperów aplikacji, dostarczając tożsamość jako usługę, z obsługą standardowych protokołów, takich jak OAuth 2,0 i OpenID Connect Connect, a także bibliotek typu "open source" dla różnych platformy ułatwiające szybkie rozpoczęcie tworzenia kodu. Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Aby uzyskać więcej informacji, zobacz [ewolucja platformy tożsamości firmy Microsoft](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Dzierżawcy

Dostawca tożsamości w chmurze służy do zapewniania wielu organizacji. Aby zapewnić, że użytkownicy z różnych organizacji są oddzielni, usługa Azure AD jest dzielona na dzierżawców z jedną dzierżawą na organizację.

Dzierżawy śledzą użytkowników i ich skojarzone aplikacje. Platforma tożsamości firmy Microsoft obsługuje również użytkowników logujących się przy użyciu osobistych kont Microsoft.

Usługa Azure AD udostępnia również Azure Active Directory B2C, dzięki czemu organizacje mogą logować użytkowników, zazwyczaj klientów przy użyciu tożsamości społecznościowych, takich jak konto Google. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Tokeny zabezpieczające

Tokeny zabezpieczające zawierają informacje o użytkownikach i aplikacjach. Usługa Azure AD korzysta z tokenów opartych na notacji JSON (JWTs) zawierających oświadczenia. Twierdzenie zawiera potwierdzenie dotyczące jednej jednostki. Aplikacje mogą używać oświadczeń dla różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy katalogu podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Element Claim składa się z par klucz-wartość, które zawierają informacje takie jak:

- Serwer tokenów zabezpieczających, który wygenerował token.
- Data wygenerowania tokenu.
- podmiot, taki jak użytkownik (z wyjątkiem demonów).
- odbiorcy, który jest aplikacją, dla której został wygenerowany token.
- Aplikacja (klient), która poprosiła o token. W przypadku aplikacji sieci Web może to być takie samo jak odbiorcy.

Aby uzyskać bardziej szczegółowe informacje dotyczące roszczeń, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md).

Jest to aplikacja, dla której Wygenerowano token, aplikację sieci Web, która została zarejestrowana przez użytkownika lub wywoływany interfejs API sieci Web, w celu zweryfikowania tokenu. Token jest podpisany przez serwer tokenu zabezpieczającego (STS) z kluczem prywatnym. Usługa STS publikuje odpowiadający mu klucz publiczny. Aby sprawdzić poprawność tokenu, aplikacja weryfikuje podpis przy użyciu klucza publicznego STS, aby sprawdzić, czy sygnatura została utworzona przy użyciu klucza prywatnego.

Tokeny są prawidłowe tylko przez ograniczony czas. Zazwyczaj usługa STS oferuje parę tokenów: token dostępu do uzyskiwania dostępu do aplikacji lub zasobów chronionych oraz token odświeżania używany do odświeżania tokenu dostępu, gdy token dostępu zbliża się do wygaśnięcia. 

Tokeny dostępu są przesyłane do internetowego interfejsu API jako token okaziciela w nagłówku `Authorization`. Aplikacja może dostarczyć token odświeżania do usługi STS, a jeśli użytkownik nie ma dostępu do aplikacji, zostanie przywrócony nowy token dostępu i nowy token odświeżania. Jest to sposób obsługi scenariusza, w którym ktoś opuszcza przedsiębiorstwo. Gdy usługa STS odbierze token odświeżenia, nie będzie wydawać innego prawidłowego tokenu dostępu, jeśli użytkownik nie jest już autoryzowany.

## <a name="application-model"></a>Model aplikacji

Aplikacje mogą logować się samodzielnie lub delegować użytkowników do dostawcy tożsamości. Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby poznać scenariusze logowania obsługiwane przez usługę Azure AD.

Aby dostawca tożsamości mógł wiedzieć, że użytkownik ma dostęp do określonej aplikacji, zarówno użytkownik, jak i aplikacja muszą być zarejestrowane przy użyciu dostawcy tożsamości. Po zarejestrowaniu aplikacji w usłudze Azure AD udostępniamy konfigurację tożsamości dla aplikacji, która umożliwia integrację z usługą Azure AD. Zarejestrowanie aplikacji umożliwia również:

- Dostosuj znakowanie aplikacji w oknie dialogowym logowania. Jest to ważne, ponieważ jest to pierwsze środowisko użytkownika z Twoją aplikacją.
- Zdecyduj, czy chcesz zezwolić użytkownikom na logowanie się tylko wtedy, gdy należą do organizacji. Jest to jedna aplikacja dzierżawy. Lub zezwól użytkownikom na logowanie się przy użyciu dowolnego konta służbowego. Jest to aplikacja wielodostępna. Możesz również zezwolić na osobiste konta Microsoft lub konto społecznościowe w serwisie LinkedIn, Google i tak dalej.
- uprawnienia zakresu żądania. Na przykład można zażądać zakresu "User. Read", który przyznaje uprawnienia do odczytu profilu zalogowanego użytkownika.
- Zdefiniuj zakresy, które definiują dostęp do internetowego interfejsu API. Zwykle, gdy aplikacja chce uzyskać dostęp do interfejsu API, będzie musiała zażądać uprawnień do zdefiniowanych zakresów.
- Udostępnij wpis tajny w usłudze Azure AD, który udowadnia tożsamość aplikacji w usłudze Azure AD.  Ma to zastosowanie w przypadku, gdy aplikacja jest poufną aplikacją kliencką. Poufna aplikacja kliencka to aplikacja, która umożliwia bezpieczne przechowywanie poświadczeń. Wymagają one zaufanego serwera wewnętrznej bazy danych do przechowywania poświadczeń.

Po zarejestrowaniu aplikacja będzie mieć identyfikator GUID, który jest udostępniany aplikacji z usługą Azure AD, gdy żąda tokenów. Jeśli aplikacja jest poufną aplikacją kliencką, będzie również współużytkować klucz tajny lub publiczny, w zależności od tego, czy zostały użyte certyfikaty lub wpisy tajne.

Platforma tożsamości firmy Microsoft reprezentuje aplikacje przy użyciu modelu, który spełnia dwie główne funkcje:

Zidentyfikuj aplikację według obsługiwanych przez siebie protokołów uwierzytelniania i podaj wszystkie identyfikatory, adresy URL, wpisy tajne i powiązane informacje, które są niezbędne do uwierzytelnienia.
Platforma tożsamości firmy Microsoft:

* Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania.
* Program przechowuje wszystkie dane dotyczące podejmowania decyzji o zasobach, do których aplikacja może potrzebować, i w jakich okolicznościach należy spełnić dane żądanie.
* Oferuje infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji oraz do innych dzierżaw usługi Azure AD.
* Obsługuje wyrażanie zgody użytkownika podczas żądania tokenu i ułatwia dynamiczne Inicjowanie obsługi aplikacji między dzierżawcami

Wyrażanie zgody polega na tym, że właściciel zasobu udziela autoryzacji dla aplikacji klienckiej w celu uzyskiwania dostępu do chronionych zasobów w ramach określonych uprawnień w imieniu właściciela zasobu. Platforma tożsamości firmy Microsoft:

* Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
* Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

Na platformie tożsamości Microsoft **obiekt aplikacji** opisuje aplikację jako jednostkę abstrakcyjną. W czasie wdrażania platforma tożsamości firmy Microsoft używa obiektu aplikacji jako planu do utworzenia jednostki **usługi**, która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. Jednostka usługi definiuje, co aplikacja może w rzeczywistości wykonać w konkretnym katalogu docelowym, kto może z niego korzystać, do jakich zasobów ma dostęp i tak dalej. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji za pomocą **zgody**.

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizacji platformy tożsamości firmy Microsoft, który jest oparty na wyrażaniu zgody. Pokazuje dwie dzierżawy (a i B). Dzierżawca jest właścicielem aplikacji. Dzierżawca B tworzy wystąpienie aplikacji za pośrednictwem nazwy głównej usługi.  

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się przy użyciu aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są uzyskiwane i weryfikowane w celu uwierzytelnienia.
1. Użytkownik zostanie poproszony o podanie zgody dla aplikacji w celu uzyskania dostępu do dzierżawy B.
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako strategii tworzenia jednostki usługi w dzierżawie B.
1. Użytkownik otrzymuje żądany token.

Ten proces można powtórzyć w przypadku dodatkowych dzierżawców. Dzierżawca A zachowuje plan dla aplikacji (obiektu aplikacji). Użytkownicy i Administratorzy wszystkich innych dzierżawców, w których aplikacja otrzymuje zgodę, zachowują kontrolę nad tym, co aplikacja może wykonać za pośrednictwem odpowiedniego obiektu głównego usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Przepływ logowania aplikacji sieci Web przy użyciu usługi Azure AD

Gdy użytkownik nawiguje w przeglądarce do aplikacji sieci Web, następuje:

- Aplikacja sieci Web określa, czy użytkownik jest uwierzytelniany.
- Jeśli użytkownik nie jest uwierzytelniony, aplikacja internetowa deleguje do usługi Azure AD, aby zalogować użytkownika. Ten proces logowania będzie zgodny z zasadami organizacji, co może oznaczać, że użytkownik musi wprowadzić poświadczenia przy użyciu uwierzytelniania wieloskładnikowego lub nie używać w ogóle hasła (na przykład korzystania z usługi Windows Hello).
- Użytkownik zostanie poproszony o zgodę na dostęp wymagany przez aplikację kliencką. Dlatego aplikacje klienckie muszą być zarejestrowane w usłudze Azure AD, aby usługa Azure AD mogła dostarczać tokeny reprezentujące dostęp, do którego użytkownik wyraził zgodę.

Po pomyślnym uwierzytelnieniu użytkownika:

- Usługa Azure AD wysyła token do aplikacji sieci Web.
- Plik cookie jest zapisywany, skojarzony z domeną usługi Azure AD, która zawiera tożsamość użytkownika w pliku JAR cookie w przeglądarce. Przy następnym użyciu przeglądarki w celu przejścia do punktu końcowego autoryzacji usługi Azure AD przeglądarka prezentuje plik cookie, aby użytkownik nie musiał logować się ponownie. Jest to również sposób, w jaki uzyskuje się Logowanie jednokrotne. Plik cookie jest tworzony przez usługę Azure AD i może być zrozumiały tylko dla usługi Azure AD.
- Następnie aplikacja internetowa sprawdza poprawność tokenu. Jeśli sprawdzanie poprawności zakończy się pomyślnie, aplikacja sieci Web wyświetli chronioną stronę i zapisze plik cookie sesji w pliku cookie w przeglądarce. Gdy użytkownik nawiguje do innej strony, aplikacja sieci Web wie, że użytkownik jest uwierzytelniany w oparciu o plik cookie sesji.

Poniższy diagram sekwencji podsumowuje tę interakcję:

![proces uwierzytelniania aplikacji sieci Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak aplikacja internetowa decyduje o tym, czy użytkownik jest uwierzytelniony

Deweloperzy aplikacji sieci Web mogą wskazać, czy wszystkie lub tylko niektóre strony wymagają uwierzytelniania. Na przykład w ASP.NET/ASP.NET rdzeń można to zrobić, dodając atrybut `[Authorize]` do akcji kontrolera. 

Ten atrybut powoduje, że ASP.NET sprawdza obecność pliku cookie sesji zawierającego tożsamość użytkownika. Jeśli plik cookie nie istnieje, ASP.NET przekierowuje uwierzytelnianie do określonego dostawcy tożsamości. Jeśli dostawca tożsamości to usługa Azure AD, aplikacja sieci Web przekierowuje uwierzytelnianie do https://login.microsoftonline.com, co spowoduje wyświetlenie okna dialogowego logowania.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Jak aplikacja sieci Web deleguje logowanie do usługi Azure AD i uzyskuje token

Uwierzytelnianie użytkowników odbywa się za pośrednictwem przeglądarki. Protokół OpenID Connect używa standardowych komunikatów protokołu HTTP.
- Aplikacja sieci Web wysyła HTTP 302 (redirect) do przeglądarki w celu korzystania z usługi Azure AD.
- Po uwierzytelnieniu użytkownika usługa Azure AD wysyła token do aplikacji sieci Web przy użyciu przekierowania za pośrednictwem przeglądarki.
- Przekierowanie jest dostarczane przez aplikację internetową w formie identyfikatora URI przekierowania. Ten identyfikator URI przekierowania jest zarejestrowany w obiekcie aplikacji usługi Azure AD. Może istnieć kilka identyfikatorów URI przekierowania, ponieważ aplikacja może zostać wdrożona w kilku adresach URL. W związku z tym aplikacja sieci Web będzie również musiała określić identyfikator URi przekierowania, który ma być używany.
- Usługa Azure AD weryfikuje, czy identyfikator URI przekierowania Wysłany przez aplikację sieci Web jest jednym z zarejestrowanych identyfikatorów URI przekierowania dla aplikacji.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Przepływ logowania aplikacji klasycznych i mobilnych przy użyciu usługi Azure AD

Opisany powyżej przepływ ma zastosowanie z niewielkimi różnicami w aplikacjach komputerowych i mobilnych.

Aplikacje klasyczne i mobilne mogą korzystać z osadzonego formantu sieci Web lub przeglądarki systemowej w celu uwierzytelniania. Na poniższym diagramie przedstawiono, w jaki sposób aplikacja klasyczna lub mobilna używa biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu uzyskania tokenów dostępu i wywołania interfejsów API sieci Web.

![Aplikacja klasyczna, która wygląda](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL używa przeglądarki do uzyskiwania tokenów, a podobnie jak w przypadku aplikacji internetowych, delegowania uwierzytelniania do usługi Azure AD.

Ponieważ usługa Azure AD zapisuje ten sam plik cookie tożsamości w przeglądarce, jak w przypadku aplikacji sieci Web, jeśli aplikacja natywna lub mobilna korzysta z przeglądarki systemu, natychmiast otrzyma Logowanie jednokrotne przy użyciu odpowiedniej aplikacji sieci Web.

Domyślnie program MSAL korzysta z przeglądarki systemu, z wyjątkiem .NET Framework aplikacji klasycznych, w których osadzony formant służy do zapewnienia bardziej zintegrowanego środowiska użytkownika.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się ze [słownikiem Microsoft Identity platform Developer](developer-glossary.md) , aby zapoznać się z typowymi terminami.
- Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby dowiedzieć się więcej o innych scenariuszach uwierzytelniania użytkowników obsługiwanych przez platformę tożsamości firmy Microsoft.
- Zobacz [biblioteki MSAL](msal-overview.md) , aby dowiedzieć się więcej o bibliotekach firmy Microsoft, które ułatwiają tworzenie aplikacji współpracujących z kontami Microsoft, kontami usługi Azure AD i Azure AD B2C użytkowników w jednym, usprawnionym modelu programowania.
- Aby dowiedzieć się, jak skonfigurować uwierzytelnianie dla aplikacji App Service, zobacz [integrowanie App Service z platformą tożsamości firmy Microsoft](/azure/app-service/configure-authentication-provider-aad) .
