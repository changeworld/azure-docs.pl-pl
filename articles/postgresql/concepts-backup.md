---
title: Tworzenie kopii zapasowych i przywracanie — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej o automatycznych kopiach zapasowych i przywracaniu bazy danych usługi Azure database dla serwera PostgreSQL — single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619621"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Tworzenie kopii zapasowych i przywracanie w bazie danych azure dla postgreSQL — pojedynczy serwer

Usługa Azure Database for PostgreSQL automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie skonfigurowanym lokalnie nadmiarowo lub geograficznie nadmiarowym. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią każdej strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Usługa Azure Database for PostgreSQL wykonuje kopie zapasowe plików danych i dziennika transakcji. W zależności od obsługiwanego maksymalnego rozmiaru magazynu, robimy pełne i różnicowe kopie zapasowe (serwery magazynu o maksymalnej pojemności 4 TB) lub kopie zapasowe migawek (do 16 TB serwerów magazynu max). Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w skonfigurowanym okresie przechowywania kopii zapasowej. Domyślny okres przechowywania kopii zapasowej wynosi siedem dni. Opcjonalnie można go skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania 256-bitowego AES.

Tych plików kopii zapasowej nie można wyeksportować. Kopie zapasowe mogą być używane tylko do przywracania operacji w usłudze Azure Database dla PostgreSQL. Za pomocą [pg_dump](howto-migrate-using-dump-and-restore.md) można skopiować bazę danych.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Ogólnie rzecz biorąc, pełne kopie zapasowe występują co tydzień, różnicowe kopie zapasowe występują dwa razy dziennie dla serwerów z maksymalną obsługiwaną pamięcią masową 4 TB. Kopie zapasowe migawek są wykonywane co najmniej raz dziennie dla serwerów, które obsługują do 16 TB miejsca do magazynowania. Kopie zapasowe dziennika transakcji w obu przypadkach są wykonywane co pięć minut. Pierwsza migawka pełnej kopii zapasowej jest zaplanowana natychmiast po utworzeniu serwera. Początkowa pełna kopia zapasowa może trwać dłużej na dużym przywróconym serwerze. Najwcześniejszym momentem w czasie, do którego można przywrócić nowy serwer, jest czas, w którym początkowa pełna kopia zapasowa została ukończona. Ponieważ migawki są natychmiastowe, serwery z obsługą do 16 TB pamięci masowej można przywrócić aż do czasu tworzenia.

### <a name="backup-redundancy-options"></a>Opcje nadmiarowości kopii zapasowych

