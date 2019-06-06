---
title: Odwołanie — struktury zaufania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat o zasadach niestandardowych usługi Azure Active Directory B2C i platformy środowiska tożsamości.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 47e45a7dac8abc65f414fedd0fd910e3a7a78113
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508822"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiowanie relacji zaufania struktur za pomocą platformy środowiska tożsamości usługi Azure AD B2C

Usługa Azure Active Directory B2C (Azure AD B2C), niestandardowe zasady, które używają platformy środowiska tożsamości zapewnić organizacji scentralizowanej usłudze. Ta usługa zmniejsza złożoność Federację tożsamości w dużych społeczności zainteresowania. Złożoność jest redukowana do pojedynczą relacją zaufania i wymiany jednej metadanych.

Azure AD B2C zasady niestandardowe, które umożliwiają struktura środowiska tożsamości należy odpowiedzieć na następujące pytania:

- Co to są informacje prawne, zabezpieczeń, ochrony prywatności i zasady ochrony danych, które należy przestrzegać?
- Kim są kontakty i jakie procesy staje się akredytowanych uczestnika?
- Kim są dostawcy informacji o tożsamości akredytowanych (znany także jako "dostawców oświadczeń") i do czego oferują one?
- Kim są akredytowanych jednostki uzależnionej strony (i ewentualnie co one wymagają)?
- Co to są techniczne "na potrzeby przesyłu" wymagania współdziałania dla uczestników?
- Co to są reguły operational "środowiska uruchomieniowego", które musi także zostać wymuszony wymiany informacji o tożsamości cyfrowej?

Odpowiedzi na te pytania, utworzenia niestandardowych zasad usługi Azure AD B2C, korzystających z platformy środowiska tożsamości Użyj Framework zaufania (TF). Zastanówmy się, że ta konstrukcja i go zawiera.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Zrozumienie podstaw zarządzania w ramach zaufania i Federacji

W ramach zaufania jest napisane specyfikacji tożsamości, zabezpieczeń, ochrony prywatności i zasady ochrony danych, do których uczestnicy społeczności zainteresowania muszą być zgodne.

Tożsamość federacyjna stanowi podstawę do osiągnięcia weryfikacji tożsamości użytkownika końcowego w skali Internetu. Przez delegowanie zarządzania tożsamościami stronom trzecim, jednej tożsamości cyfrowych dla użytkownika końcowego mogą być używane wiele jednostek zależnych.  

Weryfikacji tożsamości wymaga, że dostawcy tożsamości (IdPs) i dostawców atrybut (AtPs) stosować się do określonych bezpieczeństwa, prywatności i zasad operacyjnych i rozwiązań.  Jeśli nie mogą wykonywać bezpośrednie inspekcji, jednostki uzależnione (jednostek uzależnionych) należy opracować relacje zaufania za pomocą dostawców tożsamości i AtPs zdecydują się na pracy z.  

Wraz z rozwojem liczba konsumenci i dostawcy informacji o tożsamości cyfrowej trudno jest nadal parowania Zarządzanie te relacje zaufania lub nawet parowania exchange techniczne metadanych, które są wymagane dla łączności sieci.  Koncentratory Federacji zostały osiągnięte tylko ograniczony sukces w rozwiązywaniu tych problemów.

### <a name="what-a-trust-framework-specification-defines"></a>Specyfikacja zaufania Framework definiuje
TFs są linchpins modelu Framework zaufania Otwórz tożsamości programu Exchange (OIX), gdzie każdy społeczności zainteresowania podlega szczególnych specyfikacji TF. Definiuje specyfikacji TF:

- **Bezpieczeństwo i prywatność metryki community zainteresowania z definicji:**
    - Poziomy gwarancji (Dowaniu), które są oferowane/wymagane przez uczestników; na przykład, uporządkowany zestaw oceny zaufania autentyczności informacje o tożsamości cyfrowej.
    - Poziomy ochrony (LOP), które są oferowane/wymagane przez uczestników; na przykład, uporządkowany zestaw klasyfikacje zaufanie do ochrony informacji tożsamości cyfrowej, który jest obsługiwany przez uczestnicy społeczności zainteresowania.

- **Opis informacji cyfrowych tożsamość o ma oferowana/jest wymagana przez uczestników**.

- **Techniczne zasad do produkcji i użycie informacji o tożsamości cyfrowej, a zatem pomiaru Dowaniu i LOP. Te zasady napisane zazwyczaj obejmują następujące kategorie zasad:**
    - Tożsamość sprawdzania zasad, na przykład: *Jak zdecydowanie jest sprawdzane informacje o tożsamości danej osoby?*
    - Zasady zabezpieczeń, na przykład: *Jak zdecydowanie są informacje o integralności i poufności chronione?*
    - Zasady ochrony prywatności, na przykład: *Jaki formant użytkownika ma za pośrednictwem osobowe (PII)* ?
    - Przeżywalność zasady, na przykład: *Jeśli dostawca zaprzestaje operacje, jakie ciągłości działania i ochrony danych osobowych funkcji?*

