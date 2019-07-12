---
title: Wyświetlanie metryk z usługą Azure Monitor
description: W tym artykule pokazano, jak można monitorować metryki z usługi Azure portal wykresami i wiersza polecenia platformy Azure.
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
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795821"
---
# <a name="monitor-media-services-metrics"></a>Monitorowanie metryk usług Media Services 

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Szczegółowy opis tej funkcji i aby zobaczyć, dlaczego chcesz przy użyciu dzienników metryki i diagnostyka usługi Azure Media Services, zobacz [usługi Media Services monitora, metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

Usługa Azure Monitor zapewnia kilka sposobów na korzystanie z metryk, w tym wykresy je w portalu, uzyskując dostęp do nich za pośrednictwem interfejsu API REST lub ich zapytań przy użyciu interfejsu wiersza polecenia. W tym artykule pokazano, jak można monitorować metryki z usługi Azure portal wykresami i wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md)
- Przegląd [usługi Media Services monitora, metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Wyświetl metryki w witrynie Azure portal

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do swojego konta usługi Azure Media Services, a następnie wybierz pozycję **metryki**.
1. Kliknij przycisk **zasobów** pole, a następnie wybierz zasób, dla którego chcesz monitorować metryki. 

    **Wybierz zasób** zostanie wyświetlone okno po prawej stronie z listą zasobów dostępnych dla Ciebie. W takim przypadku zobaczysz 

    * &lt;Nazwa konta usługi Media Services&gt;
    * &lt;Nazwa konta usługi Media Services&gt;/&lt;przesyłania strumieniowego Nazwa punktu końcowego&gt;
    * &lt;Nazwa konta magazynu&gt;

    Wybierz zasób i naciśnij klawisz **Zastosuj**. Aby uzyskać szczegółowe informacje o obsługiwanych zasobów i metryk, zobacz [usługi Media Services monitora, metryk](media-services-metrics-diagnostic-logs.md).
 
    ![Metryki](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Aby przełączyć między zasobami, dla których chcesz monitorować metryki, kliknij polecenie **zasobów** pole ponownie, a następnie powtórz ten krok.
1. (Opcjonalnie) nazwij wykres (edit name, naciskając klawisz ołówka u góry).
1. Dodawanie metryki, które chcesz wyświetlić.

    ![Metryki](media/media-services-metrics/metrics03.png)
1. Możesz przypiąć wykres do pulpitu nawigacyjnego.

## <a name="view-metrics-with-azure-cli"></a>Wyświetlanie metryk z wiersza polecenia platformy Azure

Aby uzyskać metryki "Wyjście" z interfejsem wiersza polecenia, należy uruchomić następujące `az monitor metrics` interfejsu wiersza polecenia:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Aby uzyskać inne metryki, zastąp "Wyjście" Nazwa metryki, który Cię interesuje.

## <a name="see-also"></a>Zobacz także

* [Metryki usługi Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

[Dzienniki diagnostyczne](media-services-diagnostic-logs-howto.md)
