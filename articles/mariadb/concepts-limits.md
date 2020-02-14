---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: df44cbefaec943a2df483f4804650b939c796cb5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191147"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
Poniżej opisano pojemności, obsługa aparatu magazynu, uprawnień obsługę, dane manipulowania instrukcji oraz ograniczenia funkcjonalności w usłudze bazy danych.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i rdzeni wirtualnych są następujące:

|**Warstwa cenowa**|**Rdzeń wirtualny**| **Maksymalna liczba połączeń**|
|---|---|---|
|Podstawowa| 1| 50|
|Podstawowa| 2| 100|
|Ogólnego przeznaczenia| 2| 600|
|Ogólnego przeznaczenia| 4| 1250|
|Ogólnego przeznaczenia| 8| 2500|
|Ogólnego przeznaczenia| 16| 5000|
|Ogólnego przeznaczenia| 32| 10 000|
|Ogólnego przeznaczenia| 64| 20000|
|Pamięć| 2| 800|
|Pamięć| 4| 2500|
|Pamięć| 8| 5000|
|Pamięć| 16| 10 000|
|Pamięć| 32| 20000|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak ProxySQL, aby efektywnie zarządzać połączeniami.

Tworzenie nowych połączeń klientów do MariaDB trwa po upływie czasu i po jego ustanowieniu te połączenia zajmują zasoby bazy danych, nawet jeśli są bezczynne. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pulę połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń, będzie pomóc w uniknięciu tego działania. Aby dowiedzieć się więcej o konfigurowaniu ProxySQL, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [ROZMIAR](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FOLDERU](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola Administrator: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić właściwości ACID systemu DBMS. Jako takie utrzymanie integralności usługi i umowa SLA na poziomie produktu, ta usługa nie ujawnia roli Administrator. Domyślne konto użytkownika, który jest tworzony, gdy tworzone jest nowe wystąpienie bazy danych, umożliwia użytkownikowi wykonać większość instrukcji DDL i DML w wystąpieniu zarządzanym bazy danych.
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu polecenia `--skip-definer` podczas wykonywania mysqldump.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulacji danych

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwana, ale parametr `[LOCAL]` musi być określony i kierowany do ścieżki UNC (usługa Azure Storage zainstalowana za pomocą protokołu SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejsza rozmiar magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatycznej migracji między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwane.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Podczas korzystania z funkcji Odzyskiwanie, nowy serwer jest tworzony przy użyciu tej samej konfiguracji, co serwer, który opiera się na.
- Przywracanie usuniętych serwera nie jest obsługiwana.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamiczne przeniesienie wstępnie utworzonych serwerów między subskrypcją i grupą zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Obecnie znane problemy
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj polecenia `select version();`.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
