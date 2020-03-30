---
title: Monitoruj usługi Usługi Azure Storage za pomocą usługi Azure Monitor for Storage (wersja zapoznawcza)| Dokumenty firmy Microsoft
description: W tym artykule opisano funkcję Usługi Azure Monitor dla Magazyn, która zapewnia administratorom magazynu szybkie zrozumienie problemów z wydajnością i wykorzystaniem ich kont usługi Azure Storage.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662523"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorowanie usługi magazynu za pomocą usługi Azure Monitor dla magazynu (wersja zapoznawcza)

Usługa Azure Monitor for Storage (wersja zapoznawcza) zapewnia kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony widok wydajności, pojemności i dostępności usług Azure Storage. Można obserwować pojemność magazynu i wydajność na dwa sposoby, widok bezpośrednio z konta magazynu lub widoku z usługi Azure Monitor, aby wyświetlić między grupami kont magazynu. 

Ten artykuł pomoże Ci zrozumieć środowisko, w jakie usługa Azure Monitor for Storage (wersja zapoznawcza) zapewnia użyteczną wiedzę na temat kondycji i wydajności kont magazynu na dużą skalę, z możliwością skoncentrowania się na hotspotach i diagnozowania opóźnień, ograniczania przepustowości, i problemy z dostępnością.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Wprowadzenie do usługi Azure Monitor dla magazynu (wersja zapoznawcza)

Przed zagłębieniem się w doświadczenie, należy zrozumieć, jak prezentuje i wizualizuje informacje. Niezależnie od tego, czy wybierzesz funkcję Magazyn bezpośrednio z konta magazynu, czy z usługi Azure Monitor, usługa Azure Monitor for Storage zapewnia spójne środowisko. 

W połączeniu zapewnia:

* **W perspektywie skali** pokazujący widok migawki ich dostępności na podstawie kondycji usługi magazynu lub operacji interfejsu API, wykorzystanie pokazujące całkowitą liczbę żądań, które otrzymuje usługa magazynu i opóźnienie pokazujące średni czas, przez jaki usługa magazynu lub typ operacji interfejsu API jest przyjmowany do przetwarzania żądań. Można również wyświetlić pojemność według obiektu blob, pliku, tabeli i kolejki.

* **Szczegółowe omówienie** określonego konta magazynu w celu zdiagnozowania problemów lub przeprowadzenia szczegółowej analizy według kategorii — dostępności, wydajności, awarii i pojemności. Wybranie jednej z tych opcji zapewnia szczegółowy widok metryk.  

* **Można dostosowywać,** gdzie można zmienić metryki, które chcesz zobaczyć, zmodyfikować lub ustawić progi, które są zgodne z limitami, i zapisać jako własny skoroszyt. Wykresy w skoroszycie można przypiąć do pulpitu nawigacyjnego platformy Azure.  

Ta funkcja nie wymaga włączania ani konfigurowania czegokolwiek, metryki magazynu z kont magazynu są zbierane domyślnie. Jeśli nie znasz metryk dostępnych w usłudze Azure Storage, wyświetl opis i definicję w metrykach usługi Azure Storage, przeglądając [metryki usługi Azure storage.](../../storage/common/storage-metrics-in-azure-monitor.md)

>[!NOTE]
>Dostęp do tej funkcji nie podlega żadnym opłatom, a opłaty będą naliczane tylko za podstawowe funkcje usługi Azure Monitor, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegółów cen usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>Usługa Azure Monitor dla magazynów nie obsługuje [kont ogólnego przeznaczenia w wersji 1.](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)
>

## <a name="view-from-azure-monitor"></a>Widok z usługi Azure Monitor

W usłudze Azure Monitor można wyświetlać szczegóły transakcji, opóźnień i pojemności z wielu kont magazynu w ramach subskrypcji oraz identyfikować wydajność, problemy z pojemnością i awarie.

