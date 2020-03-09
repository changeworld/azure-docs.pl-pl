---
title: Poznaj kondycję maszyn wirtualnych platformy Azure | Microsoft Docs
description: W tym artykule opisano, jak zrozumieć kondycję maszyn wirtualnych i podstawowych systemów operacyjnych za pomocą Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3cecb04a4f299051860c45425f0fc4e13c3722ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395805"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Informacje o kondycji maszyn wirtualnych platformy Azure

Platforma Azure obejmuje usługi dla określonych ról lub zadań w obszarze monitorowania, ale nie zapewnia szczegółowych perspektyw związanych z kondycją systemów operacyjnych (OSs) hostowanych na maszynach wirtualnych platformy Azure. Chociaż można używać Azure Monitor w różnych warunkach, nie jest on przeznaczony do modelowania i reprezentowania kondycji podstawowych składników lub ogólnej kondycji maszyn wirtualnych.

Korzystając z Azure Monitor dla maszyn wirtualnych kondycji, można aktywnie monitorować dostępność i wydajność systemu operacyjnego gościa systemu Windows lub Linux. Funkcja kondycji korzysta z modelu, który reprezentuje najważniejsze składniki i ich relacje, zawiera kryteria określające sposób mierzenia kondycji składników i wysyła alert po wykryciu nieprawidłowego stanu.

Wyświetlanie ogólnej kondycji maszyny wirtualnej platformy Azure i bazowego systemu operacyjnego można zaobserwować z dwóch perspektyw: bezpośrednio z maszyny wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów z Azure Monitor.

W tym artykule pokazano, jak szybko ocenić, zbadać i rozwiązać problemy z kondycją wykryte przez funkcję kondycji Azure Monitor dla maszyn wirtualnych.

Informacje o konfigurowaniu Azure Monitor dla maszyn wirtualnych można znaleźć w temacie [Enable Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

>[!NOTE]
>Niedawno [ogłoszono zmiany wprowadzane](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) do funkcji kondycji w oparciu o Opinie otrzymane od naszych klientów publicznej wersji zapoznawczej. Mając na względzie liczbę wprowadzonych zmian, będziemy mogli przestać oferować funkcję kondycji dla nowych klientów. Istniejący klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania dotyczące ogólnej dostępności](vminsights-ga-release-faq.md). 

## <a name="monitoring-configuration-details"></a>Szczegóły konfiguracji monitorowania

Ta sekcja zawiera opis domyślnych kryteriów kondycji do monitorowania maszyn wirtualnych z systemami Windows i Linux. Wszystkie kryteria kondycji są wstępnie skonfigurowane do wysyłania alertu po wykryciu złej kondycji.

