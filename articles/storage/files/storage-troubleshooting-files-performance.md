---
title: Przewodnik po rozwiązywaniu problemów z wydajnością usługi Azure Files
description: Znane problemy z wydajnością z udziałami plików platformy Azure i skojarzonymi z nimi obejściemi.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598089"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Rozwiązywanie problemów z wydajnością usługi Azure Files

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure. Zapewnia potencjalne przyczyny i obejścia, gdy te problemy występują.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Duże opóźnienia, niska przepustowość i ogólne problemy z wydajnością

### <a name="cause-1-share-experiencing-throttling"></a>Przyczyna 1: Udostępnianie występuje dławienie

Domyślny przydział na udział premium wynosi 100 GiB, który zapewnia 100 bazowych we/wy (z możliwością rozerwania do 300 na godzinę). Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej i jego relacji z usługami We/Wy, zobacz sekcję [Aprowizowana akcja](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) przewodnika planowania.

Aby potwierdzić, czy twój udział jest ograniczany, możesz korzystać z metryk platformy Azure w portalu.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz **pozycję Wszystkie usługi,** a następnie wyszukaj **dane**.

1. Wybierz pozycję **Metryki**.

1. Wybierz konto magazynu jako zasób.

1. Wybierz **pozycję Plik** jako obszar nazw metryki.

1. Wybierz **transakcje** jako metrykę.

1. Dodaj filtr **dla ResponseType** i sprawdź, czy wszystkie żądania mają kod odpowiedzi **SuccessWithThrottling** (dla SMB) lub **ClientThrottlingError** (dla REST).