- **Profile techniczne dla celów produkcyjnych i użycie informacji o tożsamości cyfrowej. Tego typu profile uwzględniają:**
    - Interfejsy zakresu, dla których informacji o tożsamości cyfrowej znajduje się w temacie Dowaniu określony.
    - Wymagania techniczne współdziałania w sprawie podczas transmisji.

- **Opisy różnych ról, które mogą wykonywać uczestnicy społeczności i wymagania kwalifikacyjne, które są wymagane do spełnienia tych ról.**

Ten sposób specyfikacji TF decyduje, jak informacje o tożsamości są wymieniane między uczestnikami społeczności zainteresowania: jednostek uzależnionych, tożsamości i dostawców atrybut i atrybut weryfikatory.

Specyfikacja TF jest jeden lub wiele dokumentów, które służą jako odniesienie do nadzoru społeczności zainteresowań, który reguluje potwierdzenia i użycie informacji o tożsamości cyfrowej w ramach społeczności. To zbiór udokumentowanego zasadom i procedurom, zaprojektowane, aby ustanowić zaufanie w tożsamości cyfrowych, które są używane w transakcji internetowych między członkami społeczności zainteresowania.  

Innymi słowy Specyfikacja TF definiuje reguły do tworzenia ekosystemu tożsamości federacyjnych możliwego do użycia dla społeczności.

Obecnie ma umowy powszechne korzyści takie podejście. Brak żadnych wątpliwości, specyfikacje framework ułatwienie rozwoju ekosystemu tożsamości cyfrowej weryfikowalny właściwości zabezpieczeń i zapewnienia prywatności, co oznacza, że można się ponownie między wieloma społeczności o wspólnych zainteresowaniach zaufania.

W tym Przyczyna, niestandardowe zasady usługi Azure AD B2C, korzystających z platformy środowiska tożsamości używa specyfikację jako podstawa jej reprezentacji danych TF ułatwiające współdziałania.  

Funkcje B2C niestandardowe zasady usługi Azure korzystających z platformy środowiska tożsamości reprezentują specyfikacji TF jako kombinację danych człowieka, jak i czytelny dla komputerów. Niektóre sekcje w tym modelu (zazwyczaj sekcje, które są bardziej opracowane głównie pod kątem zarządzania) są reprezentowane jako odwołania w opublikowanej dokumentacji zasad zabezpieczeń i prywatności wraz z procedur powiązanych (jeśli istnieje). Innych sekcjach opisano szczegółowo metadanych i środowisko uruchomieniowe reguł konfiguracji, które ułatwiają automatyzację operacyjnej.

## <a name="understand-trust-framework-policies"></a>Omówienie zasad zaufania Framework

Pod względem implementacji specyfikacji TF składa się z zestawu zasad, umożliwiające pełną kontrolę nad tożsamości zachowania i doświadczenia.  Usługa Azure AD B2C niestandardowych zasad korzystających z platformy środowiska tożsamości umożliwiają tworzenie i tworzyć własne TF za pośrednictwem takie deklaratywne zasad, które można zdefiniować i skonfigurować:

- Odwołanie do dokumentu lub odwołania, które definiują ekosystemu tożsamości federacyjnej, społeczności, które odnoszą się do TF. Są one linki do dokumentacji TF. Reguły (wstępnie zdefiniowane) operational "środowiska uruchomieniowego", lub podróży użytkownika, które zautomatyzować i/lub kontroli programu exchange i użycie oświadczenia. Te podróży użytkownika są skojarzone z Dowaniu (i LOP). Zasady mogą więc podróży użytkownika ze zróżnicowanymi LOAs (i LOPs).

- Dostawcy tożsamości i atrybut, lub dostawców oświadczeń, w społeczności odsetek i profile techniczne, które obsługują wraz z akredytacji Dowaniu/LOP (out-of-band), które odnoszą się do nich.

- Integracja za pomocą atrybutu weryfikatory lub dostawców oświadczeń.

- Jednostki uzależnione w społeczności (przez wnioskowania).

- Metadane do ustanowienia komunikacji sieciowej między uczestnikami. Te metadane, oraz profile techniczne są używane podczas wykonywania transakcji można zainstalować "na potrzeby przesyłu" współdziałanie jednostkę uzależnioną i innych uczestników społeczności.

- Konwersja protokołu, jeśli istnieje (na przykład, SAML 2.0, OAuth2, WS-Federation i OpenID Connect).

- Wymagania dotyczące uwierzytelniania.

- Wieloskładnikowe orkiestracji ewentualne.

- Udostępniony schemat dla wszystkich oświadczeń, które są dostępne i mapowań, aby uczestnicy społeczności zainteresowania.

