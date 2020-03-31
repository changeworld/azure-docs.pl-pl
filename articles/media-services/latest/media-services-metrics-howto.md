---
title: Wyświetlanie metryk za pomocą usługi Azure Monitor
description: W tym artykule pokazano, jak monitorować metryki za pomocą wykresów witryny Azure portal i interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382940"
---
# <a name="monitor-media-services-metrics"></a>Monitorowanie metryk usług Media Services

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Aby uzyskać szczegółowy opis tej funkcji i zobaczyć, dlaczego chcesz używać metryk i dzienników diagnostycznych usługi Azure Media Services, zobacz [Monitorowanie metryk usługi Media Services i dzienników diagnostycznych.](media-services-metrics-diagnostic-logs.md)

Usługa Azure Monitor udostępnia kilka sposobów interakcji z metrykami, w tym tworzenie wykresów w portalu, uzyskiwanie do nich dostępu za pośrednictwem interfejsu API REST lub wykonywanie zapytań za pomocą interfejsu wiersza polecenia platformy Azure. W tym artykule pokazano, jak monitorować metryki za pomocą wykresów witryny Azure portal i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie konta usługi Media Services](create-account-cli-how-to.md)
- Przeglądanie [metryk i dzienników diagnostycznych monitora usługi Media Services](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Wyświetlanie metryk w witrynie Azure portal

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do konta usługi Azure Media Services i wybierz **metryki**.
1. Kliknij pole **ZASÓB** i wybierz zasób, dla którego chcesz monitorować metryki.

    Po prawej stronie pojawi się okno **Wybierz zasób** z dostępną listą zasobów. W tym przypadku widzisz:

    * &lt;Nazwa konta usługi Media Services&gt;
    * &lt;Nazwa punktu&gt;/&lt;końcowego przesyłania strumieniowego konta usługi Media Services&gt;
    * &lt;nazwa konta magazynu&gt;

    Wybierz zasób i naciśnij klawisz **Zastosuj**. Aby uzyskać szczegółowe informacje na temat obsługiwanych zasobów i metryk, zobacz [Monitorowanie metryk usługi Media Services](media-services-metrics-diagnostic-logs.md).

    ![Metryki](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Aby przełączać się między zasobami, dla których chcesz monitorować metryki, kliknij ponownie pole **ZASÓB** i powtórz ten krok.
1. (Opcjonalnie) nadaj wykresowi nazwę (edytuj nazwę, naciskając ołówek u góry).
1. Dodaj metryki, które chcesz wyświetlić.

    ![Metryki](media/media-services-metrics/metrics03.png)
1. Możesz przypiąć wykres do pulpitu nawigacyjnego.

## <a name="view-metrics-with-azure-cli"></a>Wyświetlanie metryk za pomocą interfejsu wiersza polecenia platformy Azure

Aby uzyskać metryki "Wychodzące" z platformą Azure `az monitor metrics` CLI, należy uruchomić następujące polecenie:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Aby uzyskać inne metryki, zastąp "Wychodzący" dla nazwy metryki, która Cię interesuje.

## <a name="see-also"></a>Zobacz też

* [Metryki usługi Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Tworzenie, wyświetlanie i zarządzanie alertami metryk za pomocą usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

[Dzienniki diagnostyczne](media-services-diagnostic-logs-howto.md)