Aby wyświetlić wykorzystanie i dostępność kont magazynu we wszystkich subskrypcjach, wykonaj następujące kroki.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Wybierz **pozycję Monitor** z lewego okienka w witrynie Azure portal i w sekcji **Insights** wybierz pozycję Konta **magazynu (wersja zapoznawcza).**

    ![Widok wielu kont magazynu](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Skoroszyt przeglądowy

W **skoroszycie Przegląd** dla wybranej subskrypcji tabela wyświetla interaktywne dane magazynu i stan dostępności usług dla maksymalnie 10 kont magazynu pogrupowanych w ramach subskrypcji. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* **Subskrypcje** — wyświetlane są tylko subskrypcje, które mają konta magazynu.  

* **Konta magazynu** — domyślnie wstępnie wybrano 10 kont magazynu. Jeśli wybierzesz wszystkie lub wiele kont magazynu w selektorze zakresu, zostanie zwróconych maksymalnie 200 kont magazynu. Jeśli na przykład masz łącznie 573 konta magazynu w trzech wybranych subskrypcjach, zostanie wyświetlonych tylko 200 kont. 

* **Zakres czasu** — domyślnie wyświetlane są informacje z ostatnich 4 godzin na podstawie odpowiednich dokonanych wyborów.

Kafelek licznika w obszarze listy rozwijanej zestawia całkowitą liczbę kont magazynu w subskrypcji i odzwierciedla liczbę wybranych sum. Istnieje warunkowe kodowanie kolorów lub mapy cieplne dla kolumn w skoroszycie, które zgłaszają metryki transakcji lub błędy. Najgłębszy kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach. W przypadku kolumn opartych na błędach wartość jest czerwona, a dla kolumn opartych na metrykach wartość jest niebieska.

Wybierz wartość w kolumnach **Dostępność**, **Opóźnienie E2E**, **Opóźnienie serwera**i typ **błędu transakcji/Błędy** kieruje użytkownika do raportu dostosowanego do określonego typu metryk magazynu, które pasują do kolumny wybranej dla tego konta magazynu. Aby uzyskać więcej informacji na temat skoroszytów dla każdej kategorii, zobacz sekcję [Szczegółowe skoroszyty magazynu](#detailed-storage-workbooks) poniżej. 

>[!NOTE]
>Aby uzyskać szczegółowe informacje na temat błędów, które mogą być wyświetlane w raporcie, zobacz [Schemat typu odpowiedzi](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) i poszukaj typów odpowiedzi, takich jak **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. W zależności od wybranych kont magazynu, jeśli zgłaszanych jest więcej niż trzy typy błędów, wszystkie inne błędy są reprezentowane w kategorii **Inne**.

Domyślny próg **dostępności** wynosi:

* Ostrzeżenie - 99%
* Krytyczne - 90%

Aby ustawić próg dostępności na podstawie wyników obserwacji lub wymagań, [przejrzyj zmodyfikuj próg dostępności](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Skoroszyt pojemności

Wybierz **pozycję Pojemność** u góry strony i zostanie otwarty skoroszyt **Pojemności.** Pokazuje ilość całkowitego miejsca do magazynowania używanego na koncie i pojemność używaną przez każdą usługę danych na koncie, aby pomóc w identyfikacji w obszarze wykorzystywanego magazynu.

![Wiele kont magazynu Pojemność skoroszytu](./media/storage-insights-overview/storage-account-capacity-02.png) 

Istnieje warunkowe kodowanie kolorów lub mapy cieplne dla kolumn w skoroszycie, które zgłaszają metryki pojemności o niebieskiej wartości. Najgłębszy kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach.

Po wybraniu wartości w jednej z kolumn w skoroszycie można przejść do skoroszytu **Pojemność** dla konta magazynu. Dalsze szczegóły dotyczące raportu szczegółowego opisano w sekcji [Szczegółowe skoroszyty magazynu](#detailed-storage-workbooks) poniżej. 

## <a name="view-from-a-storage-account"></a>Widok z konta magazynu

Aby uzyskać dostęp do usługi Azure Monitor dla maszyn wirtualnych bezpośrednio z konta magazynu:

1. W witrynie Azure portal wybierz pozycję Konta magazynu.

2. Z listy wybierz konto magazynu. W sekcji Monitorowanie wybierz pozycję Insights (wersja zapoznawcza).

    ![Strona Przegląd wybranego konta magazynu](./media/storage-insights-overview/storage-account-direct-overview-01.png)

W **skoroszycie Przegląd** dla konta magazynu przedstawiono kilka metryk wydajności magazynu, które pomagają szybko ocenić:

* Kondycja usługi Magazyn, aby natychmiast sprawdzić, czy problem poza formantem ma wpływ na usługę magazynu w regionie, w którym jest wdrażana, która jest podana w kolumnie **Podsumowanie.**

* Interaktywne wykresy wydajności przedstawiające najważniejsze szczegóły dotyczące pojemności, dostępności, transakcji i opóźnień.  

* Kafelki metryki i stanu przedstawiające dostępność usługi, całkowitą liczbę transakcji do usługi magazynu, opóźnienie E2E i opóźnienie serwera.

Wybranie dowolnego z **przycisków dla błędów,** **wydajności,** **dostępności**i **pojemności** powoduje otwarcie odpowiedniego skoroszytu. 

![Strona Przegląd wybranego konta magazynu](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Szczegółowe skoroszyty magazynu

Niezależnie od tego, czy w kolumnach **wybrano**wartość w kolumnach Dostępność, **Opóźnienie E2E,** **Opóźnienie serwera**i typ błędu **transakcji/Błędy** ze skoroszytu **Przegląd konta** magazynu wielokrotnego, czy też jeden z przycisków dla **błędów,** **wydajności,** **dostępności**i **pojemności** z skoroszytu **Przegląd** z określonego konta magazynu, każdy dostarcza zestaw interaktywnych informacji związanych z magazynowaniem dostosowany do tej kategorii.  

* **Dostępność** otwiera **skoroszyt Dostępności.** Pokazuje bieżący stan kondycji usługi Azure Storage, tabelę przedstawiającą dostępny stan kondycji każdego obiektu sklasyfikowanego według usługi danych zdefiniowanej na koncie magazynu z linią trendu reprezentującą wybrany zakres czasu oraz wykres trendu dostępności dla każdej usługi transmisji danych na koncie.  

    ![Przykład raportu dostępności](./media/storage-insights-overview/storage-account-availability-01.png)

* **Opóźnienie E2E** i **opóźnienie serwera** otwiera skoroszyt **wydajności.** Zawiera kafelek stanu zestawienia przedstawiający opóźnienie E2E i opóźnienie serwera, wykres wydajności E2E w porównaniu z opóźnieniem serwera oraz tabelę podziału opóźnienia udanych wywołań według interfejsu API skategoryzowane według usługi danych zdefiniowanej na koncie magazynu.

    ![Przykład raportu o skuteczności](./media/storage-insights-overview/storage-account-performance-01.png)

* Wybranie dowolnej z kategorii błędów wymienionych w siatce powoduje otwarcie **skoroszytu Niepowodzenie.** Raport zawiera kafelki metryki wszystkich innych błędów po stronie klienta, z wyjątkiem opisanych i udanych żądań, błędów ograniczania przepustowości klienta, wykres wydajności dla metryki wymiaru **typu odpowiedzi** transakcji specyficzne dla atrybutu ClientOtherError oraz dwie tabele — Transakcje według nazwy **interfejsu API** i Transakcje według **typu odpowiedzi**.

   ![Przykład raportu o niepowodzeniu](./media/storage-insights-overview/storage-account-failures-01.png)

* **Pojemność** otwiera skoroszyt **Pojemności.** Pokazuje całkowitą ilość miejsca do magazynowania używanego dla każdego obiektu danych magazynu na koncie na kafelkach i na wykresie oraz liczbę obiektów danych są przechowywane na koncie.  

    ![Strona Pojemność wybranego konta magazynu](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Przypinanie i eksportowanie

Możesz przypiąć dowolną sekcję metryki do pulpitu nawigacyjnego platformy Azure, wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru pin sekcji metryki do pulpitu nawigacyjnego](./media/storage-insights-overview/workbook-pin-example.png)

Skoroszyty **omówienia** konta **Capacity** wieloaspołonowego i magazynu obsługują eksportowanie wyników w formacie programu Excel przez wybranie ikony strzałki w dół po prawej stronie ikony pinezycy.

![Przykład wyników siatki skoroszytu](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Dostosowywanie usługi Azure Monitor dla magazynu (wersja zapoznawcza)

W tej sekcji przedstawiono typowe scenariusze edytowania skoroszytu w celu dostosowania ich do potrzeb związanych z analizą danych:

* Zakres skoroszytu, aby zawsze wybierać określone konta subskrypcji lub magazynu
* Zmienianie danych w siatce
* Zmienianie progu dostępności
* Zmienianie renderowania kolorów

Dostosowania są zapisywane w niestandardowym skoroszycie, aby zapobiec zastąpieniu domyślnej konfiguracji w opublikowanym skoroszycie. Skoroszyty są zapisywane w grupie zasobów w sekcji **Moje raporty,** która jest prywatna dla Ciebie, lub w sekcji **Raporty udostępnione,** dostępnej dla wszystkich osób z dostępem do grupy zasobów. Po zapisaniu niestandardowego skoroszytu należy przejść do galerii skoroszytu, aby go uruchomić.

![Uruchamianie galerii skoroszytu na pasku poleceń](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Określanie konta subskrypcji lub magazynu

Można skonfigurować wieloa subskrypcję i konta magazynu **Przegląd** lub **Pojemności** skoroszyty do zakresu do określonych subskrypcji lub kont magazynu w każdym uruchomieniu, wykonaj następujące kroki.

1. Wybierz **pozycję Monitor** z portalu, a następnie wybierz pozycję Konta magazynu **(wersja zapoznawcza)** z lewego okienka.

2. W **skoroszycie Przegląd** na pasku poleceń wybierz pozycję **Edytuj**.

3. Wybierz z listy rozwijanej **Subskrypcje** co najmniej jedną subskrypcję, której domyślnie ma być domyślna. Pamiętaj, że skoroszyt obsługuje wybieranie w sumie 10 subskrypcji.  

4. Wybierz z listy rozwijanej **Konta magazynu** jedno lub więcej kont, które mają być domyślne. Pamiętaj, że skoroszyt obsługuje wybieranie do 200 kont magazynu. 

5. Wybierz **pozycję Zapisz na** pasku poleceń, aby zapisać kopię skoroszytu z dostosowaniami, a następnie kliknij pozycję Gotowe **edytowanie,** aby powrócić do trybu czytania.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modyfikowanie danych i kolorów w skoroszycie

Wstępnie utworzone skoroszyty zawierają dane metryki i masz możliwość modyfikowania lub usuwania jednej z wizualizacji i dostosowywania do określonych potrzeb zespołu.

W naszym przykładzie pracujemy ze skoroszytem pojemności konta wieloaspo subskrypcji i magazynu, aby zademonstrować, jak:

* Usuwanie metryki
* Zmienianie renderowania kolorów

Można wykonać te same zmiany w przypadku jednego ze **skoroszytów awarii,** **wydajności,** **dostępności**i **pojemności.**

1. Wybierz **pozycję Monitor** z portalu, a następnie wybierz pozycję Konta magazynu **(wersja zapoznawcza)** z lewego okienka.

2. Wybierz **pozycję Pojemność,** aby przełączyć się do skoroszytu pojemności i na pasku poleceń wybierz pozycję **Edytuj** z paska poleceń.

    ![Wybieranie edytowania w celu zmodyfikowania skoroszytu](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Obok sekcji metryki wybierz pozycję **Edytuj**.

    ![Wybierz edytuj, aby zmodyfikować metryki skoroszytu pojemności](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Usuniemy kolumnę **Osi czasu używanego konta,** więc wybierz pozycję **Ustawienia kolumn** w siatce metryk.

    ![Edytowanie ustawień kolumny](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. W okienku **Ustawienia kolumny Edytowanie** wybierz w sekcji **Kolumny** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$| Konto używane pojemność Timeline$**, a w obszarze listy rozwijanej **Moduł renderowania kolumn** wybierz **opcję Ukryte**.

6. Wybierz **pozycję Zapisz i zamknij,** aby zatwierdzić zmianę.

Teraz zmieńmy motyw kolorów dla metryk pojemności w raporcie, aby używać zielonego zamiast niebieskiego.

1. Wybierz **pozycję Ustawienia kolumn** w siatce metryk.

2. W okienku **Ustawienia kolumny Edytowanie** Wybierz w sekcji **Kolumny** **microsoft.storageaccounts-Capacity-UsedCapacity$|microsoft.storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/usługi plików-Pojemność-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storageaccounts/tableservices-Capacity-TableCapacity$**. W obszarze listy rozwijanej **Paleta kolorów**wybierz **zielony**.

3. Wybierz **pozycję Zapisz i zamknij,** aby zatwierdzić zmianę.

4. Wybierz **pozycję Zapisz na** pasku poleceń, aby zapisać kopię skoroszytu z dostosowaniami, a następnie kliknij pozycję Gotowe **edytowanie,** aby powrócić do trybu czytania.  

### <a name="modify-the-availability-threshold"></a>Modyfikowanie progu dostępności

W tym przykładzie pracujemy ze skoroszytem pojemności konta magazynu i pokazujemy, jak zmodyfikować próg dostępności. Domyślnie dostępność procentu raportowania kafelków i siatki jest skonfigurowana z minimalnym progiem 90 i maksymalnym progiem 99. Firma We zostanie zmieniona minimalna wartość progowa **% w** siatce Dostępność według nazwy **interfejsu API** na 85%, co oznacza, że stan kondycji zmieni się na krytyczny, jeśli próg jest mniejszy niż 85%. 

1. Wybierz **pozycję Konta magazynu** z portalu, a następnie wybierz konto magazynu z listy.

2. Z lewego okienka wybierz **pozycję Insights (preview).**

3. W skoroszycie wybierz pozycję **Dostępność,** aby przełączyć się do skoroszytu dostępności, a następnie wybierz pozycję **Edytuj** z paska poleceń. 

4. Przewiń w dół do dołu strony i po lewej stronie obok siatki **Dostępność według interfejsu API** wybierz pozycję **Edytuj**.

    ![Edytowanie ustawień siatki dostępności według nazwy interfejsu API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Wybierz pozycję **Ustawienia kolumny,** a następnie w okienku **Edytuj ustawienia kolumny** w sekcji **Kolumny** wybierz pozycję **Dostępność (%) (Progi + Sformatowany)**.

6. Zmień wartość stanu **krytycznego** stanu kondycji z **90** na **85,** a następnie kliknij przycisk **Zapisz i zamknij**.

    ![Modyfikowanie wartości progowej dostępności dla stanu krytycznego](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Wybierz **pozycję Zapisz na** pasku poleceń, aby zapisać kopię skoroszytu z dostosowaniami, a następnie kliknij pozycję Gotowe **edytowanie,** aby powrócić do trybu czytania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja pomoże Ci w diagnostyce i rozwiązywaniu niektórych typowych problemów, które mogą wystąpić podczas korzystania z usługi Azure Monitor dla magazynu (wersja zapoznawcza). Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Rozwiązywanie problemów z wydajnością, pojemnością lub dostępnością

Aby pomóc w rozwiązywaniu problemów związanych z magazynem, które można zidentyfikować za pomocą usługi Azure Monitor dla magazynu (w wersji zapoznawczej), zobacz [wskazówki dotyczące rozwiązywania problemów](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)z usługą Azure Storage .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Dlaczego widzę tylko 200 kont magazynu?

Liczba wybranych kont magazynu ma limit 200, niezależnie od liczby wybranych subskrypcji.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co się stanie po kliknięciu ostatnio przypiętego kafelka na pulpicie nawigacyjnym?

* Jeśli klikniesz dowolne miejsce na kafelku, przejdziesz do karty, z której kafelek został przypięty. Na przykład, jeśli przypniesz wykres na karcie "Przegląd konta magazynu", a następnie po kliknięciu tego kafelka na pulpicie nawigacyjnym otworzy się ten domyślny widok, jednak jeśli przypniesz wykres z własnej zapisanej kopii, otworzy widok zapisanej kopii.
* Ikona filtru w lewym górnym rogu tytułu otwiera kartę "Konfigurowanie ustawień kafelków".
* Ikona elipsy w prawym górnym rogu daje opcje "Dostosuj dane tytułu", "dostosuj", "odśwież" i "usuń z pulpitu nawigacyjnego".

### <a name="what-happens-when-i-save-a-workbook"></a>Co się stanie po zapisaniu skoroszytu?

* Po zapisaniu skoroszytu można utworzyć nową kopię skoroszytu wraz z edycjami i zmienić jego tytuł. Zapisywanie nie zastępuje skoroszytu, bieżący skoroszyt będzie zawsze widokiem domyślnym.
* **Niezapisany skoroszyt** jest tylko widokiem domyślnym.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Dlaczego nie widzę wszystkich moich subskrypcji w portalu?

Portal będzie wyświetlał dane tylko dla wybranych subskrypcji podczas uruchamiania portalu. Aby zmienić wybrane subskrypcje, przejdź do prawego górnego rogu i kliknij notes z ikoną filtru. Spowoduje to wyświetle kartę Katalog + subskrypcje.

![Katalog i subskrypcja](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Jak zmienić kolorowanki i próg dostępności?

Zapoznaj się [z sekcją Modyfikuj próg dostępności,](storage-insights-overview.md#modify-the-availability-threshold) aby uzyskać szczegółowe kroki dotyczące zmiany kolorowania i progów dostępności.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Jak analizować i rozwiązywać problemy z danymi wyświetlanymi w usłudze Azure Monitor for Storage?

 Szczegółowe informacje na temat analizowania danych usługi Azure Storage wyświetlanych w usłudze Azure Monitor, diagnozowania i rozwiązywania problemów z usługą Microsoft Azure Storage można znaleźć w artykule [Monitor, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting)

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Dlaczego nie widzę wszystkich typów błędów w metrykach?

Obecnie wyświetlane są maksymalnie trzy różne typy błędów, a pozostałe błędy są zgrupowane w jednym zasobniku. Jest kontrolowany za pomocą splitByLimit i może być modyfikowany. Aby zmienić tę właściwość:

1. Kliknij na edytuj skoroszyt.
2. Przejdź do danych, kliknij edytuj, a następnie wybierz **pozycję Transakcje, Suma** lub cokolwiek metryki, które chcesz edytować.

    ![Przejdź do danych i kliknij edytuj, a następnie na "Transakcje, sumy"](./media/storage-insights-overview/fqa7.png)

1. Następnie zmień liczbę podziałów.

    ![Wybierz parametry metryczne"](./media/storage-insights-overview/fqa7-2.png)

Jeśli chcesz zobaczyć n różne typy błędów niż określić splitByLimit jako n + 1, 1 dodatkowe dla pozostałych błędów.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Skoroszyt został zapisany na niektórych kontach magazynu. Dlaczego nie mogę go teraz znaleźć?

Każdy skoroszyt jest zapisywany na koncie magazynu, na które został zapisany. Spróbuj znaleźć określone konto magazynu, na którym użytkownik zapisał skoroszyt. W przeciwnym razie nie ma możliwości znalezienia określonego skoroszytu bez znajomości zasobu (konta magazynu).

### <a name="what-is-time-range"></a>Co to jest zakres czasu?

Zakres czasu pokazuje dane z określonego przedziału czasowego. Na przykład jeśli zakres czasu wynosi 24 godziny, to wyświetla dane z ostatnich 24 godzin.

### <a name="what-is-time-granularity-time-grain"></a>Co to jest ziarnistość czasu (ziarno czasu)?

Szczegółowość czasu jest różnicą czasu między dwoma punktami danych. Na przykład jeśli ziarno czasu jest ustawiona na 1 sekundę, co oznacza, że metryki są zbierane co sekundę.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaka jest szczegółowość czasu po przypięciu dowolnej części skoroszytów do pulpitu nawigacyjnego?

Domyślna szczegółowość czasu jest ustawiona na automatyczną, obecnie nie można jej zmienić w tej chwili.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Jak zmienić przedział czasu/ zakres czasu kroku skoroszytu na pulpicie nawigacyjnym?

Domyślnie zakres przedziału czasu/czasu na kafelku pulpitu nawigacyjnego jest ustawiony na 24 godziny, aby zmienić to kliknięcie na elipsy w prawym górnym rogu, wybierz **pozycję Dostosuj dane kafelka,** zaznacz pole "zastąp ustawienia czasu pulpitu nawigacyjnego na poziomie tytułu", a następnie wybierz przedział czasu za pomocą menu rozwijanego.  

![Wybierz elipsy w prawym rogu kafelka i wybierz pozycję Dostosuj te dane](./media/storage-insights-overview/fqa-data-settings.png)

![W obszarze Konfigurowanie ustawień kafelków wybierz pozycję rozwijanej przedział czasu, aby zmienić przedział czasu/przedział czasu](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Jak zmienić tytuł skoroszytu lub kroku skoroszytu przypiętego do pulpitu nawigacyjnego?

Tytuł kroku skoroszytu lub skoroszytu przypiętego do pulpitu nawigacyjnego zachowuje tę samą nazwę, jaką miał w skoroszycie. Aby zmienić tytuł, należy zapisać własną kopię skoroszytu. Następnie będzie można nazwać skoroszyt przed naciśnięciem zapisz.

![Wybierz pozycję Zapisz u góry, aby zapisać kopię skoroszytu i zmienić jego nazwę](./media/storage-insights-overview/fqa-change-workbook-name.png)

Aby zmienić nazwę kroku w zapisanym skoroszycie, wybierz edytuj pod tym krokiem, a następnie wybierz bieg na samym dole ustawień.

![Wybierz edytuj u dołu kroku skoroszytu, aby otworzyć ustawienia](./media/storage-insights-overview/fqa-edit.png)
![W ustawieniach wybierz bieg u dołu, aby móc zmienić nazwę kroku](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj [alerty metryk](../platform/alerts-metric.md) i [powiadomienia o kondycji usługi,](../../service-health/alerts-activity-log-service-notifications.md) aby skonfigurować automatyczne alerty ułatwiające wykrywanie problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, tworzenia nowych i dostosowywania istniejących raportów oraz nie tylko, przeglądając [tworzenie interaktywnych raportów za pomocą skoroszytów usługi Azure Monitor](../app/usage-workbooks.md).

* Aby uzyskać szczegółowy przewodnik dotyczący korzystania z analizy magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
