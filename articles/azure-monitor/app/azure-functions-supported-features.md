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
ms.openlocfilehash: 9ad0579ff9c25753b1e4816b80948b4d8d1232f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083067"
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
| &bull; Zależności                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; magistrali usług|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Centrum zdarzeń  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Yes               | 
| | | | 
| **Obsługiwane funkcje**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Yes             | Yes               | 
| &bull; Próbkowania                     | Yes             | Yes               | 
| &bull; Puls                   |                 | Yes               | 
| | | | 
| **Korelacja**                       |                   |                   |               
| &bull; magistrali usług                     |                   | Yes               | 
| &bull; Centrum zdarzeń                       |                   | Yes               | 
| | | | 
| **Można konfigurować**                      |                   |                   |           
| &bull;W pełni konfigurowalne.<br/>Zobacz [usługi Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) instrukcje.<br/>Zobacz [platformy Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) dla wszystkich opcji.               |                   | Yes                   | 


## <a name="sampling"></a>Próbkowanie

Usługa Azure Functions umożliwia pobieranie próbek domyślnie w swojej konfiguracji. Aby uzyskać więcej informacji, zobacz [skonfigurować próbkowania](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
