---
title: Odwołanie — struktury zaufania w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Temat dotyczący zasad niestandardowych usługi Azure Active Directory B2C i struktury środowiska tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188889"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiowanie struktur zaufania za pomocą platformy środowiska tożsamości usługi Azure AD B2C

Zasady niestandardowe usługi Azure Active Directory B2C (Azure AD B2C), które korzystają z struktury środowiska tożsamości, zapewniają organizacji scentralizowaną usługę. Ta usługa zmniejsza złożoność federacji tożsamości w dużej społeczności zainteresowania. Złożoność jest zredukowana do jednej relacji zaufania i pojedynczej wymiany metadanych.

Zasady niestandardowe usługi Azure AD B2C używają struktury środowiska tożsamości, aby umożliwić odpowiadanie na następujące pytania:

- Jakie są zasady prawne, bezpieczeństwa, prywatności i ochrony danych, których należy przestrzegać?
- Kim są kontakty i jakie są procesy stawunia się akredytowanym uczestnikiem?
- Kim są akredytowani dostawcy informacji o tożsamości (znani również jako "dostawcy oświadczeń") i co oferują?
- Kim są akredytowane podmioty uzależniające (i opcjonalnie, czego potrzebują)?
- Jakie są techniczne wymagania interoperacyjności "na drutach" dla uczestników?
- Jakie są operacyjne reguły "runtime", które muszą być wymuszane w celu wymiany informacji o tożsamości cyfrowej?

Aby odpowiedzieć na wszystkie te pytania, zasady niestandardowe usługi Azure AD B2C, które używają struktury środowiska tożsamości, należy użyć konstrukcji struktury zaufania (TF). Rozważmy tę konstrukcję i to, co zapewnia.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Poznaj podstawę zarządzania ramami zaufania i federacją

Struktura zaufania to pisemna specyfikacja zasad dotyczących tożsamości, bezpieczeństwa, prywatności i ochrony danych, do których muszą być zgodne uczestnicy społeczności będącej przedmiotem zainteresowania.

Tożsamość federacyjnej stanowi podstawę do uzyskania pewności tożsamości użytkownika końcowego w skali internetowej. Delegując zarządzanie tożsamościami stronom trzecim, pojedyncza tożsamość cyfrowa dla użytkownika końcowego może być ponownie usuowana z wieloma jednostkami uzależniającymi.

Zapewnienie tożsamości wymaga, aby dostawcy tożsamości (IdPs) i dostawcy atrybutów (AtPs) przestrzegali określonych zasad i praktyk zabezpieczeń, prywatności i operacyjnych.  Jeśli nie mogą przeprowadzać bezpośrednich inspekcji, podmioty uzależniające muszą rozwijać relacje zaufania z ips i atps, z którymi decydują się pracować.

Wraz ze wzrostem liczby konsumentów i dostawców informacji o tożsamości cyfrowej trudno jest kontynuować łączenie tych relacji zaufania, a nawet w parze wymieniać metadane techniczne, które są wymagane do łączności sieciowej.  Centra federacji osiągnęły tylko ograniczony sukces w rozwiązywaniu tych problemów.

### <a name="what-a-trust-framework-specification-defines"></a>Co definiuje specyfikacja struktury zaufania
TFs są linchpins modelu Open Identity Exchange (OIX) Trust Framework, gdzie każda społeczność interesów jest regulowana przez określoną specyfikację TF. Taka specyfikacja TF definiuje:

- **Wskaźniki bezpieczeństwa i prywatności dla społeczności będącej przedmiotem zainteresowania z definicją:**
    - Poziomy pewności (LOA), które są oferowane/wymagane przez uczestników; na przykład uporządkowany zestaw ocen zaufania dla autentyczności informacji o tożsamości cyfrowej.
    - Poziomy ochrony (LOP), które są oferowane/wymagane przez uczestników; na przykład uporządkowany zestaw ocen zaufania dla ochrony informacji o tożsamości cyfrowej, które są obsługiwane przez uczestników społeczności zainteresowania.

- **Opis informacji o tożsamości cyfrowej oferowanych/wymaganych przez uczestników.**

- **Polityka techniczna dotycząca produkcji i konsumpcji cyfrowych informacji o tożsamości, a tym samym pomiaru LOA i LOP. Te pisemne zasady zazwyczaj obejmują następujące kategorie zasad:**
    - Zasady sprawdzania tożsamości, na przykład: *Jak silnie sprawdzane są informacje o tożsamości danej osoby?*
    - Zasady bezpieczeństwa, na przykład: *Jak silnie chroni się integralność i poufność informacji?*
    - Zasady ochrony prywatności, na przykład: *Jaką kontrolę ma użytkownik nad danymi osobowymi umożliwiającymi identyfikację?*
    - Zasady przetrwania, na przykład: *Jeśli dostawca przestaje działać, w jaki sposób działa ciągłość i ochrona danych umożliwiających identyfikację?*

