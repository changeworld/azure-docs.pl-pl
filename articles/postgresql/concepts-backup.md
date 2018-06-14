---
title: Kopia zapasowa i przywracanie bazy danych Azure dla PostgreSQL
description: Więcej informacji na temat automatycznego tworzenia kopii zapasowych i przywracanie bazy danych Azure, PostgreSQL serwera.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0f7ec38d2c271ebaa15e681a71eb32be7151921f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29693361"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Kopia zapasowa i przywracanie bazy danych Azure dla PostgreSQL

Bazy danych platformy Azure dla PostgreSQL automatycznie tworzy kopii zapasowych serwera i przechowuje je w magazynie użytkownika skonfigurowane lokalnie nadmiarowego lub geograficznie nadmiarowy. Tworzenie kopii zapasowych może służyć do przywrócenia serwera do punktu w czasie. Kopii zapasowej i przywracania są integralną część każdej strategii ciągłości biznesowej, ponieważ ich ochronę danych przed przypadkowym uszkodzenia lub usunięcia.

## <a name="backups"></a>Tworzenie kopii zapasowych

Bazy danych platformy Azure dla PostgreSQL przyjmuje pełnej, różnicowej i kopie zapasowe dziennika transakcji. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w okresie przechowywania kopii zapasowych skonfigurowany. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować go się do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą szyfrowania AES 256-bitowego.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Ogólnie rzecz biorąc, pełne kopie zapasowe są wykonywane co tydzień, różnicowe kopie zapasowe występują dwa razy dziennie, oraz kopie zapasowe dziennika transakcji co pięć minut. Pierwsza pełna kopia zapasowa jest zaplanowane, natychmiast po utworzeniu serwera. Na serwerze przywróconej dużych początkowa kopia zapasowa może trwać dłużej. Najbliższy punkt w czasie, którą można przywrócić do nowego serwera to czas, jaką zakończeniu wstępnej pełnej kopii zapasowej.

### <a name="backup-redundancy-options"></a>Opcje tworzenia kopii zapasowej redundancji

Bazy danych platformy Azure dla PostgreSQL zapewnia elastyczność wyboru między lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu kopii zapasowych w warstwach ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Gdy kopie zapasowe są przechowywane w geograficznie nadmiarowego magazynu kopii zapasowych, ich nie tylko znajdują się w regionie, w którym znajduje się serwer, ale również są replikowane do [centrum danych sparowanego](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Zapewnia to lepszą ochronę i możliwość przywracania w przypadku awarii serwera w innym regionie. Podstawowa warstwa oferuje tylko lokalnie nadmiarowego magazynu kopii zapasowej.

> [!IMPORTANT]
> Konfigurowanie lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu dla kopii zapasowej jest dozwolone tylko podczas server Utwórz. Po zainicjowaniu obsługi serwera, można zmienić opcji nadmiarowość magazynu kopii zapasowej.

### <a name="backup-storage-cost"></a>Koszt magazynowania kopii zapasowej

Bazy danych platformy Azure dla PostgreSQL zawiera maksymalnie 100% magazynu elastycznie serwera jako magazynu kopii zapasowej bez ponoszenia dodatkowych kosztów. Zazwyczaj jest to odpowiednie do przechowywania kopii zapasowych siedmiu dni. Wszelkie dodatkowe magazynu kopii zapasowej jest pobierana w miesiącu GB.

Na przykład jeśli po uprzednim udostępnieniu serwera z 250 GB, masz 250 GB miejsca do magazynowania kopii zapasowej bez dodatkowych opłat. Rozliczany magazynu ponad 250 GB.

## <a name="restore"></a>Przywracanie

W bazie danych Azure PostgreSQL wykonywanie przywracania tworzy nowy serwer z oryginalnego serwera kopii zapasowych.

Dostępne są dwa typy przywracania:

- **W momencie przywracania** jest dostępny z obu opcji tworzenia kopii zapasowej nadmiarowości i tworzy nowy serwer, w tym samym regionie co oryginalny serwer.
- **Geograficzne** jest dostępna tylko wtedy, gdy skonfigurowano serwer dla magazynu geograficznie nadmiarowego i umożliwia przywracanie serwera w innym regionie.

Szacowany czas odzyskiwania zależy od wielu czynników, takich jak bazy danych o rozmiarach, rozmiar dziennika transakcji, przepustowości sieci i liczba baz danych odzyskiwania w tym samym regionie, w tym samym czasie. Czas odzyskiwania jest zazwyczaj mniej niż 12 godzin.

> [!IMPORTANT]
> W przypadku usunięcia serwera, wszystkie bazy danych, które należą do tego serwera, również zostaną usunięte i nie może zostać odzyskany. Nie można przywrócić usuniętego serwera.

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od z opcji tworzenia kopii zapasowej nadmiarowość, można wykonać przywracanie do dowolnego punktu w czasie w okresie przechowywania kopii zapasowych. Serwer jest tworzony w tym samym regionie Azure co oryginalny serwer. Jest tworzony z oryginalnego serwera konfiguracji dla warstwy cenowej, obliczeniowe generacji, liczba vCores, rozmiar magazynu okres przechowywania kopii zapasowych i opcję tworzenia kopii zapasowej nadmiarowości.

W momencie przywracania jest przydatne w wielu scenariuszach. Na przykład, jeśli użytkownik przypadkowo usunął danych, porzuca ważne tabeli lub bazy danych, lub jeśli aplikacja przypadkowo zastępuje danych nieprawidłowych danych z powodu wada aplikacji.

Może być konieczne odczekanie dla następnej kopii zapasowej dziennika transakcji do wykonania, zanim będzie można przywrócić do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Jeśli skonfigurowano serwer z magazynu geograficznie nadmiarowego kopii zapasowych, można przywrócić serwer do innego regionu Azure, w którym usługa jest dostępna. Geograficzne jest domyślną opcją odzyskiwania, gdy serwer jest niedostępny z powodu zdarzenia w regionie, w którym znajduje się serwer. Jeśli na dużą skalę zdarzenia w wynikach regionu w niedostępności aplikacji bazy danych, można przywrócić serwer z magazynu geograficznie nadmiarowego kopii zapasowych na serwerze w innym regionie. Istnieje opóźnienie między gdy wykonywana jest kopia zapasowa i kiedy są replikowane do innego regionu. To opóźnienie może trwać do godziny, tak, w przypadku awarii można się do godzinę utraty danych.

### <a name="perform-post-restore-tasks"></a>Wykonaj zadania wykonywane po przywróceniu

Po przywróceniu z dowolnego z tych mechanizmów odzyskiwania należy wykonać następujące zadania w celu pobrania użytkowników i aplikacje kopii działa:

- Jeśli nowy serwer należy zamienić na oryginalnym serwerze, przekierowywać klientów i aplikacje klienckie na nowym serwerze
- Upewnij się, że reguły zapory poziomu serwera odpowiednie w przypadku użytkownikom łączenie z
- Upewnij się, odpowiednie logowania i uprawnień na poziomie bazy danych znajdują się w miejscu
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ciągłość prowadzenia działalności biznesowej, zobacz [omówienie ciągłości działalności biznesowej](concepts-business-continuity.md).
- Aby przywrócić do punktu w czasie przy użyciu portalu Azure, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu portalu Azure](howto-restore-server-portal.md).
- Aby przywrócić do punktu w czasie przy użyciu wiersza polecenia platformy Azure, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu interfejsu wiersza polecenia](howto-restore-server-cli.md).