---
title: Zabezpieczeń platformy Azure i zgodności plan - automatyzacji aplikacji sieci Web FedRAMP - inspekcji i odpowiedzialności
description: Automatyzacja aplikacji FedRAMP Web — kontroli i odpowiedzialności
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: acb0677d48084108a9a5219f92dead10948228c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="audit-and-accountability-au"></a>Inspekcja i Accountability (AU)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-au-1"></a>Australia kontroli NIST 800 53-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Inspekcja i Accountability zasad i procedur

**Australia 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] zasad inspekcji i odpowiedzialności, którego dotyczy cel, zakres, ról, obowiązki, zobowiązań zarządzania Współpraca między jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji inspekcji i accountability zasady i skojarzona inspekcji i kontrolek accountability; przegląda i aktualizuje bieżące zasady inspekcji i odpowiedzialności [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur kontroli i odpowiedzialności [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady inspekcji i odpowiedzialności przedsiębiorstw i procedur klienta mogą być wystarczające, aby rozwiązać ten formant. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-2a"></a>Australia kontroli NIST 800 53-2.a

#### <a name="audit-events"></a>Inspekcja zdarzeń

**Australia 2.a** organizacji określa, czy system informacji obsługuje następujące zdarzenia inspekcji: [przypisania: zdefiniowane przez organizację występowaniu zdarzeń].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Funkcja inspekcji dla tego planu są udostępniane przez Azure Monitor i usługą analizy dzienników. Azure Monitor udostępnia dzienniki inspekcji szczegółowych dotyczących działań związanych z zasobami wdrożone. Te i dzienniki na poziomie systemu operacyjnego są zbierane przez analizy dzienników i przechowywane w obszarze roboczym analizy dzienników. Analiza dzienników danych inspekcji są powiązane z różnych zasobów wdrożone przez to rozwiązanie i może zostać rozszerzony do aplikacji wdrożonych przez klienta sieci web. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-2b"></a>Australia kontroli NIST 800 53-2.b

#### <a name="audit-events"></a>Inspekcja zdarzeń

**Australia 2.b** organizacji koordynuje funkcji inspekcji zabezpieczeń z inne jednostki organizacyjnej wymaga informacji związanych z inspekcji w celu zwiększenia wzajemnego pomocy technicznej i w celu ułatwienia zaznaczenie występowaniu zdarzeń.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać na ustalonych proces przedsiębiorstw, który określa występowaniu zdarzeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-2c"></a>Australia kontroli NIST 800 53-2.c

#### <a name="audit-events"></a>Inspekcja zdarzeń

**Australia 2.c** organizacji przewiduje uzasadnienie, dlaczego podlegającą inspekcji zdarzeń są uważane za odpowiednie do obsługi dochodzenia po faktu naruszenia zabezpieczeń.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdarzenia inspekcji przez ten plan obejmują informacje wystarczające do określenia, w przypadku wystąpienia zdarzenia, źródło zdarzenia, wynik zdarzenia oraz inne szczegółowe informacje, które obsługuje zbadanie zdarzeń zabezpieczeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-2d"></a>Australia kontroli NIST 800 53-2.d

#### <a name="audit-events"></a>Inspekcja zdarzeń

**Australia 2.d** organizacji ustali, że następujące zdarzenia są określane w ramach systemu informacje: [przypisania: zdefiniowane przez organizację inspekcji zdarzeń (podzbiorem występowaniu zdarzeń zdefiniowanych w a. Australia 2) oraz częstotliwość (lub situation requiring) inspekcji dla każdego zdarzenia zidentyfikowanych].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdarzenia inspekcji przez ten plan obejmują sprawdzana przez działanie Azure dzienniki dla wdrożonych zasobów, dzienniki na poziomie systemu operacyjnego, dzienniki usługi Active Directory, a do dzienników serwera SQL. Klienci mogą wybrać dodatkowe zdarzenia do inspekcji należy do potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-2-3"></a>Australia kontroli NIST 800 53-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Zdarzenia inspekcji | Przeglądy i aktualizacje

**Australia-2 (3)** organizacji przegląda i aktualizuje inspekcji zdarzeń [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać ustalonych przeglądać przedsiębiorstw i zaktualizować procesu dla zestawu określonych zdarzeń inspekcji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-3"></a>Australia kontroli NIST 800 53-3

#### <a name="content-of-audit-records"></a>Zawartość rekordów inspekcji

**Australia 3** system informacji generuje rekordów inspekcji zawierające informacje, które określa, jaki typ zdarzenia wystąpiły po wystąpieniu zdarzenia, w którym wystąpiło zdarzenie, źródło zdarzenia, wynik zdarzenia i tożsamości dowolnego osoby lub tematy skojarzone ze zdarzeniem.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zależy od możliwości wbudowanej inspekcji Azure, Windows Server i programu SQL Server. Te inspekcji rekordów inspekcji przechwytywania rozwiązania ze szczegółami wystarczające by spełnić ich wymagań tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-3-1"></a>Australia kontroli NIST 800 53-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Zawartości rekordów inspekcji | Informacje o dodatkowych inspekcji

**Australia-3 (1)** system informacji generuje rekordów inspekcji, zawierający poniższe informacje dodatkowe: [przypisania: organizacji zdefiniowane dodatkowe, szczegółowe informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdarzenia dziennika aktywności platformy Azure, użyj szczegółowe schemat, który zawiera pola więcej niż 20 rodzaje informacji inspekcji. Oprócz dziennik aktywności ten plan wdraża rozwiązania analizy dzienników, które obsługuje zestaw źródeł danych, takich jak dzienniki systemu Windows, Linux dzienniki dzienników diagnostyki Azure i dzienników klienta.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-3-2"></a>Australia kontroli NIST 800 53-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Zawartości rekordów inspekcji | Scentralizowane zarządzanie planowanej inspekcji zawartości rekordów

**Australia-3 (2)** system informacji zapewnia scentralizowanego zarządzania i konfiguracji zawartości do przechwycenia w rekordach inspekcji generowanych przez [przypisania: składniki systemowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wszystkich maszyn wirtualnych wdrożonych przez ten plan są przyłączone do domeny usługi Active Directory wdrożone. Wszystkie maszyny wirtualne przyłączonych do domeny zaimplementować zasady grupy, które można skonfigurować do centralnie zarządzać konfiguracją systemu inspekcji poziomu systemu operacyjnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-4"></a>Australia kontroli NIST 800 53-4

#### <a name="audit-storage-capacity"></a>Pojemność magazynu inspekcji

**Australia 4** organizacji przydziela pojemność magazynu rekordów inspekcji przy użyciu [przypisania: wymagania dotyczące magazynu rekordów inspekcji zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan przydziela wystarczającą pojemność magazynu przechowywanie rekordów inspekcji przez okres jednego roku. Wszystkie rekordy inspekcji są zbierane przez analizy dzienników, która jest skonfigurowana pod kątem przechowywania jeden rok. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-5a"></a>Australia kontroli NIST 800 53-5.a

#### <a name="response-to-audit-processing-failures"></a>Odpowiedź na inspekcji niepowodzenia przetwarzania

**Australia 5.a** alerty systemu informacji [przypisania: techniczną zdefiniowany przez organizację lub ról] w przypadku inspekcji niepowodzenie przetwarzania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Stan usługi dla monitora Azure Log Analytics jest dostępna na Azure stan witryny sieci Web i bloku kondycji usługi w portalu Azure. Alerty można skonfigurować za pomocą analizy dzienników umożliwia powiadamianie innych typów inspekcji niepowodzenia przetwarzania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-5b"></a>Australia kontroli NIST 800 53-5.b

#### <a name="response-to-audit-processing-failures"></a>Odpowiedź na inspekcji niepowodzenia przetwarzania

**Australia 5.b** systemu informacji przyjmuje następujące dodatkowe czynności: [przypisania: zdefiniowane przez organizację akcje do wykonania (np. Zamknij system informacji, zastępować najstarsze rekordów inspekcji, zatrzymać generowanie rekordów inspekcji)].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wszystkie rekordy inspekcji generowanych przez wdrożone przez ten plan zasobów są zbierane przez analizy dzienników i zachowywane przez jeden rok. Alokacja magazynu przechowywania rekordów inspekcji jest dynamicznie przydzielane zapewnienie wystarczającej jest dostępna. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-5-1"></a>Australia kontroli NIST 800 53-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Odpowiedź na inspekcji niepowodzenia przetwarzania | Pojemność magazynu inspekcji

**Australia-5 (1)** system informacji zapewnia ostrzeżenie [przypisania: personelu zdefiniowany przez organizację, ról i/lub lokalizacje] w [przypisania: organizacji zdefiniowane przez okres] gdy wolumin magazynu rekordów inspekcji przydzielone osiągnie [ Przypisania: procent zdefiniowany przez organizację] maksymalna repozytorium inspekcji rekordów pojemności.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wszystkie rekordy inspekcji generowanych przez wdrożone przez ten plan zasobów są zbierane przez analizy dzienników i zachowywane przez jeden rok. Alokacja magazynu przechowywania rekordów inspekcji jest dynamicznie przydzielane zapewnienie wystarczającej jest dostępna. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-5-2"></a>Australia kontroli NIST 800 53-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Odpowiedź na inspekcji niepowodzenia przetwarzania | Alerty w czasie rzeczywistym

**Australia-5 (2)** system informacji zapewnia alert w [przypisania: zdefiniowane przez organizację w czasie rzeczywistym okres] do [przypisania: personelu zdefiniowany przez organizację, ról i/lub lokalizacje] sytuacje są następujące zdarzenia inspekcji w błąd: [przypisania: zdefiniowany przez organizację błąd zdarzeń inspekcji wymagające alertów w czasie rzeczywistym].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Stan usługi dla platformy Azure jest dostępna w bloku usługi kondycji w portalu Azure. Alerty można skonfigurować za pomocą analizy dzienników umożliwia powiadamianie innych typów inspekcji niepowodzenia przetwarzania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-6a"></a>Australia kontroli NIST 800 53-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Przejrzyj inspekcji, analizy i raportowania

**Australia 6.a** organizacji przegląda i analizuje rekordów inspekcji systemu informacji [przypisania: częstotliwość zdefiniowany przez organizację] do oznaczenia [przypisania: zdefiniowany przez organizację nieodpowiednie lub nietypowe działanie].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądania i analizowania rekordów inspekcji zasobów wdrożonych przez klienta (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-6b"></a>Australia kontroli NIST 800 53-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Przejrzyj inspekcji, analizy i raportowania

**Australia 6.b** organizacji raporty wyników [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za raportowania wyników nieodpowiednie lub nietypowe działania (zdefiniowany w Australia 06.a) na zasobach wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-1"></a>Australia kontroli NIST 800 53-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Inspekcja przeglądu, analizy i raportowania | Proces integracji

**Australia – 6 (1)** organizacja stosuje automatycznych mechanizmy do integracji inspekcji przeglądu, analizy i raportowania procesów do obsługi procesów organizacyjnych do badania i reagowania na podejrzane działania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać na przeglądu scentralizowane inspekcji na poziomie przedsiębiorstwa, analizy i możliwości raportowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-3"></a>Australia kontroli NIST 800 53-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Inspekcja przeglądu, analizy i raportowania | Korelowanie repozytoria inspekcji

**Australia – 6 (3)** analizuje organizacji i odpowiada inspekcji rekordów w różnych repozytoria uzyskanie świadomości oparty na analizie sytuacji w całej organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania analizy dzienników, można scentralizować dane inspekcji przez wdrożone zasoby obsługujące oparty na analizie sytuacji świadomości całej organizacji. Klienci mogą wybrać do dalszej integracji z innymi systemami analizy dzienników. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-4"></a>Australia kontroli NIST 800 53-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Inspekcja przeglądu, analizy i raportowania | Przejrzyj centralnej i analiza

**Australia – 6 (4)** systemu informacji oferuje możliwość centralnie Przejrzyj i analizować rekordów inspekcji z wielu składników w systemie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania analizy dzienników, można scentralizować dane inspekcji wdrożonych zasobów, pomocnicze scentralizowane przeglądu, analizy i raportowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-5"></a>Australia kontroli NIST 800 53-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Inspekcja przeglądu, analizy i raportowania | Integracja / skanowania i możliwości monitorowania

**Australia – 6 (5)** analizy rekordów inspekcji w organizacji jest zintegrowany z analizy [wybór (jeden lub więcej): Luka w zabezpieczeniach skanowanie informacji; dane wydajności systemu informacji monitorowania informacji; [Przypisania: zdefiniowane przez organizację danych/informacje zbierane z innych źródeł]] Aby zwiększyć możliwość identyfikacji nieodpowiednie lub nietypowe działania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża rozwiązanie zabezpieczeń i inspekcji. To rozwiązanie zapewnia kompleksowy przegląd stan zabezpieczeń. Pulpit nawigacyjny zabezpieczeń i kontrola zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów wdrożone przy użyciu danych dostępnych w wdrożonej rozwiązań analizy dzienników, integrowanie danych dzienników i danych luki w zabezpieczeniach z oceny linii bazowej i poprawki. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-6"></a>Australia kontroli NIST 800 53-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Inspekcja przeglądu, analizy i raportowania | Korelacji z monitorowania fizycznego

**Australia – 6 (6)** informacji z rekordów inspekcji z informacjami uzyskanymi z monitorowania fizycznego dostępu do zwiększenia możliwość identyfikacji podejrzane nieodpowiednie, nietypowe albo groźniejszym działania są powiązane z organizacji.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure SIM zespół używa powiązanych danych monitorowania fizycznych i jest całkowicie skorelowany z rekordów inspekcji w celu ustalenia, jeśli było żadnych skojarzonych logicznej naruszenia lub podejrzane zachowanie po wykryciu zdarzenia fizycznych. |


 ### <a name="nist-800-53-control-au-6-7"></a>Australia kontroli NIST 800 53-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Inspekcja przeglądu, analizy i raportowania | Dozwolone akcje

**Australia – 6 (7)** organizacji określa akcje dozwolone dla każdego [wybór (jeden lub więcej): informacje o procesie systemu; roli użytkownika] skojarzony z przeglądu, analizy i raportowania informacje inspekcji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyny wirtualne systemu Windows wdrożone przez ten plan wdrożenia uprawnienia poziomu systemu operacyjnego, które ograniczają działania, które użytkownik może wykonać względem informacje inspekcji. W obrębie platformy Azure użytkowników lub grup użytkowników można przypisać do ról (np. właściciela, współautora, czytnika lub niestandardowej roli zabezpieczeń) do ograniczenia Akcje dostępne w odniesieniu do wszystkich zasobów lub wdrożyć rozwiązania analizy dzienników.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-6-10"></a>Australia kontroli NIST 800 53-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Inspekcja przeglądu, analizy i raportowania | Dostosowania poziomu inspekcji

**Australia – 6 (10)** organizacji ustawia poziom inspekcji przeglądu, analizy i raportowania w systemie informacji po zmiany na podstawie informacji egzekwowania prawa, informacje dotyczące analizy lub inne wiarygodnego źródła ryzyka informacje.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za dostosowania poziomu inspekcji przeglądu, analizy i raportowania dla wdrożonych przez klienta zasobów (dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych) po zmianie ryzyko na podstawie informacji przez prawo Wymuszanie, analizy lub inne wiarygodnego źródła. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-7a"></a>Australia kontroli NIST 800 53-7.a

#### <a name="audit-reduction-and-report-generation"></a>Zmniejszenie inspekcji i generowania raportów

**Australia 7.a** systemu informacji zapewnia inspekcja redukcji inspekcji i możliwości generowania raportu, który obsługuje na żądanie przeglądu, analizy i raportowania wymagania i badania po faktu naruszenia zabezpieczeń.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania analizy dziennika. Analiza dzienników umożliwia monitorowanie Azure przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-7b"></a>Od Australia kontroli NIST 800 53-7.b

#### <a name="audit-reduction-and-report-generation"></a>Zmniejszenie inspekcji i generowania raportów

**Od 7.b AU** system informacji zapewnia inspekcji raport i zmniejszenie generacji możliwość, która nie zmienia oryginalnej zawartości lub czas kolejność rekordów inspekcji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania analizy dziennika. Analiza dzienników umożliwia monitorowanie Azure przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Zawartość i czas kolejność rekordów inspekcji nie zostaną zmodyfikowane, jeśli zebrane przez analizy dzienników. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-7-1"></a>Australia kontroli NIST 800 53-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Zmniejszenie inspekcji i generowania raportów | Automatyczne przetwarzanie

**Australia-7 (1)** system informacji zapewnia możliwość przetwarzania rekordów inspekcji dla zdarzeń na podstawie [przypisania: pól zdefiniowanych przez organizację inspekcji w rekordach inspekcji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania analizy dziennika. Analiza dzienników umożliwia monitorowanie Azure przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Usługa Log Analytics obejmuje zaawansowany język zapytań umożliwiający wyodrębnianie danych przechowywanych w repozytorium. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-8a"></a>Australia kontroli NIST 800 53-8.a

#### <a name="time-stamps"></a>Sygnatury czasowe

**Australia 8.a** system informacji za pomocą systemu wewnętrznego zegary Generowanie sygnatury czasowe dla rekordów inspekcji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wdrożone przez ten plan zasobów umożliwia generowanie sygnatury czasowe dla rekordów inspekcji zegary wewnętrznego systemu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-8b"></a>Australia kontroli NIST 800 53-8.b

#### <a name="time-stamps"></a>Sygnatury czasowe

**Australia 8.b** systemu informacji rejestruje sygnatury czasowe dla rekordów inspekcji, które mogą być mapowane na uniwersalny czas koordynowany (UTC) lub czas uniwersalny Greenwich (GMT) i spełnia [przypisania: pomiar czasu zdefiniowany przez organizację poziom szczegółowości].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wdrożone przez ten plan zasobów umożliwia generowanie sygnatury czasowe dla rekordów inspekcji zegary wewnętrznego systemu. Sygnatury czasowe są rejestrowane w formacie UTC. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-8-1a"></a>((1) tj. NIST 800 53 kontroli Australia-8

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Sygnatury czasowe | Synchronizacja z autorytatywne źródło czasu

**Australia-8 (1), tj.** zegarami systemowymi informacji wewnętrznych porównuje informacje o systemie [przypisania: częstotliwość zdefiniowany przez organizację] z [przypisania: zdefiniowane przez organizację autorytatywne źródło czasu].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wdrożone przez ten plan zasobów umożliwia generowanie sygnatury czasowe dla rekordów inspekcji zegary wewnętrznego systemu. Zegary wewnętrznego systemu są skonfigurowane do synchronizacji z autorytatywne źródło czasu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800 53 kontroli Australia-8 (1) b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Sygnatury czasowe | Synchronizacja z autorytatywne źródło czasu

**Australia-8 (1) b** system informacji synchronizuje zegarami systemowymi wewnętrzny, aby autorytatywne źródło czasu, gdy odstęp czasu jest większy niż [przypisania: organizacji zdefiniowane przez okres czasu].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Wdrożone przez ten plan zasobów umożliwia generowanie sygnatury czasowe dla rekordów inspekcji zegary wewnętrznego systemu. Zegary wewnętrznego systemu są skonfigurowane do synchronizacji z autorytatywne źródło czasu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-9"></a>Australia kontroli NIST 800 53-9

#### <a name="protection-of-audit-information"></a>Ochrona informacji inspekcji

**Australia 9** systemu informacji chroni inspekcji informacji i inspekcji narzędzi przed nieautoryzowanym dostępem, modyfikowanie i usuwanie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Formanty logicznego dostępu służą do ochrony przed nieautoryzowanym dostępem, modyfikowanie i usuwanie informacji inspekcji i narzędzi w obrębie tego planu. Usługa Azure Active Directory wymusza zatwierdzonych logicznego dostępu przy użyciu członkostwa w grupach opartej na rolach. Możliwość wyświetlania informacji inspekcji i użyć narzędzia do inspekcji może być ograniczony do użytkowników, którzy wymagają tych uprawnień. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-9-2"></a>Australia kontroli NIST 800 53-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Ochrona informacji inspekcji | Przeprowadź inspekcję tworzenia kopii zapasowej w osobnych systemach fizycznych / składników

**Australia-9 (2)** systemu informacji kopię zapasową rekordów inspekcji [przypisania: zdefiniowany przez organizację częstotliwość] na fizycznie innego systemu lub składnik systemu niż system lub składnika jest sprawdzana.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje usługę Analiza dzienników. Wdrożone maszyny wirtualne i usługi Azure konta magazynu diagnostyki połączonych źródeł do analizy dzienników i przechowywane oddzielnie od ich źródła. Dane są zbierane przez analizy dzienników w niemal w czasie rzeczywistym. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-9-3"></a>Australia kontroli NIST 800 53-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Ochrona informacji inspekcji | Ochrona usług kryptograficznych

**Australia-9 (3)** system informacji implementuje mechanizmów kryptograficznych, aby chronić integralność inspekcji informacji i inspekcji narzędzi.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje usługę Analiza dzienników. Analiza dzienników zapewnia, że dane przychodzące jest z zaufanego źródła, weryfikując certyfikaty i integralności danych za pomocą uwierzytelniania systemu Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-9-4"></a>Australia kontroli NIST 800 53-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Ochrona informacji inspekcji | Dostęp przez podzbiór uprzywilejowani użytkownicy

**Australia-9 (4)** organizacji zezwala na dostęp do zarządzania funkcja inspekcji tylko [przypisania: zdefiniowane przez organizację podzbiór użytkownicy o odpowiednich uprawnieniach].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Formanty logicznego dostępu służą do ochrony przed nieautoryzowanym dostępem, modyfikowanie i usuwanie informacji inspekcji i narzędzi w obrębie tego planu. Usługa Azure Active Directory wymusza zatwierdzonych logicznego dostępu przy użyciu członkostwa w grupach opartej na rolach. Możliwość wyświetlania informacji inspekcji i użyć narzędzia do inspekcji może być ograniczony do użytkowników, którzy wymagają tych uprawnień.
 |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-10"></a>Australia kontroli NIST 800 53-10

#### <a name="non-repudiation"></a>Bez odrzucania

**Australia 10** systemu informacji chroni przed fałszywie odmawianie o wykonać osoba (lub proces działają w imieniu osoby) [przypisania: akcje zdefiniowane w organizacji mają być objęte bez odrzucania].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Funkcja inspekcji dla tego planu są udostępniane przez Azure Monitor i usługą analizy dzienników. Azure Monitor udostępnia dzienniki inspekcji szczegółowych dotyczących działań związanych z zasobami wdrożone. Te i dzienniki na poziomie systemu operacyjnego są zbierane przez analizy dzienników i przechowywane w obszarze roboczym analizy dzienników. Te dzienniki zawierał rekordy szczegółowe informacje o systemie zdarzeń i może pomóc w ochronie przed bez odrzucania. Ponadto dostęp do rejestrowania danych jest ograniczony przy użyciu kontroli dostępu opartej na rolach, aby zapobiec unauthored modyfikacja lub usunięcie danych dziennika. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-11"></a>Australia kontroli NIST 800 53-11

#### <a name="audit-record-retention"></a>Przechowywania rekordu inspekcji

**Australia 11** Organizacja zachowuje rekordów inspekcji do [przypisania: organizacji zdefiniowane przez okres czasu zgodnie z zasadami przechowywania rekordów] zapewnia obsługę dochodzenia po faktu naruszenia zabezpieczeń i w celu spełnienia przepisami i wymagania dotyczące przechowywania informacji organizacyjnych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje usługę Analiza dzienników. Analiza dzienników umożliwia monitorowanie Azure przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Po zebraniu danych, dane przechowywane przez jeden rok na konfiguracji analizy dzienników. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-12a"></a>Australia kontroli NIST 800 53-12.a

#### <a name="audit-generation"></a>Generowanie inspekcji

**Australia 12.a** system informacji zapewnia możliwości generowania rekordów inspekcji dla podlegającą inspekcji zdarzeń zdefiniowanych w Australia-2. w [przypisania: składniki systemowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdarzenia inspekcji przez ten plan obejmują sprawdzana przez działanie Azure dzienniki dla wdrożonych zasobów, dzienniki na poziomie systemu operacyjnego, dzienniki usługi Active Directory, a do dzienników serwera SQL. Klienci mogą wybrać dodatkowe zdarzenia do inspekcji należy do potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-12b"></a>Australia kontroli NIST 800 53-12.b

#### <a name="audit-generation"></a>Generowanie inspekcji

**Australia 12.b** umożliwia systemowi informacji [przypisania: techniczną zdefiniowany przez organizację lub ról] wybierz zdarzenia podlegającą inspekcji, które są określane przez określone składniki systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Dostęp do funkcji inspekcji jest ograniczony przy użyciu kontroli dostępu opartej na rolach w obrębie platformy Azure i na poziomie systemu operacyjnego maszyny wirtualnej. Konfiguracja zdarzenia wybrane określane przez zasoby wdrożone przez ten plan można skonfigurować przez użytkowników z odpowiednią autoryzacji opartej na rolach. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-au-12c"></a>Australia kontroli NIST 800 53-12.c

#### <a name="audit-generation"></a>Generowanie inspekcji

**Australia 12.c** rekordów inspekcji dla zdarzeń zdefiniowanych w 2.d Australia wygenerowany przez system informacji. z zawartością zdefiniowane w Australia-3.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdarzenia inspekcji przez ten plan obejmują sprawdzana przez działanie Azure dzienniki dla wdrożonych zasobów, dzienniki na poziomie systemu operacyjnego, dzienniki usługi Active Directory, a do dzienników serwera SQL. Klienci mogą wybrać dodatkowe zdarzenia do inspekcji należy do potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-12-1"></a>Australia kontroli NIST 800 53-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Generowanie inspekcji | Dziennik inspekcji całego systemu / czas skorelowanego

**Australia-12 (1)** rekordów inspekcji z kompilacją systemu informacji [przypisania: składniki systemowe zdefiniowane przez organizację informacje] do dziennik inspekcji (logiczny lub fizyczny) całego systemu, będący czas korelowane w [przypisania: zdefiniowany przez organizację poziom tolerancji dla relacji między sygnaturą czasową poszczególne rekordy w dzienniku inspekcji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje usługę Analiza dzienników. Analiza dzienników umożliwia monitorowanie Azure przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Sygnatury czasowe rekordów inspekcji nie są zmieniane, w związku z tym dziennik inspekcji skorelowane czasu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-au-12-3"></a>Australia kontroli NIST 800 53-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Generowanie inspekcji | Zmiany wprowadzane przez autoryzowanych użytkowników indywidualnych

**Australia-12 (3)** system informacji zapewnia funkcje [przypisania: zdefiniowany przez organizację konkretnych osób lub ról] zmienić inspekcji na [przypisania: składniki systemowe zdefiniowane przez organizację informacje] [w oparciu Przypisania: kryteria zdefiniowane w organizacji wybieranych zdarzenia] w [przypisania: czas zdefiniowany przez organizację progi].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Dostęp do funkcji inspekcji jest ograniczony przy użyciu kontroli dostępu opartej na rolach w obrębie platformy Azure i na poziomie systemu operacyjnego maszyny wirtualnej. Konfiguracja zdarzenia wybrane określane przez zasoby wdrożone przez ten plan można skonfigurować przez użytkowników z odpowiednią autoryzacji opartej na rolach. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |
