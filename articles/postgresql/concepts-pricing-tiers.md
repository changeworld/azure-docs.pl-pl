---
title: Warstwy cenowe dla usługi Azure Database for PostgreSQL
description: W tym artykule opisano warstw cenowych dla usługi Azure Database for PostgreSQL.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4109679c03b13303c16cef8edd92ecddb6ca5e5f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712371"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Usługa Azure Database for postgresql w warstwie warstw cenowych

W jednym z trzech różnych warstw cenowych, można utworzyć usługi Azure Database for postgresql w warstwie serwera: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości zasobów obliczeniowych w rdzeni wirtualnych, które mogą być udostępniane, ilość pamięci na rdzeń wirtualny i technologia magazynu używany do przechowywania danych. Wszystkie zasoby są aprowizowane na poziomie serwera PostgreSQL. Serwer może zawierać jeden lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generacja obliczeń | Gen 4, 5. generacji | Gen 4, 5. generacji | 5. generacja |
| Rdzenie wirtualne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
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

Obliczenia, że zasoby są dostarczane jako rdzeni wirtualnych, który reprezentuje logiczny procesor CPU bazowego sprzętu. Obecnie można wybrać z obu generacji obliczeń Gen 4 i 5 ogólnego. Logiczne procesory CPU 4 generacji wykorzystują procesory Intel E5-2673 v3 (Haswell) 2,4 GHz procesorów. Logiczne procesory CPU 5 generacji wykorzystują procesory Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów. Gen 4 i 5 ogólnego są dostępne w następujących regionach ("X" oznacza dostępne). 

| **Region platformy Azure** | **4. generacji** | **5. generacji** |
|:---|:----------:|:--------------------:|
| Środkowe stany USA |  | X |
| Wschodnie stany USA |  | X |
| Wschodnie stany USA 2 |  | X |
| Środkowo-północne stany USA |  | X |
| Środkowo-południowe stany USA |  | X |
| Zachodnie stany USA |  | X |
| Zachodnie stany USA 2 |  | X |
| Brazylia Południowa |  | X |
| Kanada Środkowa |  | X |
| Kanada Wschodnia |  | X |
| Europa Północna |  | X |
| Europa Zachodnia |  | X |
| Francja Środkowa |  | X |
| Południowe Zjednoczone Królestwo |  | X |
| Zachodnie Zjednoczone Królestwo |  | X |
| Azja Wschodnia |  | X |
| Azja Południowo-Wschodnia |  | X |
| Australia Wschodnia |  | X |
| Australia Środkowa |  | X |
| Australia Środkowa 2 |  | X |
| Australia Południowo-Wschodnia |  | X |
| Indie Środkowe |  | X |
| Indie Południowe |  | X |
| Indie Zachodnie |  | X |
| Japonia Wschodnia |  | X |
| Japonia Zachodnia |  | X |
| Korea Środkowa |  | X |
| Korea Południowa |  | X |
| Chiny wschodnie 1 | X |  |
| Chiny Wschodnie 2 |  | X |
| Chiny Północne 1 | X |  |
| Chiny Północne 2 |  | X |
| Niemcy Środkowe |  | X |
| US DoD — środkowe stany  | X |  |
| US DoD — wschodnie stany  | X |  |
| Administracja USA — Arizona |  | X |
| Administracja USA — Teksas |  | X |
| Administracja USA — Wirginia |  | X |

## <a name="storage"></a>Magazyn

Magazyn, który możesz aprowizować to pojemność magazynu jest dostępne dla usługi Azure Database dla serwera PostgreSQL. Magazyn jest używany dla plików bazy danych, pliki tymczasowe, dzienników transakcji i serwer PostgreSQL dzienniki. Łączną ilość pamięci masowej, które możesz aprowizować definiuje również wydajność We/Wy, dostępne na serwerze.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 4 TB | 5 GB do 4 TB |
| Rozmiar przyrost magazynu | 1 GB | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS |

Można dodać dodatkowej pojemności, podczas i po utworzeniu serwera. Warstwa podstawowa nie zapewnia gwarancję operacji We/Wy. Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci, warstw cenowych operacje We/Wy skalowanie o rozmiarze aprowizowanego magazynu współczynnik 3:1.

