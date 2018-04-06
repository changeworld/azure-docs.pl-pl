---
title: 'Usługi Azure Active Directory B2C: Niestandardowe zasady | Dokumentacja firmy Microsoft'
description: Temat dotyczący zasad niestandardowych usługi Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 4c09fa5c454cee6ca9a0ed0d3fd4582b222c0c1a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Usługi Azure Active Directory B2C: Zasady niestandardowe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Co to są zasady niestandardowe?

Zasady niestandardowe to pliki konfiguracji definiujące zachowanie Twojej dzierżawy usługi Azure AD B2C. Podczas gdy **wbudowane zasady** są wstępnie zdefiniowane w portalu usługi Azure AD B2C do najbardziej typowych zadań tożsamości niestandardowych zasad mogą być pełni edytowane przez dewelopera tożsamości, aby ukończyć near nieograniczoną liczbę zadań. Odczytać w celu ustalenia, czy zasady niestandardowe są odpowiedniej dla Ciebie i Twojego scenariusza z tożsamością.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Porównywanie wbudowane i niestandardowe zasady

| | Wbudowane zasady | Zasady niestandardowe |
|-|-------------------|-----------------|
|Użytkownicy docelowi | Wszystkie deweloperzy aplikacji z lub bez wiedzy tożsamości | Specjaliści tożsamości: integratorów systemów. platforma, konsultantów i zespoły wewnętrznych tożsamości. Potrafisz przepływów OpenIDConnect i zrozumieć dostawców tożsamości i uwierzytelniania opartego na oświadczeniach |
|Metody konfiguracji | Portal Azure przy użyciu przyjaznych dla użytkownika interfejsu użytkownika | Bezpośredniego edytowania plików XML, a następnie przekazać do portalu Azure |
|Dostosowywanie interfejsu użytkownika | Pełne dostosowywanie interfejsu użytkownika, w tym obsługi języka HTML, CSS i javascript (wymaga domeny niestandardowej)<br><br>Obsługa wielu języków z ciągami niestandardowe | tym samym |
| Dostosowywanie atrybutu | Standardowe i niestandardowe atrybuty | tym samym |
|Token i sesji zarządzania | Niestandardowy token i wiele opcji sesji | tym samym |
|Dostawcy tożsamości| **Dzisiaj**: wstępnie zdefiniowane lokalne, społecznościowych dostawcy<br><br>**Przyszłe**: OAuth opartych na standardach OIDC SAML, | **Dzisiaj**: SAML opartych na standardach OIDC OAUTH,<br><br>**Przyszłe**: WsFed |
|Zadania tożsamości (przykłady) | Zarejestruj się lub zaloguj się przy użyciu wielu kont społecznościowych i lokalne<br><br>Samoobsługowe resetowanie haseł<br><br>Edycja profilu<br><br>Scenariusze uwierzytelniania wieloskładnikowego<br><br>Dostosowywanie tokeny i sesji<br><br>Przepływ tokenu dostępu | Wykonaj zadania tego samego jako wbudowanych zasad przy użyciu dostawcy tożsamości niestandardowej lub użyj zakresy niestandardowe<br><br>Ustanów użytkownika w innym systemie w czasie rejestracji<br><br>Wysyłania powitalnej wiadomości e-mail, przy użyciu własnego dostawcę usługi poczty e-mail<br><br>Użyj magazynu użytkowników spoza B2C<br><br>Sprawdzanie poprawności użytkownika informacje z zaufanego systemu za pomocą interfejsu API |

## <a name="policy-files"></a>Pliki zasad

Zasady niestandardowe jest reprezentowany jako jeden lub kilka plików w formacie XML które odwoływać się do siebie w łańcuchu hierarchicznej. Definiowanie elementów XML: schemat oświadczeń oświadczeń przekształcenia, definicje zawartości profile technical/dostawców oświadczeń i Userjourney aranżacji kroków, między innymi elementami.

Firma Microsoft zaleca użycie trzech typów plików zasad:

