---
title: Migrowanie klasycznej alertów w usłudze Azure Monitor, przy użyciu narzędzia migracji dobrowolne
description: Dowiedz się, jak za pomocą narzędzia do migracji dobrowolne Migrowanie klasycznej reguły alertu.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632266"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Skorzystaj z narzędzia migracji dobrowolne migracji klasycznej reguły alertu

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w lipcu 2019 r. Narzędzie migracji, aby wyzwolić dobrowolnie migracji jest dostępne w witrynie Azure portal i wprowadza się klienci, którzy używają klasycznej reguły alertu. Ten artykuł przeprowadzi Cię na temat korzystania z narzędzia migracji dobrowolnie migracji regułami alertów klasycznych przed automatyczną migrację rozpoczyna się w lipcu 2019 r.

## <a name="benefits-of-new-alerts"></a>Zalety nowych alertów

Alertów klasycznych są zastępowane przez nowe ujednolicone zgłaszania alertów w usłudze Azure Monitor. Na nową platformę alertów ma następujące zalety:

- Alert dla różnych metryk wielowymiarowych dla [wiele nowych usług platformy Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Metryka nowe alerty pomocy technicznej [reguł alertów z wielu zasobów](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) który znacznie zmniejsza obciążenie związane z zarządzaniem wiele reguł.
- Mechanizm ujednoliconego powiadomień
  - [Grupy akcji](action-groups.md) to mechanizm modułowej powiadomień, który współdziała z wszystkich nowych typów alertów (metryk, dzienników i dziennika aktywności)
  - Ponadto można korzystać z zalet nowych mechanizmów wysyłania powiadomień, takie jak wiadomości SMS, głos i łącznik ITSM
- [Ujednolicone środowisko alertów](alerts-overview.md) udostępnia wszystkie alerty w różnym sygnałom (działanie metryk, dzienników i dzienników) w jednym miejscu

## <a name="before-you-migrate"></a>Przed przeprowadzeniem migracji

W ramach migracji klasyczne reguły alertów są konwertowane na równoważne nowe reguły alertu i są tworzone grupy akcji.

- Format ładunku powiadomień, a także interfejsów API do tworzenia i zarządzania nimi nowe reguły alertu różni się od tych reguł alertów klasycznych ponieważ obsługują one więcej funkcji. Dowiedz się, [sposób przygotowania do migracji](alerts-prepare-migration.md).

- Nie można migrować niektóre klasycznej reguły alertu, za pomocą narzędzia. [Dowiedz się, jakie reguły nie są ona i zobacz, jak migrować je](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Proces migracji nie będzie miało wpływ na ocenę klasycznej reguły alertu. Będą one nadal uruchomić i wysyłania alertów, do momentu ich migrowania i nowe reguły alertu rozpoczęcia ewaluacji.


## <a name="how-to-use-the-migration-tool"></a>Sposób użycia narzędzia migracji

Poniższa procedura opisuje sposób włączania migracji regułami alertów klasycznych w witrynie Azure portal:

1. W [witryny Azure portal](https://portal.azure.com), kliknij pozycję **Monitor**.

2. Kliknij przycisk **alerty** klikać **Zarządzaj regułami alertów** lub **wyświetlanie alertów klasycznych**.

3. Kliknij przycisk **Przeprowadź migrację do nowych zasad** można przejść do strony docelowej migracji. Ta strona pokazuje listę wszystkich subskrypcji i stan migracji dla nich.

    ![docelowa migracji](media/alerts-migration/migration-landing.png "migracji zasad")

4. Wszystkie subskrypcje, które można poddać migracji za pomocą narzędzia zostaną oznaczone jako **gotowa na migrację**.

    > [!NOTE]
    > Narzędzie migracji jest wprowadzane w fazach wszystkie subskrypcje, korzystających z klasycznej reguły alertu. W we wczesnej fazie wdrożenie mogą widzieć niektóre subskrypcje, co nie jest gotowy do migracji.

5. Wybierz co najmniej jedną subskrypcję, a następnie kliknij pozycję **migracji w wersji zapoznawczej**

6. Na tej stronie można wyświetlić szczegóły klasycznego reguł alertów, które będą migrowane do jednej subskrypcji w danym momencie. Możesz również **Pobierz szczegóły migracji dla tej subskrypcji** w formacie CSV.

    ![Podgląd migracji](media/alerts-migration/migration-preview.png "migracji w wersji zapoznawczej")

7. Podaj co najmniej jeden **adresy e-mail** Aby otrzymywać powiadomienia o stanie migracji. Wyślemy wiadomość e-mail po zakończeniu migracji lub wymaga akcję ze strony użytkownika.

8. Kliknij pozycję **Uruchom migrację**. Przeczytaj informacje wyświetlane w oknie dialogowym potwierdzenia i upewnij się, gdy wszystko będzie gotowe do rozpoczęcia procesu migracji.

    >[!IMPORTANT]
    > Po rozpoczęciu procesu migracji dla subskrypcji, nie można edytowania/tworzenia klasycznego reguł alertów dla subskrypcji. Jednak regułami alertów klasycznych będą nadal działa i podaje alerty do momentu zostaną zmigrowani. To jest zapewnienie jakości między reguł alertów klasycznych i nowych reguł utworzonych podczas migracji. Po zakończeniu migracji dla Twojej subskrypcji, nie można użyć klasycznej reguły alertu już.

    ![Potwierdź migracji](media/alerts-migration/migration-confirm.png "Potwierdź Uruchom migrację")

9. Jak możemy ukończyć migrację lub potrzebujesz akcję ze strony użytkownika, otrzymasz wiadomość e-mail na adresy e-mail, podany w kroku 8. Może również okresowo sprawdzać stan ze stroną docelową migracji w portalu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Dlaczego mojej subskrypcji jest wymieniony jako gotowe do migracji?**

Narzędzie migracji jest wprowadzane w fazach dla wszystkich klientów. W wczesnych faz, większość lub wszystkie swoje subskrypcje, może być oznaczona jako **nie jest gotowy do migracji**. Jednak w połowie kwietnia, wszystkie subskrypcje powinny być gotowy do migracji.

Gdy subskrypcja będzie gotowe do migracji, właściciele subskrypcji otrzyma wiadomość e-mail z powiadomieniem dostępności narzędzia. Zorientować się, dla tego powiadomienia.

### <a name="who-can-trigger-the-migration"></a>**Kto może wyzwalać migracji?**

Użytkownicy, którzy mają roli Współautor monitorowania przypisane do nich na poziomie subskrypcji będzie można wyzwolić migracji. Dowiedz się więcej o [kontroli opartej na rolach dostęp dla procesu migracji](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Jak długo migracji ma wykonać?**

W przypadku większości subskrypcji migracji zazwyczaj kończy się mniej niż godzinę. Można śledzenie bieżącego postęp migracji na stronie docelowej migracji.  W tym czasie, należy zapewnić alerty nadal są uruchomione w systemie alertów klasycznych lub nowym.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Co mogę zrobić, jeśli wystąpił problem podczas migracji?**

Postępuj zgodnie z [Rozwiązywanie problemów z przewodnikiem, aby zobaczyć kroki korygowania problemów, które mogą występować podczas migracji](alerts-understand-migration.md#common-issues-and-remediations). W razie potrzeby dowolną akcję ze strony użytkownika do ukończenia migracji będzie otrzymywać powiadomienia o adresy e-mail podany podczas migracji.

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do migracji](alerts-prepare-migration.md)
- [Zrozumienie sposobu działania narzędzia migracji](alerts-understand-migration.md)
