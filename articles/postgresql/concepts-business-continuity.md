---
title: Omówienie ciągłości działalności biznesowej z bazą danych Azure dla PostgreSQL
description: Omówienie ciągłości działalności biznesowej z bazą danych Azure dla PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1b981b650d75556f4521aaf0f089443bb88d064a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29693410"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Omówienie ciągłości działalności biznesowej z bazą danych Azure dla PostgreSQL

Ten przegląd zawiera opis możliwości, które bazy danych Azure PostgreSQL zapewnia ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii. Więcej informacji na temat opcji odzyskiwania z destrukcyjne zdarzenia, które mogą spowodować utratę danych lub spowodować bazy danych i aplikacji staną się niedostępne. Dowiedz się, co robić, gdy błąd użytkownika albo aplikacji wpływa na integralność danych, region platformy Azure ma awarii lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości działalności biznesowej

Bazy danych platformy Azure dla PostgreSQL zapewnia funkcjach ciągłości biznesowej, obejmujące automatyczne kopie zapasowe i przez użytkowników do inicjowania przywracania geo. Każdy ma różne charakterystyki szacowany czas odzyskiwania (Wstaw, Distributed File System) i ryzyko utraty danych. Po zrozumieniu te opcje można wybrać między nimi, a używane razem w różnych scenariuszach. Podczas opracowywania planu zapewnienia ciągłości działalności, należy zrozumieć maksymalny dopuszczalny czas oczekiwania aplikacji pełni odzyskuje po zdarzeniu destrukcyjne — jest to z celem czas odzyskiwania (RTO). Należy także zrozumieć maksymalna ilość danych ostatnie aktualizacje (interwał czasu) aplikacja może tolerować utraty podczas odzyskiwania po zdarzeniu destrukcyjne — jest to Twoje cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano Wstaw i cel punktu odzyskiwania dla dostępnych funkcji:

| **Możliwości** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Geograficznie — Przywracanie z kopii zapasowych z replikacją geograficzną | Nieobsługiwane | Wstaw < 12 godzin<br/>Cel punktu odzyskiwania < 1 h | Wstaw < 12 godzin<br/>Cel punktu odzyskiwania < 1 h |

> [!IMPORTANT]
> W przypadku usunięcia serwera, wszystkie bazy danych, które należą do tego serwera, również zostaną usunięte i nie może zostać odzyskany. Nie można przywrócić usuniętego serwera.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po wystąpieniu błędu użytkownik lub aplikacja

Możesz użyć usługi tworzenia kopii zapasowych, aby odzyskać serwer z różnych zdarzeń destrukcyjne. Użytkownik może przypadkowego usunięcia niektóre dane, Porzuć przypadkowo ważne tabeli lub nawet upuść całą bazę danych. Aplikacja może przypadkowo zastąpić danych nieprawidłowych danych z powodu wada aplikacji i tak dalej.

Można wykonywać na punkt w — czas przywracania Utwórz kopię serwera do znanego dobry punkt w czasie. Ten punkt w czasie musi być w okresie przechowywania kopii zapasowych, które zostały skonfigurowane dla serwera. Po przywróceniu danych na nowym serwerze, możesz zastąpić oryginalny serwer nowo przywrócony serwera lub skopiuj potrzebnych danych z serwera przywrócone na oryginalnym serwerze.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii centrum danych Azure regionalne

Sporadycznie centrum danych platformy Azure może mieć awarię. W przypadku wystąpienia awarii powoduje zakłócenia firm tylko może trwać kilka minut, ale może trwać godzin.

Jedną z opcji jest czekać na serwerze do trybu online po awarii centrum danych. Działa to w przypadku aplikacji, które można pozwolić sobie, aby serwer w trybie offline dla pewnym opóźnieniem od czasu, na przykład Środowisko deweloperskie. Centrum danych po awarii nie wiesz, jak długo może ostatniej awarii, więc ta opcja działa tylko, jeśli serwer nie ma potrzeby jakiś czas.

Druga opcja to korzystanie z bazy danych Azure dla funkcji geograficzne PostgreSQL firmy, która przywraca na serwerze przy użyciu kopii zapasowych geograficznie nadmiarowego. Te kopie zapasowe są dostępne, nawet wtedy, gdy region, który serwer znajduje się w jest w trybie offline. Można przywrócić z tych kopii zapasowych innego regionu i przełączyć serwer w tryb online.

> [!IMPORTANT]
> Geograficzne jest możliwe tylko wtedy, jeśli zainicjowano obsługę administracyjną serwera z magazynu geograficznie nadmiarowego kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat automatycznego tworzenia kopii zapasowych, zobacz [kopii zapasowych w bazie danych Azure PostgreSQL](concepts-backup.md). 
- Aby przywrócić do punktu w czasie przy użyciu portalu Azure, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu portalu Azure](howto-restore-server-portal.md).
- Aby przywrócić do punktu w czasie przy użyciu wiersza polecenia platformy Azure, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu interfejsu wiersza polecenia](howto-restore-server-cli.md).