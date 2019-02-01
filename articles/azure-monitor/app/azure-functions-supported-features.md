---
title: Usługa Azure Application Insights — usługi Azure Functions obsługiwane funkcje | Dokumentacja firmy Microsoft
description: Usługa Application Insights obsługiwane funkcje dotyczące usługi Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: dd28bc3925b0f07a441c46a26498ef1a14c3e650
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510327"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Usługa Application Insights dla usługi Azure Functions obsługiwane funkcje

Platforma Azure oferuje funkcje [wbudowana integracja](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) za pomocą usługi Application Insights, który jest dostępny za pośrednictwem interfejsu ILogger. Poniżej znajduje się lista aktualnie obsługiwanych funkcji. Przejrzyj przewodnik usługi Azure Functions dla [wprowadzenie](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Obsługiwane funkcje

| Azure Functions                       | Wersja 1                | W wersji 2 (Konferencja Ignite 2018 r.)  | 
|-----------------------------------    |---------------    |------------------ |
| **Usługa Application Insights SDK platformy .NET**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Automatyczne zbieranie**        |                 |                   |               
| &bull; Żądania                     | Yes             | Yes               | 
| &bull; Wyjątki                   | Yes             | Yes               | 
| &bull; Liczniki wydajności         | Yes             |                   |
| &bull; Zależności                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Centrum zdarzeń  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Yes               | 
| | | | 
| **Obsługiwane funkcje**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Yes             | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Bezpieczny kanał kontrolny|                 | Yes               | 
| &bull; Próbkowania                     | Yes             | Yes               | 
| &bull; Puls                   |                 | Yes               | 
| | | | 
| **Korelacja**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Yes               | 
| &bull; Centrum zdarzeń                       |                   | Yes               | 
| | | | 
| **Można konfigurować**                      |                   |                   |           
| &bull;W pełni konfigurowalne.<br/>Zobacz [usługi Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) instrukcje.<br/>Zobacz [platformy Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) dla wszystkich opcji.               |                   | Yes                   | 


## <a name="performance-counters"></a>Liczniki wydajności

Automatyczne zbieranie liczników wydajności działają tylko Windows maszyn.


## <a name="live-metrics--secure-control-channel"></a>Metryki na żywo i bezpiecznego kanału kontroli

Filtry niestandardowe określonych kryteriów są wysyłane do składnika metryki na żywo w zestaw SDK usługi Application Insights. Filtry potencjalnie mogą zawierać poufne informacje, takie jak customerIDs. Kanał można zabezpieczyć przy użyciu klucza tajnego klucza interfejsu API. Zobacz [bezpieczny kanał kontrolny](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) instrukcje.

## <a name="sampling"></a>Próbkowanie

Usługa Azure Functions umożliwia pobieranie próbek domyślnie w swojej konfiguracji. Aby uzyskać więcej informacji, zobacz [skonfigurować próbkowania](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
