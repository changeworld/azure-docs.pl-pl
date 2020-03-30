---
title: Ciągłość działania — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano ciągłość działania (przywracanie punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981930"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Omówienie ciągłości działania dzięki usłudze Azure Database for PostgreSQL — single server

W tym opisie opisano możliwości, które usługa Azure Database for PostgreSQL zapewnia ciągłość działania i odzyskiwanie po awarii. Dowiedz się więcej o opcjach odzyskiwania po zdarzeniach zakłócających działanie, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Usługa Azure Database for PostgreSQL zapewnia funkcje ciągłości biznesowej, które obejmują automatyczne tworzenie kopii zapasowych i możliwość inicjowania przywracania geograficznego przez użytkowników. Każdy z nich ma inną charakterystykę szacowanego czasu odzyskiwania (ERT) i potencjalnej utraty danych. Po zrozumieniu tych opcji, można wybrać spośród nich i używać ich razem dla różnych scenariuszy. W miarę opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, zanim aplikacja zostanie w pełni odzyskana po przełomowym zdarzeniu — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość ostatnich aktualizacji danych (przedział czasu) aplikacja może tolerować utraty podczas odzyskiwania po zdarzenie zakłócające — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano ert i rpo dla dostępnych funkcji:

| **Możliwości** | **Podstawowa (Basic)** | **Cel ogólny** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z replikowanych geograficznie kopii zapasowych | Nieobsługiwane | ERT < 12 godz.<br/>RPO < 1 godz. | ERT < 12 godz.<br/>RPO < 1 godz. |

> [!IMPORTANT]
> Nie **można** przywrócić usuniętych serwerów. Jeśli usuniesz serwer, wszystkie bazy danych, które należą do serwera są również usuwane i nie można odzyskać. Użyj [blokady zasobów platformy Azure,](../azure-resource-manager/management/lock-resources.md) aby zapobiec przypadkowemu usunięciu serwera.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi, aby odzyskać serwer z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo upuścić ważną tabelę, a nawet upuścić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane ze złymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać **przywracanie punktu w czasie,** aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi znajdować się w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowo przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera do oryginalnego serwera.

## <a name="recover-from-an-azure-data-center-outage"></a>Odzyskiwanie po awarii centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje zakłócenia biznesowe, które mogą trwać tylko kilka minut, ale może trwać wiele godzin.

Jedną z opcji jest oczekiwanie na powrót serwera do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą sobie pozwolić na serwer w trybie offline przez pewien okres czasu, na przykład środowiska programistycznego. Gdy centrum danych ma awarię, nie wiadomo, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez jakiś czas.

## <a name="geo-restore"></a>Przywracanie geograficzne

Funkcja przywracania geograficznego przywraca serwer przy użyciu geob nadmiarowych kopii zapasowych. Kopie zapasowe są hostowane w [regionie sparowanym](../best-practices-availability-paired-regions.md)serwera. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu. Przywracanie geograficzne tworzy nowy serwer z danymi z kopii zapasowych. Dowiedz się więcej o przywracaniu geograficznym z [artykułu .](concepts-backup.md)

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko wtedy, gdy serwer jest aprowizowany z geograficznie nadmiarowym magazynem kopii zapasowych. Jeśli chcesz przełączyć się z lokalnie nadmiarowych na geob nadmiarowe kopie zapasowe dla istniejącego serwera, należy wykonać zrzut przy użyciu pg_dump istniejącego serwera i przywrócić go na nowo utworzonym serwerze skonfigurowanym przy użyciu geograficznie nadmiarowych kopii zapasowych.

## <a name="cross-region-read-replicas"></a>Repliki odczytu między regionami
Repliki odczytu między regionami umożliwiają zwiększenie ciągłości działania i planowania odzyskiwania po awarii. Repliki odczytu są aktualizowane asynchronicznie przy użyciu technologii replikacji fizycznej PostgreSQL. Dowiedz się więcej o replikach odczytu, dostępnych regionach i sposobie pracy awaryjnej w [artykule Pojęć replik odczytu](concepts-read-replicas.md). 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [automatycznych kopiach zapasowych w usłudze Azure Database for PostgreSQL](concepts-backup.md). 
- Dowiedz się, jak przywrócić przy użyciu [witryny Azure portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](howto-restore-server-cli.md)
- Dowiedz się więcej o [odczytywania replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).