---
title: Odwołanie - platform zaufania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat dotyczący zasad niestandardowych usługi Azure Active Directory B2C i platforma obsługi tożsamości.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b75efe7464c32863781353549f73048b4e127ddf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710223"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiowanie relacji zaufania platformy Framework obsługi tożsamości usługi Azure AD B2C

Usługa Azure Active Directory B2C (Azure AD B2C) zasad niestandardowych, które używają Framework obsługi tożsamości Podaj organizacji za pomocą scentralizowanej usługi. Ta usługa zmniejsza złożoność federacji tożsamości w dużych społeczności zainteresowań. Złożoność ograniczono pojedynczą relacją zaufania i exchange pojedynczego metadanych.

Azure AD B2C niestandardowych zasad, które pozwalają odpowiedzieć na następujące pytania za pomocą architektury środowiska tożsamości:

- Co to są prawnych, zabezpieczeń, prywatności i zasady ochrony danych, które należy przestrzegać?
- Kim są kontaktów i jakie procesy staje się akredytowanego uczestnika?
- Kim są dostawców informacji o tożsamości akredytowanego (znanej także jako "dostawców oświadczeń") i do czego oferują?
- Kim są uzależnione akredytowanego (i opcjonalnie, co to są one wymagane)?
- Co to są techniczne "umieszczonego w" współdziałanie wymagania uczestników?
- Co to są reguły operational "runtime", które muszą zostać wymuszone wymiany informacji o tożsamości cyfrowych?

Odpowiedzi na te pytania, utworzyć zasady niestandardowe usługi Azure AD B2C, które używają Użyj Framework środowisko Identity Framework zaufania (TF). Zastanówmy się, że ta konstrukcja i go zawiera.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Zrozumienie foundation zarządzania w ramach zaufania i Federacji

Framework zaufania jest zapisywane specyfikacji tożsamości, zabezpieczeń, prywatności i zasady ochrony danych, do których uczestnicy społeczności zainteresowania muszą być zgodne.

Tożsamości federacyjnych stanowi podstawę do osiągnięcia weryfikacji tożsamości użytkownika końcowego na dużą skalę Internet. Przez delegowanie zarządzania tożsamościami osobom trzecim, wiele jednostek uzależnionych mogą być używane jednej tożsamości cyfrowych dla użytkownika końcowego.  

Weryfikacji tożsamości wymaga, aby atrybut dostawców (AtPs) i dostawcy tożsamości (IdPs) jest zgodna zabezpieczeń, ochrony prywatności i operacyjne zasady i praktyki dotyczące.  Jeśli nie mogą wykonywać bezpośredniej kontroli, jednostki uzależnione (RPs) należy opracować relacji zaufania z IdPs i AtPs wybiera się on do pracy z.  

Wraz z rozwojem liczba konsumenci i dostawcy informacji o tożsamości cyfrowych, trudno jest nadal zarządzania parowania tych relacji zaufania, lub nawet parowania wymiany metadanych technicznych, która jest wymagana do obsługi łączności sieciowej.  Koncentratory federacyjnego zostały osiągnięte tylko ograniczone sukces w rozwiązywaniu tych problemów.

### <a name="what-a-trust-framework-specification-defines"></a>Definiuje specyfikacji Framework zaufania
TFs są linchpins modelu Framework zaufania Otwórz tożsamości programu Exchange (OIX), gdzie społeczności każdej istotnej podlega warunkom określonym specyfikacji TF. Definiuje specyfikację TF:

- **Zabezpieczenia i prywatność metryki community płynących z definicji:**
    - Poziomy ubezpieczenia (LOA), które są oferowane/wymagane przez uczestników; na przykład, uporządkowany zestaw klasyfikacje zaufania autentyczności informacji o tożsamości cyfrowych.
    - Poziomy ochrony (LOP), które są oferowane/wymagane przez uczestników; na przykład, uporządkowany zestaw klasyfikacje zaufania dla ochrony informacji tożsamości cyfrowych, który jest obsługiwany przez uczestnicy społeczności zainteresowań.

- **Opis tożsamości cyfrowych informacje, które ma oferowana/wymagane przez uczestników**.

- **Techniczne zasady produkcji i zużycia informacji o tożsamości cyfrowych, a zatem pomiaru LOA i LOP. Te zasady napisane zwykle obejmują następujące kategorie zasad:**
    - Tożsamość sprawdzania zasad, na przykład: *jak silnie jest sprawdzane informacje o tożsamości osoby?*
    - Zasady zabezpieczeń, na przykład: *jak silnie są informacje o integralności i poufności chronione?*
    - Zasady zachowania poufności informacji, na przykład: *jakie kontrolki użytkownika ma za pośrednictwem dane osobowe (dane osobowe)*?
    - Zasady przeżywalność, na przykład: *Jeśli dostawca przestaje operacje, jak ciągłości i ochrony danych osobowych funkcji?*

