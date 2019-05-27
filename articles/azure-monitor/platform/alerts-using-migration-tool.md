---
title: Migrowanie klasycznej alertów w usłudze Azure Monitor przy użyciu narzędzia do migracji dobrowolne
description: Dowiedz się, jak używać narzędzia dobrowolne migracji do migracji klasycznej reguły alertu.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 00229cca1d7fb238b330ec98cd35d0bb59bc821a
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015623"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Skorzystaj z narzędzia migracji dobrowolne migracji klasycznej reguły alertu

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w 2019 września (został pierwotnie 2019 lipca). Narzędzie migracji jest dostępne w portalu Azure, aby klienci używają reguł alertów klasycznych i którzy chcą wyzwolić migrację samodzielnie. W tym artykule wyjaśniono, jak używać narzędzia migracji do dobrowolnie migracji regułami alertów klasycznych, przed rozpoczęciem migracji automatyczne, w 2019 września.

> [!NOTE]
> Ze względu na opóźnienie wdrożenie narzędzie do migracji została dacie wycofania migracji alertów klasycznych [rozszerzony do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od daty pierwotnie ogłoszone się 30 czerwca 2019 r.

## <a name="benefits-of-new-alerts"></a>Zalety nowych alertów

Alertów klasycznych są zastępowane przez nowy, ujednolicony alertów w usłudze Azure Monitor. Na nową platformę alertów ma następujące zalety:

- Zgłoś alert, na różnych metryk wielowymiarowych [wiele nowych usług platformy Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Metryka nowe alerty pomocy technicznej [reguł alertów z wielu zasobów](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , znacznie zmniejsza obciążenie związane z zarządzaniem wiele reguł.
- Mechanizm powiadomień ujednoliconego obsługuje:
  - [Grupy akcji](action-groups.md), mechanizm powiadamiania modułowe, która współdziała z wszystkich nowych typów alertów (metryk, dzienników i dziennika działań).
  - Nowe mechanizmy powiadamiania, takie jak wiadomości SMS, głos i łącznik ITSM.
- [Ujednolicone środowisko alertów](alerts-overview.md) udostępnia wszystkie alerty w różnym sygnałom (metryk, dzienników i dziennika aktywności) w jednym miejscu.

## <a name="before-you-migrate"></a>Przed przeprowadzeniem migracji

Proces migracji klasycznej reguły alertu jest konwertowany na nowe, równoważne reguł alertów i tworzy grupy akcji. W ramach przygotowania należy pamiętać o następujących kwestiach:

- Format ładunku powiadomień i interfejsów API do tworzenia i zarządzania nimi nowe reguły alertu różnią się od tych reguł alertów klasycznych ponieważ obsługują one więcej funkcji. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Nie można migrować niektóre reguły alertów klasycznych za pomocą narzędzia. [Dowiedz się, jakie reguły nie mogą być migrowane i co należy zrobić z nimi](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Proces migracji nie będzie mieć wpływ na ocenę regułami alertów klasycznych. Będą one nadal uruchomić i wysyłania alertów, dopóki migrowaniu i nowe reguły alertu zaczęły obowiązywać.

## <a name="how-to-use-the-migration-tool"></a>Sposób użycia narzędzia migracji

Aby wyzwolić migracji regułami alertów klasycznych w witrynie Azure portal, wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Monitor**.

1. Wybierz **alerty**, a następnie wybierz pozycję **Zarządzaj regułami alertów** lub **wyświetlanie alertów klasycznych**.

1. Wybierz **Przeprowadź migrację do nowych zasad** można przejść do strony docelowej migracji. Ta strona zawiera listę wszystkich subskrypcji i ich stan migracji:

    ![docelowa migracji](media/alerts-migration/migration-landing.png "migracji zasad")

    Wszystkie subskrypcje, które można poddać migracji za pomocą narzędzia są oznaczone jako **gotowa na migrację**.

    > [!NOTE]
    > Narzędzie migracji jest wprowadzane w fazach wszystkie subskrypcje, korzystających z klasycznej reguły alertu. W wczesnych faz wprowadzania mogą widzieć niektóre subskrypcje oznaczona jako nie jest gotowy do migracji.

1. Wybierz co najmniej jedną subskrypcję, a następnie wybierz **migracji w wersji zapoznawczej**.

    Wynikowy strona pokazuje szczegóły klasycznego reguł alertów, które będą migrowane do jednej subskrypcji w danym momencie. Możesz również wybrać **Pobierz szczegóły migracji dla tej subskrypcji** Aby uzyskać szczegółowe informacje w formacie CSV.

    ![Podgląd migracji](media/alerts-migration/migration-preview.png "migracji w wersji zapoznawczej")

1. Określ co najmniej jeden adres e-mail, aby otrzymywać powiadomienia o migracji stanu. Otrzymasz wiadomość e-mail po zakończeniu migracji, czy wymaga żadnych akcji ze strony użytkownika.

1. Wybierz **Uruchom migrację**. Przeczytaj informacje wyświetlane w oknie dialogowym potwierdzenia i upewnij się, że jesteś gotowy do rozpoczęcia procesu migracji.

    > [!IMPORTANT]
    > Po zainicjowaniu migracji subskrypcji nie będziesz w stanie edytować lub tworzyć klasyczne reguły alertów dla tej subskrypcji. To ograniczenie zapewnia bez wprowadzania zmian w klasycznym reguł alertu zostaną utracone podczas migracji do nowych zasad. Mimo że nie będzie można zmienić regułami alertów klasycznych, nadal będą one nadal, uruchamiania i udostępnia alerty, dopóki nie zostały one migracji. Po zakończeniu migracji dla Twojej subskrypcji, już nie można użyć klasycznej reguły alertu.

    ![Potwierdź migracji](media/alerts-migration/migration-confirm.png "Potwierdź Uruchom migrację")

1. Po zakończeniu migracji lub akcji jest wymagana od Ciebie, otrzymasz wiadomość e-mail na adresy, które podano wcześniej. Może również okresowo sprawdzać stan w stroną docelową migracji w portalu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Dlaczego Moja subskrypcja jest wymieniony jako nie jest gotowy do migracji?

Narzędzie migracji jest wprowadzane klientów fazami. W wczesnych faz większości lub wszystkich subskrypcji, ponieważ może być oznaczona jako **nie jest gotowy do migracji**. 

Gdy subskrypcja będzie gotowe do migracji, właściciel subskrypcji otrzyma wiadomość e-mail z informacją o dostępnej narzędzie. Zorientować się, dla tego komunikatu.

### <a name="who-can-trigger-the-migration"></a>Kto może wyzwalać migracji?

Użytkownicy, którzy mają przypisane do nich na poziomie subskrypcji roli Współautor monitorowania mogą wyzwalać migracji. [Dowiedz się więcej na temat kontroli dostępu opartej na rolach, które proces migracji](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak długo będzie trwać migracji?

Migracja jest zakończona dla większości subskrypcji w mniej niż godzinę. Można śledzenie bieżącego postęp migracji na stronie docelowej migracji. Podczas migracji mieć pewność, że alerty nadal działają w systemie alertów klasycznych lub w nowym.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mogę zrobić, jeśli mam napotkania problemów podczas migracji?

Zobacz [przewodnik rozwiązywania problemów z](alerts-understand-migration.md#common-problems-and-remedies) Aby uzyskać pomoc dotyczącą problemów, które mogą występować podczas migracji. W razie potrzeby dowolną akcję ze strony użytkownika do ukończenia migracji zostanie wyświetlone powiadomienie o adresy e-mail, podane podczas konfigurowania narzędzia.

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do migracji](alerts-prepare-migration.md)
- [Zrozumienie sposobu działania narzędzia migracji](alerts-understand-migration.md)
