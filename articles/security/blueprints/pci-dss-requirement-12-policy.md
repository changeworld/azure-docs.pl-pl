---
title: Plan Azure przetwarzania płatności — wymagania dotyczące zasad
description: Wymaganie PCI DSS 12
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2fb238e9b95180d6156159c87ec008a71943e698
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące zasad dla PCI DSS zgodne środowisk  
## <a name="pci-dss-requirement-12"></a>Wymaganie PCI DSS 12

**Obsługa zasad, które dotyczy informacji zabezpieczeń dla wszystkich pracowników**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Zasady silne zabezpieczenie ustawia sygnał zabezpieczeń dla całej jednostki i informuje o personelu oczekiwano z nich. Wszystkie osoby należy zwrócić uwagę wrażliwość danych i ich obowiązków jego ochrony. Na potrzeby 12 wymaganie "personel" odwołuje się do pełnym i niepełnym pracowników tymczasowych, wykonawców oraz doradców IT, którzy są "rezydentna" w witrynie jednostki lub w przeciwnym razie ma dostęp do środowiska danych imię.

## <a name="pci-dss-requirement-121"></a>Wymaganie PCI DSS 12.1

**12.1** Ustanów, publikowania, obsługiwania i rozpowszechniać zasady zabezpieczeń.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do nawiązywania i utrzymywania zasad zabezpieczeń informacji.|



### <a name="pci-dss-requirement-1211"></a>Wymaganie PCI DSS 12.1.1

**12.1.1** co najmniej raz Przejrzyj zasady zabezpieczeń i aktualizowania zasad, po zmianie środowiska.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do aktualizowania swoich informacji zasad zabezpieczeń co najmniej raz, lub gdy zmian w środowisku danych posiadacza (CRP).|



## <a name="pci-dss-requirement-122"></a>Wymaganie PCI DSS 12.2

**12.2** wdrożenie oceny ryzyka Przetwarzaj tego:
- Jest wykonywane co najmniej raz, po znaczących zmian w środowisku (na przykład nabycia, łączenia, przeniesienie itp.)
- Identyfikuje krytyczne zasoby, zagrożeń i luk w zabezpieczeniach
- Wyniki analizy formalne, udokumentowanych ryzyka.
- > Przykłady metodologii oceny ryzyka obejmować, ale nie są ograniczone do 800-30 OKTAWY, ISO 27005 i NIST SP.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do implementowania proces oceny ryzyka, która dotyczy wszystkich zagrożeń 12.2 zapotrzebowania na liście.|



## <a name="pci-dss-requirement-123"></a>Wymaganie PCI DSS 12.3

**12.3** opracowanie zasad użytkowania dla technologii krytyczne i zdefiniuj właściwe korzystanie z tych technologii.

> [!NOTE]
> Przykłady krytyczne technologie obejmują, ale nie są ograniczone do dostępu zdalnego i sieci bezprzewodowych, laptopy, tablety, elektronicznych nośnik wymienny, korzystania z poczty e-mail i użycia internetowego.
Upewnij się, że tych zasad użytkowania wymagają następujących czynności.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1231"></a>Wymaganie PCI DSS 12.3.1

**12.3.1** wyraźnej zgody przez autoryzowanego strony

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1232"></a>Wymaganie PCI DSS 12.3.2

**12.3.2** uwierzytelniania do użycia technologii

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1233"></a>Wymaganie PCI DSS 12.3.3

**12.3.3** listę takich urządzeń i personelu dysponujący dostępem

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1234"></a>Wymaganie PCI DSS 12.3.4

**12.3.4** metodę dokładnie i łatwo określić właściciela, informacje kontaktowe i celu (na przykład etykietowanie, kodowanie i/lub tworzenia spisu urządzeń)

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1235"></a>Wymaganie PCI DSS 12.3.5

**12.3.5** dopuszczalnych działań korzysta z technologii

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1236"></a>Wymaganie PCI DSS 12.3.6

**12.3.6** lokalizacje sieciowe akceptowalne dla technologii

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do określenia lokalizacji sieciowych akceptowalne dla maszyn wirtualnych w chmurze, magazynu i usług pomocniczych.|



### <a name="pci-dss-requirement-1237"></a>Wymaganie PCI DSS 12.3.7

**12.3.7** listę produktów firmowego

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do określenia lokalizacji sieciowych akceptowalne dla maszyn wirtualnych w chmurze, magazynu i usług pomocniczych.|



### <a name="pci-dss-requirement-1238"></a>Wymaganie PCI DSS 12.3.8

