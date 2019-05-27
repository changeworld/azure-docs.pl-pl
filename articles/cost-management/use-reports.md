---
title: Użyj raportach usługi Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano celem raporty Cloudyn, które znajdują się w portalu Cloudyn, aby pomóc Ci efektywnie używać ich.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: f056515e87d01d0a30fec7f792fcb6e5e91c0c89
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969041"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Raporty dostępne w portalu Cloudyn

W tym artykule opisano celem raporty Cloudyn, które znajdują się w portalu Cloudyn. Opisano również, jak skutecznie używać raportów. Większość raportów są intuicyjne i jednolity wygląd i sposób działania. Większość działań, które można wykonać w jednym raporcie, możesz również wykonać w innych raportach. Aby uzyskać ogólne informacje o sposobie raporty usługi Cloudyn, jak zapisywać i dostosowywać lub aby zaplanować raporty, w tym temacie [omówienie raportów kosztów](understanding-cost-reports.md).

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Typy raportów

Istnieją trzy typy raportów Cloudyn:

- Raporty nadmiernego czasu. Na przykład raport kosztu w czasie. Pokazywanie danych szeregów czasowych przedziałach zaznaczonego rozwiązanie problemu dotyczącego wstępnie zdefiniowanych i raportów w czasie nadmiernego, w którym zostanie wyświetlane rozdzielczość co tydzień ostatnich dwóch miesięcy. Grupowanie i filtrowanie można użyć, aby powiększyć do różnych punktów danych.
  - Czas nadmiernego Raporty ułatwiają wyświetlanie trendów i wykrywanie wzrostów lub anomalii.
- Raporty analizy. Na przykład raport analizy kosztów. Te raporty pokazują dane zagregowane w okresie zdefiniować i umożliwia grupowanie i filtrowanie danych.
  - Raporty mogą ułatwić wyświetlanie wzrostów i ustalaniu przyczyn anomalii analizy i wyświetlane są szczegółowe podziału danych.
- Raporty tabelarycznym. Możesz wyświetlić dowolny raport w postaci tabeli, ale niektóre raporty są wyświetlane tylko w postaci tabeli. Te raporty zawierają szczegółowe listy elementów.
  - Zalecenia są tabelaryczne raportów — nie ma żadnych wizualizacji zaleceń. Jednak można wizualizować wyniki zalecenia. Na przykład oszczędności, wraz z upływem czasu.
  - Tabelaryczne raportów przydają się jako listy akcji lub eksportowanie danych do dalszego przetwarzania. Na przykład tworzenia raportu obciążenia zwrotnego.

Raporty kosztu przedstawiają albo _rzeczywiste_ lub _amortyzowanego_ kosztów.

Rzeczywisty koszt raporty zawierają płatności wybranego horyzoncie czasowym. Na przykład wszystkich opłat jednorazowych takie jak zakupy wystąpienia zarezerwowanego (RI) są wyświetlane w raportach rzeczywistego kosztu jako gwałtowny wzrost kosztów.

Raporty amortyzowanego kosztu rozkładają się jednorazowych opłat w okresie, do których mają zastosowanie. Na przykład jednorazowe opłaty za zakup wystąpienia Zarezerwowanego są rozkładane w okresie rezerwacji, a nie są wyświetlane jako kolekcji. Amortyzowany widok jest jedynym sposobem, aby zobaczyć wartość true, trendy i przewidywania kosztów.

W niektórych przypadkach amortyzacji jest przedstawiany jako osobny raport. Przykłady obejmują raporty analizy kosztów i analiza amortyzowanego kosztu. W innych przypadkach amortyzacji jest zasad raportu, takie jak raporty alokacji kosztów i analizy kosztów.

Można zaplanować dowolny raport okresowe dostarczania. Koszt raporty umożliwiają ustawienie progu, dzięki czemu są one przydatne w przypadku alertów.

## <a name="cost-analysis-vs-cost-allocation"></a>Analiza kosztów a alokacji kosztów

_Analiza kosztów_ raporty wyświetlania danych rozliczeń od dostawców rozwiązań w chmurze. Korzystanie z raportów można grupy i przejść do różnych segmentów danych podzielonych z pliku rozliczeń. Raporty umożliwiają nawigowanie szczegółową kosztów między dostawcą chmury nieprzetworzonych danych dotyczących rozliczeń.