- **Profile techniczne do produkcji i konsumpcji cyfrowych informacji o tożsamości. Profile te obejmują:**
    - Interfejsy zakresu, dla których informacje o tożsamości cyfrowej są dostępne w określonym LOA.
    - Wymagania techniczne dotyczące interoperacyjności on-the-wire.

- **Opisy różnych ról, które mogą wykonywać uczestnicy społeczności i kwalifikacje, które są wymagane do wypełnienia tych ról.**

W ten sposób specyfikacja TF reguluje sposób wymiany informacji o tożsamości między uczestnikami społeczności interesów: jednostkami uzależniającymi, dostawcami tożsamości i atrybutów oraz weryfikatorami atrybutów.

Specyfikacja TF jest jednym lub wieloma dokumentami, które służą jako punkt odniesienia dla zarządzania społecznością interesów, która reguluje twierdzenie i zużycie informacji o tożsamości cyfrowej w społeczności. Jest to udokumentowany zestaw zasad i procedur mających na celu ustanowienie zaufania do tożsamości cyfrowych, które są używane do transakcji online między członkami społeczności zainteresowania.

Innymi słowy specyfikacja TF definiuje zasady tworzenia opłacalnego ekosystemu tożsamości federacyjnej dla społeczności.

Obecnie istnieje powszechne porozumienie co do korzyści z takiego podejścia. Nie ma wątpliwości, że specyfikacje ram zaufania ułatwiają rozwój ekosystemów tożsamości cyfrowej o sprawdzalnych cechach bezpieczeństwa, pewności i prywatności, co oznacza, że mogą być ponownie użytkowane w wielu interesujących społecznościach.

Z tego powodu zasady niestandardowe usługi Azure AD B2C, które używają struktury środowiska tożsamości używa specyfikacji jako podstawy jego reprezentacji danych dla TF w celu ułatwienia współdziałania.

Zasady niestandardowe usługi Azure AD B2C, które wykorzystują platformę środowiska tożsamości, reprezentują specyfikację TF jako mieszaninę danych umożliwiających odczytanie przez człowieka i dane z komputera. Niektóre sekcje tego modelu (zazwyczaj sekcje, które są bardziej zorientowane na zarządzanie) są reprezentowane jako odwołania do opublikowanej dokumentacji zasad zabezpieczeń i prywatności wraz z powiązanymi procedurami (jeśli istnieją). Inne sekcje opisują szczegółowo metadane konfiguracji i reguły środowiska uruchomieniowego, które ułatwiają automatyzację operacyjną.

## <a name="understand-trust-framework-policies"></a>Poznaj zasady struktury zaufania

Pod względem implementacji specyfikacji TF składa się z zestawu zasad, które umożliwiają pełną kontrolę nad zachowaniami tożsamości i środowiska.  Zasady niestandardowe usługi Azure AD B2C, które wykorzystują platformę środowiska tożsamości, umożliwiają tworzenie i tworzenie własnych tf za pomocą takich zasad deklaratywnych, które można zdefiniować i skonfigurować:

- Odwołanie do dokumentu lub odwołania, które definiują sfederowany ekosystem tożsamości społeczności, który odnosi się do TF. Są to linki do dokumentacji TF. (Wstępnie zdefiniowane) operacyjne reguły "runtime" lub procesy pozyskiwania użytkowników, które automatyzują i/lub kontrolują wymianę i użycie oświadczeń. Te podróże użytkownika są skojarzone z LOA (i LOP). Zasady mogą zatem mieć podróże użytkowników z różnymi LOA (i LOPs).

- Dostawcy tożsamości i atrybutów lub dostawcy oświadczeń w społeczności będącej przedmiotem zainteresowania oraz profile techniczne, które wspierają, wraz z (pozapasmową) akredytacją LOA/LOP, która się do nich odnosi.

- Integracja z weryfikatorami atrybutów lub dostawcami oświadczeń.

- Podmioty uzależniające we wspólnocie (przez wnioskowanie).

- Metadane do ustanawiania komunikacji sieciowej między uczestnikami. Te metadane, wraz z profilami technicznymi, są używane podczas transakcji do pionu "na przewod" interoperacyjności między jednostką uzależniającą i innych uczestników społeczności.

- Konwersja protokołu, jeśli istnieje (na przykład SAML 2.0, OAuth2, Federacja WS i OpenID Connect).

- Wymagania dotyczące uwierzytelniania.

- Aranżacja wieloczynnikowa, jeśli istnieje.

- Współużytkowane schemat dla wszystkich oświadczeń, które są dostępne i mapowania do uczestników społeczności zainteresowania.

- Wszystkie przekształcenia oświadczeń, wraz z możliwością minimalizacji danych w tym kontekście, aby utrzymać wymianę i wykorzystanie oświadczeń.