- **Profile techniczne produkcji i zużycia informacji o tożsamości cyfrowych. Takie profile zawierają:**
    - Zakres interfejsy, dla których jest dostępna w określonym LOA informacji o tożsamości cyfrowych.
    - Wymagania techniczne dotyczące współdziałania o transmisji.

- **Opisy różnych ról, które mogą wykonywać uczestnicy społeczności i kwalifikacji, które są wymagane do wykonania tych ról.**

W związku z tym specyfikacji TF kontroluje sposób wymiany informacji o tożsamości między uczestnikami społeczności odsetek: uzależnionych, tożsamości i dostawców atrybut i atrybut weryfikatory.

Specyfikacja TF jest jednym lub wieloma dokumentami służących jako punkt odniesienia dla zarządu Wspólnoty odsetek reguluje potwierdzenia i użycie informacji cyfrowych tożsamość w ramach społeczności. Jest udokumentowane zbiór zasadami i procedurami przeznaczone do ustanawiania relacji zaufania w tożsamości cyfrowych, które są używane w transakcji internetowych między członkami społeczności odsetek.  

Innymi słowy Specyfikacja TF definiuje reguły tworzenia ekosystemu działało tożsamości federacyjnych dla społeczności.

Obecnie jest powszechnie zgodą korzyści takie podejście. Brak bez wątpienia który specyfikacje framework ułatwić projektowanie tożsamości cyfrowych ekosystemu zweryfikowania właściwości zabezpieczeń, bezpieczeństwa i ochrony prywatności, co oznacza, że może być ponownie między kilka Wspólnot odsetek zaufania.

Z tego powodu zasad niestandardowych usługi Azure AD B2C, które używają Framework obsługi tożsamości używa specyfikacji jako podstawa jej reprezentację danych TF ułatwia współdziałanie.  

Azure zasady niestandardowe AD B2C, korzystających z tożsamości Framework środowisko reprezentują specyfikacji TF jako kombinacja odczytania przez człowieka i danych. Niektóre sekcje w tym modelu (zazwyczaj sekcje, które są bardziej opracowane głównie pod kątem zarządzania) są reprezentowane jako odwołania do opublikowanych dokumentacji zasad zabezpieczeń i prywatności oraz procedur powiązanych (jeśli istnieje). Innych sekcjach opisano szczegółowo konfiguracji metadanych i środowiska uruchomieniowego reguły ułatwiających operacyjne automatyzacji.

## <a name="understand-trust-framework-policies"></a>Zrozumieć zasady zaufania Framework

W implementacji Specyfikacja TF składa się z zestawu zasad, umożliwiające pełną kontrolę nad zachowania tożsamości i środowiska.  Azure AD B2C niestandardowych zasad korzystających z Framework obsługi tożsamości umożliwiają tworzenie i tworzyć własne TF za pomocą takich deklaratywne zasad, które można zdefiniować i skonfigurować:

- Odwołania do dokumentu lub zdefiniuj ekosystemu tożsamości federacyjnych społeczności, które odnoszą się do TF odwołań. Są one łącza do dokumentacji TF. Reguły (wstępnie zdefiniowane) operational "runtime" lub podróże użytkownika, które zautomatyzować i/lub sterowania programu exchange i użycia oświadczeń. Te podróże użytkowników są skojarzone z LOA (i LOP). Zasady mogą więc podróże użytkownika ze zróżnicowanymi LOAs (i LOPs).

- Dostawcy tożsamości i atrybutu, lub dostawców oświadczeń, społeczności odsetek i profili technicznych, które obsługują wraz z akredytacji LOA/LOP (poza pasmem), które odnoszą się do nich.

- Integracja z atrybutu weryfikatory lub dostawców oświadczeń.

- Jednostki uzależnione mogą w społeczności (za pomocą wnioskowania).

- Metadane dla nawiązywania komunikacji sieciowej między uczestników. Te metadane, oraz profile technicznych, są używane podczas transakcji łączący "umieszczonego w" współdziałanie jednostki uzależnionej, a inni uczestnicy społeczności.

- Konwersja protokołu ewentualne (na przykład SAML, OAuth2, WS-Federation i OpenID Connect).

- Wymagania dotyczące uwierzytelniania.

- Wieloskładnikowego aranżacji ewentualne.

- Udostępniony schemat wszystkie oświadczenia, które są dostępne i ich mapowania na uczestnicy społeczności zainteresowań.

- Wszystkie oświadczenia przekształcenia, wraz z minimalizacja danych w tym kontekście, do obsługi programu exchange i użycie oświadczeń.