| Nazwa monitora | Częstotliwość (min) | Czas trwania lookback (min) | Operator | Próg | Alert w stanie | Ważność | Kategoria obciążenia | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Dysk logiczny w trybie online | 5 | 15 | <> | 1 (prawda) | Krytyczny | Sev1 | Linux | 
| Wolne miejsce na dysku logicznym | 5 | 15 | < | 200 MB (ostrzeżenie)<br> 100 MB (krytyczna) | Ostrzeżenie | Sev1<br> Sev2 | Linux | 
| Wolne węzły I na dysku logicznym (%) | 5 | 15 | < | 5% | Krytyczny | Sev1 | Linux | 
| Wolne miejsce na dysku logicznym (%) | 5 | 15 | < | 5% | Krytyczny | Sev1 | Linux | 
| Stan karty sieciowej | 5 | 15 | <> | 1 (prawda) | Ostrzeżenie | Sev2 | Linux | 
| Dostępna pamięć (MB) w systemie operacyjnym | 5 | 10 | < | 2,5 MB | Krytyczny | Sev1 | Linux | 
| Średni czas dysku w s/odczyt | 5 | 25 | > | 0,05 s | Krytyczny | Sev1 | Linux | 
| Średni czas dysku w s/transfer | 5 | 25 | > | 0,05 s | Krytyczny | Sev1 | Linux | 
| Średni czas dysku w s/zapis | 5 | 25 | > | 0,05 s | Krytyczny | Sev1 | Linux | 
| Stan dysku | 5 | 25 | <> | 1 (prawda) | Krytyczny | Sev1 | Linux | 
| Łączny czas procesora w systemie operacyjnym (%) | 5 | 10 | >= | 95% | Krytyczny | Sev1 | Linux | 
| Procent łącznego użycia procesora CPU | 5 | 10 | >= | 95% | Krytyczny | Sev1 | System Windows | 
| Błąd lub uszkodzenie systemu plików | 60 | 60 | <> | 4 | Krytyczny | Sev1 | System Windows | 
| Średnia liczba sekund dysku logicznego na odczyt | 1 | 15 | > | 0,04 s | Ostrzeżenie | Sev2 | System Windows | 
| Średnia liczba sekund dysku logicznego na transfer | 1 | 15 | > | 0,04 s | Ostrzeżenie | Sev2 | System Windows | 
| Średni czas dysku logicznego (w sekundach) na zapis (dysk logiczny) | 1 | 15 | > | 0,04 s | Ostrzeżenie | Sev2 | System Windows | 
| Bieżąca długość kolejki dysku (dysk logiczny) | 5 | 60 | >= | 32 | Ostrzeżenie | Sev2 | System Windows | 
| Wolne miejsce na dysku logicznym (MB) | 15 | 60 | > | Ostrzeżenie 500 MB<br> krytyczne 300 MB | Krytyczny | Sev1<br> Sev2 | System Windows | 
| Wolne miejsce na dysku logicznym (%) | 15 | 60 | > | 10% ostrzeżenia<br> 5% krytyczne | Krytyczny | Sev1<br> Sev2 | System Windows |
| Procent czasu bezczynności dysku logicznego | 15 | 360 | <= | 20C | Ostrzeżenie | Sev2 | System Windows | 
| Procent wykorzystania przepustowości | 5 | 60 | >= | 60% | Ostrzeżenie | Sev2 | System Windows | 
| Procent całkowitej liczby użytych przepustowości | 5 | 60 | >= | 75% | Ostrzeżenie | Sev2 | System Windows | 
| Procent użycia przepustowości | 5 | 60 | >= | 60% | Ostrzeżenie | Sev2 | System Windows | 
| Service Health klienta DHCP | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Service Health klienta DNS | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Service Health dziennika zdarzeń systemu Windows | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Service Health zapory systemu Windows | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Service Health RPC | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Service Health serwera | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Windows Remote Management Service Health | 5 | 12 | <> | 4 (uruchomione) | Krytyczny | Sev1 | System Windows | 
| Dostępna pamięć w megabajtach | 5 | 10 | < | 100 MB | Krytyczny | Sev1 | System Windows | 
| Wolne wpisy tabeli stron systemu | 5 | 10 | <= | 5000 | Krytyczny | Sev1 | System Windows | 
| Liczba stron pamięci na sekundę | 5 | 10 | >= | 5000/s | Ostrzeżenie | Sev1 | System Windows | 
| Procent zadeklarowanej pamięci w użyciu | 5 | 10 | > | 80% | Krytyczny | Sev1 | System Windows | 
| Średni czas transferu na sekundę dysku | 1 | 15 | > | 0,04 s | Ostrzeżenie | Sev2 | System Windows | 
| Średni czas dysku na zapis | 1 | 15 | > | 0,04 s | Ostrzeżenie | Sev2 | System Windows | 
| Bieżąca długość kolejki dysku | 5 | 60 | >= | 32 | Ostrzeżenie | Sev2 | System Windows | 
| Procent czasu bezczynności dysku | 5 | 60 | >= | 20C | Ostrzeżenie | Sev2 | System Windows | 

>[!NOTE]
>Lookback czas trwania reprezentuje, jak często okno wyszukania sprawdza wartości metryk, na przykład w ciągu ostatnich pięciu minut.  