- **PODSTAWOWY plik**, który zawiera większość definicje i dla którego platforma Azure udostępnia kompletnego przykładu.  Firma Microsoft zaleca, że minimalna liczba zmian ten plik, aby ułatwić rozwiązywanie problemów i długoterminowej konserwacji zasad
- **plik rozszerzenia** przechowuje zmiany konfiguracji unikatowy dla dzierżawy
- **plik strony jednostki uzależnionej (RP)** pojedynczego pliku fokus zadania, który jest wywoływany bezpośrednio przez aplikację lub usługę (alias uzależniona).  Przeczytaj artykuł na definicje pliku zasad, aby uzyskać więcej informacji.  Każde zadanie unikatowy wymaga własnego planu odzyskiwania i w zależności od wymagań znakowania ta liczba może być "Całkowita liczba aplikacji x liczba przypadków użycia".


Wbudowane zasady w usłudze Azure AD B2C zgodne ze wzorcem pliku 3 przedstawione powyżej, ale Deweloper widoczny jest tylko plik jednostki uzależnionej strony (RP) podczas portalu wprowadza zmiany w tle pliku rozszerzenia.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Podstawowe koncepcje, których należy wiedzieć, korzystając z zasad niestandardowych

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Przez klienta tożsamości i dostępu do usługi Azure management (CIAM). Usługa obejmuje:

1. Katalog użytkownika w postaci specjalnych usługi Azure Active Directory dostępne za pośrednictwem programu Microsoft Graph, które przechowuje dane użytkownika dla kont lokalnych i kont federacyjnych 
2. Dostęp do **Framework obsługi tożsamości** co organizuje relacji zaufania między użytkowników i jednostek i przekazuje oświadczeń między nimi w celu wykonania zadania zarządzania tożsamości/dostęp 
3. Usługa tokenu zabezpieczającego (STS) wystawiania identyfikator tokenów, Odśwież tokenów i dostępu tokeny (i równoważne potwierdzenia SAML) i sprawdzania ich do ochrony zasobów.

Usługa Azure AD B2C współdziała z dostawców tożsamości, użytkowników i innych systemach i z katalogu lokalnego użytkownika w sekwencji do wykonania zadania tożsamości (na przykład logowania użytkownika, zarejestrować nowego użytkownika, resetowanie hasła). Platforma podstawowa, która ustanawia relację zaufania wieloosobową i wykonuje następujące czynności jest nazywany Framework obsługi tożsamości i zasad (nazywanych również przebieg użytkownika lub zasad framework zaufania) jawnie definiuje uczestników, akcje, protokołów, i sekwencja kroków w celu ukończenia.

### <a name="identity-experience-framework"></a>Platforma obsługi tożsamości

Formatuje pełni konfigurowalne, oparte na zasadach, oparte na chmurze platformy Azure, będąca aranżacją relacji zaufania między jednostkami (szeroko dostawców oświadczeń) w standardowego protokołu OpenIDConnect, OAuth, SAML, WSFed i kilka z nich niestandardowych (na przykład REST API-based systemu do systemu oświadczeń wymiany). I2E tworzy przyjazną dla użytkownika, napotyka whitelabelled, który obsługuje HTML, CSS i javascript.  Obecnie Framework obsługi tożsamości jest dostępna tylko w kontekście usługi Azure AD B2C i priorytetową zadania związane z CIAM.

### <a name="built-in-policies"></a>Wbudowane zasady

Wstępnie zdefiniowane pliki konfiguracyjne, które bezpośrednie zachowanie usługi Azure AD B2C do wykonania najczęściej używanych tożsamości zadania (tj. Rejestracja użytkownika, logowania resetowania hasła) i interakcję z zaufanych strony, których relację również wstępnie zdefiniowane w usłudze Azure AD B2C (dla przykład Facebook dostawcy tożsamości, LinkedIn, Account firmy Microsoft, konta Google).  W przyszłości wbudowane zasady mogą również przewidzieć dostosowania dostawców tożsamości, które są zazwyczaj w obszarze przedsiębiorstwa, np. Azure Active Directory Premium, Active Directory/ADFS Salesforce identyfikator dostawcy.


### <a name="custom-policies"></a>Zasady niestandardowe

