---
title: Warstwy cenowe dla usługi Azure Database for MySQL
description: W tym artykule opisano warstw cenowych dla usługi Azure Database for MySQL.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 20fb352e65a570063d9a0f55667db073f8a4ee27
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062414"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Usługa Azure Database for MySQL warstw cenowych

Serwer Azure Database for MySQL można utworzyć w jednym z trzech różnych warstw cenowych: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości zasobów obliczeniowych w rdzeni wirtualnych, które mogą być udostępniane, ilość pamięci na rdzeń wirtualny i technologia magazynu używany do przechowywania danych. Wszystkie zasoby są aprowizowane na poziomie serwera MySQL. Serwer może zawierać jeden lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generacja obliczeń | Gen 4, 5. generacji | Gen 4, 5. generacji | 5\. generacja |
| rdzenie wirtualne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Ilość pamięci na rdzeń wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 4 TB | 5 GB do 4 TB |
| Typ magazynu | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Okres przechowywania kopii zapasowej bazy danych | 7 – 35 dni | 7 – 35 dni | 7 – 35 dni |

Aby wybrać warstwę cenową, skorzystaj z poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa | Obciążenia wymagające niewielkiej obliczeniowej i wydajności we/wy. Na przykład serwery używane do tworzenia i testowania lub niewielkie, rzadko używane aplikacje. |
| Ogólne zastosowanie | Większości obciążeń biznesowych wymagających zrównoważonych zasobów obliczeniowych i pamięci, ze skalowalną przepływnością wejścia/wyjścia. Przykłady obejmują serwery do hostowania w sieci web i aplikacje mobilne i inne aplikacje dla przedsiębiorstw.|
| Pamięć | Obciążeń bazy danych o wysokiej wydajności, wymagających wydajności wewnątrzpamięciowej szybsze przetwarzanie transakcji oraz zapewnienie większej współbieżności. Na przykład serwery przetwarzania danych w czasie rzeczywistym oraz wysokowydajne aplikacje transakcyjne i analityczne.|

