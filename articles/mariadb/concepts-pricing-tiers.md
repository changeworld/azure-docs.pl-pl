---
title: Warstwy cenowe dla usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano warstw cenowych dla usługi Azure Database dla serwera MariaDB.
author: jan-eng
ms.author: janeng
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 561244efd653294694cc16a1115962473e9a7cec
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249032"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database dla serwera MariaDB warstw cenowych

Można utworzyć usługi Azure Database dla serwera MariaDB w jednym z trzech różnych warstw cenowych: podstawowa, ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości zasobów obliczeniowych w rdzeni wirtualnych, które mogą być udostępniane, ilość pamięci na rdzeń wirtualny i technologia magazynu używany do przechowywania danych. Wszystkie zasoby są aprowizowane na poziomie serwera MariaDB. Serwer może zawierać jeden lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generacja obliczeń | 5. generacja |5. generacja | 5. generacja |
| Rdzenie wirtualne | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Ilość pamięci na rdzeń wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 4 TB | 5 GB do 4 TB |
| Typ magazynu | Usługa Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Okres przechowywania kopii zapasowej bazy danych | 7 – 35 dni | 7 – 35 dni | 7 – 35 dni |

Aby wybrać warstwę cenową, skorzystaj z poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa | Obciążenia wymagające niewielkiej obliczeniowej i wydajności we/wy. Na przykład serwery używane do tworzenia i testowania lub niewielkie, rzadko używane aplikacje. |
| Ogólne zastosowanie | Większości obciążeń biznesowych wymagających zrównoważonych zasobów obliczeniowych i pamięci, ze skalowalną przepływnością wejścia/wyjścia. Przykłady obejmują serwery do hostowania w sieci web i aplikacje mobilne i inne aplikacje dla przedsiębiorstw.|
| Pamięć | Obciążeń bazy danych o wysokiej wydajności, wymagających wydajności wewnątrzpamięciowej szybsze przetwarzanie transakcji oraz zapewnienie większej współbieżności. Na przykład serwery przetwarzania danych w czasie rzeczywistym oraz wysokowydajne aplikacje transakcyjne i analityczne.|

