---
title: Niestandardowe zasady usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0724227da425eb2faeee9ac4ae8449782e62a241
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445987"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Usłudze Azure Active Directory B2C: Zasady niestandardowe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Co to są zasady niestandardowe?

Zasady niestandardowe to pliki konfiguracji definiujące zachowanie Twojej dzierżawy usługi Azure AD B2C. Natomiast **wbudowane zasady** są wstępnie zdefiniowane w portalu usługi Azure AD B2C dla typowych zadań tożsamości niestandardowych zasad może być w pełni edytowane przez dewelopera tożsamości do ukończenia niemal nieograniczoną liczbę zadań. Czytaj dalej, aby ustalić, czy zasady niestandardowe odpowiedni dla Ciebie i Twojego scenariusza z tożsamością.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Porównywanie wbudowane i niestandardowe zasady

| | Wbudowane zasady | Zasady niestandardowe |
|-|-------------------|-----------------|
|Użytkowników docelowych | Wszystkich deweloperów aplikacji z użyciem lub bez wiedzy tożsamości | Specjaliści tożsamości: integratorów systemów, konsultantów i zespołów wewnętrznych tożsamości. Opanowaniu OpenIDConnect przepływów i zrozumieć dostawców tożsamości i uwierzytelniania opartego na oświadczeniach |
|Metody konfiguracji | Witryna Azure portal z Komfortowy interfejs użytkownika | Bezpośrednie edytowanie plików XML, a następnie przekazaniu do witryny Azure portal |
|Dostosowywanie interfejsu użytkownika | Pełne dostosowanie interfejsu użytkownika, w tym HTML, CSS i javascript obsługi (wymaga domeny niestandardowej)<br><br>Obsługa wielu języków przy użyciu niestandardowych ciągów | Ten sam |
| Dostosowywanie atrybutu | Standardowe i niestandardowe atrybuty | Ten sam |
|Token i sesji zarządzania | Token niestandardowy i wiele opcji sesji | Ten sam |
|Dostawcy tożsamości| **Już dziś**: wstępnie zdefiniowane lokalne, społecznościowych dostawcy<br><br>**Przyszłość**: oparte na standardach OIDC SAML, OAuth | **Już dziś**: oparte na standardach OIDC OAUTH, SAML<br><br>**Przyszłość**: WsFed |
|Tożsamość zadania (przykłady) | Zarejestruj się lub zaloguj się przy użyciu lokalnej i wiele kont społecznościowych<br><br>Samoobsługowe resetowanie haseł<br><br>Edytuj profil<br><br>Scenariusze uwierzytelniania usługi Multi-Factor Authentication<br><br>Dostosowywanie tokenów i sesji<br><br>Przepływy tokenu dostępu | Wykonaj te same zadania jako wbudowane zasady przy użyciu niestandardowych dostawców tożsamości, lub użyj zakresy niestandardowe<br><br>Ustanów użytkownika w innym systemie w czasie rejestracji<br><br>Wysłać powitalną wiadomość e-mail przy użyciu własnego dostawcę usługi poczty e-mail<br><br>Użyj magazynu użytkowników spoza B2C<br><br>Sprawdzanie poprawności użytkownika podane informacje o systemie zaufanych za pośrednictwem interfejsu API |

## <a name="policy-files"></a>Pliki zasad

Niestandardowe zasady jest przedstawiana jako jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem w łańcuchu hierarchicznej. Zdefiniuj elementy XML: schemat oświadczenia, oświadczenia przekształcenia, definicji zawartości, profile technical/dostawców oświadczeń i kroki aranżacji podróży użytkownika, między innymi elementy.

Firma Microsoft zaleca korzystanie z trzech typów plików zasad:

