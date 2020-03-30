---
title: Migrowanie klasycznych alertów w usłudze Azure Monitor przy użyciu narzędzia do migracji dobrowolnej
description: Dowiedz się, jak korzystać z narzędzia migracji dobrowolnej do migracji klasycznych reguł alertów.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 537d84639523a74cbd9403d4ad25c34a798b7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665106"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Użyj narzędzia dobrowolnej migracji do migracji klasycznych reguł alertów

Zgodnie [z wcześniejszymi zapowiedziami](monitoring-classic-retirement.md)klasyczne alerty w usłudze Azure Monitor są wycofywane we wrześniu 2019 r. (pierwotnie w lipcu 2019 r.). Narzędzie do migracji jest dostępne w witrynie Azure portal dla klientów, którzy używają klasycznych reguł alertów i którzy sami chcą wyzwolić migrację. Z tego artykułu dowiesz się, jak korzystać z narzędzia migracji do dobrowolnej migracji klasycznych reguł alertów przed rozpoczęciem automatycznej migracji we wrześniu 2019 r.

> [!NOTE]
> Ze względu na opóźnienie we wprowadzaniu narzędzia migracji data wycofania dla migracji alertów klasycznych została [przedłużona do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) r. z pierwotnie ogłoszonej daty 30 czerwca 2019 r.

## <a name="benefits-of-new-alerts"></a>Korzyści z nowych alertów

Klasyczne alerty są zastępowane przez nowe, ujednolicone alerty w usłudze Azure Monitor. Nowa platforma alertów ma następujące zalety:

