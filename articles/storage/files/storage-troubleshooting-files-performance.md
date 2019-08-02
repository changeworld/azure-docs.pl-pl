---
title: Przewodnik rozwiązywania problemów dotyczących wydajności Azure Files
description: Znane problemy z wydajnością związane z udziałami plików platformy Azure i związanymi z nimi obejściami.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6b28d004ceacda3cec13e96ceae84d5d1ff1a2e5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699168"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Rozwiązywanie problemów z wydajnością Azure Files

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure. Zapewnia potencjalne przyczyny i obejścia w przypadku napotkania tych problemów.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Duże opóźnienia, niska przepływność i ogólne problemy z wydajnością

### <a name="cause-1-share-experiencing-throttling"></a>Przyczyna 1: Udostępnianie z ograniczeniami

Domyślny limit przydziału dla udziału w warstwie Premium to 100 GiB, który 100 zapewnia liczbę operacji wejścia/wyjścia na sekundę (z możliwością przekroczenia do 300 przez godzinę). Aby uzyskać więcej informacji na temat udostępniania i powiązania z wieloma operacjami [](storage-files-planning.md#provisioned-shares) we/wy, zobacz sekcję udostępniane udziały w przewodniku planowania.

Aby potwierdzić, że Twój udział jest ograniczany, możesz skorzystać z metryk platformy Azure w portalu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** , a następnie wyszukaj **metryki**.

1. Wybierz pozycję **Metryki**.

1. Wybierz swoje konto magazynu jako zasób.

1. Wybierz pozycję **plik** jako przestrzeń nazw metryki.

1. Wybierz **transakcje** jako metrykę.

1. Dodaj filtr dla elementu **responsetype** i sprawdź, czy jakieś żądania mają kod odpowiedzi **SuccessWithThrottling** (dla protokołu SMB) lub **ClientThrottlingError** (dla REST).

![Opcje metryk dla udziałów plików Premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Rozwiązanie

- Zwiększ pojemność udostępniania udziałów, określając wyższy limit przydziału w udziale.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Przyczyna 2: Duże obciążenie metadanych/przestrzeni nazw

Jeśli większość żądań jest skoncentrowana na metadanych, (na przykład onfile/OpenFile/CloseFile/QueryInfo/querydirectory), opóźnienie będzie gorszyć w porównaniu do operacji odczytu i zapisu.

Aby sprawdzić, czy większość żądań jest skoncentrowana na metadanych, można użyć tych samych kroków jak powyżej. Oprócz dodawania filtru dla elementu responsetypeDodaj filtr dla **nazwy interfejsu API**.

![Filtrowanie nazw interfejsów API w metrykach](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Obejście

- Sprawdź, czy aplikacja może zostać zmodyfikowana, aby zmniejszyć liczbę operacji na metadanych.

### <a name="cause-3-single-threaded-application"></a>Przyczyna 3: Aplikacja jednowątkowa

Jeśli aplikacja używana przez klienta jest jednowątkowa, może to znacząco obniżyć liczbę operacji we/wy na sekundę na podstawie rozmiaru udostępnionego udziału.

### <a name="solution"></a>Rozwiązanie

- Zwiększenie równoległości aplikacji przez zwiększenie liczby wątków.
- Przejdź do aplikacji, w których możliwa jest równoległość. Na przykład w przypadku operacji kopiowania klienci mogą korzystać z AzCopy lub RoboCopy z klientów systemu Windows lub z polecenia **Parallel** na klientach z systemem Linux.

## <a name="very-high-latency-for-requests"></a>Bardzo duże opóźnienie dla żądań

### <a name="cause"></a>Przyczyna

Maszyna wirtualna klienta może znajdować się w innym regionie niż udział plików.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyny wirtualnej znajdującej się w tym samym regionie co udział plików.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nie może osiągnąć maksymalnej przepływności obsługiwanej przez sieć

Jedną z potencjalnych przyczyn tego problemu jest brak obsługi wielu kanałów SMB. Obecnie udziały plików platformy Azure obsługują tylko jeden kanał, więc istnieje tylko jedno połączenie z maszyny wirtualnej klienta do serwera. To pojedyncze połączenie jest oznaczane pojedynczym rdzeniem na maszynie wirtualnej klienta, dzięki czemu maksymalna przepływność osiągalna z maszyny wirtualnej jest powiązana z jednym rdzeniem.

### <a name="workaround"></a>Obejście

- Uzyskanie maszyny wirtualnej z większym rdzeniem może pomóc w zwiększeniu przepływności.
- Uruchomienie aplikacji klienckiej z wielu maszyn wirtualnych spowoduje zwiększenie przepływności.
- Jeśli to możliwe, Użyj interfejsów API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Przepływność na klientach z systemem Linux jest znacznie mniejsza w porównaniu z klientami systemu Windows.

### <a name="cause"></a>Przyczyna

Jest to znany problem z implementacją klienta SMB w systemie Linux.

### <a name="workaround"></a>Obejście

- Rozłożenie obciążenia na wiele maszyn wirtualnych
- Na tej samej maszynie wirtualnej Użyj wielu punktów instalacji z opcją **nosharesock** i rozłożyć obciążenie na te punkty instalacji.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Duże opóźnienia w przypadku dużych obciążeń metadanych obejmujących liczne operacje otwierania/zamykania.

### <a name="cause"></a>Przyczyna

Brak obsługi dzierżaw katalogów.

### <a name="workaround"></a>Obejście

- Jeśli to możliwe, unikaj nadmiernego dojścia otwierającego/zamykającego w tym samym katalogu w krótkim czasie.
- W przypadku maszyn wirtualnych z systemem Linux Zwiększ limit czasu pamięci podręcznej wpisów w katalogu, określając **actimeo =\<sek >** jako opcję instalacji. Domyślnie jest to jedna sekunda, więc większa wartość, taka jak trzy lub pięć, może pomóc.
- W przypadku maszyn wirtualnych z systemem Linux Uaktualnij jądro do wersji 4,20 lub nowszej.

## <a name="low-iops-on-centosrhel"></a>Niska liczba operacji we/wy na sekundę w CentOS/RHEL

### <a name="cause"></a>Przyczyna

Głębokość we/wy większa niż 1 nie jest obsługiwana w przypadku CentOS/RHEL.

### <a name="workaround"></a>Obejście

- Uaktualnij do wersji CentOS 8/RHEL 8.
- Zmień na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Kopiowanie plików do i z Azure Files w systemie Linux

Jeśli występują wolne kopiowanie plików do i z Azure Files, zapoznaj się z sekcją " [wolne kopiowanie plików do i z Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) " w przewodniku rozwiązywania problemów z systemem Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Wzorzec "wahania/Piła-Tooth" dla operacji we/wy na sekundę

### <a name="cause"></a>Przyczyna

Aplikacja kliencka stale przekracza liczbę operacji wejścia/wyjścia na sekundę. Obecnie nie ma możliwości obciążania żądania po stronie usługi, więc jeśli klient przekroczy liczbę operacji wejścia/wyjścia na sekundę, zostanie on ograniczony przez usługę. Ograniczanie wydajności może spowodować, że klient napotyka wzorzec IOPS "wahania/Piła-Tooth". W takim przypadku średnia liczba operacji we/wy osiągniętych przez klienta może być mniejsza niż liczba operacji we/wy na sekundę.

### <a name="workaround"></a>Obejście

- Zmniejsz obciążenie żądaniami z aplikacji klienckiej, aby udział nie został ograniczony.
- Zwiększ limit przydziału udziału, aby udział nie został ograniczony.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadmierne wywołania DirectoryOpen/DirectoryClose

### <a name="cause"></a>Przyczyna

Jeśli liczba wywołań DirectoryOpen/DirectoryClose jest między najpopularniejszymi wywołaniami interfejsu API i nie oczekuje się, że klient nie będzie mógł nawiązywać wielu wywołań, może to być problem z oprogramowaniem antywirusowym zainstalowanym na maszynie wirtualnej klienta platformy Azure.

### <a name="workaround"></a>Obejście

- Rozwiązanie tego problemu jest dostępne w [aktualizacji platformy w kwietniu dla systemu Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Tworzenie pliku jest wolniejsze niż oczekiwano

### <a name="cause"></a>Przyczyna

Obciążenia, które opierają się na tworzeniu dużej liczby plików, nie będą widzieć znaczącej różnicy między wydajnością udziałów plików w warstwie Premium a standardowymi udziałami plików.

### <a name="workaround"></a>Obejście

- Brak.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niska wydajność z Windows 8.1 lub serwera 2012 R2

### <a name="cause"></a>Przyczyna

Większe niż oczekiwane opóźnienie dostępu Azure Files do obciążeń intensywnie korzystających z operacji we/wy.

### <a name="workaround"></a>Obejście

- Zainstaluj dostępną [poprawkę](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).
