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
ms.devlang: multiple
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 05958f35f80a53da27e020d367799519ef5a9bd7
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901587"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Usługa Application Insights dla usługi Azure Functions obsługiwane funkcje

Poniżej przedstawiono listę aktualnie obsługiwanych funkcji [Integracja usługi Application Insights z usługą Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring). Przejrzyj przewodnik usługi Azure Functions dla [wprowadzenie](https://github.com/Azure/Azure-Functions/wiki/App-Insights).


| Azure Functions                       | Wersja 1                | W wersji 2 (Konferencja Ignite 2018 r.)  | 
|-----------------------------------    |---------------    |------------------ |
| **Usługa Application Insights SDK platformy .NET**         | **2.5.0**             | **2.7.2**                 |
| | | | 
| **Automatyczne zbieranie**              |                   |                   |               
| &bull; Żądania                           | Yes               | Yes               | 
| &bull; Wyjątki                         | Yes               | Yes               | 
| &bull; Zależności               |                   |                   |               
| &mdash; HTTP                              |                   | Yes               | 
| &mdash; magistrali usług                        |                   | Yes               | 
| &mdash; Centrum zdarzeń                          |                   | Yes               | 
| &mdash; SQL                               |                   | Yes               | 
| | | | 
| **Obsługiwane funkcje**                    |                   |                   |               
| &bull; QuickPulse/LiveMetrics                         | Yes               | Yes               | 
| &bull; Próbkowania                           | Yes               | Yes               | 
| &bull; Puls                         |       | Yes               | 
| | | | 
| **Korelacja**                           |                   |                   |               
| &bull; magistrali usług                         |                   | Yes               | 
| &bull; Centrum zdarzeń                           |                   | Yes               | 
| | | | 
| **Można konfigurować**                  |                   |                   |           
| &bull;W pełni konfigurowalne.<br/>Zobacz [usługi Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) instrukcje.<br/>Zobacz [platformy Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) dla wszystkich opcji.               |                   | Yes                   | 