**12.3.8** automatycznego rozłączenia sesji dla technologii dostępu zdalnego po określonym czasie braku aktywności

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykorzystuje funkcję blokady sesji AD firmy Microsoft, która wymusza dokumentów blokady sesji po okresie braku aktywności. Połączenia sieciowe są zakończone po 30 minutach braku aktywności. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1239"></a>Wymaganie PCI DSS 12.3.9

**12.3.9** aktywacji technologii dostępu zdalnego dla dostawców i partnerów biznesowych tylko wtedy, gdy jest to wymagane przez dostawców i partnerów biznesowych z bezpośrednim dezaktywacji po użyciu

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-12310"></a>Wymaganie PCI DSS 12.3.10

**12.3.10** dla personelu podczas uzyskiwania dostępu do danych posiadacza za pomocą technologii dostępu zdalnego, zakazać kopiowanie, przenoszenie i przechowywanie danych posiadacza na lokalnych dyskach twardych i nośniki wymienne, elektronicznych, chyba że dla zdefiniowanej potrzeb biznesowych.
W przypadku, gdy istnieje potrzeba biznesowa autoryzowanych, dane można chronić zgodnie z wszystkich odpowiednich PCI DSS wymagania dotyczące zasad użytkowania może wymagać.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zapewnienia, że personel podczas uzyskiwania dostępu do danych posiadacza za pomocą technologii dostępu zdalnego są zabronione w myśl kopiowanie, przenoszenie i przechowywanie danych posiadacza na lokalnych dyskach twardych i nośniki wymienne, elektronicznych, chyba że do określonych potrzeb.|



## <a name="pci-dss-requirement-124"></a>Wymaganie PCI DSS 12.4

**12.4** upewnij się, zasady zabezpieczeń i procedur jasno zdefiniować obowiązki związane z zabezpieczeniami informacji dla wszystkich pracowników.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1241"></a>Wymaganie PCI DSS 12.4.1

**12.4.1** dodatkowych wymogów dotyczących tylko dostawców usług: zarządzania zarządzający ustanawia odpowiedzialność za ochronę danych posiadacza i PCI DSS zgodności programu do uwzględnienia:
- Ogólna odpowiedzialność za utrzymanie zgodności PCI DSS
- Definiowanie karty komunikacji i PCI DSS zgodności programu do zarządu 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klientów, którzy dostawcy usług są odpowiedzialne za dokumentację programu zgodności PCI.|



## <a name="pci-dss-requirement-125"></a>Wymaganie PCI DSS 12,5

**12,5** przypisać osobie lub zespołu następujące obowiązki zarządzania zabezpieczeń informacji.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do definiowania i przypisywanie informacji o swoim pracownikom obowiązki związane z zabezpieczeniami.|



### <a name="pci-dss-requirement-1251"></a>Wymaganie PCI DSS 12.5.1

**12.5.1** Ustanów, dokumentów i dystrybucji zasad zabezpieczeń i procedur.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do definiowania i przypisywanie informacji o swoim pracownikom obowiązki związane z zabezpieczeniami.|



### <a name="pci-dss-requirement-1252"></a>Wymaganie PCI DSS 12.5.2

**12.5.2** monitora i analizowanie alerty zabezpieczeń i informacje i dystrybucji do odpowiedniego personelu.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do definiowania i przypisywanie informacji o swoim pracownikom obowiązki związane z zabezpieczeniami.|



### <a name="pci-dss-requirement-1253"></a>Wymaganie PCI DSS 12.5.3

**12.5.3** Ustanów, dokumentów i dystrybucji zabezpieczeń zdarzenia Eskalacja i odpowiedź procedur w celu zapewnienia szybkiego i skutecznego obsługę wszystkich sytuacji.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1254"></a>Wymaganie PCI DSS 12.5.4

**12.5.4** administrowania kontami użytkowników, w tym dodawanie, usuwanie i modyfikowanie.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



### <a name="pci-dss-requirement-1255"></a>Wymaganie PCI DSS 12.5.5

**12.5.5** monitorowanie i sterowanie wszystkie dostępu do danych.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad dyktowanie prawidłowego użycia, wdrażania i uwierzytelniania dla technologii krytyczne w ich CRP.|



## <a name="pci-dss-requirement-126"></a>Wymaganie PCI DSS 12,6

**12,6** wdrożenia programu świadomości posiadanie zabezpieczeń w celu uświadomić wszyscy pracownicy znaczenie zasady zabezpieczeń danych posiadacza i procedur.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia i obsługi zasad otaczającego rozpoznawanie zabezpieczeń dla pracowników z dostępem do CRP.|



### <a name="pci-dss-requirement-1261"></a>Wymaganie PCI DSS 12.6.1

**12.6.1** Poinformuj pracowników na zatrudnienia i co najmniej raz. 