Usługa Azure Database for PostgreSQL zapewnia elastyczność wyboru między lokalnie nadmiarowym lub geograficznie nadmiarowym magazynem kopii zapasowych w warstwach ogólnego przeznaczenia i zoptymalizowanym pod kątem pamięci. Gdy kopie zapasowe są przechowywane w geograficznie nadmiarowym magazynie kopii zapasowych, są one nie tylko przechowywane w regionie, w którym serwer jest hostowany, ale są również replikowane do [sparowanego centrum danych](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Zapewnia to lepszą ochronę i możliwość przywrócenia serwera w innym regionie w przypadku awarii. Warstwa Podstawowa oferuje tylko lokalnie nadmiarowy magazyn kopii zapasowych.

> [!IMPORTANT]
> Konfigurowanie lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. Po zainicjowaniu obsługi administracyjnej serwera nie można zmienić opcji nadmiarowości magazynu kopii zapasowych.

### <a name="backup-storage-cost"></a>Koszt przechowywania kopii zapasowej

Usługa Azure Database for PostgreSQL zapewnia do 100% aprowizowanego magazynu serwera jako magazyn kopii zapasowych bez dodatkowych kosztów. Zazwyczaj jest to odpowiednie dla przechowywania kopii zapasowej siedmiu dni. Wszelkie dodatkowe miejsce do przechowywania kopii zapasowych jest naliczane w gb-miesiąc.

Na przykład jeśli aprowizacji serwera z 250 GB, masz 250 GB miejsca kopii zapasowej bez dodatkowych opłat. Pobierana jest opłata za przechowywanie przekraczającą 250 GB.

## <a name="restore"></a>Przywracanie

W usłudze Azure Database for PostgreSQL wykonanie przywracania tworzy nowy serwer z oryginalnych kopii zapasowych serwera.

Dostępne są dwa typy przywracania:

- **Przywracanie w czasie** jest dostępne z opcją nadmiarowości kopii zapasowej i tworzy nowy serwer w tym samym regionie co oryginalny serwer.
- **Przywracanie geograficzne** jest dostępne tylko wtedy, gdy serwer został skonfigurowany do magazynowania geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, w tym rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i całkowita liczba baz danych odzyskiwanie w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

> [!IMPORTANT]
> Nie **można** przywrócić usuniętych serwerów. Jeśli usuniesz serwer, wszystkie bazy danych, które należą do serwera są również usuwane i nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu, przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, administratorzy mogą korzystać z [blokad zarządzania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od opcji nadmiarowości kopii zapasowej można wykonać przywracanie do dowolnego punktu w czasie w okresie przechowywania kopii zapasowej. Nowy serwer jest tworzony w tym samym regionie platformy Azure co oryginalny serwer. Jest on tworzony przy obliczu oryginalnej konfiguracji serwera dla warstwy cenowej, generowania obliczeń, liczby koperów wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowej.

Przywracanie w czasie jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usuwa dane, porzuca ważną tabelę lub bazę danych lub jeśli aplikacja przypadkowo zastępuje dobre dane ze złymi danymi z powodu wady aplikacji.

Może być konieczne zaczekanie na następną kopię zapasową dziennika transakcji, aby można było przywrócić do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Serwer można przywrócić do innego regionu platformy Azure, w którym usługa jest dostępna, jeśli serwer został skonfigurowany do tworzenia kopii zapasowych zbędnych geograficznie. Serwery obsługujące do 4 TB pamięci masowej można przywrócić do regionu połączonego z geo sparowaniem lub do dowolnego regionu obsługującego do 16 TB pamięci masowej. W przypadku serwerów obsługujących do 16 TB pamięci masowej można przywrócić kopie geograficzne w dowolnym regionie obsługującym również serwery o pojemności 16 TB. Przejrzyj [warstwy cenowe Usługi Azure Database dla postgesql](concepts-pricing-tiers.md) dla listy obsługiwanych regionów.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy serwer jest niedostępny z powodu zdarzenia w regionie, w którym serwer jest obsługiwany. Jeśli zdarzenie na dużą skalę w regionie powoduje niedostępność aplikacji bazy danych, można przywrócić serwer z geograficznie nadmiarowych kopii zapasowych na serwerze w dowolnym innym regionie. Występuje opóźnienie między, gdy kopia zapasowa jest pobierana i kiedy jest replikowana do innego regionu. To opóźnienie może wynosić do godziny, więc w przypadku wystąpienia awarii może do jednej godziny utraty danych.

Podczas przywracania geograficznego konfiguracje serwera, które można zmienić, obejmują generowanie obliczeń, rów wirtualnych, okres przechowywania kopii zapasowych i opcje nadmiarowości kopii zapasowych. Zmiana warstwy cenowej (podstawowe, ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci) lub rozmiar magazynu nie jest obsługiwana.

### <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu z jednego z mechanizmów odzyskiwania należy wykonać następujące zadania, aby uzyskać kopię zapasową użytkowników i aplikacji:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierowuj klientów i aplikacje klienckie do nowego serwera
- Upewnij się, że istnieją odpowiednie reguły zapory na poziomie serwera i sieci wirtualnej, aby użytkownicy mogli się połączyć. Reguły te nie są kopiowane z oryginalnego serwera.
- Upewnij się, że obowiązują odpowiednie loginy i uprawnienia na poziomie bazy danych
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak przywrócić przy użyciu [witryny Azure portal](howto-restore-server-portal.md).
- Dowiedz się, jak przywrócić przy użyciu [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Aby dowiedzieć się więcej o ciągłości biznesowej, zobacz [omówienie ciągłości biznesowej](concepts-business-continuity.md).