- Powiązanie i szyfrowania.

- Magazyn oświadczeń.

### <a name="understand-claims"></a>Zrozumienie oświadczeń

> [!NOTE]
> Firma Microsoft zbiorczo odnoszą się do wszystkich możliwych typów informacji o tożsamości, które mogą być wymieniane jako "oświadczenia": oświadczeń o poświadczenie uwierzytelnienia użytkownika końcowego, czasie głosowania nad tożsamości, urządzenia komunikacji, lokalizacji fizycznej, umożliwiających zidentyfikowanie atrybuty, i tak dalej.  
>
> Używamy "oświadczenia"--zamiast "attributes"--, ponieważ w transakcji w trybie online tych artefaktów danych nie są fakty, które można bezpośrednio sprawdzić przez jednostkę uzależnioną. A nie są one potwierdzeń lub oświadczeń o fakty, których jednostki uzależnionej należy opracować wystarczający poziom zaufania udzielenia transakcji żądanej przez użytkownika końcowego.  
>
> Również używany jest termin "oświadczenia" ponieważ uprościć wymiany wszystkie rodzaje informacji o tożsamości cyfrowych w sposób ciągły, niezależnie od tego, czy podstawowy protokół usługi Azure AD B2C zasad niestandardowych, które używają Framework obsługi tożsamości zdefiniowane dla pobierania atrybut lub uwierzytelniania użytkownika.  Podobnie, używany jest termin "dostawców oświadczeń" Aby zbiorczo odwołać się do dostawców tożsamości, dostawców atrybut i atrybut weryfikatory, gdy nie chcemy rozróżnienia określonych funkcji.   

W związku z tym określają one, jak informacje o tożsamości są wymieniane między uzależniona, tożsamości i dostawców atrybut i atrybut weryfikatory. Decydować, tożsamość i dostawców atrybut są wymagane do uwierzytelniania jednostką uzależnioną. Powinny zostać uznane jako języka specyficznego dla domeny (DSL), czyli języku komputera, który ma przeznaczone do domeny określonej aplikacji z dziedziczenia, *Jeśli* instrukcje, polimorfizm.

Zasady te stanowią czytelną część konstrukcji TF w zasadach niestandardowych usługi Azure AD B2C, wykorzystując Framework obsługi tożsamości. Obejmują one wszystkich szczegółów operacyjnych, takich jak metadanych i technicznych profile, definicje schematów oświadczeń, funkcje przekształcania oświadczeń i podróże użytkownika, które są wypełnione w celu ułatwienia operacyjne aranżacji i automatyzacji dostawców oświadczeń.  

One muszą być *dokumenty życia* ponieważ istnieje szansa, że ich zawartość będzie zmieniać wraz z upływem czasu dotyczących aktywnych uczestników zadeklarowany w zasadach. Istnieje również możliwość, że warunki i postanowienia dla trwa uczestnika mogą ulec zmianie.  

Federacyjna instalacja i konserwacja znacząco uproszczony przez osłony uzależnione od bieżących reconfigurations zaufania i łączność, jak różne oświadczenia dostawców/weryfikatory join lub pozostaw (community reprezentowany przez) zestawu zasad.

Inne wyzwanie istotne jest współdziałanie. Oświadczenia dodatkowych dostawców/weryfikatory muszą być zintegrowane, ponieważ jednostki uzależnione mogą prawdopodobnie nie obsługuje wszystkie niezbędne protokoły. Niestandardowe zasady usługi Azure AD B2C rozwiązać ten problem dzięki obsłudze standardowych protokołów i stosując podróże określonego użytkownika dla żądania przeniesienia, gdy jednostek uzależnionych i dostawców atrybut nie obsługują tego samego protokołu.  

Podróże użytkownika obejmują profile protokołu i metadanych, które są używane do podłączenia "umieszczonego w" współdziałanie uzależnionej i innych uczestników. Istnieją reguły operacyjne środowiska uruchomieniowego, które są stosowane do komunikaty żądania/odpowiedzi wymiany informacji o tożsamości wymuszania zgodności z zasadami opublikowanych jako część specyfikacji TF. Pomysł podróże użytkownika ma kluczowe znaczenie dla dostosowania obsługi klienta. On również rzucają światła na działanie systemu na poziomie protokołu.

Na tej podstawie jednostki uzależnionej aplikacji i portale można w zależności od ich kontekstu wywołania zasad niestandardowych usługi Azure AD B2C, korzystających z Framework obsługi tożsamości, nazwa określone zasady i uzyskać dokładnie exchange zachowanie i informacji mają one bez żadnych bez żadnych problemów i ryzyka.
