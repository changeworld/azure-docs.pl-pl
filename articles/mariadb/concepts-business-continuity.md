---
title: Omówienie ciągłości biznesowej z usługą Azure Database dla serwera MariaDB
description: Omówienie ciągłości biznesowej z usługą Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043020"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Omówienie ciągłości biznesowej z usługą Azure Database dla serwera MariaDB

W tym omówieniu opisano możliwości usługi Azure Database dla serwera MariaDB zapewnia ciągłość biznesową i odzyskiwanie po awarii. Poznaj opcje odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, które można użyć w celu zapewnienia ciągłości działania

Usługa Azure Database dla serwera MariaDB udostępnia funkcje zachowywania ciągłości biznesowej, obejmujących automatycznych kopii zapasowych i przez użytkowników, aby zainicjować przywracanie geograficzne. Każdy ma różne charakterystyki dotyczące szacowany czas odzyskiwania (ERT, Distributed File System) i potencjalnej utraty danych. Po zrozumieniu te opcje można wybrać między nimi, a używane razem w różnych scenariuszach. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość danych najnowszych aktualizacji (przedział czasu) aplikacja może tolerować momencie odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano wartości ERT i RPO dla dostępne funkcje:

| **Możliwości** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z kopii zapasowych z replikacją geograficzną | Nieobsługiwane | ERT < 12 godz.<br/>RPO < 1 godz. | ERT < 12 godz.<br/>RPO < 1 godz. |

> [!IMPORTANT]
> Usunięcie serwera wszystkich baz danych znajdujących się na serwerze zostaną również usunięte i nie można go odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Możesz użyć usługi tworzenia kopii zapasowych, aby odzyskać serwer z różnych zdarzeń powodujących zakłócenia. Użytkownik może przypadkowo usunąć pewne dane, nieodwracalnie usunąć ważną tabelę lub nawet usunąć całą bazę danych. Aplikacja może przypadkowo zastąpić poprawnych danych błędnymi danymi wady aplikacji i tak dalej.

Można wykonywać w czasie — przywracania do punktu do utworzenia kopii serwera do znany dobry punkt w czasie. Ten punkt w czasie musi być w ramach okresu przechowywania kopii zapasowej, które zostały skonfigurowane dla serwera. Po przywróceniu danych do nowego serwera, możesz zastąpić oryginalny serwer nowo przywróconego serwera lub skopiować potrzebne dane z serwera przywrócone na oryginalnym serwerze.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii centrum danych platformy Azure regionalne

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale może trwać wiele godzin.

Jedną z opcji jest oczekiwania serwera do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które można sobie pozwolić, aby serwer w trybie offline przez pewien czas, na przykład w środowisku deweloperskim. Centrum danych po awarii nie wiesz, jak długo może trwać awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz serwera przez chwilę.

Inną opcją jest używanie bazy danych Azure dla funkcji przywracania geograficznego firmy MariaDB, która przywraca serwer za pomocą geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne, nawet wtedy, gdy w regionie, w którym serwer znajduje się w jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przełączyć serwer w tryb online.

> [!IMPORTANT]
> Funkcja przywracania geograficznego jest możliwe tylko wtedy, jeśli zainicjowano obsługę administracyjną serwera z magazynu geograficznie nadmiarowego magazynu kopii zapasowych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych, zobacz [kopii zapasowych w usłudze Azure Database dla serwera MariaDB](concepts-backup.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywrócić bazę danych do punktu w czasie za pomocą witryny Azure portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