- Wszystkie przekształcenia oświadczeń, wraz z minimalizacji danych, w tym kontekście, w celu umożliwienia programu exchange i użycie oświadczenia.

- Powiązania i szyfrowania.

- Magazyn oświadczeń.

### <a name="understand-claims"></a>Zrozumienie oświadczeń

> [!NOTE]
> Firma Microsoft wspólnie dotyczą wszystkich możliwych typów informacji o tożsamości, która może być wymieniane jako "oświadczenia": oświadczeń o poświadczenia uwierzytelniania użytkownika końcowego, konsultacji tożsamości, komunikację urządzenia, lokalizacji fizycznej, atrybuty, dane identyfikacyjne i tak dalej.  
>
> Używamy "oświadczenia" — zamiast "atrybuty"--, ponieważ w transakcji w trybie online tych artefaktów danych nie są fakty, które można bezpośrednio sprawdzić przez jednostkę uzależnioną. Przeciwnie są one potwierdzenia lub oświadczeń o fakty, dla których uzależnionej muszą tworzyć wystarczający poziom zaufania, aby udzielić transakcji żądanych przez użytkownika końcowego.  
>
> Korzystamy również termin "oświadczenia", ponieważ zasady niestandardowej usługi Azure AD B2C, które używają platformy środowiska tożsamości są zaprojektowane w celu uproszczenia exchange wszystkich typów informacji o tożsamości cyfrowej w spójny sposób niezależnie od tego, czy jest podstawowym protokole zdefiniowane dla pobierania uwierzytelniania lub atrybutu użytkownika.  Podobnie, używany jest termin "dostawców oświadczeń" do zbiorczo odwoływania się do dostawcy tożsamości, dostawców atrybut i atrybut weryfikatory, gdy nie chcemy rozróżnić określonych funkcji.   

Ten sposób ich określają, jak informacje o tożsamości są wymieniane między jednostki uzależnionej, tożsamości i dostawców atrybut i atrybut weryfikatory. Kontrolują one tożsamość i dostawców atrybutu są wymagane do uwierzytelniania na jednostki uzależnionej ze stron. Powinny zostać uznane jako języka specyficznego dla domeny (DSL), oznacza to, język komputera, który specjalizuje się w domenie określonej aplikacji za pomocą dziedziczenia, *Jeśli* instrukcji, polimorfizmu.

Zasady te stanowią czytelnym część konstrukcji TF w zasadach niestandardowych usługi Azure AD B2C, korzystanie z platformy środowiska tożsamości. Obejmują one wszystkich operacyjnej szczegóły, w tym metadane i profile techniczne, definicje schematów oświadczenia, funkcje przekształcania oświadczeń i podróży użytkownika, które są wypełnione w ułatwienia operacyjnej aranżacji i automatyzacji dostawców oświadczeń.  

One są zakłada się, że *dokumentów życia* ponieważ nie istnieje szansa, że ich zawartość będzie zmieniają się z upływem czasu dotyczące aktywnych uczestników zadeklarowane w zasadach. Istnieje również ryzyko wprowadzenia warunki i postanowienia dla bycia uczestnikiem mogą ulec zmianie.  

Federacji instalacji i konserwacji są znacznie uproszczone dzięki osłony jednostki uzależnionej stron z trwającą reconfigurations zaufania i łączność, jak różne oświadczenia dostawcy/weryfikatory join lub pozostaw (reprezentowane przez społeczność) zestawu zasad.

Współdziałanie jest inny znaczące wyzwane. Dodatkowe oświadczenia dostawcy/weryfikatorzy muszą być zintegrowane, ponieważ jednostki uzależnione prawdopodobnie nie obsługuje niezbędnych protokołów. Niestandardowe zasady usługi Azure AD B2C rozwiązują ten problem, dzięki obsłudze protokoły będące standardami branżowymi i stosując podróży konkretnego użytkownika dla żądania przeniesienia, gdy jednostek uzależnionych i dostawców atrybut nie obsługują tego samego protokołu.  

Podróży użytkownika obejmują profile protokołu i metadanych, które są używane do podłączenia "na potrzeby przesyłu" współdziałanie jednostkę uzależnioną i innych uczestników. Dostępne są także reguły operacyjnej w czasie wykonywania, które są stosowane do komunikatów żądań/odpowiedzi wymiany informacji o tożsamości wymuszania zgodności z zasadami opublikowane w ramach specyfikacji TF. Koncepcja podróży użytkownika jest kluczem do dostosowywania środowiska klienta. On również rzuca światło na, jak system działa na poziomie protokołu.

Na tej podstawie jednostki uzależnionej strony aplikacji i portale można w zależności od ich kontekstami wywoływanie niestandardowych zasad usługi Azure AD B2C, wykorzystujące platformy środowiska tożsamości przekazanie nazwy określone zasady i uzyskać dokładne zachowanie i informacji wymiany chcą, bez żadnych bez żadnych problemów i ryzyka.