- Możesz ostrzegać o różnych metrykach wielowymiarowych dla [wielu innych usług platformy Azure.](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Nowe alerty metryki obsługują [wiele zasobów alertów,](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) które znacznie zmniejszyć obciążenie związane z zarządzaniem wieloma regułami.
- Ujednolicony mechanizm powiadamiania, który obsługuje:
  - [Grupy akcji](action-groups.md), modułowy mechanizm powiadomień, który współpracuje ze wszystkimi nowymi typami alertów (metryka, dziennik i dziennik aktywności).
  - Nowe mechanizmy powiadamiania, takie jak SMS, voice i ITSM Connector.
- [Ujednolicone środowisko alertów](alerts-overview.md) przenosi wszystkie alerty na różnych sygnałach (metryka, dziennik i dziennik aktywności) w jednym miejscu.

## <a name="before-you-migrate"></a>Przed migracją

Proces migracji konwertuje klasyczne reguły alertów na nowe, równoważne reguły alertów i tworzy grupy akcji. W przygotowaniu należy pamiętać o następujących punktach:

- Zarówno format ładunku powiadomień, jak i interfejsy API do tworzenia nowych reguł alertów i zarządzania nimi różnią się od reguł alertów klasycznych, ponieważ obsługują one więcej funkcji. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Niektóre klasyczne reguły alertów nie mogą być migrowane za pomocą narzędzia. [Dowiedz się, które reguły nie mogą być migrowane i co z nimi zrobić.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Proces migracji nie wpłynie na ocenę klasycznych reguł alertów. Będą one nadal uruchamiać i wysyłać alerty, dopóki nie zostaną zmigrowane, a nowe reguły alertów zajmą się rzeczywistością.

## <a name="how-to-use-the-migration-tool"></a>Jak korzystać z narzędzia do migracji

Aby wyzwolić migrację klasycznych reguł alertów w witrynie Azure portal, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Monitor**.

1. Wybierz **pozycję Alerty**, a następnie wybierz pozycję **Zarządzaj regułami alertów** lub **Wyświetl alerty klasyczne**.

1. Wybierz **pozycję Migracja do nowych reguł,** aby przejść do strony docelowej migracji. Na tej stronie znajduje się lista wszystkich subskrypcji i ich stanu migracji:

    ![lądowanie na migracji](media/alerts-migration/migration-landing.png "Migrowanie reguł")

    Wszystkie subskrypcje, które można przeprowadzić za pomocą narzędzia, są oznaczone jako **Gotowe do migracji**.

    > [!NOTE]
    > Narzędzie migracji jest wdrażany w fazach do wszystkich subskrypcji, które używają klasycznych reguł alertów. We wczesnych fazach wdrażania mogą zostać wyświetlone niektóre subskrypcje oznaczone jako niegotowe do migracji.

1. Wybierz jedną lub więcej subskrypcji, a następnie wybierz pozycję **Podgląd migracji**.

    Wynikowa strona zawiera szczegóły klasycznych reguł alertów, które będą migrowane dla jednej subskrypcji naraz. Możesz również wybrać **pobierz szczegóły migracji dla tej subskrypcji,** aby uzyskać szczegółowe informacje w formacie CSV.

    ![podgląd migracji](media/alerts-migration/migration-preview.png "Podgląd migracji")

1. Określ jeden lub więcej adresów e-mail, które mają być powiadamiane o stanie migracji. Otrzymasz wiadomość e-mail po zakończeniu migracji lub jeśli potrzebne są jakieś działania od Ciebie.

1. Wybierz **pozycję Rozpocznij migrację**. Przeczytaj informacje wyświetlane w oknie dialogowym potwierdzenia i upewnij się, że jesteś gotowy do rozpoczęcia procesu migracji.

    > [!IMPORTANT]
    > Po zainicjowaniu migracji dla subskrypcji nie będzie można edytować ani tworzyć klasycznych reguł alertów dla tej subskrypcji. To ograniczenie gwarantuje, że żadne zmiany w klasycznych regułach alertów nie zostaną utracone podczas migracji do nowych reguł. Chociaż nie będzie można zmienić klasycznych reguł alertów, nadal będą one działać i wyświetlać alerty do czasu ich migracji. Po zakończeniu migracji dla subskrypcji nie można już używać klasycznych reguł alertów.

    ![migracji-confirm](media/alerts-migration/migration-confirm.png "Potwierdź rozpoczęcie migracji")

1. Po zakończeniu migracji lub jeśli wymagana jest akcja, otrzymasz wiadomość e-mail pod podanymi wcześniej adresami. Stan można również okresowo sprawdzać na stronie docelowej migracji w portalu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Dlaczego moja subskrypcja jest wyświetlana jako niegotowana do migracji?

Narzędzie migracji jest wdrażane dla klientów w fazach. We wczesnych fazach większość lub wszystkie subskrypcje mogą być oznaczone jako **nieprzygotowane do migracji.** 

Gdy subskrypcja stanie się gotowa do migracji, właściciel subskrypcji otrzyma wiadomość e-mail z informacją, że narzędzie jest dostępne. Miej oko na tę wiadomość.

### <a name="who-can-trigger-the-migration"></a>Kto może wywołać migrację?

Użytkownicy, którzy mają przypisaną rolę współautora monitorowania na poziomie subskrypcji, mogą wyzwolić migrację. [Dowiedz się więcej o kontroli dostępu opartej na rolach dla procesu migracji](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak długo potrwa migracja?

Migracja jest zakończona dla większości subskrypcji w mniej niż godzinę. Postęp migracji można śledzić na stronie docelowej migracji. Podczas migracji należy mieć pewność, że alerty są nadal uruchomione w klasycznym systemie alertów lub w nowym.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mogę zrobić, jeśli napotkam problem podczas migracji?

Aby uzyskać pomoc dotyczącą problemów, które mogą wystąpić podczas migracji, zapoznaj się z [przewodnikiem rozwiązywania](alerts-understand-migration.md#common-problems-and-remedies) problemów. Jeśli do ukończenia migracji jest potrzebna jakakolwiek akcja, otrzymasz powiadomienie o adresach e-mail podanych podczas konfigurowania narzędzia.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
