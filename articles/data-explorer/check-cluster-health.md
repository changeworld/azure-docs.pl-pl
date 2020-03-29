---
title: Sprawdzanie kondycji klastra usługi Azure Data Explorer
description: W tym artykule opisano kroki monitorowania kondycji klastra usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861322"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Sprawdzanie kondycji klastra usługi Azure Data Explorer

Istnieje kilka czynników, które wpływają na kondycję klastra usługi Azure Data Explorer, w tym procesora CPU, pamięci i podsystemu dysku. W tym artykule przedstawiono kilka podstawowych kroków, które można wykonać, aby ocenić kondycję klastra.

1. Zaloguj się [https://dataexplorer.azure.com](https://dataexplorer.azure.com)do .

1. W lewym okienku wybierz klaster i uruchom następujące polecenie.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Wyjście 1 jest zdrowe; wyjście 0 jest niezdrowe.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i przejdź do klastra.

1. W obszarze **Monitorowanie**wybierz **pozycję Metryki**, a następnie wybierz pozycję **Zachowaj przy życiu**, jak pokazano na poniższej ilustracji. Dane wyjściowe bliskie 1 oznacza klaster w dobrej kondycji.

    ![Metryka Utrzymywanie aktywności klastra](media/check-cluster-health/portal-metrics.png)

1. Można dodać inne metryki do wykresu. Zaznacz wykres, a następnie **dodaj metrykę**. Wybierz inną metrykę - w tym przykładzie pokazano **procesora .**

    ![Dodawanie metryki](media/check-cluster-health/add-metric.png)

1. Jeśli potrzebujesz pomocy w diagnozowaniu problemów ze zdrowiem klastra, otwórz żądanie pomocy technicznej w [witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).