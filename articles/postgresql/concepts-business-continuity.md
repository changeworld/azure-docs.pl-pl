---
title: Omówienie ciągłości biznesowej z usługą Azure Database for PostgreSQL — pojedynczy serwer
description: Omówienie ciągłości biznesowej z usługą Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: a31112f3b6f7bd79785f89822e2881b152708254
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068933"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Omówienie ciągłości biznesowej z usługą Azure Database for PostgreSQL — pojedynczy serwer

W tym omówieniu opisano możliwości usługi Azure Database for PostgreSQL zapewnia ciągłość biznesową i odzyskiwanie po awarii. Poznaj opcje odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, które można użyć w celu zapewnienia ciągłości działania

Usługa Azure Database for PostgreSQL udostępnia funkcje zachowywania ciągłości biznesowej, obejmujących automatycznych kopii zapasowych i przez użytkowników, aby zainicjować przywracanie geograficzne. Każdy ma różne charakterystyki dotyczące szacowany czas odzyskiwania (ERT, Distributed File System) i potencjalnej utraty danych. Po zrozumieniu te opcje można wybrać między nimi, a używane razem w różnych scenariuszach. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość danych najnowszych aktualizacji (przedział czasu) aplikacja może tolerować momencie odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano wartości ERT i RPO dla dostępne funkcje:

| **Możliwości** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z kopii zapasowych z replikacją geograficzną | Nieobsługiwane | ERT < 12 godz.<br/>RPO < 1 godz. | ERT < 12 godz.<br/>RPO < 1 godz. |

> [!IMPORTANT]
> Usunięto serwerów **nie** można przywrócić. Jeśli usuniesz serwera, wszystkie bazy danych, które należą do serwera również zostaną usunięte i nie można odzyskać.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Możesz użyć usługi tworzenia kopii zapasowych, aby odzyskać serwer z różnych zdarzeń powodujących zakłócenia. Użytkownik może przypadkowo usunąć pewne dane, nieodwracalnie usunąć ważną tabelę lub nawet usunąć całą bazę danych. Aplikacja może przypadkowo zastąpić poprawnych danych błędnymi danymi wady aplikacji i tak dalej.

Można wykonywać w czasie — przywracania do punktu do utworzenia kopii serwera do znany dobry punkt w czasie. Ten punkt w czasie musi być w ramach okresu przechowywania kopii zapasowej, które zostały skonfigurowane dla serwera. Po przywróceniu danych do nowego serwera, możesz zastąpić oryginalny serwer nowo przywróconego serwera lub skopiować potrzebne dane z serwera przywrócone na oryginalnym serwerze.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii centrum danych platformy Azure regionalne

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale może trwać wiele godzin.

Jedną z opcji jest oczekiwania serwera do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które można sobie pozwolić, aby serwer w trybie offline przez pewien czas, na przykład w środowisku deweloperskim. Centrum danych po awarii nie wiesz, jak długo może trwać awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz serwera przez chwilę.

Inną opcją jest do użycia usługi Azure Database dla PostgreSQL funkcji przywracania geograficznego, która przywraca serwer za pomocą geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne, nawet wtedy, gdy w regionie, w którym serwer znajduje się w jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przełączyć serwer w tryb online.

> [!IMPORTANT]
> Funkcja przywracania geograficznego jest możliwe tylko wtedy, jeśli zainicjowano obsługę administracyjną serwera z magazynu geograficznie nadmiarowego magazynu kopii zapasowych. Jeśli chcesz przełączyć się z lokalnie nadmiarowy geograficznie nadmiarowych kopii zapasowych dla istniejącego serwera, należy wykonać zrzut przy użyciu pg_dump istniejącego serwera i go przywrócić do nowo utworzonego skonfigurowane za pomocą geograficznie nadmiarowych kopii zapasowych.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [automatyczne kopie zapasowe w usłudze Azure Database for PostgreSQL](concepts-backup.md). 
- Dowiedz się, jak przywrócić dane przy użyciu [witryny Azure portal](howto-restore-server-portal.md) lub [wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Dowiedz się więcej o [odczytu replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).