- Powiązanie i szyfrowanie.

- Magazyn oświadczeń.

### <a name="understand-claims"></a>Zrozumienie oświadczeń

> [!NOTE]
> Wspólnie odnosimy się do wszystkich możliwych typów informacji o tożsamości, które mogą być wymieniane jako "oświadczenia": oświadczenia dotyczące poświadczeń uwierzytelniających użytkownika końcowego, weryfikacji tożsamości, urządzenia komunikacyjnego, fizycznej lokalizacji, cech identyfikujących dane osobowe, i tak dalej.
>
> Używamy terminu "oświadczenia" — a nie "atrybuty" - ponieważ w transakcjach online te artefakty danych nie są faktami, które mogą być bezpośrednio zweryfikowane przez jednostkę uzależniającą. Są to raczej twierdzenia lub roszczenia dotyczące faktów, dla których strona uzależniona musi rozwinąć wystarczające zaufanie do udzielenia żądanej transakcji użytkownika końcowego.
>
> Używamy również terminu "oświadczenia", ponieważ zasady niestandardowe usługi Azure AD B2C korzystające z struktury środowiska tożsamości mają na celu uproszczenie wymiany wszystkich typów informacji o tożsamości cyfrowej w spójny sposób, niezależnie od tego, czy protokół bazowy jest zdefiniowane do uwierzytelniania użytkownika lub pobierania atrybutów.  Podobnie używamy terminu "dostawcy oświadczeń", aby wspólnie odwoływać się do dostawców tożsamości, dostawców atrybutów i przypisywać weryfikatorów, gdy nie chcemy rozróżniać ich określonych funkcji.

W ten sposób regulują one sposób wymiany informacji o tożsamości między jednostką uzależnioną, dostawcami tożsamości i atrybutów oraz weryfikatorami atrybutów. Kontrolują dostawców tożsamości i atrybutów są wymagane do uwierzytelniania jednostki uzależnionej. Należy je uznać za język specyficzny dla domeny (DSL), czyli język komputerowy, który jest wyspecjalizowany dla określonej domeny aplikacji z dziedziczeniem, *jeśli* instrukcje, polimorfizm.

Te zasady stanowią część konstruowania TF do odczytu maszynowego w zasadach niestandardowych usługi Azure AD B2C wykorzystujących strukturę środowiska tożsamości. Obejmują one wszystkie szczegóły operacyjne, w tym metadane dostawców oświadczeń i profile techniczne, definicje schematów oświadczeń, funkcje przekształcania oświadczeń i procesy pozyskiwania użytkowników, które są wypełniane w celu ułatwienia aranżacji operacyjnej i automatyzacji.

Zakłada się, że są to *dokumenty żywe,* ponieważ istnieje duża szansa, że ich treść zmieni się w czasie w odniesieniu do aktywnych uczestników zadeklarowanych w polityce. Istnieje również możliwość zmiany warunków uczestnictwa w konkursie.

Konfiguracja i konserwacja federacji są znacznie uproszczone, chroniąc podmioty uzależniające przed bieżącymi rekonfiguracjami zaufania i łączności, ponieważ różni dostawcy oświadczeń/weryfikatorzy dołączają lub pozostawiają (społeczność reprezentowaną przez) zestaw zasad.

Interoperacyjność jest kolejnym istotnym wyzwaniem. Dodatkowe dostawcy oświadczeń / weryfikatorów muszą być zintegrowane, ponieważ jednostki uzależniające są mało prawdopodobne, aby wspierać wszystkie niezbędne protokoły. Zasady niestandardowe usługi Azure AD B2C rozwiązują ten problem, obsługując protokoły zgodne ze standardami branżowymi i stosując procesy pozyskiwania określonych użytkowników w celu transpozycji żądań, gdy jednostki uzależniające i dostawcy atrybutów nie obsługują tego samego protokołu.

Podróże użytkowników obejmują profile protokołów i metadane, które są używane do pionu "na przewod" współdziałanie między jednostką uzależniającą i innymi uczestnikami. Istnieją również reguły środowiska wykonawczego operacyjnego, które są stosowane do komunikatów żądania/odpowiedzi wymiany informacji o tożsamości w celu wymuszania zgodności z opublikowanymi zasadami w ramach specyfikacji TF. Idea podróży użytkownika jest kluczem do dostosowania doświadczenia klienta. Rzuca również światło na to, jak działa system na poziomie protokołu.

Na tej podstawie aplikacje i portale jednostki uzależniającej mogą, w zależności od kontekstu, wywoływać niestandardowe zasady usługi Azure AD B2C, które wykorzystują platformę środowiska tożsamości przekazującą nazwę określonej zasady i dokładnie otrzymują zachowanie i wymianę informacji chcą bez żadnych muss, zamieszanie, lub ryzyka.
