---
title: Analizowanie użycia danych w usłudze Log Analytics | Microsoft Docs
description: Pulpit nawigacyjny użycia i szacowanych kosztów w usłudze Log Analytics umożliwia szacowanie ilości danych wysyłanych do usługi Log Analytics oraz identyfikowanie potencjalnych przyczyn nieprzewidzianego wzrostu tej ilości.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2018
ms.author: magoedte
ms.openlocfilehash: ed2e77553cc72caa6a7b48fe6fa6baab0ffafec5
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802055"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analizowanie użycia danych w usłudze Log Analytics
Usługa Log Analytics zawiera informacje na temat ilości zebranych danych, źródeł, z których zostały one wysłane, oraz typów danych.  Użyj pulpitu nawigacyjnego **Użycie usługi Log Analytics** do przeglądania i analizowania użycia danych. Na pulpicie nawigacyjnym prezentowana jest ilość danych zebranych przez każde rozwiązanie i ilość danych wysyłanych przez komputery.

## <a name="understand-the-usage-dashboard"></a>Objaśnienie pulpitu nawigacyjnego Użycie
Pulpit nawigacyjny **Użycie usługi Log Analytics** udostępnia następujące informacje:

- Ilość danych
    - Ilość danych w czasie (na podstawie bieżącego zakresu czasu)
    - Ilość danych wg rozwiązania
    - Dane nieskojarzone z komputerem
- Komputery
    - Komputery wysyłające dane
    - Komputery bez danych w ostatnich 24 godzinach
- Oferty
    - Węzły wglądu w dane i analizy
    - Węzły automatyzacji i kontroli
    - Węzły zabezpieczeń  
- Wydajność
    - Ilość czasu zbierania i indeksowania danych  
- Lista zapytań