Po utworzeniu serwera, liczby rdzeni wirtualnych i warstwę cenową (z wyjątkiem do i z Basic) można zmienić w górę lub w dół w ciągu kilku sekund. Możesz również niezależnie dostosować wielkość pamięci masowej w górę oraz okres przechowywania kopii zapasowej w górę lub w dół bez przestojów aplikacji. Nie można zmienić typu magazynu kopii zapasowych, po utworzeniu serwera. Aby uzyskać więcej informacji, zobacz [skalować zasoby](#scale-resources) sekcji.

## <a name="compute-generations-and-vcores"></a>Generacji zasobów obliczeniowych i rdzeni wirtualnych

Obliczenia, że zasoby są dostarczane jako rdzeni wirtualnych, który reprezentuje logiczny procesor CPU bazowego sprzętu. Logiczne procesory CPU 5 generacji wykorzystują procesory Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów.

## <a name="storage"></a>Magazyn

Magazyn, który możesz aprowizować to pojemność magazynu jest dostępne dla usługi Azure Database dla serwera MariaDB. Magazyn jest używany dla plików bazy danych, pliki tymczasowe, dzienników transakcji i serwera MariaDB dzienniki. Łączną ilość pamięci masowej, które możesz aprowizować definiuje również wydajność We/Wy, dostępne na serwerze.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Usługa Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 4 TB | 5 GB do 4 TB |
| Rozmiar przyrost magazynu | 1 GB | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>6000 maks. IOPS |

Można dodać dodatkowej pojemności, podczas i po utworzeniu serwera. Warstwa podstawowa nie zapewnia gwarancję operacji We/Wy. Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci, warstw cenowych operacje We/Wy skalowanie o rozmiarze aprowizowanego magazynu współczynnik 3:1.

Można monitorować swoje użycie operacji We/Wy w witrynie Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Istotne metryki do monitorowania są limit przestrzeni dyskowej, procent użycia magazynu, magazynu i procent we/wy.
<!--[storage limit, storage percentage, storage used, and IO percent](concepts-monitoring.md)-->

### <a name="reaching-the-storage-limit"></a>Przekroczony limit magazynu

Serwer zostanie oznaczony jako tylko do odczytu, gdy osiągnie ilość wolnego miejsca w magazynie, mniejszy niż 5 GB lub 5% aprowizowanego magazynu, ta wartość jest mniejsza. Przykładowo po aprowizowaniu 100 GB miejsca do magazynowania i rzeczywiste użycie przechodzi przez 95 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie Jeśli aprowizowaniu 5 GB miejsca do magazynowania, serwer zostanie oznaczony jako tylko do odczytu po mniej niż 250 MB wolnego miejsca.  

Gdy usługa próbuje serwer stał się tylko do odczytu, wszystkie nowe żądania transakcji zapisu są zablokowane i istniejących aktywnych transakcji będzie w dalszym ciągu wykonują. Gdy serwer jest wartość tylko do odczytu, wszystkie operacje zapisu w kolejnych i transakcji zatwierdza kończyć się niepowodzeniem. Odczyt zapytania będą w dalszym ciągu bez przeszkód pracować. Po zwiększysz aprowizowanego magazynu, serwer będzie gotowy do akceptowania transakcji zapisu ponownie.

Zalecane jest skonfigurowanie alertu, aby otrzymywać powiadomienia, gdy magazyn serwera zbliża się do progu, dzięki czemu można uniknąć, jakim jest w stanie tylko do odczytu. 

<!--For more information, see the documentation on [how to set up an alert](howto-alert-on-metric.md).-->

## <a name="backup"></a>Backup

Usługa automatycznie wykonuje kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Można ustawić okresu przechowywania maksymalnie 35 dni. Okres przechowywania można zmienić w dowolnym momencie podczas okresu istnienia serwera. Możesz wybrać między lokalnie nadmiarowy i geograficznie nadmiarowych kopii zapasowych. Geograficznie nadmiarowe kopie zapasowe są również przechowywane w [regionu sparowanego geograficznie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu, w którym serwer zostanie utworzona. Tę nadmiarowość zapewnia poziom ochrony w przypadku awarii. Możesz również uzyskać możliwość przywracania serwera do innego regionu platformy Azure, w którym ta usługa jest dostępna za pomocą geograficznie nadmiarowych kopii zapasowych. Nie jest możliwa zmiana pomiędzy dwiema opcjami magazynu kopii zapasowej po utworzeniu serwera.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera, możesz zmienić niezależnie rdzeni wirtualnych, warstwa cenowa (z wyjątkiem do i z Basic), ilości miejsca do magazynowania i okres przechowywania kopii zapasowej. Nie można zmienić typu magazynu kopii zapasowych, po utworzeniu serwera. Liczba rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Tylko można zwiększyć rozmiar magazynu. Skalowanie zasobów może odbywać się za pośrednictwem portalu lub wiersza polecenia platformy Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Jeśli zmienisz liczbę rdzeni wirtualnych lub warstwy cenowej, kopię oryginalny serwer jest tworzony z nowych przydzielić pamięci obliczeniowej. Po skonfigurowaniu i uruchomieniu nowego serwera połączeń są przełączono na nowym serwerze. W chwili, gdy system przełącza do nowego serwera nawiązywanie nowych połączeń nie może zostać nawiązana i wycofać wszystkie niezatwierdzone transakcje. To okno zmienia się, ale w większości przypadków jest mniej niż minutę.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowej są prawdziwe operacje online. Brak przestojów, a nie ma wpływu na aplikację. Operacje We/Wy skalowania o rozmiarze aprowizowanego magazynu, możesz zwiększyć operacje We/Wy dostępne na serwerze, skalując w górę magazynu.

## <a name="pricing"></a>Cennik

Aby uzyskać najbardziej aktualne informacje o cenach, zobacz usługę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/mariadb/). Aby zobaczyć kosztów dla konfiguracji, należy [witryny Azure portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) Wyświetla miesięczny koszt w **warstwa cenowa** kartę oparciu o wybrane opcje. Jeśli nie masz subskrypcji platformy Azure, można użyć Kalkulatora cen platformy Azure można pobrać szacowana cena. Na [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) witryny sieci Web, wybierz opcję **Dodaj elementy**, rozwiń węzeł **baz danych** kategorii i wybierz polecenie **— Azure Database dla serwera MariaDB**Aby dostosować opcje.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi ograniczenia](concepts-limits.md).
- Dowiedz się, jak [Tworzenie serwera MariaDB w witrynie Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->