> [!NOTE]
> Metody zależy od roli pracowników i ich poziomu dostępu do danych imię.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zapewnienia personelu odbierają i potwierdzić bezpieczeństwo informacji i PCI-DSS świadomości szkolenia co najmniej raz.|



### <a name="pci-dss-requirement-1262"></a>Wymaganie PCI DSS 12.6.2

**12.6.2** pracowników co najmniej raz potwierdzić, że ich otwierdzam przeczytanie i zrozumienie zasady zabezpieczeń i procedur.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zapewnienia personelu odbierają i potwierdzić bezpieczeństwo informacji i PCI-DSS świadomości szkolenia co najmniej raz.|



## <a name="pci-dss-requirement-127"></a>Wymaganie PCI DSS 12,7

**12,7** ekranu potencjalnych personelu przed zatrudnienia, aby zminimalizować ryzyko ataków z wewnętrznych źródeł. (Kontrole przykłady poprzedniej historii zatrudnienia, karnej rekordu historii kredytów i odwołanie sprawdza.) 

> [!NOTE]
> Dla tych potencjalnych pracownikom zatrudnić dla niektórych pozycje takie jak kasjerzy magazynu, tylko mających dostęp do jednej karty numeru w czasie gdy ułatwienia transakcji, to wymaganie dotyczy tylko zalecenia.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zapewnienia pracowników z dostępem do CDE przejście dokładnego kontrole.|



## <a name="pci-dss-requirement-128"></a>Wymaganie PCI DSS 12,8

**12,8** zarządzania i wdrażania zasad i procedur, aby zarządzać dostawcami usługi, z którymi posiadacza dane są udostępniane, lub które mogą mieć wpływ na bezpieczeństwo danych posiadacza w następujący sposób.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do monitorowania zgodności PCI dla dostawców usług, z którymi są udostępniane dane posiadacza lub może mieć wpływ na bezpieczeństwo CDE. Klienci muszą zachować zawiera listę wszystkich usług używane w ramach ich CRP.|



### <a name="pci-dss-requirement-1281"></a>Wymaganie PCI DSS 12.8.1

**12.8.1** utrzymywać listę dostawców usług, w tym opis usługi.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do monitorowania zgodności PCI dla dostawców usług, z którymi są udostępniane dane posiadacza lub może mieć wpływ na bezpieczeństwo CDE. Klienci muszą zachować zawiera listę wszystkich usług używane w ramach ich CRP.|



### <a name="pci-dss-requirement-1282"></a>Wymaganie PCI DSS 12.8.2

**12.8.2** Obsługa pisemne umowy, obejmująca które dostawcy usług są odpowiedzialni za bezpieczeństwo danych posiadacza dostawcy usług posiadał lub inny sposób przechowywania, przetwarzania lub transmisji w imieniu klienta, lub w takim zakresie, w jakim może mieć wpływ zabezpieczeń klienta posiadacza danych środowiska. 

> [!NOTE]
> Dokładna treść potwierdzenia będzie zależeć od Umowę między stronami, szczegóły usługi dostarczanej i obowiązki przypisywane do każdej strony. Potwierdzenia musi zawierać dokładną treść w to wymaganie.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do przechowywania pisemne umowy z potwierdzeniem odpowiedzialność za zabezpieczenie posiadacza danych dostawcy usług.|



### <a name="pci-dss-requirement-1283"></a>Wymaganie PCI DSS 12.8.3

**12.8.3** zapewnienia ustalonych procesów do wykonywania dostawcy usług w tym prawidłowego ukończenia przed starannością do zaangażowania.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są odpowiedzialne za zapewnienie, że istnieje ustalonych procesów do wykonywania dostawcy usług w tym prawidłowego ukończenia przed starannością do zaangażowania.|



### <a name="pci-dss-requirement-1284"></a>Wymaganie PCI DSS 12.8.4

**12.8.4** Obsługa programu do monitorowania stanu zgodności PCI DSS dostawcy usług w co najmniej raz.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci jest odpowiedzialny za konserwację programu do monitorowania stanu zgodności PCI DSS dostawcy usług w co najmniej raz.|



### <a name="pci-dss-requirement-1285"></a>Wymaganie PCI DSS 12.8.5

