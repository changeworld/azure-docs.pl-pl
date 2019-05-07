---
title: Kopia zapasowa i przywracanie w usłudze Azure Database dla serwera MariaDB
description: Więcej informacji na temat automatycznego tworzenia kopii zapasowej i przywracanie usługi Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d6141c3184c8915c36f22d010db39aef2460dd1c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "60483055"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Kopia zapasowa i przywracanie w usłudze Azure Database dla serwera MariaDB

Azure Database dla serwera MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie lokalnie nadmiarowego lub magazynu geograficznie nadmiarowego skonfigurowane przez użytkownika. Tworzenie kopii zapasowych można przywrócić serwer do punktu w czasie. Kopia zapasowa i przywracanie są integralną część każdej strategii ciągłości biznesowej, ponieważ ich ochronę danych przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Azure Database dla serwera MariaDB przyjmuje pełnej, różnicowej i kopie zapasowe dziennika transakcji. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych skonfigurowany. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować go się do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowym szyfrowaniem AES.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Ogólnie rzecz biorąc, pełne kopie zapasowe są wykonywane co tydzień, różnicowe kopie zapasowe występują dwa razy dziennie, oraz kopie zapasowe dziennika transakcji co pięć minut. Pierwsza pełna kopia zapasowa jest zaplanowane, natychmiast, po utworzeniu serwera. Tworzenie początkowej kopii zapasowej może trwać dłużej dużych przywróconego serwera. Najwcześniejszy punkt w czasie, który można przywrócić do nowego serwera to czas, w którym zakończeniu początkowej pełnej kopii zapasowej.

### <a name="backup-redundancy-options"></a>Opcje nadmiarowości kopii zapasowej

Azure Database dla serwera MariaDB zapewnia elastyczność wyboru między lokalnie nadmiarowego lub magazynu geograficznie nadmiarowego magazynu kopii zapasowych w warstwach ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Podczas tworzenia kopii zapasowych są przechowywane w geograficznie nadmiarowego magazynu kopii zapasowych, nie są one tylko przechowywane w regionie, w którym znajduje się serwer, ale również są replikowane do [sparowanym centrum danych](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Zapewnia to lepszą ochronę i możliwość przywracania serwera w innym regionie, w przypadku awarii. Warstwa podstawowa oferuje tylko lokalnie nadmiarowy magazyn kopii zapasowych.

> [!IMPORTANT]
> Konfigurowanie lokalnie nadmiarowego lub magazynu geograficznie nadmiarowego magazynu dla kopii zapasowej jest dozwolone tylko podczas server Utwórz. Gdy serwer zostanie zaaprowizowana, nie można zmienić opcji nadmiarowości magazynu kopii zapasowych.

### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Azure Database dla serwera MariaDB zapewnia do 100% swoje zaprowizowanej pojemności magazynu serwera jako magazyn kopii zapasowych bez dodatkowych kosztów. Zazwyczaj jest to odpowiednie dla przechowywania kopii zapasowych siedmiu dni. Dodatkowy magazyn kopii zapasowych używany jest naliczana opłata w GB na miesiąc.

Na przykład jeśli aprowizowaniu serwer o rozmiarze 250 GB, masz 250 GB magazynu kopii zapasowych bez dodatkowych opłat. Magazynu przewyższającą 250 GB, jest naliczana opłata.

Aby uzyskać więcej informacji na temat kosztu magazynu kopii zapasowych, odwiedź stronę [MariaDB stronę z cennikiem](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Przywracanie

W usłudze Azure Database dla serwera MariaDB operacji przywracania tworzy nowy serwer z kopii zapasowych oryginalnego serwera.

Dostępne są dwa rodzaje przywracania:

- **W momencie przywracania** jest dostępna niezależnie od wybranej opcji nadmiarowości kopii zapasowej i tworzy nowy serwer w tym samym regionie co oryginalny serwer.
- **Przywracanie geograficzne** jest dostępna tylko wtedy, gdy skonfigurowano serwera na potrzeby magazynu geograficznie nadmiarowego umożliwia przywracanie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, w tym rozmiary baz danych, rozmiaru dziennika transakcji, przepustowości sieci i łączna liczba baz danych odzyskiwania w tym samym regionie, w tym samym czasie. Czas odzyskiwania jest zazwyczaj mniej niż 12 godzin.

> [!IMPORTANT]
> Usunięto serwerów **nie** można przywrócić. Jeśli usuniesz serwera, wszystkie bazy danych, które należą do serwera również zostaną usunięte i nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu, z przypadkowego usunięcia lub nieoczekiwanych zmian, Administratorzy mogą korzystać [zarządcze](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od swoją opcję nadmiarowości kopii zapasowej, można wykonać przywracanie do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych. Nowy serwer jest tworzony w tym samym regionie platformy Azure na podstawie oryginalnego serwera. Jest tworzona przy użyciu oryginalnego serwera konfiguracji dla warstwy cenowej, generacji obliczeń i liczbę rdzeni, rozmiaru magazynu, okres przechowywania kopii zapasowej i wybranej opcji nadmiarowości kopii zapasowej.

W momencie przywracania jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usunął dane, spada ważną tabelę lub bazy danych, czy aplikacja zastępuje przypadkowo poprawnych danych błędnymi danymi wady aplikacji.

Konieczne może czekać na następnej kopii zapasowej dziennika transakcji można podjąć przed przywróceniem do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Po skonfigurowaniu serwera na potrzeby geograficznie nadmiarowych kopii zapasowych, można przywrócić serwer do innego regionu platformy Azure, w którym ta usługa jest dostępna. Funkcja przywracania geograficznego jest domyślną opcję odzyskiwania, gdy serwer będzie niedostępny z powodu zdarzenia w regionie, w którym jest hostowany serwer. Jeśli na dużą skalę zdarzenia w regionie skutkuje brakiem dostępu aplikacji bazy danych, można przywrócić serwer z geograficznie nadmiarowych kopii zapasowych na serwerze w każdym innym regionie. Występuje opóźnienie między podczas tworzenia kopii zapasowej i kiedy są replikowane do innego regionu. To opóźnienie może trwać do godziny, więc w przypadku poważnej awarii, może wystąpić do utraty danych jedną godzinę.

Podczas przywracania geograficznego konfiguracje serwerów, które mogą być zmieniane obejmują generacja obliczeń, (rdzeń wirtualny), okres przechowywania kopii zapasowej i opcje nadmiarowości kopii zapasowej. Zmienianie warstwę cenową (podstawowa, ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci) lub rozmiar magazynu podczas przywracania geograficznego nie jest obsługiwane.

### <a name="perform-post-restore-tasks"></a>Wykonaj zadania wykonywane po przywróceniu

Po przywróceniu z dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania, aby zachęcić użytkowników i aplikacje kopii pracę:

- Jeśli nowy serwer ma zastąpić oryginalnego serwera, należy przekierować klientów i aplikacji klienckich na nowy serwer
- Upewnij się, że reguły zapory na poziomie serwera odpowiednie są stosowane dla użytkowników nawiązujących połączenie
- Upewnij się, odpowiednich danych logowania i uprawnienia na poziomie bazy danych znajdują się w miejscu
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o ciągłości działania, zobacz [omówienie ciągłości działania](concepts-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywrócić bazę danych do punktu w czasie za pomocą witryny Azure portal](howto-restore-server-portal.md).
 
<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md).-->