Pliki konfiguracyjne, które definiują zachowanie Framework obsługi tożsamości w dzierżawie usługi Azure AD B2C. Zasady niestandardowe jest dostępny jako jeden lub kilka plików XML (zobacz pliki zasad definicje) wykonanych przez platformę środowisko tożsamości, gdy została wywołana przez jednostkę uzależnioną (na przykład aplikacja). Zasady niestandardowe można edytować bezpośrednio przez dewelopera tożsamości, aby ukończyć near nieograniczoną liczbę zadań. Deweloperzy Konfigurowanie niestandardowych zasad musi definiować relacje zaufanych szczegółowo dokładne zawierają punkty końcowe metadanych, dokładne oświadczeń wymienić definicje i skonfiguruj klucze tajne, klucze i certyfikaty zgodnie z potrzebami każdego dostawcy tożsamości.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Definicje pliku zasad dla Trustframeworks Framework obsługi tożsamości

### <a name="policy-files"></a>Pliki zasad

Zasady niestandardowe jest reprezentowany jako jeden lub kilka plików w formacie XML odwołujące się do siebie nawzajem w łańcuchu hierarchicznej. Definiowanie elementów XML: schemat oświadczeń oświadczeń przekształcenia, definicje zawartości profile technical/dostawców oświadczeń i użytkownika przebieg aranżacji kroków, między innymi elementami.  Firma Microsoft zaleca użycie trzech typów plików zasad:

- **PODSTAWOWY plik** zawiera większość definicje i dla którego platforma Azure udostępnia kompletnego przykładu.  Firma Microsoft zaleca, że minimalna liczba zmian ten plik, aby ułatwić rozwiązywanie problemów i długoterminowej konserwacji zasad
- **plik rozszerzenia** przechowuje zmiany konfiguracji unikatowy dla dzierżawy
- **plik strony jednostki uzależnionej (RP)** jest pojedynczy plik fokus zadania, które jest wywoływane bezpośrednio przez aplikację lub usługę (alias uzależniona).  Przeczytaj artykuł na definicje pliku zasad, aby uzyskać więcej informacji.  Każde zadanie unikatowy wymaga własnego planu odzyskiwania i w zależności od wymagań znakowania ta liczba może być "Całkowita liczba aplikacji x liczba przypadków użycia".

![Typy zasad](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ pliku zasad | Nazwa pliku przykłady | Zalecane użycie | Dziedziczy |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Zawiera podstawowe oświadczeń schematu, przekształcenia oświadczeń, dostawców oświadczeń i podróże użytkownika skonfigurowane przez firmę Microsoft<br><br>Minimalne zmiany tego pliku | Brak |
| Rozszerzenia (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidacja zmiany w tym miejscu pliku podstawowego<br><br>Dostawców oświadczeń zmodyfikowane<br><br>Podróże zmodyfikowanego użytkownika<br><br>Definicje własnych niestandardowych schematów | Pliku podstawowego |
| Jednostka uzależniona (RP) | B2C_1A_sign_up_sign_in.xml| Ustawienia tokenu kształt i sesji tutaj| Plik Extensions(ext) |

### <a name="inheritance-model"></a>Model dziedziczenia

Po uruchomieniu pliku zasad planu odzyskiwania, Framework obsługi tożsamości w B2C doda wszystkie elementy z podstawowego, a następnie z ROZSZERZEŃ, a na koniec z pliku zasad planu odzyskiwania można złożyć bieżące zasady obowiązujące.  Elementy tego samego typu i nazwa pliku RP zastępują w ROZSZERZENIACH, i rozszerzenia zastąpień BASE.

**Wbudowane zasady** w usłudze Azure AD B2C wykonaj wzorzec pliku 3 przedstawione powyżej, ale Deweloper widoczny jest tylko plik jednostki uzależnionej strony (RP) podczas portalu dokonuje zmian w tle w pliku EXTenstions.  Wszystkie usługi Azure AD B2C udostępnia podstawowe zasady plik podlega kontroli przez zespół Azure B2C jest często aktualizowana.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md)