Niektóre _analiza kosztów_ raportów nie Grupuj kosztów według tagów zasobów. I informacje rozliczeniowe opartej tylko w raportach nie widać po przydzielania kosztów przez tworzenie modelu kosztów za pomocą [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

_Alokacja kosztów_ raporty są dostępne po utworzeniu modelu kosztów przy użyciu [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn przetwarza dane kosztów i rozliczeń oraz _odpowiada_ dane użycia i tag dane konta w chmurze. Aby dopasować dane, Cloudyn wymaga dostępu do danych użycia. W przypadku kont, których brakuje poświadczeń są oznaczone jako _nieskategoryzowanych zasobów_.

## <a name="dashboards"></a>Pulpity nawigacyjne

Pulpity nawigacyjne w Cloudy przedstawia ogólny widok raportów. Pulpity nawigacyjne składają się z elementów widget, a każdy element widget jest zasadniczo Miniatura raportu. Gdy użytkownik [dostosowywania raportów](understanding-cost-reports.md#save-and-schedule-reports), Zapisz moje raporty i są one dodawane do pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych, zobacz [wyświetlaj metryki kosztu klucza za pomocą pulpitów nawigacyjnych](dashboards.md).

## <a name="budget-information-in-reports"></a>Informacje dotyczące budżetu w raportach

Wiele raportów Cloudyn Pokaż budżetu informacji po został ręcznie utworzony. Aby raporty nie zawierają informacje budżetu, dopóki nie zostanie utworzony budżetu. Aby uzyskać więcej informacji, zobacz [ustawienia łatwość zarządzania budżetem](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Raporty i funkcji raportowania

Usługa Cloudyn oferuje następujące raporty i funkcji raportowania.

### <a name="cost-navigator-report"></a>Koszt Nawigator raportu

Raport koszt Nawigatora jest szybki sposób, aby wyświetlić swoje użycie rozliczeń za pomocą widoku pulpitu nawigacyjnego. Ma ona podzestaw filtry i podstawowe widoków, aby natychmiast wyświetlić widok podsumowania kosztów w organizacji. Koszty są wyświetlane według daty. Ponieważ raport ma służyć jako widok początkowy kosztów, nie jest tak elastyczna lub jako pełne wiele raportów lub niestandardowe pulpity nawigacyjne, które można utworzyć samodzielnie.

Domyślnie pokazuj głównych widoków w raporcie:

- Koszt wraz z upływem czasu przedstawiający tygodniu pracy paska widok wykresu. Możesz zmienić **zakres dat** wykres słupkowy zakresu dat zmiany.
- Wydatki przez usługę, przy użyciu wykresu kołowego.
- Klasyfikacja zasobów według tagów, za pomocą wykresu kołowego.
- Wydatki według jednostek kosztów, za pomocą wykresu kołowego.
- Suma kosztów według dnia, w widoku listy.

### <a name="cost-analysis-report"></a>Raport analizy kosztów

Raport analizy kosztów jest obliczanie przewidywanych kosztów i obciążeń zwrotnych, w oparciu o zasady. Agreguje swoje użycie chmury podczas wybrany przedział czasu, po zastosowaniu wszystkich reguł alokacji na koszt. Na przykład ją oblicza kosztów według tagów, ponownie przypisuje koszty zasobów bez znaczników i opcjonalnie przydziela użycie wystąpień zarezerwowanych.

Zasady skonfigurowane [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w analizy kosztów raportu i wyniki są następnie łączone w przy użyciu informacji z danych pierwotnych dostawcą chmury.

Jak obliczana jest ten raport? Usługa Cloudyn zachowuje alokacji integralności każdego połączonego konta usługi, stosując _konta koligacji_. Koligacja gwarantuje, że konto, który nie używa określonej usługi nie ma żadnych kosztów tej usługi, przydzielone do niego. Koszty są naliczane w tym konta pozostają w ramach tego konta i nie są obliczane przy użyciu zasad alokacji. Na przykład Niewykluczone, że pięć połączone konta. Jeśli tylko trzy z nich korzystać z usług magazynu koszt usługi storage tylko jest przydzielany przez tagów w trzech kont.

Raport analizy kosztów do:

- Oblicz Twojej organizacji dotyczących obciążenia zwrotnego/przewidywanych kosztów
- Kategoryzowanie wszystkie koszty
- Wyświetlanie zagregowanej całe wdrożenie na określonym przedziale czasu.
- Wyświetlanie kosztów według tagów kategorii, w zależności od zasad utworzonych w modelu kosztów.

Aby użyć raportu Analiza kosztów:

1. Wybierz zakres dat.
2. Dodawanie tagów, zgodnie z potrzebami.
3. Dodaj grupy.
4. Wybierz model kosztów, który został utworzony wcześniej.

### <a name="cost-over-time-report"></a>Raport kosztu w czasie

Koszt w czasie — raport wyświetla wyniki przydzielania kosztów w postaci szeregów czasowych. Umożliwia obserwowanie trendów i wykrywanie nieprawidłowości w danym wdrożeniu. Zasadniczo pokazuje rozłożone w określonym przedziale czasu. Raport zawiera współautorów głównego koszt w tym koszty bieżące i opłaty jednorazowe wystąpień zarezerwowanych, które upłynęły podczas wybrany przedział czasu. Zasady skonfigurowane [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w tym raporcie.

Użyj raport kosztu w czasie, aby:

- Zobacz zmian przestrzeni czasu i które wpływa zmiana jeden dzień (lub zakres dat) do następnego.
- Analizując koszty wraz z upływem czasu określonego wystąpienia.
- Zrozumieć, dlaczego wykryto wzrost kosztów, dla konkretnego wystąpienia.

Aby korzystać z raportu koszt względem czasu:

1. Wybierz zakres dat.
2. Dodawanie tagów, zgodnie z potrzebami.
3. Dodaj grupy.
4. Wybierz model kosztów, który został utworzony wcześniej.
5. Wybierz faktyczne koszty i zamortyzowany kosztów z góry.
6. Wybierz, czy można zastosować reguł alokacji do widoku raw, rozliczeń widoku danych lub do obliczenia kosztów widoku.

### <a name="actual-cost-analysis-report"></a>Raport analizy kosztów rzeczywistych

Analiza rzeczywistego kosztu przedstawia koszty dostawcy bez żadnych modyfikacji. Przedstawia on współautorów głównego kosztów, w tym koszty bieżące i opłat jednorazowych.

Raport można użyć, aby wyświetlić informacje o kosztach dla subskrypcji. W raporcie, subskrypcje platformy Azure są wyświetlane jako **nazwa konta** i **numer konta**. **Połączone konta** Pokaż AWS subskrypcje. Aby wyświetlić koszty subskrypcji podział dla każdego konta, w obszarze **grup**, wybierz typ subskrypcji, czy masz.

Należy użyć raportu Analiza rzeczywistego kosztu, aby:

- Analiza i monitorowanie kosztów pierwotne dostawcy spędzony w określonym przedziale czasu.
- Zaplanuj alertu progu.
- Analizuj niezmodyfikowanego kosztów poniesionych przez Twojego konta i jednostki.

### <a name="actual-cost-over-time-report"></a>Rzeczywisty koszt względem czasu raportu

Rzeczywisty koszt względem czasu jest raport analizy kosztów standardowych, dystrybucji koszt za pośrednictwem rozwiązania zdefiniowanego czasu. Raport przedstawia wydatków wraz z upływem czasu, aby możliwe było obserwować trendy i wykrywanie nieprawidłowości wydatków. Ten raport przedstawia współautorów głównego koszt w tym koszty bieżące i opłaty jednorazowe wystąpień zarezerwowanych, które upłynęły podczas wybrany przedział czasu.

Użyj raportu rzeczywisty koszt względem czasu, aby:

- Zobacz trendy związane z kosztami wraz z upływem czasu.
- Znajdź nieprawidłowości w kosztu.
- Znajdź wszystkie pytania związane z kosztów związanych z dostawcy usług w chmurze.

### <a name="amortized-cost-reports"></a>Raporty amortyzowanego kosztu

Ten zestaw zamortyzowany koszt raporty pokazuje linearized bez użycia na podstawie opłat za usługę lub koszty płatne jednorazowe i rozłożona równomiernie podczas ich czas na ich kosztów. Na przykład jednorazowe opłaty mogą być następujące:

- Opłaty roczne pomocy technicznej
- Opłaty roczne składnika zabezpieczeń
- Opłaty za zakupu wystąpień zarezerwowanych
- Niektóre elementy w portalu Azure Marketplace

W pliku rozliczeń, jednorazowe opłaty są określony podczas korzystania z usług początek i koniec daty (sygnatura czasowa) mają równe wartości. Usługa Cloudyn następnie rozpoznaje je jako jednorazowe opłaty, które są amortyzowanego. Nie są amortyzowanego innych usług na podstawie użycia kosztów użytkowania na żądanie.

Raporty amortyzowanego kosztu obejmują:

- Analiza amortyzowanego kosztu
- Amortyzowany koszt w funkcji czasu

### <a name="cost-analysis-report"></a>Raport analizy kosztów

Raport analizy kosztów zapewnia wgląd w wykorzystanie z chmury i wydatków w zadanym przedziale czasu. Zasady skonfigurowane [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w raporcie analizy kosztów.

Jak rozwiązanie Cloudyn obliczyć ten raport?

Cloudyn zachowuje alokacji integralności każdego połączonego konta usługi, stosując _konta koligacji_. Koligacja gwarantuje, że konto, który nie używa określonej usługi również nie ma żadnych kosztów tej usługi, przydzielone do niego. Koszty są naliczane w tym konta pozostają w ramach tego konta i nie są obliczane przy użyciu zasad alokacji. Na przykład Niewykluczone, że pięć połączone konta. Jeśli tylko trzy z nich korzystać z usług magazynu koszt usługi storage tylko jest przydzielany przez tagów w trzech kont.

Raport analizy kosztów do:

- Wyświetlanie zagregowanej całe wdrożenie na określonym przedziale czasu.
- Wyświetlanie kosztów według tagów kategorii, w zależności od zasad utworzonych w modelu kosztów.

### <a name="cost-over-time-report"></a>Raport kosztu w czasie

Raport koszt względem czasu wyświetla wydatków wraz z upływem czasu, dzięki czemu można wychwycenia trendów i nieprawidłowości powiadomienie, które znajdują się w danym wdrożeniu. Zasadniczo pokazuje rozłożone w określonym przedziale czasu. Raport zawiera współautorów głównego koszt w tym koszty bieżące i opłaty jednorazowe wystąpień zarezerwowanych, które upłynęły podczas wybrany przedział czasu. Zasady skonfigurowane [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w tym raporcie.

Użyj raport kosztu w czasie, aby:

- Zobacz zmian przestrzeni czasu i które wpływa zmiana jeden dzień (lub zakres dat) do następnego.
- Analizując koszty wraz z upływem czasu określonego wystąpienia.
- Zrozumieć, dlaczego wykryto wzrost kosztów, dla konkretnego wystąpienia.

### <a name="custom-charges-report"></a>Niestandardowy raport opłaty

Przedsiębiorstwa i dostawcy usług Kryptograficznych użytkownicy często stawia świadczenie usług dodano swoim klientom zewnętrznego lub wewnętrznego, oprócz własnych zużycie zasobów w chmurze. Należy zdefiniować opłaty niestandardowe dla dodanych usług lub rabatów, które są dodawane do rozliczeń lub raportów obciążeń zwrotnych jako niestandardowy pozycji klienta.

Opłaty za usługę niestandardowe odzwierciedlają usług, które normalnie nie są wyświetlane w rachunek. Opłaty niestandardowe, które tworzysz są następnie wyświetlane w raportach kosztów.

*Opłaty niestandardowe nie są ceny niestandardowe*. Lista opłaty niestandardowe nie pokazuje różnym tempie, możesz może być ładowania. Na przykład AWS, opłaty są wyświetlane tak samo, jak są one rozliczane.

Aby utworzyć niestandardowe opłaty:

1. W **opłaty niestandardowe**, kliknij przycisk **Dodaj nowe**. _Dodaj nowe opłaty niestandardowe_ zostanie wyświetlone okno dialogowe.
2. W **Nazwa dostawcy**, wprowadź nazwę dostawcy.
3. W **nazwa usługi**, wprowadź typ usługi.
4. W **opis**, Dodaj opis niestandardowego opłat.
5. W **typu**, wprowadź wyboru **procent** a następnie na liście rozwijanej usług wybierz usługi do uwzględnienia jako opłaty niestandardowe w raportach kosztów.
6. W **płatności**, wybierz opcję, jeśli jest jednorazowe opłaty lub opłaty cykliczne. Opłaty w przypadku opłaty cykliczne, zaznacz Amortized, jeśli chcesz opłat, aby być amortyzowanego i wybrać liczbę miesięcy.
7. W **daty**, jeśli Jednorazowa opłata jest zaznaczone, w **Data rozpoczęcia obowiązywania**, wprowadź datę są opłacane. Jeśli wybrano opłaty cykliczne, wprowadź zakres dat, w tym datę rozpoczęcia oraz datę zakończenia opłata.
8. W **drzewo jednostek**, zaznacz obiekty, które chcesz zastosować obciążenie, a następnie wybierz pozycję **na**.

_Gdy opłaty są przypisane do jednostki, użytkownicy nie mogą zmieniać je. Opłaty, które są dodawane przez administratora do obiektu nadrzędnego są przeznaczone tylko do odczytu._

Aby wyświetlić opłaty niestandardowe:

Opłaty niestandardowe są wyświetlane w raportach kosztów. Na przykład otworzyć raport analiza rzeczywistego kosztu, następnie w obszarze **rozszerzone filtry**, wybierz opcję **autonomiczny**. A następnie przefiltruj, aby pokazać **opłaty niestandardowe**.

### <a name="cost-allocation-360"></a>Alokacja kosztu 360

Cost Allocation 360 umożliwia tworzenie modeli alokacji kosztów niestandardowe można przypisać kosztów do wykorzystywanych zasobów w chmurze. Wiele raportów zawierają informacje z modeli kosztów niestandardowych, utworzonych przy użyciu modeli kosztów niestandardowych. Ponadto niektóre raporty zawierają tylko informacje o po jej utworzeniu niestandardowy model kosztów przy użyciu alokacji kosztów.

Aby uzyskać więcej informacji na temat tworzenia modeli kosztów niestandardowych zobacz [samouczka: Zarządzanie kosztami przy użyciu platformy Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Usługa Cost vs. Raport budżetu względem czasu

Cost vs. Budżetu względem czasu raportu umożliwia porównanie współautorzy głównego kosztów względem Twojego budżetu. Przypisane budżetu pojawia się w raporcie, aby wyświetlić swoje użycie budżetu (over/w obszarze/par) wraz z upływem czasu. Za pomocą pola Pokaż/Ukryj u góry raportu, możesz wybrać widok kosztów, budżet, skumulowany koszt i Łączny budżet.

### <a name="current-month-projected-cost-report"></a>Bieżący raport przewidywany koszt dla miesiąca

Raport przewidywany koszt dla bieżącego miesiąca zapewnia wgląd w Podsumowanie bieżącego koszt od początku miesiąca. W tym raporcie przedstawiono koszty od początku miesiąca z zakresu od poprzedniego miesiąca, a suma przewidywany koszt dla bieżącego miesiąca. Bieżący miesiąc przewidywany koszt jest obliczany jako suma aktualne miesięcznych kosztów i projekcji, na podstawie jej kosztu monitorowane w ciągu ostatnich 30 dni.

Raport przewidywany koszt dla bieżącego miesiąca do:

- Projekt miesięczne koszty używania przez usługę
- Projekt miesięczne koszty używania przez konto

### <a name="annual-projected-cost-report"></a>Raport przewidywany koszt roczny

Raport roczne koszty planowane służy do wyświetlania roczne koszty planowane na podstawie poprzednich trendów wydatków. Przedstawia on na następne 12 miesięcy ogólne przewidywanych koszty. Rzuty są wykonywane przy użyciu funkcji trend ekstrapolacji ciągu najbliższych 12 miesięcy, w oparciu o koszty związane z ostatnich 30 dni użytkowania.

### <a name="budget-management-settings"></a>Ustawienia zarządzania budżetem

Łatwość zarządzania budżetem umożliwia ustawienie budżetu na rok obrachunkowy.

Aby dodać budżetu do jednostki:

1. Na stronie zarządzania budżetem w obszarze **jednostek**, wybierz jednostkę, której chcesz utworzyć budżetu.
2. W tym roku budżetu wybierz roku, w którym chcesz utworzyć budżetu.
3. W każdym miesiącu ustawić swój budżet a następnie kliknij pozycję **Zapisz**.

Aby zaimportować plik do rocznych budżetu:

1. W obszarze **akcje**, wybierz opcję **wyeksportować** można pobrać pusty szablon pliku CSV do użytku jako podstawa Twojego budżetu.
2. Podaj plik CSV z wpisy budżetu i zapisać je lokalnie.
3. W obszarze **akcje**, wybierz opcję **importu**.
4. Wybierz zapisany plik, a następnie kliknij przycisk **OK**.

Aby wyeksportować budżetu ukończone jako plik CSV, w obszarze **akcje**, wybierz opcję **wyeksportować** można pobrać pliku.

Po zakończeniu swój budżet jest wyświetlany w raportach analizy kosztów i Cost vs. Raport budżetu względem czasu. Można także zaplanować raporty na podstawie progów budżetu.

### <a name="azure-resource-explorer-report"></a>Raport usługi Azure Resource Explorer

Raport usługi Azure Resource Explorer pokazuje listę zbiorcze wszystkich zasobów platformy Azure dostępne w rozwiązaniu Cloudyn. Aby wydajnie korzystać z raportu, kont systemu Azure należy rozszerzyć włączone metryki. Rozszerzonych metryk zapewniają dostęp do rozwiązania Cloudyn na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Raport usługi Azure zasobów w czasie

Zasoby platformy Azure względem czasu przedstawia podział wszystkich zasobów w określonym okresie. Aby wydajnie korzystać z raportu, kont systemu Azure należy rozszerzyć włączone metryki. Rozszerzonych metryk zapewniają dostęp do rozwiązania Cloudyn na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Raport Explorer wystąpienia

Raport wystąpienia Eksploratora jest używany do wyświetlania różnych metryk zasobów na maszynach wirtualnych. Umożliwia przechodzenie do szczegółów do konkretnych wystąpień Aby wyświetlić informacje, takie jak:
- Program odstępach czasu
- Cykl życia w wybranym okresie
- Użycie procesora CPU
- Dane wejściowe sieci
- Ruch danych wyjściowych
- Aktywnych dysków

Raport wystąpienia Eksploratora gromadzi informacje o wszystkich interwałów uruchomionej w zakresie określonym dniu i w związku z tym agreguje dane. Aby wyświetlić wszystkich uruchomionych interwałów zakresie dat., rozwiń wystąpienie. Koszt każde wystąpienie jest obliczana dla daty zaznaczony zakres, w zależności od usług AWS i Azure ceny listy. Są stosowane bez rabatów. Za pomocą Pokaż/Ukryj pola do raportu, można dodać dodatkowe pola.

Użyj Eksploratora wystąpienia raport, aby:

- Oblicz szacowany koszt na maszynę.
- Utwórz pełną listę znajdziesz w tym zagregowane godzin uruchamianie wszystkich maszyn, które były aktywne w zakresie czasu.
- Utwórz listę według dostawcy usług w chmurze lub konta.
- Wyświetl maszyny utworzone lub przerwana w zakresie czasu.
- Wyświetl wszystkie obecnie zatrzymanych maszyn.
- Wyświetl znaczniki poszczególnych maszyn.

### <a name="instances-over-time-report"></a>Raport wystąpienia względem czasu

Za pomocą raportu wystąpień w czasie, widać maksymalna liczba maszyn, które było aktywnych każdego ciągu w wybranym zakresie czasu. W przypadku definiowanych rozdzielczości według tygodnia lub miesiąca, wyniki są maksymalną liczbę maszyn aktywne w danym dniu w danym miesiącu. Wybierz zakres dat, aby wybrać filtry, które mają być wyświetlane w raporcie.

### <a name="instance-utilization-over-time-report"></a>Raport wykorzystania w czasie wystąpienia

Ten raport zawiera podział użycia procesora CPU lub pamięci wraz z upływem czasu dla wszystkich wystąpień.

### <a name="compute-power-cost-over-time-report"></a>Raport koszt energii względem czasu obliczeń

Raport obliczenia mocy względem czasu zawiera podział mocy obliczeniowej za pośrednictwem w określonym zakresie dat. Mimo że inne raporty pokazują liczba uruchomionych maszyn lub godziny, w czasie wykonywania, ten raport przedstawia podstawowe godziny, godziny użycia jednostki obliczeniowej lub godzinach GB pamięci RAM.

Użyj raportu, aby:

- Sprawdź moc obliczeniową w ramach zakresu określonej daty.
- Godziny obliczeniowe widoku opartych na modelach alokacji kosztów.

Ten raport jest połączony z Twojej [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) zasady, dzięki czemu są wyświetlane wyniki na podstawie zdefiniowanych znakowanie i zasady koszt wybranych zasad. Jeśli nie masz zasad utworzonych na wyniki nie są wyświetlane.

### <a name="compute-power-average-cost-over-time-report"></a>Obliczenia raportów Power średni koszt względem czasu

Raport obliczenia Power średni koszt względem czasu umożliwia wyświetlanie więcej niż tylko koszt każdej uruchomionej maszynie. Ten raport prezentuje swoje średni koszt na godzinę wystąpienia, podstawowa godzina, godzin jednostek obliczeniowych i godzinę GB pamięci RAM. Raport zapewnia wgląd w wydajność wdrożenia.

Ten raport jest połączony z Twojej [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) zasady, dzięki czemu są wyświetlane wyniki na podstawie zdefiniowanych znakowanie i zasady koszt wybranych zasad. Jeśli nie masz zasad utworzonych na wyniki nie są wyświetlane.

### <a name="s3-cost-over-time-report"></a>Raport S3 koszt względem czasu

Raport S3 koszt względem czasu zawiera podział kosztów Amazon Simple Storage Service (S3) na przedział wraz z upływem czasu określonym przedziale czasu. Raport pomaga znaleźć zasobników, które są sterowniki głównego kosztów i pokazuje trendy w usługi S3 użycia i wydatków.

### <a name="s3-distribution-of-cost-report"></a>Raport S3 dystrybucji kosztów

Raport umożliwia analizowania koszt S3 w ostatnim miesiącu przez klasę zasobnika i magazynu. Aby ustawić próg widzialności, można użyć widoku wykresu kołowego. Alternatywnie można użyć widoku tabeli, aby wyświetlić sumy częściowe.

### <a name="s3-bucket-properties-report"></a>Raport o właściwościach przedział S3

Aby wyświetlić właściwości przedział S3, należy użyć raportu. Aby ustawić próg widzialności, można użyć widoku wykresu kołowego. Alternatywnie można użyć widoku tabeli, aby wyświetlić sumy częściowe.

### <a name="rds-instances-over-time-report"></a>Raport usług pulpitu zdalnego wystąpienia względem czasu

Raport umożliwia wyświetlanie informacji o podziale wszystkich wystąpień Amazon Relational Database Service (RDS) w podanym okresie.

### <a name="rds-active-instances-report"></a>Raport aktywnych wystąpień usług pulpitu zdalnego

Raport umożliwia analizowania aktywnych wystąpień usług pulpitu zdalnego. W raporcie Rozwiń element wiersza, aby wyświetlić dodatkowe informacje.

### <a name="azure-reserved-instances-report"></a>Raport usługi Azure Reserved Instances

Raport Azure Reserved Instances zapewnia pojedynczy widok wszystkich wystąpień zarezerwowanych platformy Azure. Ten raport wyświetla każdy zakup, ponieważ jest element wiersza. Raport zawiera także szczegółowe informacje o ten zakup, takie jak konto, które zakupione w ramach jej typ zakupów i typu wystąpienia, i tak dalej pozostało dni. Możesz pokazać lub ukryć dane raportu przy użyciu Pokaż/Ukryj pola.

Umożliwia wyświetlenie raportu zarezerwowane wystąpienia platformy Azure:

- Lista wszystkie rezerwacje według daty zakupu.
- Czas pozostały do wygaśnięcia wystąpienia Zarezerwowanego.
- Jednorazowe opłaty.
- Konto, które zakupione w ramach wystąpienia zarezerwowane, i kiedy.

### <a name="aws-reserved-instances-report"></a>Raport wystąpienia zarezerwowane usługi AWS

Raport wystąpienia zarezerwowane usługi AWS zapewnia pojedynczy widok wszystkich usług AWS zarezerwowanych wystąpień. Ten raport wyświetla każdy zakup jest własnej pozycji i szczegółowe informacje o ten zakup, takie jak konto, które zakupione w ramach jej typ zakupów i typu wystąpienia, i tak dalej pozostało dni. Możesz pokazać lub ukryć dane raportu przy użyciu Pokaż/Ukryj pola.

Raport wystąpienia zarezerwowane usługi AWS do wyświetlenia:

- Lista wszystkie rezerwacje według daty zakupu.
- Czas pozostały do wygaśnięcia wystąpienia Zarezerwowanego.
- Jednorazowe opłaty.
- Oryginalny identyfikator zakupu (identyfikator rezerwacji).
- Wystąpienia zarezerwowane kupione na konto i kiedy.

### <a name="ec2-ri-buying-recommendations-report"></a>Raport zalecenia zakupu wystąpień Zarezerwowanych usługi EC2

Podstawą zużycie zasobów w chmurze jest model na żądanie, gdzie zasobów ponosić kosztów tylko wtedy, gdy jest używany. Bez zobowiązań z góry — płacisz tylko za to, czego używasz, gdy jest używany.

Usługi AWS oferuje cennik modelu jego elastycznej obliczenia usług w chmurze (usługi EC2) zamiast — wystąpienia zarezerwowanego (RI). Tym modelu cenowym gwarantuje użytkowników pojemność zawsze wtedy, gdy jej potrzebują przez okres wystąpienia Zarezerwowanego. Wystąpienie zarezerwowane oferuje rabaty cenowe w stosunku do cen na żądanie. W zamian użytkowników składają zobowiązanie użycie wirtualnego wystąpienia. Zobowiązanie jest powiązany z określonej rodziny, rozmiar, strefa dostępności (AZ) i systemu operacyjnego, w okresie zobowiązania (rok lub trzy lata). Wystąpienie zarezerwowane umożliwia AWS efektywne planowanie przyszłych pojemności, jak również do uzyskania zobowiązania klienta do korzystania z jego usług.

Trzy opcje płatności za wystąpienia zarezerwowane, będące ponoszonych z góry wszystkich:

- Suma zbiorczego w dniu 0, oferujący największe rabaty
- Nie z góry - w której koszt wystąpienia Zarezerwowanego jest płatną w miesięcznych ratach w czasie trwania wystąpienia zarezerwowanego oferty najniższy rabatu
- Partial ponoszonych z góry kosztów, w których ¼ - ½, ceny czy płatna na początku, a pozostałe w miesięcznych ratach, z rabatu, która jest mniejsza, ale zamknąć, współczynnikowi ponoszonych z góry wszystkie

Cloudyn ocenia czas pracy każdego komputera w ciągu ostatnich 30 dni. Cloudyn zaleca kupowania wystąpienia zarezerwowane, gdy jest bardziej opłacalna, aby uruchomić maszynę z wystąpienia Zarezerwowanego na bieżącym poziomie przestojów.

Ten raport prezentuje uzasadnienie odpowiednie zalecenia zaoszczędzić pieniądze większość w ciągu roku. Zalecenia sugerują, zastępując wystąpienia zarezerwowane wystąpienia na żądanie. Możesz kupić wystąpienia zarezerwowane, bezpośrednio z raportu.

Każda karta otwiera się jako pełny raport. Istotne sekcje na kartach obejmują:

- **Wpływ zakupu wystąpień Zarezerwowanych usługi EC2** — w tej części przedstawiono symulacji różnicy między wystąpieniami zarezerwowanymi na żądanie w programie vs. Kliknij przycisk **powiększyć**, aby wyświetlić pełny raport wpływ zakupu wystąpień Zarezerwowanych usługi EC2 z filtrami już zdefiniowane swoje zalecenia. Ten raport przedstawia wpływ zakupu wszystkich potencjalnych zakupów wystąpień Zarezerwowanych. Oczekiwano Średni czas pracy się możliwość zapisywania w momencie zakupu wystąpień zarezerwowanych usługi EC2, można dostosować.

- **Zapisywanie analizy** -tej sekcji przedstawiono potencjalne oszczędności osiągnąć oraz miesiąca oszczędności są actualized w trakcie wypełniania Cloudyn, zalecenia. Rzeczywiste oszczędności i procent zapisywane są wyróżnione kolorem czerwonym.

- **Porównanie typów wystąpień Zarezerwowanych usługi EC2** — w tej sekcji kładzie nacisk wyróżnienia zwrot z inwestycji w zalecane wdrożenie firmy Cloudyn, w tym wszystkie odpowiednie opcje. Wyniki w tym raporcie przyjęto założenie, że na maszynie jest uruchomiony w 100% czasu. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy.

- **Wystąpienia względem czasu** — w tej sekcji przedstawia podział wszystkie wystąpienia skojarzone z zaleceń, na żądanie, Reserved Instances i miejscu. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy.
- **Punkty Breakeven** — w tej sekcji Wyświetla spis wszystkich możliwych zalecane wdrożenia i zwrot z inwestycji i miesiąc, gdy wystąpi zwrot z inwestycji. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy.

### <a name="ec2-reservations-over-time-report"></a>Raport rezerwacje usługi EC2 względem czasu

Raport rezerwacje usługi EC2 względem czasu śledzi stan wykorzystanie zakupionych wystąpień zarezerwowanych wystąpienia usługi EC2. Rozpoznawanie raportu można ustawić godziny, dnia lub tygodnia.

Użyj raportu, aby:

- Wyświetl zakupu rezerwacji, używane i nie jest używany.
- Wyświetl szczegóły, aby rozwiązanie godzin Zobacz użycie wystąpień Zarezerwowanych na godzinę.

### <a name="savings-over-time-report"></a>Oszczędności względem czasu raportu

Wybierz raport oszczędności w czasie i wyświetlić oszczędności osiągane przy użyciu zarezerwowanych wystąpień, a także dodatkowe wystąpienia. Ten raport prezentuje zwrot z inwestycji osiągnąć wraz z upływem czasu wynikających z zakupu wystąpień Zarezerwowanych.

Aby wyświetlić oszczędności wynikające z wystąpienia zarezerwowane, pogrupować wyniki według **modelu cen** i wybierz **rezerwacji**. Aby wyświetlić oszczędności wystąpień Zarezerwowanych osiągnięte przez dane konto lub typu wystąpienia, Dodaj odpowiednie grupowanie i filtrowanie do typu konta lub wystąpienia.

Aby wyświetlić oszczędności wynikające z użycia wystąpienia w miejscu, filtrować **modelu cen** do **miejscu**. Domyślny filtr dla tego raportu jest wystąpień Zarezerwowanych i wystąpień Spot.

### <a name="rds-ri-buying-recommendations-report"></a>Raport zalecenia zakupu wystąpień Zarezerwowanych usług pulpitu zdalnego

Raport zalecenia zakupu wystąpień Zarezerwowanych usług pulpitu zdalnego zaleca się użycie wystąpień zarezerwowanych usług pulpitu zdalnego zamiast wystąpienia na żądanie.

Każda karta otwiera się jako pełny raport. Istotne sekcje na kartach obejmują:

- **Wpływ zakupu wystąpień Zarezerwowanych usług pulpitu zdalnego** — ta sekcja zawiera symulacji różnicy między żądanie zarezerwowanych wystąpieniach programu vs. Kliknij przycisk **powiększyć** Aby wyświetlić pełny raport wpływ zakupu wystąpień Zarezerwowanych usług pulpitu zdalnego z filtrami już zdefiniowane swoje zalecenia. Ten raport umożliwia wszystkich potencjalnych zakupów wystąpień Zarezerwowanych w rozgrywkach zakupu.  Można dopasować oczekiwanego Średni czas pracy i zobacz możliwości zapisywania, kupując wystąpienia zarezerwowane.
- **Zapisywanie analizy** — ta sekcja zawiera potencjalne oszczędności osiągnąć i miesiąc oszczędności są actualized w trakcie wypełniania Cloudyn, zalecenia. Rzeczywiste oszczędności i procent zapisywane są wyróżnione kolorem czerwonym.

- **Porównanie typów wystąpień Zarezerwowanych usług pulpitu zdalnego** — w tej sekcji kładzie nacisk wyróżnienia zwrot z inwestycji w zalecane wdrożenie, w tym wszystkie odpowiednie opcje. Wyniki w tym raporcie przyjęto założenie, że na maszynie jest uruchomiony w 100% czasu. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy dla wybranej maszyny.
- **Wystąpienia względem czasu** — w tej sekcji przedstawia podział wszystkie wystąpienia skojarzone z zaleceń, na żądanie, Reserved Instances i miejscu. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy.

- **Punkty Breakeven** — w tej sekcji Wyświetla spis wszystkich możliwych zalecane wdrożenia i zwrot z inwestycji i miesiąc, gdy wystąpi zwrot z inwestycji. Kliknij przycisk **Powiększ** aby otworzyć raport szczegółowy.

### <a name="rds-reservations-over-time-report"></a>Raport usług pulpitu zdalnego rezerwacje w czasie

Raport usług pulpitu zdalnego rezerwacji w czasie umożliwia wyświetlanie podział usługi używane i nieużywane rezerwacje w podanym okresie.

### <a name="reserved-instance-purchase-impact-report"></a>Raport wpływ zakupu wystąpień zarezerwowanych

Raport wpływ zakupu wystąpień Zarezerwowanych usługi EC2 umożliwia symulowanie koszt wystąpienia zarezerwowanego a kosztem na żądanie, wraz z upływem czasu. Może pomóc Ci podejmować decyzje lepiej zakupu. Dostosuj filtry, takie jak Średni czas wykonywania, okres, platformy i inne osoby do podejmowania świadomych decyzji, gdy należy wziąć pod uwagę zakupów wystąpień Zarezerwowanych.

### <a name="cost-effective-sizing-recommendations-report"></a>Ekonomiczne zalecenia wymiarowania efektywnego raportu

Raport zalecenia wymiarowania efektywnego ekonomicznego zawiera wyniki dla platformy AWS i Azure. Użytkownicy usług AWS zakupów wystąpień Zarezerwowanych są brane pod uwagę i wyniki nie uwzględniają maszyn działających jako wystąpienie zarezerwowane. Ten raport zawiera listę niedostatecznie używanych wystąpień, które są kandydatami do downsize. Zalecenia są oparte na danych użycia i wydajności z ostatnich 30 dni. W każdym zaleceniem jest lista kandydatów do downsize, uzasadnienie downsize i łącze, aby wyświetlić szczegółowe informacje i metryki wydajności wystąpienia. A gdy odpowiednie zalecenia zaleca zmianę na nowsze typy wystąpienia generacji.

Nie można pobrać listy wystąpienia identyfikatorów, które są zalecane do downsize z poziomu tego raportu. Aby pobrać identyfikatorów wystąpień, raport wszystkie zalecenia dotyczące odpowiedniego rozmiaru.

Rozważmy następujący przykład redukcję zatrudnienia:

Masz sześć m3.xlarge uruchamianych wystąpień. Analiza Cloudyn pokazuje, że pięć z nich ma niskiego poziomu wykorzystania Procesora. Rozważ zmniejszenie rozmiaru je.

W wpływu na koszt wpływ na koszt jest obliczany. W tym przykładzie, rozwijając element wiersza widać, że bieżąca cena dla jednego wystąpienia m3.xlarge (Linux/Unix) kosztuje $0.266 na godzinę i m3.large jednego wystąpienia (Linux/Unix) koszty 0.133 $ / godz. Roczny koszt jest więc 11,651 $ pięć wystąpień m3.xlarge uruchomione w 100% wykorzystania. Roczny koszt jest 5,825 $ pięć wystąpień m3.large uruchomione w 100% wykorzystania. Potencjalne oszczędności są 5,825 $.

Aby wyświetlić wymiarowania efektywnego kosztowo uzasadnienia, kliknij i rozwiń element wiersza. W **szczegóły**:

- **Uzasadnienie zalecenie** sekcja Wyświetla bieżącego wdrożenia i liczbę wystąpień, zaleca się downsize.
- **Wpływu na koszt** sekcja wyświetla obliczanie, aby ustalić potencjalne oszczędności.
- **Potencjalne oszczędności roczne** sekcja wyświetla potencjalne oszczędności roczne, gdy zmniejszenie rozmiaru na zalecenia firmy Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Wszystkie zalecenia wymiarowania efektywnego raportu

Ten raport zawiera listę niedostatecznie używanych wystąpień, które są kandydatami do downsize. Zalecenia są oparte na danych użycia i wydajności z ostatnich 30 dni. W każdym zaleceniem możesz wyświetlić szczegółowe informacje i metryki wydajności wystąpienia.

Jeśli zakupione wystąpienia zarezerwowane usługi AWS, ten raport zawiera wyniki dla wszystkich uruchomionych wystąpieniach, w tym uruchamianie jako wystąpienia zarezerwowane wystąpienia.

Użyj raportu wszystkie zalecenia dotyczące ustalania rozmiaru w celu:

- Wyświetlenie listy wszystkich wystąpień, które są kandydatami do downsize.
- Eksportuj listę raportów zawierających identyfikatory i nazwy wystąpień.

Aby wyświetlić szczegóły zalecenia dla konkretnego wystąpienia, kliknij **+** aby rozwinąć szczegóły. Sekcja szczegóły zalecenia zawiera omówienie zalecenia.

**Tagi** sekcja zawiera listę tagów klucze i wartości dla wybranego wystąpienia. Filtruj sekcji za pomocą tagów w okienku po lewej stronie.

**Wykorzystanie procesora CPU** sekcja zawiera wykorzystanie procesora CPU dla tego wystąpienia w ciągu ostatniego miesiąca, dnia.

Kliknij wykres, aby przejść do szczegółów, a następnie otwórz wystąpienie procesora CPU przez czas raportu, aby zobaczyć podział wystąpień.

- Użyj **Pokaż/Ukryj pola** do dodawania lub usuwania pól: Sygnatura czasowa, CPU (średnio) Min procesora CPU, maksymalna liczba procesora CPU.
- Użyj **zakres dat** wprowadź datę lub zakres dat i przejść do określonych identyfikator wystąpienia.
- Użyj **rozszerzone filtry** Pokaż wszystkie lub określone identyfikator wystąpienia
- Kliknij przycisk **powiększyć** aby otworzyć raport o wykorzystaniu Procesora

Jeśli wystąpienie nie były monitorowane przez 30 dni, wyświetlany jest niekompletne dane.

**Wykorzystanie pamięci (GB)** sekcja zawiera informacje o pamięci wykorzystywany. Użytkownicy usług AWS metryki pamięci nie są automatycznie dostępne i mają zostać dodane na wystąpienie za pośrednictwem usług AWS. Opłaty za usługi AWS, należy włączyć metryki pamięci dla wystąpień usługi EC2.

**Wykorzystanie pamięci (%)** sekcja Wyświetla procent używanej pamięci.

**Ruchu w sieci dane wejściowe** sekcja Wyświetla migawkę wraz z upływem czasu ruchu sieciowego, średnia i maksymalna, dla wybranego wystąpienia. Umieść kursor nad wiersze, aby wyświetlić datę i maksymalna ruchu dla tego czasu. Kliknij przycisk **Powiększ** można otworzyć raportu ruch danych wejściowych w sieci.

**Ruchu w sieci danych wyjściowych** sekcja Wyświetla migawkę ruchu sieciowego danych wyjściowych dla wybranego wystąpienia. Umieść kursor nad wiersze, aby wyświetlić datę i maksymalna ruchu dla tego czasu. Kliknij przycisk **Powiększ** można otworzyć raportu wyjściowego ruchu w sieci.

### <a name="instance-metrics-explorer-report"></a>Raport Eksploratora metryk wystąpienia

Eksplorator metryk wystąpienia przedstawia metryki wydajności wielu chmur dla wystąpienia. Za pomocą raportu, aby wyświetlić wystąpienia, które są za pośrednictwem lub niewystarczająco wykorzystywanych na podstawie procesora CPU, pamięci i progów metryk sieci.

Aby wyświetlić wydajność wielu chmur, każde wystąpienie:

1. W **zakres dat**, wybierz zakres dat, dla którego chcesz wyświetlić wydajności.
2. W **tagi**, wybierz dowolne tagi, które chcesz wyświetlić.
3. W **filtry**, zaznacz filtry mają być wyświetlane w raporcie.
4. W **rozszerzone filtry**, dostosować progi raportu:
    - Średnia liczba procesora CPU
    - Maks.
    - Średnia liczba pamięci
    - Maksymalny rozmiar pamięci
5. W **rozszerzone filtry**, kliknij przycisk **Pokaż** i następnie wybierz typ wystąpienia, aby wyświetlić.

Aby wyświetlić metryki konkretnego wystąpienia wraz z upływem czasu:

- Przejdź do Eksploratora metryk wystąpienia raportu, a następnie kliknij przycisk **+** Aby przejrzeć szczegółowe informacje.

### <a name="rds-sizing-recommendations-report"></a>Raport zalecenia wymiarowania efektywnego usług pulpitu zdalnego

Raport zalecenia wymiarowania efektywnego usług pulpitu zdalnego zawiera zmiany rozmiaru zalecenia, aby zoptymalizować użycie chmury usług pulpitu zdalnego. Zawiera on listę niedostatecznie używanych wystąpień, które są kandydatami do downsize. Cloudyn, zalecenia są oparte na danych użycia i wydajności w ciągu ostatnich 30 dni. Zalecenia wg nazwy konta, regionu, typu wystąpienia i stanu można filtrować.

### <a name="sizing-threshold-manager-report"></a>Menedżer próg rozmiaru raportu

Zalecenia dotyczące wbudowanych odpowiedniego rozmiaru firmy Cloudyn są obliczane przy użyciu algorytmu złożonych Zasugeruj dokładne zmiany rozmiaru. Można dostosować progi redukcję zatrudnienia zaleceń.

Aby ręcznie dostosować zalecenia wymiarowania efektywnego progu:

1. W Menedżerze próg rozmiaru dopasować wymienionych progów, jak chcesz:
    - % Średnie użycie procesora CPU
    - Procent maksymalnego użycia procesora CPU
    - % Średni pamięci
    - Maksymalny rozmiar pamięci %
3. Kliknij przycisk **Zastosuj** można zapisać zmian.
4. Zmiany dotyczą natychmiast wszystkie zalecenia.

Aby przywrócić domyślne progi:

- W Menedżerze próg rozmiaru kliknij **Przywróć domyślne**.

### <a name="compute-instance-types-report"></a>Raport typów wystąpień obliczeniowych

Użyj typów wystąpień raport, aby:

- Wyświetlanie typów wystąpień usługi, rodzina, nazwa interfejsu API i nazwę.
- Wyświetl szczegóły, takie jak procesor CPU, ECU, ilość pamięci RAM i sieci.

Możesz użyć **wyszukiwania** Aby znaleźć konkretne elementy wiersza.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o używaniu raportów, w tym jak dostosować albo Zapisz i zaplanować raportów, zobacz [omówienie raportów kosztów](understanding-cost-reports.md).
- Dowiedz się więcej o na pulpitach nawigacyjnych zawartych w rozwiązaniu Cloudyn i o tym, jak tworzyć własne niestandardowe pulpity nawigacyjne, zobacz [wyświetlaj metryki kosztu klucza za pomocą pulpitów nawigacyjnych](dashboards.md).