![Pulpit nawigacyjny użycia i kosztów](./media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>Aby pracować z danymi użycia
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.<br><br> ![Azure Portal](./media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy.
4. Wybierz pozycję **Użycie i szacunkowe koszty** z listy w lewym okienku.
5. Na pulpicie nawigacyjnym **Użycie i szacowane koszty** możesz modyfikować zakres czasu, wybierając pozycję **Czas: ostatnie 24 godziny** i zmieniać przedział czasu.<br><br> ![przedział czasu](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. Wyświetl bloki kategorii użycia pokazujące obszary, które Cię interesują. Wybierz blok, a następnie kliknij w nim pozycję, aby wyświetlić więcej szczegółów w obszarze [Wyszukiwanie w dzienniku](log-analytics-log-searches.md).<br><br> ![przykładowy kluczowy wskaźnik wydajności użycia danych](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. Na pulpicie nawigacyjnym wyszukiwania w dzienniku przejrzyj wyniki zwrócone w wyniku wyszukiwania.<br><br> ![przykład wyszukiwania w dzienniku użycia](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Tworzenie alertu, gdy ilość zebranych danych jest większa od oczekiwanej
W tej sekcji opisano sposób tworzenia alertu w sytuacji, gdy:
- Ilość danych przekracza określoną wartość.
- Przewiduje się, że ilość danych przekroczy określoną wartość.

[Alerty](log-analytics-alerts-creating.md) usługi Log Analytics używają zapytań wyszukiwania. Poniższe zapytanie daje rezultat, jeśli w ciągu ostatnich 24 godzin zebrano więcej niż 100 GB danych:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Następujące zapytanie używa prostej formuły umożliwiającej przewidywanie, kiedy w ciągu jednego dnia zostanie wysłanych więcej niż 100 GB danych: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Aby utworzyć alerty dotyczące innego woluminu danych, zmień w zapytaniach wartość 100 na liczbę gigabajtów, po przekroczeniu której ma zostać wyświetlony alert.

Wykonaj kroki opisane w sekcji dotyczącej [tworzenie reguły alertu](log-analytics-alerts-creating.md#create-an-alert-rule), aby otrzymywać powiadomienia w sytuacji, gdy ilość zebranych danych jest większa niż oczekiwano.

Podczas tworzenia alertu dla pierwszego zapytania odnoszącego się do przypadku, gdy w ciągu 24 godzin występuje więcej niż 100 GB danych, ustaw wartości elementów:  
- **Nazwa** na *Data volume greater than 100 GB in 24 hours* (Wolumin danych większy niż 100 GB w ciągu 24 godzin)  
- **Ważność** na *Ostrzeżenie*  
- **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`   
- **Przedział czasu** na *24 godziny*.
- **Częstotliwość alertów** na jedną godzinę, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Generuj alert w oparciu o** na *Liczba wyników*
- **Liczba wyników** na *Większa niż 0*

Wykonaj kroki opisane w sekcji dotyczącej [dodawania akcji do reguł alertów](log-analytics-alerts-actions.md) i skonfiguruj wiadomość e-mail, element webhook lub akcję runbook dla reguły alertu.

Podczas tworzenia alertu dla drugiego zapytania dotyczącego przypadku, w którym przewiduje się, że w ciągu 24 godzin wystąpi więcej niż 100 GB danych, ustaw wartości elementów:
- **Nazwa** na *Data volume expected to greater than 100 GB in 24 hours* (Oczekiwany wolumin danych większy niż 100 GB w ciągu 24 godzin)
- **Ważność** na *Ostrzeżenie*
- **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
- **Przedział czasu** na *3 godziny*.
- **Częstotliwość alertów** na jedną godzinę, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Generuj alert w oparciu o** na *Liczba wyników*
- **Liczba wyników** na *Większa niż 0*

Po otrzymaniu alertu wykonaj kroki przedstawione w poniższej sekcji, aby rozwiązać problemy związane z większym niż oczekiwano użyciem.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania
Pulpit nawigacyjny użycia pomaga zidentyfikować przyczynę (a co za tym idzie także koszt) użycia, które przekracza oczekiwania.

Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Do usługi Log Analytics jest wysyłana większa ilość danych niż oczekiwano
- Dane wysyłane do usługi Log Analytics pochodzą z większej liczby węzłów niż oczekiwano

### <a name="check-if-there-is-more-data-than-expected"></a>Sprawdzanie, czy ilość danych przekracza oczekiwania 
Strona użycia zawiera dwie kluczowe sekcje, które ułatwiają obszarów powodujących gromadzenie większości danych.

Wykres *Objętość danych w czasie* przedstawia łączny wolumin wysłanych danych oraz komputery wysyłające większość z nich. Wykres u góry umożliwia obserwację, czy całkowite użycie danych wzrasta, pozostaje na stałym poziomie, czy też maleje. Lista komputerów obejmuje 10 komputerów wysyłających większość danych.

Wykres *Objętość danych według rozwiązania* przedstawia wolumin danych wysyłanych przez każde rozwiązanie oraz rozwiązania wysyłające większość danych. Wykres u góry przedstawia łączny wolumin danych wysyłanych z upływem czasu przez każde rozwiązanie. Te informacje umożliwiają ustalenie, czy w miarę upływu czasu dane rozwiązanie wysyła więcej, mniej, czy podobną ilość danych. Lista rozwiązań obejmuje 10 rozwiązań wysyłających większość danych. 

Na tych dwóch wykresach są pokazywane wszystkie dane. Niektóre dane podlegają opłatom, a inne nie. W celu skupienia się tylko na płatnych danych zmodyfikuj zapytanie na stronie wyszukiwania, uwzględniając parametr `IsBillable=true`.  

![wykresy woluminów danych](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Spójrz na wykres *Objętość danych w czasie*. Aby wyświetlić rozwiązania i typy danych odpowiedzialne za wysyłanie większości danych dla określonego komputera, kliknij nazwę komputera. Kliknij nazwę pierwszego komputera na liście.

Na poniższym zrzucie ekranu widać, że w przypadku tego komputera większość wysyłanych danych jest typu *Zarządzanie dziennikiem/wydajność*.<br><br> ![wolumin danych na komputerze](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Następnie wróć do pulpitu nawigacyjnego *Użycie* i spójrz na wykres *Objętość danych według rozwiązania*. Aby sprawdzić, które komputery wysyłają większość danych z określonego rozwiązania, kliknij nazwę rozwiązania na liście. Kliknij nazwę pierwszego rozwiązania na liście. 

Na poniższym zrzucie ekranu widać, że najwięcej danych związanych z rozwiązaniem Zarządzanie dziennikami wysyła komputer *mycon*.<br><br> ![wolumin danych dla rozwiązania](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Jeśli to konieczne, wykonaj dodatkową analizę w celu zidentyfikowania dużych woluminów w ramach rozwiązania lub typu danych. Przykładowe zapytania:

+ Rozwiązanie **zabezpieczające**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Rozwiązanie do **zarządzania dziennikami**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Typ danych **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Typ danych **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Typ danych **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Typ danych **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Wykonaj następujące kroki, aby zmniejszyć wolumin zebranych danych dzienników:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [inspekcja systemu plików](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [inspekcja magazynu wymiennego](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](log-analytics-data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](log-analytics-data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](log-analytics-data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Sprawdzanie, czy liczba węzłów przekracza oczekiwania
Jeśli korzystasz z warstwy cenowej *Na węzeł (OMS)*, opłaty są naliczane na podstawie liczby używanych węzłów i rozwiązań. Aby sprawdzić, z ilu węzłów poszczególnych ofert korzystasz, przejdź do sekcji *ofert* pulpitu nawigacyjnego Użycie.<br><br> ![pulpit nawigacyjny Użycie](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Kliknij opcję **Zobacz wszystko**, aby wyświetlić pełną listę komputerów, które wysyłają dane dla wybranej oferty.

Użyj funkcji [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów.


## <a name="next-steps"></a>Następne kroki
* Zobacz temat [Wyszukiwanie w dziennikach w usłudze Log Analytics](log-analytics-log-searches.md), aby dowiedzieć się, jak korzystać z języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
* Wykonaj kroki opisane w sekcji dotyczącej [tworzenia reguły alertu](log-analytics-alerts-creating.md#create-an-alert-rule), aby otrzymywać powiadomienia, gdy kryteria wyszukiwania zostaną spełnione.
* Użyj funkcji [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów
* Aby skonfigurować efektywne zasady zbierania zdarzeń zabezpieczeń, przejrzyj [zasady filtrowania usługi Azure Security Center](../security-center/security-center-enable-data-collection.md)
* Zmień [konfigurację licznika wydajności](log-analytics-data-sources-performance-counters.md)
* Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](log-analytics-data-sources-windows-events.md)
* Aby zmodyfikować ustawienia zbierania dla dziennika systemowego, przejrzyj [konfigurację dziennika systemowego](log-analytics-data-sources-syslog.md)