- **Plik podstawowy**, który zawiera większość definicje i dla której platforma Azure oferuje pełny przykład.  Zalecane jest wprowadzone z minimalną liczbą zmian do tego pliku, aby ułatwić rozwiązywanie problemów i długoterminowe konserwacji zasad
- **plik rozszerzenia** przechowuje zmiany konfiguracji unikatowy dla Twojej dzierżawy
- **plik strony jednostki uzależnionej (RP)** oznacza to pojedynczy skoncentrowane na zadania pliku który jest wywoływany bezpośrednio przez aplikację lub usługę (zwane również uzależniona).  Przeczytaj artykuł na temat definicji pliku zasad, aby uzyskać więcej informacji.  Każde zadanie unikatowy wymaga własnego planu odzyskiwania, a następnie w zależności od znakowania wymagania ta liczba może być "łączna liczba aplikacji x łącznej liczby przypadków użycia."


Wbudowane zasady w usłudze Azure AD B2C oparte na wzorcu pliku trzy przedstawione powyżej, ale Deweloper widzi tylko pliku jednostki uzależnionej strona (RP), podczas gdy portal sprawia, że zmiany w tle do pliku rozszerzenia.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Podstawowe pojęcia, które należy wiedzieć podczas za pomocą zasad niestandardowych

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Firmy klienta tożsamość i dostęp do usługi Azure management (CIAM). Usługa obejmuje:

1. Katalog użytkownika, w postaci specjalnych usługi Azure Active Directory dostępne za pośrednictwem programu Microsoft Graph, które przechowuje dane użytkowników dla kont lokalnych i federacyjnej kont 
2. Dostęp do **struktura środowiska tożsamości** który organizuje relacji zaufania między użytkownikami a jednostkami i przekazuje oświadczenia między nimi w celu wykonania zadania zarządzania tożsamości/dostępem 
3. Usługa tokenu zabezpieczającego (STS) wydawanie, identyfikator tokenów, odświeżanie tokenów i dostęp do tokenów (oraz równoważne twierdzenia SAML) i zatwierdzanie ich do ochrony zasobów.

Usługa Azure AD B2C wchodzi w interakcję z dostawcami tożsamości, użytkownicy, inne systemy i z katalogiem lokalnym użytkownika w sekwencji w celu wykonania zadania tożsamości (na przykład logowania użytkownika, rejestrowanie nowego użytkownika, resetowanie hasła). Struktura środowiska tożsamości i zasad (nazywane również podróży użytkownika lub zasad framework zaufania) nosi nazwę podstawowej platformy, która ustanawia relację zaufania wieloosobowa i wykonuje następujące kroki jawnie definiuje aktorzy, akcje, protokołów i sekwencja kroków w celu ukończenia.

### <a name="identity-experience-framework"></a>Platforma obsługi tożsamości

W pełni konfigurowalne, oparte na zasadach, oparte na chmurze platformy Azure, które służy do aranżacji relacji zaufania między jednostkami (szeroko dostawców oświadczeń) w standardowym protokołem formaty, takie jak OpenIDConnect, OAuth, SAML, WSFed i kilka z nich niestandardowej (na przykład REST API-based systemu do systemu oświadczeń wymiany). I2E tworzy przyjazny dla użytkownika, etykietą biały procesy, które obsługują HTML, CSS i javascript.  Obecnie struktura środowiska tożsamości jest dostępna tylko w kontekście usługi Azure AD B2C i priorytetami zadania związane z CIAM.

### <a name="built-in-policies"></a>Wbudowane zasady

Wstępnie zdefiniowane konfiguracji pliki, które bezpośrednie zachowanie usługi Azure AD B2C do wykonania najbardziej często używane zadania tożsamości (oznacza to, że rejestracja użytkownika, logowanie, resetowania hasła) i wchodzić w interakcje z zaufanym osobom lub podmiotom których relację również wstępnie zdefiniowane w usłudze Azure AD B2C ( na przykład Facebook dostawcy tożsamości, LinkedIn, Account firmy Microsoft, konta Google).  W przyszłości wbudowane zasady mogą również wprowadzić dostosowywania dostawców tożsamości, które zwykle znajdują się w obszaru przedsiębiorstwa, takich jak Azure Active Directory Premium systemu Azure, usługi Active Directory/ADFS itp Salesforce identyfikator dostawcy.


### <a name="custom-policies"></a>Zasady niestandardowe

