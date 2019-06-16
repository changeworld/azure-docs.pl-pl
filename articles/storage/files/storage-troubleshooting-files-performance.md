---
title: Usługa Azure wydajności pliki przewodnik rozwiązywania problemów
description: Znane problemy z wydajnością za pomocą udziałów plików platformy Azure — wersja premium (wersja zapoznawcza) i skojarzone obejścia.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190057"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Rozwiązywanie problemów z wydajnością usługi Azure Files

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure — wersja premium (wersja zapoznawcza). Zapewnia możliwe przyczyny i potencjalne rozwiązania po napotkaniu te problemy.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Ogólne problemy z wydajnością, duże opóźnienie i niska przepływność

### <a name="cause-1-share-experiencing-throttling"></a>Przyczyny 1: Udostępnianie obciążony ograniczania przepustowości

Domyślny limit przydziału w udziale jest GiB 100, która zapewnia plan bazowy bezpieczeństwa 100 operacji We/Wy (z możliwości przejścia do 300 na godzinę). Aby uzyskać więcej informacji o aprowizacji i jej relacji z operacji We/Wy, zobacz [akcji aprowizacji](storage-files-planning.md#provisioned-shares) części przewodnika planowania.

Aby upewnić się, jeśli jest ograniczane swój udział w portalu można wykorzystać metryk usługi Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** a następnie wyszukaj **metryki**.

1. Wybierz pozycję **Metryki**.

1. Wybierz konto magazynu, co zasób.

1. Wybierz **pliku** jako metryki przestrzeni nazw.

1. Wybierz **transakcji** jako metrykę.

1. Dodaj filtr dla **ResponseType** i sprawdź, czy wszystkie żądania mają kod odpowiedzi **SuccessWithThrottling**.

![Opcje metryki dla udziałów plików w warstwie premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Rozwiązanie

- Zwiększ udostępnianie zaprowizowaną pojemnością, określając wyższy limit przydziału w udziale.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Przyczyny 2: Duże obciążenie metadanych/obszaru nazw

Jeśli większość żądań jest metadanych, które zawiera, (na przykład createfile/openfile/closefile/informacji kwerendy queryinfo/querydirectory), a następnie opóźnienie będzie niższa, w porównaniu do operacji odczytu/zapisu.

Aby upewnić się, jeśli większość żądań metadanych na interfejsach, można użyć te same kroki jak powyżej. Z wyjątkiem zamiast opcji dodawania filtru dla **ResponseType**, Dodaj filtr dla **Nazwa interfejsu API**.

![Filtr nazwy interfejsu API w swoje metryki](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Obejście

- Sprawdź, w przypadku aplikacji można modyfikować, aby zmniejszyć liczbę operacji na metadanych.

### <a name="cause-3-single-threaded-application"></a>Przyczyny 3: Aplikacja jednowątkowa

Jeśli używanej przez klienta aplikacji jest jednowątkowym, może to spowodować znacznie niższe operacje We/Wy/przepływności niż możliwe oparty na rozmiarze aprowizowanego udziału.

### <a name="solution"></a>Rozwiązanie

- Zwiększyć równoległość aplikacji przez odpowiednie zwiększenie liczby wątków.
- Przełącz się do aplikacji, w którym możliwe jest równoległości. Na przykład dla operacji kopiowania klientów można użyć narzędzia AzCopy lub RoboCopy z klientami Windows lub **równoległe** polecenia na komputerach klienckich z systemem Linux.

## <a name="very-high-latency-for-requests"></a>Bardzo duże opóźnienie dla żądań

### <a name="cause"></a>Przyczyna

Maszyny Wirtualnej klienta może znajdować się w regionie innym niż premium udziału plików.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyną Wirtualną, która znajduje się w tym samym regionie, co udziału plików — wersja premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Nie można osiągnąć maksymalną przepływność obsługiwane przez sieć klienta

Brak jest jednej potencjalną przyczyną tego fo SMB Obsługa wielu kanałów pomocy technicznej. Obecnie udziałów plików platformy Azure obsługują tylko pojedynczy kanał, więc ma tylko jedno połączenie z maszyny Wirtualnej klienta do serwera. To pojedyncze połączenie jest ustalana na podstawie pojedynczego rdzenia na kliencie maszyny Wirtualnej, więc maksymalna przepływność osiągalna z maszyny Wirtualnej jest ograniczone przez pojedynczego rdzenia.

### <a name="workaround"></a>Obejście

- Uzyskiwanie maszyny Wirtualnej za pomocą większe core może pomóc zwiększyć przepływność.
- Uruchomienie aplikacji klienckiej z wielu maszyn wirtualnych spowoduje zwiększenie przepływności.
- Jeśli jest to możliwe, należy użyć interfejsów API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Przepływność na klientach z systemem Linux jest znacznie mniejsza w porównaniu z klientami Windows.

### <a name="cause"></a>Przyczyna

Jest to znany problem z implementacji klienta SMB w systemie Linux.

### <a name="workaround"></a>Obejście

- Rozłożenie obciążenia na wiele maszyn wirtualnych
- Na tej samej maszyny Wirtualnej, użyj wielu punktów instalacji, za pomocą **nosharesock** opcji i rozpowszechniania obciążenie między te punkty instalacji.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Dużemu metadanych dużych obciążeń obejmujących rozległych operacji otwarte i zamknięte.

### <a name="cause"></a>Przyczyna

Brak obsługi dzierżawy katalogów.

### <a name="workaround"></a>Obejście

- Jeśli to możliwe Unikaj nadmiernego uchwyt otwierającego/zamykającego na tym samym katalogu w krótkim okresie czasu.
- W przypadku maszyn wirtualnych systemu Linux należy zwiększyć limit czasu pamięci podręcznej wpisu katalogu, określając **actimeo =<sec>**  jako opcji instalacji. Domyślnie jest 1 sekundę, więc większej wartości takie jak trzy lub pięć może pomóc.
- W przypadku maszyn wirtualnych systemu Linux uaktualniania jądra 4.20 lub nowszej.

## <a name="low-iops-on-centosrhel"></a>Niski operacje We/Wy na CentOS/RHEL

### <a name="cause"></a>Przyczyna

Głębokość we/wy jest większa niż jeden nie jest obsługiwana w systemie CentOS/RHEL.

### <a name="workaround"></a>Obejście

- Uaktualnianie do CentOS 8 / RHEL 8.
- Zmień na Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Niestabilny/saw-tooth wzorzec operacji We/Wy

### <a name="cause"></a>Przyczyna

Aplikacja kliencka systematycznie przekracza linii bazowej operacje We/Wy. Obecnie nie ma żadnych po stronie usługi wygładzanie obciążenia żądaniami, więc jeśli klient przekroczy linii bazowej operacje We/Wy, zostanie ograniczona przez usługę. Czy ograniczania przepustowości może spowodować klienta występują niestabilny/saw-tooth wzorzec operacji We/Wy. W tym przypadku średnie operacje We/Wy osiągnięte przez klienta może być niższa niż linia bazowa operacji We/Wy.

### <a name="workaround"></a>Obejście

- Tak, aby nie ograniczeni udziału, zmniejszenie obciążenia żądania od aplikacji klienckiej.
- Tak, aby nie ograniczeni udziału, należy zwiększyć limit przydziału udziału.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadmierne wywoływanie DirectoryOpen/DirectoryClose

### <a name="cause"></a>Przyczyna

Jeśli liczba wywołań DirectoryOpen/DirectoryClose należy najważniejszych wywołań interfejsów API, a nie będziesz już klienta znaczenie, że wiele wywołań, być może wystąpił problem z oprogramowanie antywirusowe zainstalowane na kliencie maszyny Wirtualnej platformy Azure.

### <a name="workaround"></a>Obejście

- Poprawkę rozwiązującą ten problem jest dostępna w [kwietnia aktualizacji platformy dla Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Tworzenie plików jest mniejsza niż oczekiwana

### <a name="cause"></a>Przyczyna

Obciążenia, które opierają się na tworzeniu dużą liczbę plików nie będą widzieć istotne różnice między wydajności premium udziałów plików i udziały plików standardowych.

### <a name="workaround"></a>Obejście

- Brak.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niska wydajność z Windows 8.1 lub Server 2012 R2

### <a name="cause"></a>Przyczyna

Wyższy niż oczekiwany czas oczekiwania na dostęp do usługi Azure Files dla obciążeń intensywnie korzystających z operacji We/Wy.

### <a name="workaround"></a>Obejście

- Instalowanie dostępnych [poprawkę](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).