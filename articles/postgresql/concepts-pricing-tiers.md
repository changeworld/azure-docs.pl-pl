---
title: Warstw cenowych dla bazy danych Azure dla PostgreSQL
description: W tym artykule opisano warstw cenowych w bazie danych Azure dla PostgreSQL.
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 8c8a2f6076c14dabe70f90538ca8c8b97fb93de0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Bazy danych platformy Azure dla PostgreSQL warstw cenowych

Można utworzyć bazy danych Azure PostgreSQL serwera w jednym z trzech różnych warstw cenowych: Basic, ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Warstw cenowych są zróżnicowane według ilości zasobów obliczeniowych w vCores, które można udostępnić, ilość pamięci na vCore i technologii magazynowania używany do przechowywania danych. Wszystkie zasoby są udostępniane na poziomie serwera PostgreSQL. Serwer może mieć jedną lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowana pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeniowe | Gen 4, 5 Gen | Gen 4, 5 Gen | 5. generacja |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Ilość pamięci na vCore | Baseline | 2 x Basic | 2 x ogólnego przeznaczenia |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 1 TB | 5 GB do 1 TB |
| Typ magazynu | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Okres przechowywania kopii zapasowych bazy danych | 7-35 dni | 7-35 dni | 7-35 dni |

Wybierz warstwę cenową, skorzystaj z poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa | Obciążenia wymagające światła obliczeniowych i wydajność We/Wy. Przykłady obejmują serwery używane do tworzenia i testowania lub niewielkie, rzadko używane aplikacje. |
| Ogólne zastosowanie | Większość obciążeń biznesowych, które wymagają zrównoważonym obliczeniową i pamięć z skalowalne przepływność we/wy. Przykłady obejmują serwery hostingu sieci web i aplikacji mobilnych i inne aplikacje przedsiębiorstwa.|
| Pamięć | Obciążeń wysokiej wydajności bazy danych, które wymagają wydajności w pamięci dla szybsze przetwarzanie transakcji i wyższe współbieżności. Przykładami serwerów na potrzeby przetwarzania danych w czasie rzeczywistym i wysokiej wydajności aplikacji transakcyjnej lub analitycznych.|

Po utworzeniu serwera liczba vCores można zmienić w górę lub w dół w ciągu kilku sekund. Ponadto można niezależnie dostosować ilość pamięci masowej w górę i okres przechowywania kopii zapasowych w górę lub w dół bez przestojów aplikacji. Aby uzyskać więcej informacji zobacz sekcję "Zasoby są skalowane".

## <a name="compute-generations-vcores-and-memory"></a>Generacje obliczeń, vCores i pamięci

Obliczeń zasoby są dostarczane jako vCores, reprezentujące Procesora logicznego podstawowej sprzętu. Obecnie są dostępne dwie generacje obliczeń Gen 4 i Gen 5. Gen 4 procesory logiczne są oparte na Intel E5-2673 v3 procesorów 2,4 GHz (Haswell). Gen 5 procesorów logicznych są oparte na Intel E5-2673 v4 procesorów 2.3 GHz (Broadwell). Gen 4 i Gen 5 są dostępne w następujących regionach ("X" oznacza dostępna). 

| **Region platformy Azure** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Środkowe stany USA |  | X |
| Wschodnie stany USA | X | X |
| Wschodnie stany USA 2 | X |  |
| Środkowo-północne stany USA | X |  |
| Środkowo-południowe stany USA | X |  |
| Zachodnie stany USA | X | X |
| Zachodnie stany USA 2 |  | X |
| Kanada Środkowa | X | X |
| Kanada Wschodnia | X | X |
| Brazylia Południowa | X |  |
| Europa Północna | X | X |
| Europa Zachodnia | X | X |
| Zachodnie Zjednoczone Królestwo |  | X |
| Południowe Zjednoczone Królestwo |  | X |
| Azja Wschodnia | X |  |
| Azja Południowo-Wschodnia | X |  |
| Australia Wschodnia |  | X |
| Indie Środkowe | X |  |
| Indie Zachodnie | X |  |
| Japonia Wschodnia | X |  |
| Japonia Zachodnia | X |  |
| Korea Południowa |  | X |

W zależności od warstwy cenowej każdego vCore jest udostępniane z określoną ilością pamięci. Gdy zwiększyć lub zmniejszyć liczbę vCores serwera, ilość pamięci zwiększa lub zmniejsza proporcjonalnie. Warstwa ogólnego przeznaczenia zapewnia podwójne ilość pamięci na vCore w porównaniu do warstwy podstawowa. Warstwa zoptymalizowanych pod kątem pamięci miejsce double ilość pamięci w porównaniu do warstwy ogólnego przeznaczenia.

