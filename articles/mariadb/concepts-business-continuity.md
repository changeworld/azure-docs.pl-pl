---
title: Ciągłość działania — usługa Azure Database for MariaDB
description: Dowiedz się więcej o ciągłości biznesowej (przywracanie punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532395"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Opis ciągłości działania w usłudze Azure Database for MariaDB

W tym artykule opisano możliwości, które usługa Azure Database for MariaDB zapewnia ciągłość działania i odzyskiwanie po awarii. Dowiedz się więcej o opcjach odzyskiwania po zdarzeniach zakłócających działanie, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Usługa Azure Database for MariaDB zapewnia funkcje ciągłości biznesowej, które obejmują automatyczne tworzenie kopii zapasowych i możliwość inicjowania przywracania geograficznego przez użytkowników. Każdy z nich ma inną charakterystykę szacowanego czasu odzyskiwania (ERT) i potencjalnej utraty danych. Po zrozumieniu tych opcji, można wybrać spośród nich i używać ich razem dla różnych scenariuszy. W miarę opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, zanim aplikacja zostanie w pełni odzyskana po przełomowym zdarzeniu — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość ostatnich aktualizacji danych (przedział czasu) aplikacja może tolerować utraty podczas odzyskiwania po zdarzenie zakłócające — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano ert i rpo dla dostępnych funkcji:

| **Możliwości** | **Podstawowa (Basic)** | **Cel ogólny** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z replikowanych geograficznie kopii zapasowych | Nieobsługiwane | ERT < 12 godz.<br/>RPO < 1 godz. | ERT < 12 godz.<br/>RPO < 1 godz. |

> [!IMPORTANT]
> Jeśli usuniesz serwer, wszystkie bazy danych zawarte na serwerze są również usuwane i nie można ich odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi, aby odzyskać serwer z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo upuścić ważną tabelę, a nawet upuścić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane ze złymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać przywracanie punktu w czasie, aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi znajdować się w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowo przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera do oryginalnego serwera.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii regionalnego centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje zakłócenia biznesowe, które mogą trwać tylko kilka minut, ale może trwać wiele godzin.

Jedną z opcji jest oczekiwanie na powrót serwera do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą sobie pozwolić na serwer w trybie offline przez pewien okres czasu, na przykład środowiska programistycznego. Gdy centrum danych ma awarię, nie wiadomo, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez jakiś czas.

Inną opcją jest użycie usługi Azure Database dla funkcji przywracania geograficznego MariaDB, która przywraca serwer przy użyciu geob nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym znajduje się serwer, jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer do trybu online.

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko wtedy, gdy serwer jest aprowizowany z geograficznie nadmiarowym magazynem kopii zapasowych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych, zobacz [Kopie zapasowe w usłudze Azure Database for MariaDB](concepts-backup.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywracanie bazy danych do punktu w czasie przy użyciu witryny Azure portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
