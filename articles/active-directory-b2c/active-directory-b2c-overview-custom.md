---
title: Niestandardowe zasady usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7921454cc9269278db58fcc50bc63ca49b41b1e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317432"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Zasady niestandardowe w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zasady niestandardowe są pliki konfiguracji definiujące zachowanie Twojej dzierżawy usługi Azure Active Directory (Azure AD) B2C. Przepływy użytkownika są wstępnie zdefiniowane w portalu usługi Azure AD B2C dla typowych zadań tożsamości. Zasady niestandardowe mogą być w pełni edytowane przez dewelopera tożsamości w celu wykonania wielu różnych zadań.

## <a name="comparing-user-flows-and-custom-policies"></a>Porównanie przepływy użytkownika i zasad niestandardowych

| | Przepływy użytkowników | Zasady niestandardowe |
|-|-------------------|-----------------|
| Użytkowników docelowych | Wszystkich deweloperów aplikacji z użyciem lub bez wiedzy tożsamości. | Specjaliści tożsamości, integratorów systemów, konsultantów i zespołów wewnętrznych tożsamości. Są doświadczenia z przepływów OpenIDConnect i zrozumieć dostawców tożsamości i uwierzytelniania opartego na oświadczeniach. |
| Metody konfiguracji | Witryny Azure portal na przyjazny dla użytkownika — interfejs użytkownika (UI). | Bezpośrednie edytowanie plików XML, a następnie przekazaniu do witryny Azure portal. |
| Dostosowywanie interfejsu użytkownika | Pełne dostosowanie interfejsu użytkownika, w tym HTML, CSS i JavaScript.<br><br>Obsługa wielu języków przy użyciu niestandardowych ciągów. | Ten sam |
| Dostosowywanie atrybutu | Standardowe i niestandardowe atrybuty. | Ten sam |
| Token i sesji zarządzania | Token niestandardowy i wiele opcji sesji. | Ten sam |
| Dostawcy tożsamości | Wstępnie zdefiniowane dostawcy uwierzytelniania lokalnego lub społecznościowych i większość dostawców tożsamości OIDC, takich jak federacji z dzierżawami usługi Azure Active Directory. | Oparte na standardach OIDC, uwierzytelnianiem OAUTH i SAML.  Uwierzytelnianie jest również możliwe za pomocą integracji z interfejsami API REST. |
| Tożsamość zadania | Zarejestruj się lub zaloguj się przy użyciu lokalnego lub wiele kont społecznościowych.<br><br>Samoobsługowe resetowanie haseł.<br><br>Edytuj profil.<br><br>Multi-Factor Authentication.<br><br>Dostosowywanie tokenów i sesji.<br><br>Przepływy tokenu dostępu. | Wykonaj te same zadania, co przepływy użytkownika przy użyciu niestandardowych dostawców tożsamości, lub użyj zakresy niestandardowe.<br><br>Ustanowienie konta użytkownika w innym systemie, w chwili rejestracji.<br><br>Wysłać powitalną wiadomość e-mail przy użyciu własnego dostawcę usługi poczty e-mail.<br><br>Użyj magazynu użytkowników spoza usługi Azure AD B2C.<br><br>Sprawdź podane informacje o systemie zaufanego za pomocą interfejsu API przez użytkownika. |

## <a name="policy-files"></a>Pliki zasad

Używane są te trzy rodzaje plików zasad:

- **Plik podstawowy** — zawiera większość definicje. Zalecane jest, wprowadzić minimalną liczbę zmian do tego pliku, aby ułatwić rozwiązywanie problemów i długoterminowe konserwacji zasad.
- **Plik rozszerzenia** — przechowuje zmiany konfiguracji unikatowy dla Twojej dzierżawy.
- **Jednostki uzależnionej strona (RP) pliku** — pojedynczy plik skoncentrowane na zadanie, który jest wywoływany bezpośrednio przez aplikację lub usługę (nazywane również uzależnionej). Każde zadanie unikatowy wymaga własnego planu odzyskiwania, a następnie w zależności od znakowania wymagania, ta liczba może być "łączna liczba aplikacji x łącznej liczby przypadków użycia."

Przepływy użytkownika w usłudze Azure AD B2C oparte na wzorcu pliku trzy przedstawione powyżej, ale Deweloper widoczny jest tylko plik RP podczas portalu Azure sprawia, że zmiany w tle do pliku rozszerzenia.

## <a name="custom-policy-core-concepts"></a>Niestandardowe zasady podstawowe pojęcia

Zawiera tożsamość i dostęp do zarządzania (CIAM) obsługi klienta na platformie Azure:

- Katalog użytkownika, który jest dostępny za pomocą programu Microsoft Graph i która przechowuje dane użytkowników dla kont lokalnych i federacyjnej kont.
- Dostęp do **struktura środowiska tożsamości** który organizuje relacji zaufania między użytkownikami a jednostkami i przekazuje oświadczenia między nimi w celu wykonania zadania zarządzania tożsamości i dostępu. 
- Usługa tokenu zabezpieczającego (STS), który wysyła identyfikator tokenów, odświeżanie tokenów i dostęp do tokenów (oraz równoważne twierdzenia SAML) i sprawdza poprawność ich do ochrony zasobów.

Usługa Azure AD B2C wchodzi w interakcję z dostawcami tożsamości, użytkownicy, inne systemy i z katalogiem lokalnym użytkownika w sekwencji w celu wykonania zadania tożsamości. Na przykład logowania użytkownika, rejestrowanie nowego użytkownika lub resetowania hasła. Struktura środowiska tożsamości i zasad (nazywane również podróży użytkownika lub zasad framework zaufania) ustanawia relację zaufania wieloosobowa i jawnie definiuje aktorzy, akcje, protokołów i sekwencji kroków, aby zakończyć.

Struktura środowiska tożsamości jest w pełni konfigurowalne, oparte na zasadach, oparte na chmurze platformy Azure, które organizuje relacji zaufania między jednostkami w formatach standardowego protokołu, takie jak OpenIDConnect, OAuth, SAML, WSFed i kilka z nich niestandardowych, na przykład REST. Oparte na interfejsie API systemu do system oświadczeń wymiany. Szablon tworzy przyjazny dla użytkownika, etykietą biały procesy, które obsługują HTML i CSS.

Niestandardowe zasady są reprezentowane jako jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem zgodnie z łańcuchem hierarchii. Elementy XML zdefiniować schemat oświadczenia, przekształcenia oświadczeń, definicji zawartości dostawców oświadczeń, profile techniczne i kroki aranżacji podróży użytkownika, między innymi elementy. Zasady niestandardowe są dostępne jako jednego lub kilku plików XML, które są wykonywane przez platformy środowiska tożsamości, gdy wywoływany przez jednostkę uzależnioną. Deweloperzy Konfigurowanie niestandardowych zasad należy zdefiniować zaufane relacje szczegółowo dokładnej obejmujący punkty końcowe metadanych, dokładna oświadczeń wymiany definicje i skonfigurować wpisy tajne, klucze i certyfikaty, odpowiednio do potrzeb każdego dostawcy tożsamości.

### <a name="inheritance-model"></a>Model dziedziczenia

Aplikacja wywołuje plik zasad jednostki Uzależnionej, struktura środowiska tożsamości w usłudze Azure AD B2C dodaje wszystkie elementy z pliku podstawowego, z pliku rozszerzenia, a następnie plik zasad jednostki Uzależnionej można złożyć bieżące zasady obowiązywać.  Elementy tego samego typu i nazwy w pliku RP będzie zastępują w rozszerzeń i base zastąpienia rozszerzenia.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md)
