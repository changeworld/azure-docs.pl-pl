---
title: Tworzenie kopii zapasowych i przywracanie — usługa Azure Database for MariaDB
description: Dowiedz się więcej o automatycznych kopiach zapasowych i przywracaniu usługi Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: c4d5a9ca85237bde1277904a478a0b8828fc2b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369237"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Tworzenie kopii zapasowych i przywracanie w bazie danych platformy Azure dla mariadb

Usługa Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie skonfigurowanym lokalnie nadmiarowo lub geograficznie nadmiarowym. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią każdej strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Usługa Azure Database for MariaDB wykonuje pełne, różnicowe i kopie zapasowe dziennika transakcji. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w skonfigurowanym okresie przechowywania kopii zapasowej. Domyślny okres przechowywania kopii zapasowej wynosi siedem dni. Opcjonalnie można go skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania 256-bitowego AES.

Te pliki kopii zapasowej nie są narażone na użytkownika i nie można ich wyeksportować. Te kopie zapasowe mogą być używane tylko do przywracania operacji w usłudze Azure Database dla MariaDB. Za pomocą [mysqldump](howto-migrate-dump-restore.md) można skopiować bazę danych.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Na ogół pełne kopie zapasowe są wykonywane co tydzień, różnicowe kopie zapasowe są wykonywane dwa razy dziennie, a kopie zapasowe dziennika transakcji są wykonywane co pięć minut. Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu serwera. Początkowa kopia zapasowa może trwać dłużej na dużym przywróconym serwerze. Najwcześniejszym momentem w czasie, do którego można przywrócić nowy serwer, jest czas, w którym początkowa pełna kopia zapasowa została ukończona.

### <a name="backup-redundancy-options"></a>Opcje nadmiarowości kopii zapasowych

Usługa Azure Database for MariaDB zapewnia elastyczność wyboru między lokalnie nadmiarowym lub nadmiarowym magazynem kopii zapasowych w warstwach ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Gdy kopie zapasowe są przechowywane w geograficznie nadmiarowym magazynie kopii zapasowych, są one nie tylko przechowywane w regionie, w którym serwer jest hostowany, ale są również replikowane do [sparowanego centrum danych](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Zapewnia to lepszą ochronę i możliwość przywrócenia serwera w innym regionie w przypadku awarii. Warstwa Podstawowa oferuje tylko lokalnie nadmiarowy magazyn kopii zapasowych.

> [!IMPORTANT]
> Konfigurowanie lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. Po zainicjowaniu obsługi administracyjnej serwera nie można zmienić opcji nadmiarowości magazynu kopii zapasowych.

### <a name="backup-storage-cost"></a>Koszt przechowywania kopii zapasowej

Usługa Azure Database for MariaDB zapewnia do 100% aprowizowanego magazynu serwera jako magazynu kopii zapasowych bez dodatkowych kosztów. Zazwyczaj jest to odpowiednie dla przechowywania kopii zapasowej siedmiu dni. Wszelkie dodatkowe miejsce do przechowywania kopii zapasowych jest naliczane w gb-miesiąc.

Na przykład jeśli aprowizacji serwera z 250 GB, masz 250 GB miejsca kopii zapasowej bez dodatkowych opłat. Pobierana jest opłata za przechowywanie przekraczającą 250 GB.

Aby uzyskać więcej informacji na temat kosztów przechowywania kopii zapasowych, odwiedź [stronę z cennikiem MariaDB](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Przywracanie

W usłudze Azure Database for MariaDB wykonanie przywracania tworzy nowy serwer z oryginalnych kopii zapasowych serwera i przywraca wszystkie bazy danych zawarte na serwerze.

Dostępne są dwa typy przywracania:

- **Przywracanie w czasie** jest dostępne z opcją nadmiarowości kopii zapasowej i tworzy nowy serwer w tym samym regionie co oryginalny serwer, wykorzystując kombinację pełnych i rejestrowych kopii zapasowych.
- **Przywracanie geograficzne** jest dostępne tylko wtedy, gdy serwer został skonfigurowany do magazynowania geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu przy użyciu najnowszej wykonanej kopii zapasowej.

Szacowany czas odzyskiwania zależy od kilku czynników, w tym rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i całkowita liczba baz danych odzyskiwanie w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

> [!IMPORTANT]
> Nie **można** przywrócić usuniętych serwerów. Jeśli usuniesz serwer, wszystkie bazy danych, które należą do serwera są również usuwane i nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu, przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, administratorzy mogą korzystać z [blokad zarządzania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od opcji nadmiarowości kopii zapasowej można wykonać przywracanie do dowolnego punktu w czasie w okresie przechowywania kopii zapasowej. Nowy serwer jest tworzony w tym samym regionie platformy Azure co oryginalny serwer. Jest on tworzony przy obliczu oryginalnej konfiguracji serwera dla warstwy cenowej, generowania obliczeń, liczby koperów wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowej.

Przywracanie w czasie jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usuwa dane, porzuca ważną tabelę lub bazę danych lub jeśli aplikacja przypadkowo zastępuje dobre dane ze złymi danymi z powodu wady aplikacji.

Może być konieczne zaczekanie na następną kopię zapasową dziennika transakcji, aby można było przywrócić do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Serwer można przywrócić do innego regionu platformy Azure, w którym usługa jest dostępna, jeśli serwer został skonfigurowany do tworzenia kopii zapasowych zbędnych geograficznie. Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy serwer jest niedostępny z powodu zdarzenia w regionie, w którym serwer jest obsługiwany. Jeśli zdarzenie na dużą skalę w regionie powoduje niedostępność aplikacji bazy danych, można przywrócić serwer z geograficznie nadmiarowych kopii zapasowych na serwerze w dowolnym innym regionie. Przywracanie geograficzne wykorzystuje najnowszą kopię zapasową serwera. Występuje opóźnienie między, gdy kopia zapasowa jest pobierana i kiedy jest replikowana do innego regionu. To opóźnienie może wynosić do godziny, więc w przypadku wystąpienia awarii może do jednej godziny utraty danych.

Podczas przywracania geograficznego konfiguracje serwera, które można zmienić, obejmują generowanie obliczeń, rów wirtualnych, okres przechowywania kopii zapasowych i opcje nadmiarowości kopii zapasowych. Zmiana warstwy cenowej (Podstawowe, ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci) lub rozmiar magazynu podczas przywracania geograficznego nie jest obsługiwana.

### <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu z jednego z mechanizmów odzyskiwania należy wykonać następujące zadania, aby uzyskać kopię zapasową użytkowników i aplikacji:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierowuj klientów i aplikacje klienckie do nowego serwera
- Upewnij się, że odpowiednie reguły sieci wirtualnej są na miejscu dla użytkowników do łączenia. Reguły te nie są kopiowane z oryginalnego serwera.
- Upewnij się, że obowiązują odpowiednie loginy i uprawnienia na poziomie bazy danych
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o ciągłości biznesowej, zobacz [omówienie ciągłości biznesowej](concepts-business-continuity.md).
- Aby przywrócić do punktu w czasie przy użyciu portalu Azure, zobacz [przywracanie serwera do punktu w czasie przy użyciu portalu Azure](howto-restore-server-portal.md).
- Aby przywrócić do punktu w czasie przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przywracanie serwera do punktu w czasie przy użyciu interfejsu wiersza polecenia](howto-restore-server-cli.md).
