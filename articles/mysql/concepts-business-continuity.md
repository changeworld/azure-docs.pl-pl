---
title: Ciągłość działania — usługa Azure Database for MySQL
description: Dowiedz się więcej o ciągłości biznesowej (przywracanie punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537231"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Opis ciągłości działania w usłudze Azure Database for MySQL

W tym artykule opisano możliwości, które usługa Azure Database for MySQL zapewnia ciągłość działania i odzyskiwanie po awarii. Dowiedz się więcej o opcjach odzyskiwania po zdarzeniach zakłócających działanie, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Usługa Azure Database for MySQL zapewnia funkcje ciągłości biznesowej, które obejmują automatyczne tworzenie kopii zapasowych i możliwość inicjowania przywracania geograficznego przez użytkowników. Każdy z nich ma inną charakterystykę szacowanego czasu odzyskiwania (ERT) i potencjalnej utraty danych. Po zrozumieniu tych opcji, można wybrać spośród nich i używać ich razem dla różnych scenariuszy. W miarę opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, zanim aplikacja zostanie w pełni odzyskana po przełomowym zdarzeniu — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość ostatnich aktualizacji danych (przedział czasu) aplikacja może tolerować utraty podczas odzyskiwania po zdarzenie zakłócające — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano ert i rpo dla dostępnych funkcji:

| **Możliwości** | **Podstawowa (Basic)** | **Cel ogólny** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z replikowanych geograficznie kopii zapasowych | Nieobsługiwane | ERT < 12 godz.<br/>RPO < 1 godz. | ERT < 12 godz.<br/>RPO < 1 godz. |

> [!IMPORTANT]
> Nie **można** przywrócić usuniętych serwerów. Jeśli usuniesz serwer, wszystkie bazy danych, które należą do serwera są również usuwane i nie można odzyskać.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi, aby odzyskać serwer z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo upuścić ważną tabelę, a nawet upuścić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane ze złymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać przywracanie punktu w czasie, aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi znajdować się w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowo przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera do oryginalnego serwera.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii regionalnego centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje zakłócenia biznesowe, które mogą trwać tylko kilka minut, ale może trwać wiele godzin.

Jedną z opcji jest oczekiwanie na powrót serwera do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą sobie pozwolić na serwer w trybie offline przez pewien okres czasu, na przykład środowiska programistycznego. Gdy centrum danych ma awarię, nie wiadomo, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez jakiś czas.

Inną opcją jest użycie funkcji przywracania geograficznego usługi Azure Database dla mysql, która przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym znajduje się serwer, jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer do trybu online.

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko wtedy, gdy serwer jest aprowizowany z geograficznie nadmiarowym magazynem kopii zapasowych. Jeśli chcesz przełączyć się z lokalnie nadmiarowych na geob nadmiarowe kopie zapasowe dla istniejącego serwera, musisz wykonać zrzut przy użyciu mysqldump istniejącego serwera i przywrócić go do nowo utworzonego serwera skonfigurowanego z geobeksoleganymi kopiami zapasowymi.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [automatycznych kopiach zapasowych w usłudze Azure Database for MySQL](concepts-backup.md).
- Dowiedz się, jak przywrócić przy użyciu [witryny Azure portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](howto-restore-server-cli.md)
- Dowiedz się więcej o [odczytywania replik w usłudze Azure Database for MySQL](concepts-read-replicas.md).