**12.8.5** zachować informacje o używanych wymaganiach PCI DSS są zarządzane przez każdy dostawca usług i które są zarządzane przez obiekt.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zachowania kopię [odpowiedzialność podsumowanie macierzy](https://aka.ms/pciblueprintcrm32), który opisano wymagania PCI DSS, które są odpowiedzialne za klienta, jak i tych, które są odpowiedzialne za Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Wymaganie PCI DSS 12.9

**12.9** dodatkowych wymogów dotyczących tylko dostawców usług: usługodawców potwierdza na piśmie klientów, które są odpowiedzialne za bezpieczeństwa posiadacza danych dostawcy usług posiada lub w przeciwnym razie są przechowywane, procesów, lub przesyła w imieniu klienta lub w zakresie, że ich może mieć wpływ na bezpieczeństwo środowiska danych posiadacza klienta. 

> [!NOTE]
> Dokładna treść potwierdzenia będzie zależeć od Umowę między stronami, szczegóły usługi dostarczanej i obowiązki przypisywane do każdej strony. Potwierdzenia musi zawierać dokładną treść w to wymaganie.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci, których usługodawców są odpowiedzialne za potwierdzeniem obowiązków utrzymania zgodności PCI. |



## <a name="pci-dss-requirement-1210"></a>Wymaganie PCI DSS 12.10

**12.10** zaimplementowania odpowiedzi na zdarzenia. Przygotuj się na reagowanie na naruszenie zabezpieczeń systemu.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12101"></a>Wymaganie PCI DSS 12.10.1

**12.10.1** utworzyć plan odpowiedzi na zdarzenia do zaimplementowania w przypadku naruszenia systemu. Sprawdź adresy planu na co najmniej następujących:
- Role, obowiązkach i strategii komunikacja i kontaktu w przypadku naruszenia zabezpieczeń, w tym powiadomienia o marek płatności, co najmniej
- Procedury w odpowiedzi na określone zdarzenia
- Procedury odzyskiwania i ciągłość prowadzenia działalności
- Procesy tworzenia kopii zapasowej danych
- Analiza wymogów prawnych dokonywania raportowania
- Zakres i odpowiedzi wszystkich składników systemu
- Odwołanie lub włączenia procedur odpowiedzi na zdarzenia z marek płatności

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12102"></a>Wymaganie PCI DSS 12.10.2

**12.10.2** przeglądu i plan testu, obejmujący wszystkie elementy na liście wymaganie 12.10.1, co najmniej raz.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12103"></a>Wymaganie PCI DSS 12.10.3

**12.10.3** wyznaczyć określonych pracowników, które mają być dostępne na zasadzie 24/7 do reagowania na alerty.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12104"></a>Wymaganie PCI DSS 12.10.4

**12.10.4** zapewnić szkolenie odpowiednie do personelu naruszenia zabezpieczeń obowiązki odpowiedzi.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12105"></a>Wymaganie PCI DSS 12.10.5

**12.10.5** obejmują alerty zabezpieczeń monitorowania, między innymi do wykrywania nieautoryzowanego dostępu, zapobiegania nieautoryzowanego dostępu, zapory i integralność pliku monitorowania systemami.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



### <a name="pci-dss-requirement-12106"></a>Wymaganie PCI DSS 12.10.6

**12.10.6** opracowanie procesu do modyfikacji i rozwijać plan odpowiedzi na zdarzenia według wnioski i włączenie rozwój branży.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do opracowywania planu IR i testowania, która uwzględnia wszystkie formanty klienta odnoszących się do udostępnionego touch punktów i wszystkich aplikacji klienta wykorzystanie infrastruktury platformy Azure. Odpowiada klienta Podaj dokładne informacje kontaktowe na platformie Azure, w przypadku zdarzenia muszą być zgłaszane do nich, które mogą mieć wpływ na ich aplikacji i danych.|



## <a name="pci-dss-requirement-1211"></a>Wymaganie PCI DSS 12.11

**12.11** **dodatkowych wymogów dotyczących tylko dostawców usług:** wykonać przeglądami co najmniej co kwartał, aby potwierdzić personelu znajdują się zasady zabezpieczeń i procedur operacyjnych.
Przeglądy muszą obejmować następujące procesy:
- Przegląda codzienne dziennika
- Przeglądy zestaw reguł zapory
- Stosowanie standardów konfiguracji do nowych systemów
- Reagowanie na alerty zabezpieczeń
- Zmień procesów zarządzania 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klientów, którzy dostawcy usług są odpowiedzialne za dokumentację ich przeglądami procesów za potwierdzenie wydajności kontroli zgodności PCI.|



### <a name="pci-dss-requirement-12111"></a>Wymaganie PCI DSS 12.11.1

**12.11.1** dodatkowych wymogów dotyczących tylko dostawców usług: Obsługa procesu przeglądu co kwartał, aby uwzględnić w dokumentacji:
- Dokumentowanie wyników oceny
- Przejrzyj i podpisywania wyniki według pracowników przypisanych odpowiedzialność za program zgodności PCI DSS 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klientów, którzy dostawcy usług są odpowiedzialne za dokumentację ich przeglądami procesów za potwierdzenie wydajności kontroli zgodności PCI.|