![Opcje metryki dla udziałów plików premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Aby otrzymać alert, jeśli udział plików jest ograniczony, zobacz [Jak utworzyć alert, jeśli udział plików jest ograniczony](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Rozwiązanie

- Zwiększ pojemność aprowizowana udziału, określając wyższy przydział na udział.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Przyczyna 2: Duże obciążenie metadanych/przestrzeni nazw

Jeśli większość żądań są zorientowane na metadane (takie jak createfile/openfile/closefile/queryinfo/querydirectory), opóźnienie będzie gorsze w porównaniu do operacji odczytu/zapisu.

Aby potwierdzić, czy większość żądań jest zorientowana na metadane, możesz wykonać te same kroki, co powyżej. Z wyjątkiem zamiast dodawania filtru **dla ResponseType**, dodaj filtr **dla nazwy interfejsu API**.

![Filtrowanie nazwy interfejsu API w metrykach](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Obejście

- Sprawdź, czy aplikacja może zostać zmodyfikowana, aby zmniejszyć liczbę operacji metadanych.
- Dodaj dysk VHD w udziale plików i zainstaluj VHD przez SMB z klienta, aby wykonać operacje plików względem danych. Takie podejście działa dla pojedynczego modułu zapisującego i wielu scenariuszy czytników i umożliwia operacje metadanych, aby być lokalne, oferując wydajność podobną do lokalnego magazynu podłączonego bezpośrednio.

### <a name="cause-3-single-threaded-application"></a>Przyczyna 3: Aplikacja jednowątkowa

Jeśli aplikacja używana przez klienta jest jednowątkowy, może to spowodować znacznie niższe IOPS/przepływność niż maksymalna możliwa na podstawie aprowizowanego rozmiaru udziału.

### <a name="solution"></a>Rozwiązanie

- Zwiększ równoległość aplikacji, zwiększając liczbę wątków.
- Przełącz się do aplikacji, w których równoległość jest możliwa. Na przykład w przypadku operacji kopiowania klienci mogą używać AzCopy lub RoboCopy z klientów systemu Windows lub polecenia **równoległego** na klientach systemu Linux.

## <a name="very-high-latency-for-requests"></a>Bardzo duże opóźnienie dla żądań

### <a name="cause"></a>Przyczyna

Maszyna wirtualna klienta może znajdować się w innym regionie niż udział plików.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyny Wirtualnej, która znajduje się w tym samym regionie co udział plików.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nie może osiągnąć maksymalnej przepustowości obsługiwanej przez sieć

Jedną z potencjalnych przyczyn tego jest brak fo SMB wielokanałowej obsługi. Obecnie udziały plików platformy Azure obsługują tylko jeden kanał, więc istnieje tylko jedno połączenie z maszyny Wirtualnej klienta do serwera. To pojedyncze połączenie jest powiązane z jednym rdzeniem na maszynie Wirtualnej klienta, więc maksymalna przepływność osiągalna z maszyny Wirtualnej jest powiązana z jednym rdzeniem.

### <a name="workaround"></a>Obejście

- Uzyskanie maszyny Wirtualnej z większym rdzeniem może pomóc zwiększyć przepływność.
- Uruchamianie aplikacji klienckiej z wielu maszyn wirtualnych zwiększy przepływność.

- W miarę możliwości należy używać interfejsów API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Przepływność na klientach systemu Linux jest znacznie niższa w porównaniu do klientów systemu Windows.

### <a name="cause"></a>Przyczyna

Jest to znany problem z implementacją klienta SMB w systemie Linux.

### <a name="workaround"></a>Obejście

- Rozłóż obciążenie na wielu maszynach wirtualnych.
- Na tej samej maszynie wirtualnej użyj wielu punktów instalacji z opcją **nosharesock** i rozłóż obciążenie na tych punktach instalacji.
- W systemie Linux spróbuj zdedużyć opcję **nostrictsync,** aby uniknąć wymuszania opróżniania SMB przy każdym wywołaniu **fsync.** W przypadku usług Azure Files ta opcja nie koliduje z spójnością danych, ale może spowodować przestarzałe metadane pliku na liście katalogów ( polecenie**ls -l).** Bezpośrednie zapytanie metadanych pliku (polecenie**stat)** zwróci najbardziej aktualne metadane pliku.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Duże opóźnienia dla dużych obciążeń metadanych obejmujących rozległe operacje otwierania/zamykania.

### <a name="cause"></a>Przyczyna

Brak obsługi dzierżaw katalogowych.

### <a name="workaround"></a>Obejście

- Jeśli to możliwe, należy unikać nadmiernego otwierania/zamykania uchwytu w tym samym katalogu w krótkim czasie.
- W przypadku maszyn wirtualnych z systemem Linux należy zwiększyć limit czasu pamięci podręcznej wpisu katalogu, określając **actimeo=\<sec>** jako opcję instalacji. Domyślnie jest to jedna sekunda, więc większa wartość, taka jak trzy lub pięć, może pomóc.
- W przypadku maszyn wirtualnych z systemem Linux uaktualnij jądro do wersji 4.20 lub nowszej.

## <a name="low-iops-on-centosrhel"></a>Niskie IOPS na CentOS/ RHEL

### <a name="cause"></a>Przyczyna

Głębokość we/wy większa niż jedna nie jest obsługiwana w centos/rhel.

### <a name="workaround"></a>Obejście

- Uaktualnienie do CentOS 8 / RHEL 8.
- Zmień na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Powolne kopiowanie plików do i z usługi Azure Files w systemie Linux

Jeśli występują powolne kopiowanie plików do i z usługi Azure Files, zapoznaj się [z powolne kopiowanie plików do i z usługi Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) sekcji w przewodniku rozwiązywania problemów z systemem Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Jittery/saw-tooth wzór dla IOPS

### <a name="cause"></a>Przyczyna

Aplikacja kliencka konsekwentnie przekracza bazowe usługi We/Wy. Obecnie nie ma żadnych po stronie usługi wygładzanie obciążenia żądania, więc jeśli klient przekracza bazowych we/wy, zostanie on ograniczony przez usługę. To ograniczanie może spowodować, że klient doświadcza jittery/saw-tooth we/wy wzorzec we/wy. W takim przypadku średnia liczba we/wy osiągnięta przez klienta może być niższa niż bazowy we/wy.

### <a name="workaround"></a>Obejście

- Zmniejsz obciążenie żądania z aplikacji klienckiej, tak aby udział nie został ograniczony.
- Zwiększ przydział udziału, aby udział nie został ograniczony.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadmierne directoryOpen/DirectoryZmieńnikuj

### <a name="cause"></a>Przyczyna

Jeśli liczba wywołań DirectoryOpen/DirectoryClose jest jednym z głównych wywołań interfejsu API i nie oczekujesz, że klient będzie wykonywać wiele wywołań, może to być problem z programem antywirusowym zainstalowanym na maszynie wirtualnej klienta platformy Azure.

### <a name="workaround"></a>Obejście

- Poprawka dotycząca tego problemu jest dostępna w [kwietniowej aktualizacji platformy dla systemu Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Tworzenie pliku jest wolniejsze niż oczekiwano

### <a name="cause"></a>Przyczyna

Obciążenia, które opierają się na tworzeniu dużej liczby plików nie zobaczą istotnej różnicy między wydajnością udziałów plików w wersji premium a udziałami plików standardowych.

### <a name="workaround"></a>Obejście

- Brak.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niska wydajność systemu Windows 8.1 lub Server 2012 R2

### <a name="cause"></a>Przyczyna

Wyższe niż oczekiwano opóźnienia dostępu do plików platformy Azure dla obciążeń intensywnie korzystających z we/wy.

### <a name="workaround"></a>Obejście

- Zainstaluj dostępną [poprawkę](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczony

1. W [portalu Azure](https://portal.azure.com)kliknij **monitor**. 

2. Kliknij **pozycję Alerty,** a następnie kliknij pozycję **+ Nowa reguła alertu**.

3. Kliknij **przycisk Wybierz,** aby wybrać **zasób konta/pliku magazynu** zawierający udział plików, na którym chcesz ostrzegać, a następnie kliknij przycisk **Gotowe**. Jeśli na przykład nazwa konta magazynu to contoso, wybierz zasób contoso/plik.

4. Kliknij **przycisk Dodaj,** aby dodać warunek.

5. Zostanie wyświetlona lista sygnałów obsługiwanych dla konta magazynu, wybierz metrykę **Transakcje.**

6. W bloku **Konfiguruj logikę sygnału** przejdź do wymiaru **typu Odpowiedź,** kliknij listę rozwijaną **Wartości wymiaru** i wybierz pozycję **SuccessWithThrottling** (dla SMB) lub **ClientThrottlingError** (dla REST). 

  > [!NOTE]
  > Jeśli SuccessWithThrottling lub ClientThrottlingError wartość wymiaru nie jest wymieniony, oznacza to, że zasób nie został ograniczony.  Aby dodać wartość wymiaru, **+** kliknij obok listy rozwijanej **Wartości wymiaru,** wpisz **SuccessWithThrottling** lub **ClientThrottlingError**, kliknij przycisk **OK,** a następnie powtórz krok #6.

7. Przejdź do wymiaru **Udział plików,** kliknij pozycję rozwijaną **Wartości wymiaru** i wybierz udziały plików, na których chcesz otrzymywać alerty. 

  > [!NOTE]
  > Jeśli udział plików jest standardowym udziałem plików, lista rozwijana wartości wymiarów będzie pusta, ponieważ metryki na jedną akcję nie są dostępne dla standardowych udziałów plików. Alerty ograniczania standardowych udziałów plików zostaną wyzwolone, jeśli dowolny udział plików na koncie magazynu zostanie ograniczony, a alert nie określi, który udział plików został ograniczony. Ponieważ metryki na jedną akcję nie są dostępne dla standardowych udziałów plików, zaleca się, aby mieć jeden udział plików na konto magazynu. 

8. Zdefiniuj **parametry alertu** (próg, operator, szczegółowość agregacji i częstotliwość), które są używane do oceny reguły alertu metryki, a następnie kliknij przycisk **Gotowe**.

  > [!TIP]
  > Jeśli używasz progu statycznego, wykres metryki może pomóc określić rozsądny próg, jeśli udział plików jest obecnie ograniczany. Jeśli używasz progu dynamicznego, wykres metryczny wyświetli obliczone progi na podstawie ostatnich danych.

9. Dodaj **grupę akcji** (e-mail, SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

10. Wypełnij **szczegóły alertu,** takie jak **nazwa reguły alertu,** **Opis** i **ważność**.

11. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.

Aby dowiedzieć się więcej o konfigurowaniu alertów w usłudze Azure Monitor, zobacz [Omówienie alertów na platformie Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