>[!NOTE]
>Częstotliwość określa, jak często alert dotyczący metryki sprawdza, czy warunki są spełnione, na przykład co minutę.  Jest to szybkość, z jaką jest wykonywane kryterium kondycji, a lookback to czas trwania oceny kryterium kondycji. Na przykład kryterium kondycji ocenia, czy **użycie procesora CPU** jest większe niż 95% z częstotliwością wynoszącą 5 minut i pozostaje większe niż 95% przez 15 minut (3 kolejne cykle oceny), stan zostanie zaktualizowany do ważności krytycznej, jeśli nie jest jeszcze.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Aby się zalogować, przejdź do [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Wprowadzenie do Azure Monitor dla maszyn wirtualnych kondycji

Przed użyciem funkcji kondycji dla pojedynczej maszyny wirtualnej lub grupy maszyn wirtualnych ważne jest, aby zrozumieć, jak prezentowane są informacje i jakie wizualizacje reprezentuje.

### <a name="view-health-directly-from-a-vm"></a>Wyświetlanie kondycji bezpośrednio z maszyny wirtualnej

Aby wyświetlić kondycję maszyny wirtualnej platformy Azure, wybierz pozycję **Insights (wersja zapoznawcza)** w lewym okienku maszyny wirtualnej. Na stronie usługi VM Insights karta **kondycja** jest domyślnie otwarta i wyświetlany jest widok kondycji maszyny wirtualnej.

![Przegląd kondycji Azure Monitor dla maszyn wirtualnych wybranej maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health-01.png)

W sekcji **kondycja maszyny wirtualnej gościa** w tabeli przedstawiono zbiorcze informacje o kondycji składników wydajności monitorowane według kryteriów kondycji dla maszyny wirtualnej oraz łączną liczbę alertów dotyczących kondycji maszyn wirtualnych wygenerowanych przez składniki w złej kondycji. Te składniki obejmują **procesor CPU**, **pamięć**, **dysk**i **Sieć**. Rozwiń cudzysłów ostrokątny obok pozycji kondycja maszyny wirtualnej gościa, aby wyświetlić kondycję jej składników.

![Azure Monitor dla maszyn wirtualnych stan kondycji składnika wybranej maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health-02.png)

Wybranie stanu obok składnika spowoduje otwarcie środowiska diagnostyki kondycji w kontekście wybranego składnika. Pokazuje składową stanu tego składnika, opisując, jakie kryteria kondycji są używane do obliczania jego kondycji. Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji i praca z kryteriami kondycji](#health-diagnostics). Aby uzyskać więcej informacji na temat alertów, zobacz [Alerts](#alerts).

Stany kondycji zdefiniowane dla maszyny wirtualnej są opisane w poniższej tabeli:

|Ikona |Stan kondycji |Znaczenie |
|-----|-------------|---------------|
| |W dobrej kondycji |Maszyna wirtualna znajduje się w określonych warunkach kondycji. Ten stan wskazuje, że nie wykryto problemów, a maszyna wirtualna działa normalnie. W przypadku nadrzędnego monitora zbiorczego kondycja jest rzutowana i odzwierciedla najlepszy przypadek lub najgorszy przypadek elementu podrzędnego.|
| |Krytyczny |Stan nie znajduje się w określonym stanie kondycji, co oznacza, że wykryto co najmniej jeden krytyczny problem. Te problemy muszą zostać rozwiązane w celu przywrócenia normalnej funkcjonalności. W przypadku nadrzędnego monitora zbiorczego stan kondycji jest rzutowany i odzwierciedla stan najlepszego lub najgorszego przypadku podrzędnego.|
| |Ostrzeżenie |Stan jest między dwoma progami dla zdefiniowanego warunku kondycji, gdzie jeden wskazuje na stan ostrzegawczy, a drugi wskazuje stan krytyczny (trzy progi stanu kondycji można skonfigurować) lub jeśli problem niekrytyczny może spowodować krytyczne problemy, jeśli nierozpoznane. W przypadku nadrzędnego monitora zbiorczego, jeśli co najmniej jeden element podrzędny jest w stanie ostrzeżenia, obiekt nadrzędny będzie odzwierciedlał stan ostrzegawczy. Jeśli jeden element podrzędny jest w stanie krytycznym i inny element podrzędny w stanie ostrzeżenia, w zestawie nadrzędnym zostanie wyświetlony stan kondycji jako krytyczny.|
| |Nieznane |Nie można obliczyć stanu z kilku powodów. W poniższej sekcji znajdują się dodatkowe szczegółowe informacje i możliwe rozwiązania. |

Nieznany stan kondycji może być spowodowany przez następujące problemy:

- Agent został ponownie skonfigurowany i nie będzie już zgłaszany do obszaru roboczego określonego po włączeniu Azure Monitor dla maszyn wirtualnych. Aby skonfigurować agenta do raportowania w obszarze roboczym, zobacz [Dodawanie lub usuwanie obszaru roboczego](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Maszyna wirtualna została usunięta.
- Obszar roboczy skojarzony z Azure Monitor dla maszyn wirtualnych został usunięty. Możesz odzyskać obszar roboczy, jeśli masz korzyści z pomocy technicznej Premium. Przejdź do [witryny Premier](https://premier.microsoft.com/) i Otwórz żądanie pomocy technicznej.
- Zależności rozwiązania zostały usunięte. Aby ponownie włączyć rozwiązania ServiceMap i InfrastructureInsights w obszarze roboczym Log Analytics, zainstaluj ponownie rozwiązanie ServiceMap przy użyciu [szablonu Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Aby ponownie zainstalować rozwiązanie InfastructureInsights, vminsights@microsoft.compoczty e-mail. 
- Maszyna wirtualna została zamknięta.
- Usługa maszyny wirtualnej platformy Azure jest niedostępna lub trwa wykonywanie konserwacji.
- Osiągnięto [dzienny limit ilości danych lub przechowywania](../platform/manage-cost-storage.md) obszaru roboczego.

Wybierz pozycję **Wyświetl diagnostykę kondycji** , aby otworzyć stronę przedstawiającą wszystkie składniki maszyny wirtualnej, skojarzone kryteria kondycji, zmiany stanu i inne problemy wykryte przez składniki monitorowania powiązane z maszyną wirtualną.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics).

W sekcji **kondycja** tabeli przedstawiono zbiorcze informacje o kondycji składników wydajności monitorowanych przez kryteria kondycji. Te składniki obejmują **procesor CPU**, **pamięć**, **dysk**i **Sieć**. Wybranie składnika powoduje otwarcie strony zawierającej listę wszystkich kryteriów monitorowania i kondycji tego składnika.

Gdy uzyskujesz dostęp do kondycji z maszyny wirtualnej platformy Azure z systemem Windows, stan kondycji pierwszych pięciu głównych usług systemu Windows jest widoczny w obszarze **podstawowa kondycja usług**. Wybranie dowolnej z tych usług spowoduje otwarcie strony zawierającej listę kryteriów kondycji dla tego składnika wraz z jego stanem kondycji.

Wybranie nazwy kryteriów kondycji powoduje otwarcie okienka właściwości. W tym okienku można przejrzeć szczegóły konfiguracji, w tym, czy kryteria kondycji mają odpowiedni alert Azure Monitor.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji i praca z kryteriami kondycji](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Agreguj perspektywę maszyny wirtualnej

Aby wyświetlić zbieranie informacji o kondycji dla wszystkich maszyn wirtualnych w grupie zasobów, wybierz **Azure monitor** z listy nawigacji w portalu, a następnie wybierz pozycję **Virtual Machines (wersja zapoznawcza)** .

![Widok monitorowania usługi VM Insights na podstawie Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Z listy rozwijanej **subskrypcja** i **Grupa zasobów** wybierz odpowiednią grupę zasobów zawierającą maszyny wirtualne powiązane z grupą, aby wyświetlić raportowany stan kondycji. Wybór dotyczy tylko funkcji kondycji i nie jest przenoszone na karty **wydajności** ani **mapy** .

Karta **kondycja** zawiera następujące informacje:

* Liczba maszyn wirtualnych w stanie krytycznym lub złej kondycji, a także ich kondycja lub nie przesłanie danych (nazywanych nieznanym stanem).
* I ile maszyn wirtualnych w systemie operacyjnym System operacyjny zgłasza stan złej kondycji.
* Liczba maszyn wirtualnych w złej kondycji ze względu na problem wykryty z procesorem, dyskiem, pamięcią lub kartą sieciową uporządkowaną według stanu kondycji.
* Liczba maszyn wirtualnych w złej kondycji ze względu na problem wykryty przez podstawową usługę systemu operacyjnego, sklasyfikowany według stanu kondycji.

Na karcie **kondycja** można zidentyfikować problemy krytyczne wykryte przez kryteria kondycji MONITORUJĄCe maszynę wirtualną oraz przeglądać szczegóły alertu i powiązane artykuły merytoryczne. Te artykuły mogą pomóc w diagnozowaniu i rozwiązywaniu problemów. Wybierz dowolną z serwerów, aby otworzyć stronę [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) odfiltrowaną o tej ważności.

Lista **Dystrybucja maszyn wirtualnych według systemu operacyjnego** przedstawia maszyny wirtualne wymienione w systemie Windows Edition lub dystrybucji systemu Linux wraz z ich wersjami. W każdej kategorii systemu operacyjnego maszyny wirtualne są podzielone w dalszej części na podstawie kondycji maszyny wirtualnej.

![Perspektywa dystrybucji maszyn wirtualnych usługi VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Wybierz dowolną kolumnę, w tym **liczbę maszyn wirtualnych**, **krytyczne**, **Ostrzeżenie**, **kondycja**lub **nieznany**. Wyświetl listę filtrowanych wyników na stronie **Virtual Machines** , które pasują do wybranej kolumny.

Na przykład, aby przejrzeć wszystkie maszyny wirtualne z systemem Red Hat Enterprise Linux wersja 7,5, wybierz wartość **Liczba maszyn** wirtualnych dla tego systemu operacyjnego i wyświetli listę maszyn wirtualnych pasujących do tego filtru i ich bieżącego stanu kondycji.

![Przykładowy pakiet maszyn wirtualnych z systemem Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Po kliknięciu pola wyboru **Pokaż kondycję** zostanie zwrócony stan kondycji dla przefiltrowanych wyników w tabeli.  

![Przykładowy stan kondycji maszyn wirtualnych z systemem Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Dla każdego z elementów na liście można kliknąć odpowiedni stan kondycji, aby uruchomić diagnostykę kondycji, która pokazuje, w jaki sposób kondycja jest oceniana dla wybranej maszyny wirtualnej. 

Na stronie **Virtual Machines** , jeśli wybierzesz nazwę maszyny wirtualnej w kolumnie **Nazwa maszyny wirtualnej**, nastąpi przekierowanie do strony **wystąpienie maszyny wirtualnej** . Ta strona zawiera więcej szczegółów dotyczących alertów i kryteriów kondycji, które mają wpływ na wybraną maszynę wirtualną. Zafiltruj Szczegóły stanu kondycji, wybierając ikonę **stanu kondycji** w lewym górnym rogu strony, aby zobaczyć, które składniki są w złej kondycji. Możesz również wyświetlać alerty kondycji maszyny wirtualnej zgłoszone przez składnik złej kondycji uporządkowany według ważności alertu.

W widoku **listy maszyn wirtualnych** wybierz nazwę maszyny wirtualnej, aby otworzyć stronę **kondycji** dla tej maszyny wirtualnej, podobnie jak w przypadku wybrania usługi **Insights (wersja zapoznawcza)** bezpośrednio z maszyny wirtualnej.

![Wgląd w szczegółowe dane maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health.png)

**Virtual Machines (wersja zapoznawcza) na stronie Azure monitor** zawiera zbiorcze informacje o stanie kondycji maszyny wirtualnej i alertów. Ten stan kondycji jest kategoryzowany według wagi, która reprezentuje alerty kondycji maszyny wirtualnej zgłoszone w przypadku zmiany stanu kondycji z zdrowe na złej kondycji na podstawie kryteriów. Wybranie opcji **maszyny wirtualne w warunku krytycznym** spowoduje otwarcie strony z listą co najmniej jednej maszyny wirtualnej w krytycznym stanie kondycji.

Wybranie stanu kondycji dla jednej z maszyn wirtualnych powoduje wyświetlenie widoku **diagnostyki kondycji** maszyny wirtualnej. W tym widoku można określić, które kryteria kondycji odzwierciedlają problem z stanem kondycji. Gdy zostanie otwarta strona **Diagnostyka kondycji** , zostanie wyświetlona wszystkie składniki maszyny wirtualnej i powiązane z nimi kryteria kondycji z bieżącym stanem kondycji.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics).

Wybranie opcji **Wyświetl wszystkie kryteria kondycji** spowoduje otwarcie strony zawierającej listę wszystkich kryteriów kondycji dostępnych w ramach tej funkcji. Informacje mogą być przefiltrowane na podstawie następujących opcji:

* **Typ**. Istnieją trzy typy kryteriów kondycji służące do oceny warunków i zestawiania ogólnej kondycji monitorowanej maszyny wirtualnej:
    - **Jednostka**. Mierzy pewne aspekty maszyny wirtualnej. Ten typ kryteriów kondycji może sprawdzać licznik wydajności w celu określenia wydajności składnika, uruchomienia skryptu do wykonania transakcji syntetycznej lub obserwowania zdarzenia, które wskazuje na błąd. Filtr jest domyślnie ustawiony na wartość Jednostka.
    - **Zależność**. Zawiera zbiorcze informacje o kondycji między różnymi jednostkami. To kryterium kondycji pozwala, aby kondycja jednostki zależała od kondycji innego typu jednostki, na której bazuje pomyślne działanie.
    - **Agreguj**. Zapewnia łączny stan kondycji podobnych kryteriów kondycji. Kryterium kondycji jednostki i zależności jest zwykle konfigurowane w ramach zagregowanego kryterium kondycji. Oprócz zapewnienia lepszej ogólnej organizacji wielu różnych kryteriów kondycji przeznaczonych dla jednostki, zagregowane kryterium kondycji zapewnia unikatowy stan kondycji dla różnych kategorii jednostek.

* **Kategoria**. Typ kryteriów kondycji służących do grupowania podobnych kryteriów do celów raportowania. Te kategorie to **dostępność** i **wydajność**.

Aby zobaczyć, które wystąpienia są w złej kondycji, wybierz wartość w kolumnie **składnik w złej kondycji** . Na tej stronie tabeli wymieniono składniki, które są w krytycznym stanie kondycji.

## <a name="health-diagnostics"></a>Diagnostyka kondycji

Strona **Diagnostyka kondycji** umożliwia wizualizację modelu kondycji maszyny wirtualnej. Ta strona zawiera listę wszystkich składników maszyny wirtualnej, skojarzonych z nimi kryteriów kondycji, zmian stanu i innych istotnych problemów zidentyfikowanych przez monitorowane składniki powiązane z maszyną wirtualną.

![Przykład strony diagnostyki kondycji dla maszyny wirtualnej](./media/vminsights-health/health-diagnostics-page-01.png)

Uruchom diagnostykę kondycji, korzystając z następujących metod:

* Stan kondycji zbiorczej dla wszystkich maszyn wirtualnych z perspektywy zagregowanej maszyny wirtualnej w Azure Monitor:

    1. Na stronie **kondycja** wybierz ikonę dla **krytycznego**, **ostrzegawczego**, **zdrowego**lub **nieznanego** stanu kondycji w sekcji **kondycja maszyny wirtualnej gościa**.
    2. Przejdź do strony, na której znajdują się wszystkie maszyny wirtualne pasujące do tej filtrowanej kategorii.
    3. Wybierz wartość w kolumnie **stan kondycji** , aby otworzyć zakres diagnostyki kondycji dla tej maszyny wirtualnej.

* Według systemu operacyjnego z perspektywy zagregowanej maszyny wirtualnej w Azure Monitor. W obszarze **dystrybucja maszyny wirtualnej**wybranie jednej z wartości kolumny spowoduje otwarcie strony **Virtual Machines** i zwrócenie listy w tabeli odpowiadającej filtrowanej kategorii. Wybranie wartości w kolumnie **stan kondycji** spowoduje otwarcie diagnostyki kondycji dla wybranej maszyny wirtualnej.
 
* Z poziomu maszyny wirtualnej gościa na karcie **kondycja** Azure monitor dla maszyn wirtualnych wybierz pozycję **Wyświetl diagnostykę kondycji**.

Diagnostyka kondycji organizuje informacje o kondycji w dwie kategorie: dostępność i wydajność.
 
Wszystkie kryteria kondycji zdefiniowane dla składnika, takie jak dysk logiczny, procesor i tak dalej, można wyświetlić bez filtrowania dwóch kategorii. Te widoki mogą znajdować się w całym widoku kryteriów lub przez filtrowanie wyników według kategorii po wybraniu opcji **dostępność** lub **wydajność**.

Ponadto kategorię kryteriów można zobaczyć obok kolumny **kryteria kondycji** . Jeśli kryteria nie pasują do wybranej kategorii, komunikat informujący o **braku kryteriów kondycji dostępnych dla wybranej kategorii** zostanie wyświetlony w kolumnie **kryteria kondycji** .

Stan kryteriów kondycji definiuje się przy użyciu jednego z czterech typów: **krytyczny**, **ostrzegawczy**, w **dobrej kondycji**i **nieznany**. Pierwsze trzy można konfigurować, co oznacza, że możesz modyfikować wartości progowe monitorów bezpośrednio w okienku Konfiguracja **kryteriów kondycji** . Jest to możliwe również przy użyciu [operacji monitorowania aktualizacji](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)interfejsu API REST Azure monitor. **Nieznane** nie można skonfigurować i jest zarezerwowane dla konkretnych scenariuszy.

Strona **Diagnostyka kondycji** ma trzy główne sekcje:

* Model składnika
* Kryteria kondycji
* Zmiany stanu

![Sekcje strony diagnostyki kondycji](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Model składnika

Kolumna z lewej strony **diagnostyki kondycji** jest **modelem składników**. Wszystkie składniki, które są skojarzone z maszyną wirtualną, są wyświetlane w tej kolumnie wraz z bieżącym stanem kondycji.

W poniższym przykładzie odnalezione składniki to **dysk**, **dysk logiczny**, **procesor**, **pamięć**i **system operacyjny**. W tej kolumnie są odnajdywane i wyświetlane wiele wystąpień tych składników.

Na przykład na poniższej ilustracji przedstawiono, że maszyna wirtualna ma dwa wystąpienia dysków logicznych, **C:** i **D:** , które są w dobrej kondycji:

![Przykładowy model składników przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kryteria kondycji

Kolumna środkowa na stronie Diagnostyka kondycji zawiera **kryteria kondycji**. Model kondycji zdefiniowany dla maszyny wirtualnej jest wyświetlany w drzewie hierarchicznym. Model kondycji maszyny wirtualnej składa się z kryteriów jednostkowych i zagregowanych.

![Przykładowe kryteria kondycji prezentowane w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kryterium kondycji mierzy kondycję monitorowanego wystąpienia, które może być wartością progową, stanem jednostki itd. Kryterium kondycji ma dwa lub trzy konfigurowalne progi stanu kondycji, zgodnie z wcześniejszym opisem. W dowolnym momencie kryterium kondycji może dotyczyć tylko jednego z jego potencjalnych Stanów.

Model kondycji definiuje kryteria określające kondycję ogólnego celu i składników docelowych. Hierarchia kryteriów jest wyświetlana w sekcji **kryteria kondycji** na stronie **Diagnostyka kondycji** .

Zasady zbiorczego określania kondycji są częścią konfiguracji zagregowanych kryteriów kondycji (wartość domyślna to **najgorszy**). Domyślny zestaw kryteriów kondycji uruchomionych w ramach tej funkcji można znaleźć w sekcji [szczegóły konfiguracji monitorowania](#monitoring-configuration-details) w tym artykule.

Aby uzyskać listę wszystkich kryteriów kondycji, można również użyć [listy wystąpień monitorowania](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) interfejsu API REST Azure monitor przez zasób. Te kryteria zawierają szczegóły konfiguracji uruchomione względem zasobu maszyny wirtualnej platformy Azure.

Można zmienić konfigurację typu kryteriów kondycji **jednostki** , wybierając link wielokropka po prawej stronie. Wybierz pozycję **Pokaż szczegóły** , aby otworzyć okienko Konfiguracja.

![Przykład konfigurowania kryteriów kondycji](./media/vminsights-health/health-diagnostics-vm-example-02.png)

W okienku Konfiguracja dla wybranych kryteriów kondycji w przypadku użycia przykładowego **średniego dysku w sekundach dla zapisu**próg można skonfigurować przy użyciu innej wartości liczbowej. Jest to Monitor dwustanowy, co oznacza, że może ulec zmianie tylko w **dobrej kondycji** na **Ostrzeżenie**.

Inne kryteria kondycji czasami używają trzech stanów, w których można skonfigurować wartość ostrzegawczą i krytyczną dla progów kondycji. Próg można także zmodyfikować przy użyciu [konfiguracji monitora](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)interfejsu API REST Azure monitor.

>[!NOTE]
>Stosowanie zmian konfiguracji kryteriów kondycji do jednego wystąpienia stosuje je do wszystkich monitorowanych wystąpień. Na przykład po wybraniu opcji **dysk-1 D:** , a następnie zmodyfikowaniu **średniego czasu dysku na próg zapisu** , zmiana dotyczy wszystkich wystąpień odnalezionych i monitorowanych na maszynie wirtualnej.


![Konfigurowanie kryteriów kondycji przykładu monitora jednostkowego](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Jeśli chcesz dowiedzieć się więcej na temat kryteriów kondycji, dodaliśmy artykuły merytoryczne ułatwiające identyfikowanie problemów, przyczyn i rozwiązań. Wybierz pozycję **Wyświetl informacje** na stronie, aby wyświetlić powiązany artykuł merytoryczny.

Aby zapoznać się ze wszystkimi artykułami merytorycznymi zawartymi w Azure Monitor dla maszyn wirtualnych kondycji, zobacz [Azure monitor dokumentacji dotyczącej kondycji](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Zmiany stanu

W skrajnej prawej kolumnie strony **Diagnostyka kondycji** są **zmieniane zmiany stanu**. Ta kolumna zawiera wszystkie zmiany stanu skojarzone z kryteriami kondycji wybranymi w sekcji **kryteria kondycji** lub zmianę stanu maszyny wirtualnej w przypadku wybrania maszyny wirtualnej z **modelu składnika** lub kolumny **kryteriów kondycji** tabeli.

![Przykładowe zmiany stanu prezentowane w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-statechanges.png)

W poniższej sekcji przedstawiono stan kryteriów kondycji i skojarzony czas. Te informacje pokazują najnowszy stan w górnej części kolumny.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Skojarzenie modelu składnika, kryteriów kondycji i kolumn zmian stanu

Trzy kolumny są wzajemnie połączone. Po wybraniu wystąpienia w kolumnie **model składnika** kolumna **kryteriów kondycji** jest filtrowana do tego widoku składnika. Odpowiednio, kolumna **zmiany stanu** jest aktualizowana na podstawie wybranych kryteriów kondycji.

![Przykład wybierania monitorowanego wystąpienia i wyników](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Na przykład po wybraniu pozycji *dysk-1 D:* z listy w obszarze **model składnika**, filtry **kryteriów kondycji** na *dysk-1D:* , a **zmiany stanu** przedstawiają zmianę stanu na podstawie dostępności *dysku-1 D:* .

Aby wyświetlić zaktualizowany stan kondycji, można odświeżyć stronę Diagnostyka kondycji, wybierając łącze **Odśwież** . Jeśli istnieje aktualizacja stanu kondycji kryterium kondycji na podstawie wstępnie zdefiniowanego interwału sondowania, to zadanie pozwala uniknąć oczekiwania i odzwierciedla najnowszy stan kondycji. **Stan kryteriów kondycji** to filtr, który umożliwia określanie zakresu wyników w oparciu o wybrany stan kondycji: dobra kondycja, ostrzeżenie, krytyczne, nieznane i wszystkie. Czas **ostatniej aktualizacji** w prawym górnym rogu reprezentuje godzinę ostatniego odświeżenia strony diagnostyki kondycji.

## <a name="alerts"></a>Alerty

Azure Monitor dla maszyn wirtualnych Health integruje się z [alertami platformy Azure](../../azure-monitor/platform/alerts-overview.md). Zgłasza alert po wykryciu wstępnie zdefiniowanych kryteriów, zmienia się ze stanu dobrej kondycji na nieprawidłowy. Alerty są pogrupowane według ważności, od ważność od 0 do ważność 4, z ważność 0 jako najwyższy poziom.

Alerty nie są skojarzone z grupą akcji w celu powiadomienia użytkownika o wyzwoleniu alertu. Użytkownik z rolą właściciela w zakresie subskrypcji musi skonfigurować powiadomienia, wykonując czynności opisane w sekcji [Konfigurowanie alertów](#configure-alerts) .

Całkowita liczba alertów dotyczących kondycji maszyn wirtualnych sklasyfikowanych według ważności jest dostępna na pulpicie nawigacyjnym **kondycji** w sekcji **alerty** . Po wybraniu całkowitej liczby alertów lub liczby odpowiadającej poziomowi ważności zostanie otwarta strona **alerty** z listami wszystkich alertów pasujących do wybranych elementów.

Na przykład, jeśli wybierzesz wiersz odpowiadający **poziomowi ważnośćemu 1**, zobaczysz następujący widok:

![Przykład wszystkich alertów poziomu ważności 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Nie ma zakresu strony **wszystkie alerty** , aby wyświetlić tylko alerty pasujące do wybranej opcji. Jest on również filtrowany według **typu zasobu** , aby pokazać tylko alerty kondycji zgłoszone przez zasób maszyny wirtualnej. Ten format jest odzwierciedlany na liście alertów pod **zasobem docelowym**kolumny, gdzie pokazuje maszynę wirtualną platformy Azure zgłoszony alert w przypadku spełnienia warunku złej kondycji.

Alerty z innych typów zasobów lub usług nie są przeznaczone do uwzględnienia w tym widoku. Te alerty obejmują alerty dziennika, które są oparte na kwerendach dzienników lub alertach metryk, które normalnie są wyświetlane na stronie domyślnej Azure Monitor [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) .

Możesz filtrować ten widok, wybierając wartości z menu rozwijanego w górnej części strony.

|Kolumna |Opis |
|-------|------------|
|Subskrypcja |Wybierz subskrypcję platformy Azure. W widoku są uwzględniane tylko alerty w wybranej subskrypcji. |
|Grupa zasobów |Wybierz pojedynczą grupę zasobów. W widoku są uwzględniane tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
|Typ zasobu |Wybierz co najmniej jeden typ zasobu. Domyślnie wybrane są tylko alerty docelowych **maszyn wirtualnych** i są one uwzględnione w tym widoku. Ta kolumna jest dostępna tylko po określeniu grupy zasobów. |
|Zasób |Wybierz zasób. W widoku są uwzględniane tylko alerty z tym zasobem. Ta kolumna jest dostępna tylko po określeniu typu zasobu. |
|Ważność |Wybierz ważność alertu lub wybierz pozycję **wszystkie** , aby uwzględnić alerty wszystkich serwerów. |
|Warunek monitorowania |Wybierz warunek monitora, aby odfiltrować alerty, jeśli zostały one wyzwolone lub rozwiązane przez system, jeśli warunek nie jest już aktywny. Lub zaznacz **wszystko** , aby uwzględnić alerty wszystkich warunków. |
|Stan alertu |Wybierz stan alertu, **Nowy**, **potwierdzenie**, **Zamknięcie**lub **wszystkie** , aby uwzględnić alerty wszystkich stanów. |
|Monitorowanie usługi |Wybierz usługę lub wybierz pozycję **wszystkie** , aby uwzględnić wszystkie usługi. Ta funkcja obsługuje tylko alerty z usługi VM Insights.|
|Przedział czasu| W widoku są uwzględniane tylko alerty wywoływane w wybranym przedziale czasu. Obsługiwane wartości to Ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Po wybraniu alertu zostanie wyświetlona strona **szczegóły alertu** . Ta strona zawiera szczegółowe informacje dotyczące alertu i pozwala zmienić jego stan.

Aby dowiedzieć się więcej na temat zarządzania alertami, zobacz [Tworzenie i wyświetlanie alertów oraz zarządzanie nimi przy użyciu Azure monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Tworzenie nowych alertów na podstawie kryteriów kondycji lub modyfikowanie istniejących reguł alertów dotyczących kondycji w Azure Monitor z portalu nie jest obecnie obsługiwane.


![Okienko szczegółów alertu dotyczące wybranego alertu](./media/vminsights-health/alert-details-pane-01.png)

Można zmienić stan alertu dla jednego lub wielu alertów, zaznaczając je, a następnie wybierając pozycję **Zmień stan** ze strony **wszystkie alerty** w lewym górnym rogu. Wybierz jeden z stanów w okienku **Zmień stan alertów** , Dodaj opis zmiany w polu **komentarz** , a następnie wybierz **przycisk OK** , aby zatwierdzić zmiany. Gdy informacje są weryfikowane i stosowane są zmiany, śledź postęp w obszarze **powiadomienia** w menu.

### <a name="configure-alerts"></a>Konfigurowanie alertów
Z Azure Portal nie można zarządzać określonymi zadaniami zarządzania alertami. Te zadania muszą być wykonywane przy użyciu [interfejsu API REST Azure monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Są to:

- Włączanie lub wyłączanie alertu dla kryteriów kondycji
- Konfigurowanie powiadomień dla alertów kryteriów kondycji

Każdy przykład używa [ARMClient](https://github.com/projectkudu/armclient) na komputerze z systemem Windows. Jeśli nie znasz tej metody, zobacz [using ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Włączanie lub wyłączanie reguły alertu

Aby włączyć lub wyłączyć alert dla określonych kryteriów kondycji, właściwość **alertGeneration** musi być modyfikowana przy użyciu wartości **wyłączone** lub **włączone**.

Aby zidentyfikować *elementu monitorid* dla określonych kryteriów kondycji, w poniższym przykładzie pokazano, jak wykonać zapytanie o tę wartość dla kryteriów **LogicalDisk\Avg dysku s na transfer**:

1. W oknie terminalu wpisz **login armclient. exe**. W ten sposób zostanie wyświetlony komunikat z prośbą o zalogowanie się do platformy Azure.

2. Wprowadź następujące polecenie, aby pobrać wszystkie aktywne kryterium kondycji dla określonej maszyny wirtualnej i zidentyfikuj wartość właściwości *elementu monitorid* :

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    Poniższy przykład przedstawia dane wyjściowe polecenia *Get armclient* . Zwróć uwagę na wartość *elementu monitorid*. Ta wartość jest wymagana w następnym kroku, w którym należy określić identyfikator kryteriów kondycji i zmodyfikować jej właściwość w celu utworzenia alertu.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Wprowadź następujące polecenie, aby zmodyfikować właściwość *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Wprowadź polecenie GET użyte w kroku 2, aby sprawdzić, czy wartość właściwości jest ustawiona na **wyłączone**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Skojarz grupę akcji z kryteriami kondycji

Azure Monitor dla maszyn wirtualnych Health obsługuje powiadomienia SMS i wiadomości e-mail, gdy alerty są generowane na podstawie kryteriów kondycji w złej kondycji. Aby skonfigurować powiadomienia, zanotuj nazwę skonfigurowanej grupy akcji do wysyłania wiadomości SMS lub powiadomień e-mail.

>[!NOTE]
>Ta akcja musi zostać wykonana względem każdej monitorowanej maszyny wirtualnej, dla której ma zostać odebrane powiadomienie. Nie dotyczy to wszystkich maszyn wirtualnych w grupie zasobów.

1. W oknie terminalu wprowadź *nazwę logowania armclient. exe*. W ten sposób zostanie wyświetlony komunikat z prośbą o zalogowanie się do platformy Azure.

2. Wprowadź następujące polecenie, aby skojarzyć grupę akcji z regułami alertów:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Aby sprawdzić, czy wartość właściwości **actionGroupResourceIds** została pomyślnie zaktualizowana, wprowadź następujące polecenie:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Dane wyjściowe powinny wyglądać podobnie do następujących kryteriów:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Następne kroki

- Aby zidentyfikować ograniczenia i ogólną wydajność maszyn wirtualnych, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).

- Aby dowiedzieć się więcej o wykrytych zależnościach aplikacji, zobacz [View Azure monitor dla maszyn wirtualnych map](vminsights-maps.md).