Można monitorować swoje użycie operacji We/Wy w witrynie Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Istotne metryki do monitorowania są [limit przestrzeni dyskowej, procent użycia magazynu, magazynu i procent We/Wy](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Przekroczony limit magazynu

Serwer zostanie oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca w magazynie spadnie poniżej 5 GB lub 5% zaaprowizowanej pojemności magazynu, w zależności od tego, która wartość będzie niższa. Przykładowo po aprowizowaniu 100 GB miejsca do magazynowania i rzeczywiste użycie przechodzi przez 95 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli zaaprowizowano 5 GB pojemności magazynu, serwer zostanie oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca spanie poniżej 250 MB.  

Gdy serwer jest wartość tylko do odczytu, wszystkie istniejące sesje są odłączone i niezatwierdzone transakcje są wycofywane. Wszystkie operacje zapisu w kolejnych i transakcji zatwierdza kończyć się niepowodzeniem. Wszystkie kolejne zapytania odczytu będzie działać nieprzerwanie.  

Można zwiększyć ilość aprowizowanego magazynu na serwerze lub Rozpocznij nową sesję w trybie i upuść danych odczytu i zapisu do odzyskania ilość wolnego miejsca. Uruchamianie `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` ustawia bieżącej sesji, aby odczytać trybie zapisu. Aby uniknąć uszkodzenia danych, nie należy wykonywać dowolne operacje zapisu, gdy serwer jest nadal w stanie tylko do odczytu.

Zalecane jest skonfigurowanie alertu, aby otrzymywać powiadomienia, gdy magazyn serwera zbliża się do progu, dzięki czemu można uniknąć, jakim jest w stanie tylko do odczytu. Aby uzyskać więcej informacji, zobacz dokumentację na [sposobu ustawiania alertu](howto-alert-on-metric.md).

## <a name="backup"></a>Backup

Usługa automatycznie wykonuje kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Można ustawić okresu przechowywania maksymalnie 35 dni. Okres przechowywania można zmienić w dowolnym momencie podczas okresu istnienia serwera. Możesz wybrać między lokalnie nadmiarowy i geograficznie nadmiarowych kopii zapasowych. Geograficznie nadmiarowe kopie zapasowe są również przechowywane w [regionu sparowanego geograficznie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu, w którym serwer zostanie utworzona. Tę nadmiarowość zapewnia poziom ochrony w przypadku awarii. Możesz również uzyskać możliwość przywracania serwera do innego regionu platformy Azure, w którym ta usługa jest dostępna za pomocą geograficznie nadmiarowych kopii zapasowych. Nie jest możliwa zmiana pomiędzy dwiema opcjami magazynu kopii zapasowej po utworzeniu serwera.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera, niezależnie od siebie można zmienić rdzeni wirtualnych, generacja sprzętu, warstwa cenowa (z wyjątkiem do i z Basic), ilości miejsca do magazynowania i okres przechowywania kopii zapasowej. Nie można zmienić typu magazynu kopii zapasowych, po utworzeniu serwera. Liczba rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Tylko można zwiększyć rozmiar magazynu. Skalowanie zasobów może odbywać się za pośrednictwem portalu lub wiersza polecenia platformy Azure. Na przykład skalowania przy użyciu wiersza polecenia platformy Azure, zobacz [Monitor i skala usługi Azure Database for postgresql w warstwie serwera za pomocą wiersza polecenia platformy Azure](scripts/sample-scale-server-up-or-down.md).

Po zmianie liczby rdzeni wirtualnych generacja sprzętu lub warstwy cenowej kopię oryginalny serwer jest tworzony z nowego przydzielić pamięci obliczeniowej. Po skonfigurowaniu i uruchomieniu nowego serwera połączeń są przełączono na nowym serwerze. W chwili, gdy system przełącza do nowego serwera nawiązywanie nowych połączeń nie może zostać nawiązana i wycofać wszystkie niezatwierdzone transakcje. To okno zmienia się, ale w większości przypadków jest mniej niż minutę.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowej są prawdziwe operacje online. Brak przestojów, a nie ma wpływu na aplikację. Operacje We/Wy skalowania o rozmiarze aprowizowanego magazynu, możesz zwiększyć operacje We/Wy dostępne na serwerze, skalując w górę magazynu.

## <a name="pricing"></a>Cennik

Aby uzyskać najbardziej aktualne informacje o cenach, zobacz usługę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/PostgreSQL/). Aby zobaczyć kosztów dla konfiguracji, należy [witryny Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Wyświetla miesięczny koszt w **warstwa cenowa** kartę oparciu o wybrane opcje. Jeśli nie masz subskrypcji platformy Azure, można użyć Kalkulatora cen platformy Azure można pobrać szacowana cena. Na [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) witryny sieci Web, wybierz opcję **Dodaj elementy**, rozwiń węzeł **baz danych** kategorii i wybierz polecenie **— Azure Database for postgresql w warstwie** Aby dostosować opcje.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzysz serwer PostgreSQL w witrynie portal](tutorial-design-database-using-azure-portal.md).
- Dowiedz się więcej o [limitów usług](concepts-limits.md). 
- Dowiedz się, jak [skalowanie w poziomie za pomocą odczytu replik](howto-read-replicas-portal.md).