Po utworzeniu serwera, liczba rdzeni wirtualnych, generacja sprzętu i ceny warstwy (z wyjątkiem do i z Basic) można zmienić w górę lub w dół w ciągu kilku sekund. Możesz również niezależnie dostosować wielkość pamięci masowej w górę oraz okres przechowywania kopii zapasowej w górę lub w dół bez przestojów aplikacji. Nie można zmienić typu magazynu kopii zapasowych, po utworzeniu serwera. Aby uzyskać więcej informacji, zobacz [skalować zasoby](#scale-resources) sekcji.

## <a name="compute-generations-and-vcores"></a>Generacji zasobów obliczeniowych i rdzeni wirtualnych

Obliczenia, że zasoby są dostarczane jako rdzeni wirtualnych, który reprezentuje logiczny procesor CPU bazowego sprzętu. Chiny wschodnie 1, Chiny Północne 1, dod — środkowe stany USA i dod — wschodnie stany USA wykorzystują Gen 4 logiczne procesory CPU, które wykorzystują procesory Intel E5-2673 v3 (Haswell) 2,4 GHz procesorów. Korzystanie z innych regionów Gen 5 logiczne procesory CPU, które wykorzystują procesory Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów.

## <a name="storage"></a>Magazyn

Magazyn, który możesz aprowizować to pojemność magazynu jest dostępne dla usługi Azure Database dla serwera MySQL. Magazyn jest używany dla plików bazy danych, pliki tymczasowe, dzienników transakcji i serwer MySQL dzienniki. Łączną ilość pamięci masowej, które możesz aprowizować definiuje również wydajność We/Wy, dostępne na serwerze.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 4 TB | 5 GB do 4 TB |
| Rozmiar przyrost magazynu | 1 GB | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS |

Dodanie pojemności dodatkowego miejsca do magazynowania, podczas i po utworzeniu serwera i umożliwić systemu w celu zwiększenia magazynu automatycznie w zależności od użycia magazynu obciążenia. Warstwa podstawowa nie zapewnia gwarancję operacji We/Wy. Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci, warstw cenowych operacje We/Wy skalowanie o rozmiarze aprowizowanego magazynu współczynnik 3:1.

Można monitorować swoje użycie operacji We/Wy w witrynie Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Istotne metryki do monitorowania są [limit przestrzeni dyskowej, procent użycia magazynu, magazynu i procent We/Wy](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Duże storage (wersja zapoznawcza)

Firma Microsoft coraz limity magazynu w warstwach naszego ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Nowo utworzonych serwerów tego wyrażenia zgody na wersję zapoznawczą można aprowizować maksymalnie 16 TB pamięci. Operacje We/Wy skalować je w stosunku 3:1 do 20 000 operacji We/Wy. Podobnie jak w przypadku bieżącego magazynu jest ogólnie dostępna, możesz dodać dodatkową pojemność po utworzeniu serwera i umożliwiają systemu w celu zwiększenia magazynu automatycznie w zależności od użycia magazynu obciążenia.

|              | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:-------------|:--------------------|:---------------------|
| Typ magazynu | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | 32 GB do 16 TB| 32-16 TB |
| Rozmiar przyrost magazynu | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalnie 20 000 operacji We/Wy| 3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalnie 20 000 operacji We/Wy |

> [!IMPORTANT]
> Duże magazyny jest obecnie dostępna w publicznej wersji zapoznawczej w następujących regionach: Wschodnie stany USA, wschodnie stany USA 2, środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Azja południowo-wschodnia, Japonia, część wschodnia, Korea środkowa, Australia Wschodnia.
>
> Obecnie nie obsługuje duże magazyny w wersji zapoznawczej:
>
> * Geograficznie nadmiarowych kopii zapasowych
> * Obejmujące wiele regionów replikacji

### <a name="reaching-the-storage-limit"></a>Przekroczony limit magazynu

Serwery z mniej niż 100 GB zainicjowano obsługę administracyjną magazynu są oznaczone tylko do odczytu, jeśli ilość wolnego miejsca jest mniejsza niż 512MB lub 5% rozmiaru aprowizowanego magazynu. Serwery z więcej niż 100 GB zainicjowano obsługę administracyjną magazynu są oznaczone odczytu, tylko wtedy, gdy ilość wolnego miejsca jest mniejsza niż 5 GB.

Przykładowo po aprowizowaniu 110 GB miejsca do magazynowania i rzeczywiste użycie przekroczą 105 GB danych, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli aprowizowaniu 5 GB miejsca do magazynowania, serwer zostanie oznaczony tylko do odczytu po przekraczać 512 MB wolnego miejsca.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. Gdy zwiększysz zaaprowizowaną pojemność magazynu, serwer będzie ponownie gotowy do akceptowania transakcji zapisu.

Zaleca się włączenie funkcji magazynu automatyczne powiększanie lub skonfigurować alert, aby otrzymywać powiadomienia, gdy magazyn serwera zbliża się do progu więc można uniknąć, jakim jest w stanie tylko do odczytu. Aby uzyskać więcej informacji, zobacz dokumentację na [sposobu ustawiania alertu](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Magazyn automatyczne powiększanie

Jeśli rosnąć automatycznie magazynu jest włączona, Magazyn automatycznie rozszerza się bez wywierania wpływu na obciążenie pracą. W przypadku serwerów z mniej niż 100 GB zainicjowano obsługę administracyjną magazynu rozmiar aprowizowanego magazynu jest zwiększana o 5 GB, jak ilość wolnego miejsca jest poniżej większą niż 1 GB lub 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB magazynu aprowizowanego rozmiaru aprowizowanego magazynu zwiększa się o 5% gdy ilość wolnego miejsca jest poniżej 5% rozmiaru aprowizowanego magazynu. Limity pamięci masowej, jak określono powyżej mają zastosowanie.

Przykładowo po aprowizowaniu 1000 GB miejsca do magazynowania i rzeczywiste użycie przekroczą 950 GB, rozmiar magazynu serwera zostaje zwiększona do 1050 GB. Alternatywnie Jeśli aprowizowaniu 10 GB miejsca do magazynowania, rozmiar magazynu jest wzrost do 15 GB podczas kosztuje mniej niż 1 GB pamięci.

## <a name="backup"></a>Backup

Usługa automatycznie wykonuje kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Można ustawić okresu przechowywania maksymalnie 35 dni. Okres przechowywania można zmienić w dowolnym momencie podczas okresu istnienia serwera. Możesz wybrać między lokalnie nadmiarowy i geograficznie nadmiarowych kopii zapasowych. Geograficznie nadmiarowe kopie zapasowe są również przechowywane w [regionu sparowanego geograficznie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu, w którym serwer zostanie utworzona. Tę nadmiarowość zapewnia poziom ochrony w przypadku awarii. Możesz również uzyskać możliwość przywracania serwera do innego regionu platformy Azure, w którym ta usługa jest dostępna za pomocą geograficznie nadmiarowych kopii zapasowych. Nie jest możliwa zmiana pomiędzy dwiema opcjami magazynu kopii zapasowej po utworzeniu serwera.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera, niezależnie od siebie można zmienić rdzeni wirtualnych, generacja sprzętu, warstwa cenowa (z wyjątkiem do i z Basic), ilości miejsca do magazynowania i okres przechowywania kopii zapasowej. Nie można zmienić typu magazynu kopii zapasowych, po utworzeniu serwera. Liczba rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Tylko można zwiększyć rozmiar magazynu. Skalowanie zasobów może odbywać się za pośrednictwem portalu lub wiersza polecenia platformy Azure. Na przykład skalowania przy użyciu wiersza polecenia platformy Azure, zobacz [Monitor i skala serwera Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure](scripts/sample-scale-server.md).

Po zmianie liczby rdzeni wirtualnych generacja sprzętu lub warstwy cenowej kopię oryginalny serwer jest tworzony z nowego przydzielić pamięci obliczeniowej. Po skonfigurowaniu i uruchomieniu nowego serwera połączeń są przełączono na nowym serwerze. W chwili, gdy system przełącza do nowego serwera nawiązywanie nowych połączeń nie może zostać nawiązana i wycofać wszystkie niezatwierdzone transakcje. To okno zmienia się, ale w większości przypadków jest mniej niż minutę.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowej są prawdziwe operacje online. Brak przestojów, a nie ma wpływu na aplikację. Operacje We/Wy skalowania o rozmiarze aprowizowanego magazynu, możesz zwiększyć operacje We/Wy dostępne na serwerze, skalując w górę magazynu.

## <a name="pricing"></a>Cennik

Aby uzyskać najbardziej aktualne informacje o cenach, zobacz usługę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/mysql/). Aby zobaczyć kosztów dla konfiguracji, należy [witryny Azure portal](https://portal.azure.com/#create/Microsoft.MySQLServer) Wyświetla miesięczny koszt w **warstwa cenowa** kartę oparciu o wybrane opcje. Jeśli nie masz subskrypcji platformy Azure, można użyć Kalkulatora cen platformy Azure można pobrać szacowana cena. Na [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) witryny sieci Web, wybierz opcję **Dodaj elementy**, rozwiń węzeł **baz danych** kategorii i wybierz polecenie **— Azure Database for MySQL**Aby dostosować opcje.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzysz serwer MySQL w witrynie portal](howto-create-manage-server-portal.md).
- Dowiedz się więcej o [limitów usług](concepts-limits.md).
- Dowiedz się, jak [skalowanie w poziomie za pomocą odczytu replik](howto-read-replicas-portal.md).
