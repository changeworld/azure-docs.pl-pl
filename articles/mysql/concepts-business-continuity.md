---
title: Omówienie ciągłości biznesowej z usługą Azure Database for MySQL
description: Omówienie ciągłości działania usługi Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8d0a8ac1aedcac26f6ac67e0e1f2a9d265a9b576
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714348"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql"></a>Omówienie ciągłości biznesowej z usługą Azure Database for MySQL

W tym omówieniu opisano możliwości usługi Azure Database for MySQL zapewnia ciągłość biznesową i odzyskiwanie po awarii. Poznaj opcje odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, które można użyć w celu zapewnienia ciągłości działania

Usługa Azure Database for MySQL zapewnia funkcje zachowywania ciągłości biznesowej, obejmujących automatycznych kopii zapasowych i przez użytkowników, aby zainicjować przywracanie geograficzne. Każdy ma różne charakterystyki dotyczące szacowany czas odzyskiwania (ERT, Distributed File System) i potencjalnej utraty danych. Po zrozumieniu te opcje można wybrać między nimi, a używane razem w różnych scenariuszach. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość danych najnowszych aktualizacji (przedział czasu) aplikacja może tolerować momencie odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia — jest to Twoja cel punktu odzyskiwania (RPO).

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

Inną opcją jest korzystanie z bazy danych Azure MySQL funkcji przywracania geograficznego, która przywraca serwer za pomocą geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne, nawet wtedy, gdy w regionie, w którym serwer znajduje się w jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przełączyć serwer w tryb online.

> [!IMPORTANT]
> Funkcja przywracania geograficznego jest możliwe tylko wtedy, jeśli zainicjowano obsługę administracyjną serwera z magazynu geograficznie nadmiarowego magazynu kopii zapasowych. Jeśli chcesz przełączyć się z lokalnie nadmiarowy geograficznie nadmiarowych kopii zapasowych dla istniejącego serwera, należy wykonać zrzut przy użyciu pg_dump istniejącego serwera i przywracania go w nowo utworzonym serwerem skonfigurowane za pomocą geograficznie nadmiarowych kopii zapasowych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych, zobacz [kopii zapasowych w usłudze Azure Database for MySQL](concepts-backup.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywrócić bazę danych do punktu w czasie za pomocą witryny Azure portal](howto-restore-server-portal.md).
- Aby przywrócić do punktu w czasie za pomocą wiersza polecenia platformy Azure, zobacz [przywrócić bazę danych do punktu w czasie za pomocą interfejsu wiersza polecenia](howto-restore-server-cli.md).