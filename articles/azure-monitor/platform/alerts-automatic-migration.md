---
title: Dowiedz się, jak działa proces automatycznej migracji alertów klasycznych usługi Azure Monitor
description: Dowiedz się, jak działa proces automatycznej migracji.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668251"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Opis procesu automatycznej migracji dla klasycznych reguł alertów

Zgodnie [z wcześniejszymi zapowiedziami](monitoring-classic-retirement.md)klasyczne alerty w usłudze Azure Monitor są wycofywane we wrześniu 2019 r. (pierwotnie w lipcu 2019 r.). W ramach procesu wycofywania [narzędzie do migracji](alerts-using-migration-tool.md) jest dostępne w witrynie Azure portal dla klientów, aby samodzielnie wyzwolić migrację. Jeśli narzędzie do migracji nie jest używane do 31 sierpnia 2019 r., usługa Azure Monitor rozpocznie proces automatycznej migracji alertów klasycznych od 1 września 2019 r.
W tym artykule oprowadza cię proces automatycznej migracji i pomaga rozwiązać wszelkie problemy, na które możesz napotkać.

  > [!NOTE]
  > Ten artykuł dotyczy tylko chmury publicznej platformy Azure. Proces wycofywania alertów klasycznych usługi Azure Monitor w chmurze azure dla instytucji rządowych i usłudze Azure China 21Vianet zostanie ogłoszony w przyszłości.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Co się stanie podczas automatycznego procesu migracji?

- Od **1 września 2019**r. klienci nie będą mogli tworzyć nowych klasycznych reguł alertów z wyjątkiem [niektórych wskaźników.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - W przypadku wyjątków klient może nadal tworzyć nowe klasyczne reguły alertów i używać klasycznych alertów do czerwca 2020 r.
- Od **1 września 2019**r. migracja alertów klasycznych będzie wyzwalana w partiach dla wszystkich klientów, którzy mają klasyczne alerty.
- Podobnie jak w przypadku narzędzia dobrowolnej migracji, niektóre klasyczne reguły alertów, które nie można migrować, pozostaną takie, jak są. Te klasyczne reguły alertów będą nadal obsługiwane do czerwca 2020 r. Jednak wszystkie nieprawidłowe klasyczne reguły alertów zostaną usunięte, ponieważ nie są funkcjonalne.
Wszystkie klasyczne reguły alertów, które monitorują usunięte zasoby docelowe lub [metryki, które nie są już obsługiwane,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) są uznawane za nieprawidłowe.
- Po rozpoczęciu migracji dla subskrypcji, chyba że występują jakiekolwiek problemy, migracja powinna zostać zakończona w ciągu godziny. Klienci mogą monitorować stan migracji w [bloku migracji w usłudze Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Właściciele subskrypcji otrzymają wiadomość e-mail po pomyślnym zakończeniu migracji.
- Jeśli podczas migracji wystąpią jakiekolwiek problemy, właściciele subskrypcji otrzymają również wiadomość e-mail z informacją o tym samym. Klienci mogą użyć bloku migracji, aby wyświetlić pełne szczegóły problemu.
- W przypadku, gdy potrzebna jest akcja od klienta, taka jak tymczasowe wyłączenie blokady zasobów lub zmiana przypisania zasad, klienci będą musieli rozwiązać wszelkie problemy do 31 października 2019 r. Jeśli problemy nie zostaną rozwiązane do tego czasu, pomyślna migracja klasycznych alertów nie może być zagwarantowana.

    > [!NOTE]
    > Jeśli nie chcesz czekać na uruchomienie procesu automatycznej migracji, nadal możesz wyzwolić migrację dobrowolnie za pomocą narzędzia migracji.

## <a name="important-things-to-note"></a>Ważne rzeczy, na które należy zwrócić uwagę

Proces migracji konwertuje klasyczne reguły alertów na nowe, równoważne reguły alertów i tworzy grupy akcji. W przygotowaniu należy pamiętać o następujących punktach:

- Formaty ładunku powiadomień dla nowych reguł alertów różnią się od tych z klasycznych reguł alertów, ponieważ obsługują one więcej funkcji. Jeśli masz aplikacje logiki, elementy runbook lub elementy webhook, które są wyzwalane przez klasyczną regułę alertu, mogą przestać działać zgodnie z oczekiwaniami po zakończeniu migracji z powodu różnic w ładunkach powiadomień. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Niektóre klasyczne reguły alertów nie mogą być migrowane za pomocą narzędzia. [Dowiedz się, których reguł nie można migrować i co z nimi zrobić.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Proces migracji nie wpłynie na ocenę klasycznych reguł alertów. Będą one nadal uruchamiać i wysyłać alerty, dopóki nie zostaną zmigrowane, a nowe reguły alertów zajmą się rzeczywistością.

## <a name="what-if-the-automatic-migration-fails"></a>Co zrobić, jeśli automatyczna migracja nie powiedzie się?

Gdy proces automatycznej migracji zakończy się niepowodzeniem, właściciele subskrypcji otrzymają wiadomość e-mail z powiadomieniem o problemie. Bloku migracji można użyć w usłudze Azure Monitor, aby wyświetlić pełne szczegóły problemu.

Aby uzyskać pomoc dotyczącą problemów, które mogą wystąpić podczas migracji, zapoznaj się z [przewodnikiem rozwiązywania](alerts-understand-migration.md#common-problems-and-remedies) problemów.

  > [!NOTE]
  > W przypadku, gdy potrzebna jest akcja od klienta, taka jak tymczasowe wyłączenie blokady zasobów lub zmiana przypisania zasad, klienci będą musieli rozwiązać wszelkie problemy do 31 października 2019 r. Jeśli problemy nie zostaną rozwiązane do tego czasu, pomyślna migracja klasycznych alertów nie może być zagwarantowana.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