## <a name="storage"></a>Magazyn

Zainicjowanie obsługi magazynu jest pojemności magazynu dostępnych do bazy danych Azure PostgreSQL serwera. Magazyn jest używany dla plików bazy danych, pliki tymczasowe, dzienników transakcji i serwer PostgreSQL dzienniki. Łączną ilość pamięci masowej, które należy udostępnić definiuje również dostępne wydajność We/Wy do serwera.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowana pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | 5 GB do 1 TB | 5 GB do 1 TB | 5 GB do 1 TB |
| Rozmiar przyrost magazynu | 1 GB | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | Zmienna |3 IOPS/GB<br/>Min 100 IOPS | 3 IOPS/GB<br/>Min 100 IOPS |

Podczas i po utworzeniu serwera można dodać dodatkowej pojemności. Warstwa podstawowa nie ma gwarancji IOPS. Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci, warstw cenowych IOPS skalowania o rozmiarze zainicjowanego magazynu w stosunku 3:1.

Można monitorować użycia we/wy w portalu Azure lub przy użyciu poleceń wiersza polecenia platformy Azure. Odpowiednich metryk do monitorowania są [limit magazynu, procent użycia magazynu, Magazyn używany i procent We/Wy](concepts-monitoring.md).

## <a name="backup"></a>Backup

Usługa automatycznie wykonuje kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Można ustawić okresu przechowywania, do 35 dni. W dowolnym momencie można dostosować zachowanie przez cały okres istnienia serwera. Można wybrać tworzenie kopii zapasowych lokalnie nadmiarowego i geograficznie nadmiarowy. Geograficznie nadmiarowego kopie zapasowe są także przechowywane w [łączyć geograficznie regionu](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu, w której serwer jest tworzony. Dublowanie zapewnia poziom ochrony w przypadku awarii. Możesz również uzyskać możliwość przywrócenia serwera do innego regionu Azure, w którym usługa jest dostępna z geograficznie nadmiarowego kopii zapasowych. Nie jest to można zmienić po utworzeniu serwera z dwóch opcji magazynu kopii zapasowej.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera niezależnie zmienić vCores, ilość miejsca w magazynie i okres przechowywania kopii zapasowych. Po utworzeniu serwera nie można zmienić warstwy cenowej lub typ magazynu kopii zapasowej. vCores i okres przechowywania kopii zapasowych można skalować w górę lub w dół. Tylko można zwiększyć rozmiar magazynu. Skalowanie zasobów można zrobić za pomocą portalu lub wiersza polecenia platformy Azure. Przykład odbierającej za pomocą interfejsu wiersza polecenia Azure, zobacz [monitora i skalowania bazy danych Azure PostgreSQL serwera przy użyciu interfejsu wiersza polecenia Azure](scripts/sample-scale-server-up-or-down.md).

Jeśli zmienisz numer vCores z nowej alokacji obliczeniowe jest utworzona kopia oryginalnego serwera. Po skonfigurowaniu i uruchomieniu nowego serwera połączeń są przełączono się na nowym serwerze. W momencie gdy system przełącza się na nowym serwerze można ustalić bez nowych połączeń, a wszystkie niezatwierdzone transakcje są wycofywane. To okno jest różny, ale w większości przypadków jest mniej niż minutę.

Skalowanie magazynu i zmienić okres przechowywania kopii zapasowych to PRAWDA operacje online. Bez przestojów nie istnieje i nie ma wpływu na aplikację. IOPS skalowania o rozmiarze zainicjowanego magazynu, można zwiększyć IOPS dostępne z serwerem, skalując w górę magazynu.

## <a name="pricing"></a>Cennik

Najbardziej aktualne informacje cenowej, zobacz Usługa [cennikiem](https://azure.microsoft.com/pricing/details/PostgreSQL/). Aby wyświetlić koszt konfiguracji, należy [portalu Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) zawiera miesięczny koszt **warstwa cenowa** kartę na podstawie opcji wybrania. Jeśli nie masz subskrypcji platformy Azure, można użyć Azure Kalkulator cen można pobrać szacowanej ceny. Na [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) witryny sieci Web, wybierz opcję **Dodaj elementy**, rozwiń węzeł **baz danych** kategorii i wybierz polecenie **PostgreSQLbazydanychAzure** Aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć serwer PostgreSQL w portalu](tutorial-design-database-using-azure-portal.md).
- Dowiedz się, jak [monitorować i skalowania bazy danych Azure PostgreSQL serwera przy użyciu interfejsu wiersza polecenia Azure](scripts/sample-scale-server-up-or-down.md).
- Dowiedz się więcej o [usługi ograniczenia](concepts-limits.md).