Pliki konfiguracji definiujące zachowanie platformy środowiska tożsamości w dzierżawie usługi Azure AD B2C. Zasady niestandardowe są dostępne jako jeden lub kilka pliki XML (patrz definicje zasad plików), które są wykonywane przez platformy środowiska tożsamości, gdy wywoływany przez jednostkę uzależnioną (na przykład aplikacja). Zasady niestandardowe można edytować bezpośrednio przez dewelopera tożsamości do ukończenia niemal nieograniczoną liczbę zadań. Deweloperzy Konfigurowanie niestandardowych zasad należy zdefiniować zaufane relacje szczegółowo dokładnej obejmujący punkty końcowe metadanych, dokładna oświadczeń wymiany definicje i skonfigurować wpisy tajne, klucze i certyfikaty, odpowiednio do potrzeb każdego dostawcy tożsamości.

## <a name="policy-file-definitions-for-identity-experience-framework-trust-frameworks"></a>Zasady plik definicji dla struktury zaufania platformy środowiska tożsamości

### <a name="policy-files"></a>Pliki zasad

Niestandardowe zasady jest przedstawiana jako jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem w łańcuchu hierarchicznej. Zdefiniuj elementy XML: schemat oświadczenia, oświadczenia przekształcenia, definicji zawartości, profile technical/dostawców oświadczeń i kroki aranżacji podróży użytkownika, między innymi elementy.  Firma Microsoft zaleca korzystanie z trzech typów plików zasad:

- **Plik podstawowy** zawiera większość definicje i dla której platforma Azure oferuje pełny przykład.  Zalecane jest wprowadzone z minimalną liczbą zmian do tego pliku, aby ułatwić rozwiązywanie problemów i długoterminowe konserwacji zasad
- **plik rozszerzenia** przechowuje zmiany konfiguracji unikatowy dla Twojej dzierżawy
- **plik strony jednostki uzależnionej (RP)** jest pojedynczy plik skoncentrowane na zadanie, który jest wywoływany bezpośrednio przez aplikację lub usługę (zwane również uzależniona).  Przeczytaj artykuł na temat definicji pliku zasad, aby uzyskać więcej informacji.  Każde zadanie unikatowych wymaga własnego planu odzyskiwania i w zależności od znakowania wymagania ta liczba może być "łączna liczba aplikacji x łącznej liczby przypadków użycia".

![Typy plików zasad](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ pliku zasad | Nazwa pliku przykłady | Zalecane użycie | Dziedziczy z |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Zawiera schemat oświadczeń core, przekształcenia oświadczeń, dostawców oświadczeń i podróży użytkownika skonfigurowane przez firmę Microsoft<br><br>Minimalny zmiany do tego pliku | Brak |
| Rozszerzenia (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidacja zmiany w pliku podstawowego tutaj<br><br>Dostawców oświadczeń zmodyfikowane<br><br>Podróży użytkownika<br><br>Definicje własny niestandardowy schemat | Plik podstawowy |
| Jednostki uzależnionej (RP) | B2C_1A_sign_up_sign_in.xml| Ustawienia tokenu kształt i sesji w tym miejscu| Plik Extensions(ext) |

### <a name="inheritance-model"></a>Model dziedziczenia

Gdy aplikacja wywołuje plik zasad jednostki Uzależnionej, struktura środowiska tożsamości w B2C doda wszystkie elementy z podstawowego, a następnie z ROZSZERZEŃ, a na koniec z pliku zasad jednostki Uzależnionej można złożyć obowiązuje bieżące zasady.  Elementy tego samego typu i nazwy w pliku RP zastępują w ROZSZERZENIACH, i rozszerzenia zastępuje BASE.

**Wbudowane zasady** w usłudze Azure AD B2C oparte na wzorcu pliku 3 przedstawiono powyżej, ale Deweloper widoczny jest tylko pliku jednostki uzależnionej strona (RP), podczas gdy portal sprawia, że zmiany w tle do pliku rozszerzeń.  Wszystkie usługi Azure AD B2C współużytkuje plik podstawowy zasad jest kontrolowana przez zespół Azure B2C, która jest często aktualizowany.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md)
