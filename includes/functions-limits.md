---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: f2470f937d2d812bf79cea3c23d89a50717a5a92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277395"
---
| Resource | [Plan zużycia](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plan w warstwie Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plan usługi App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Skalowanie w poziomie | Oparte na zdarzeniach | Oparte na zdarzeniach | [Ręczne/automatycznego skalowania](../articles/app-service/web-sites-scale.md) | 
| Maksymalna liczba wystąpień | 200 | 20 | 10-20 |
|Domyślne [limit czasu](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Maksymalna liczba [limit czasu](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | niepowiązane | niepowiązane<sup>3</sup> |
| Maksymalna liczba połączeń wychodzących (na wystąpienie) | 600 aktywne (łącznie 1200) | niepowiązane | niepowiązane |
| Maksymalny rozmiar żądań (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maksymalna długość ciągu zapytania<sup>4</sup> | 4096 | 4096 | 4096 |
| Maksymalna długość adresu URL żądania<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) za wystąpienie usługi | 100 | 210-840 | 100-840 |
| Maksymalny rozmiar pamięci (GB na każde wystąpienie) | 1,5 | 3.5-14 | 1.75-14 |
| Aplikacje funkcji na plan |100 |100 |niepowiązane<sup>5</sup> |
| [Plany usługi App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [regionu](https://azure.microsoft.com/global-infrastructure/regions/) |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów |
| Storage<sup>6</sup> |1 GB |250 GB |50 1000 GB |
| Domeny niestandardowe na aplikację</a> |500<sup>7</sup> |500 |500 |
| Domena niestandardowa [Obsługa protokołu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |nieograniczone połączenia SNI SSL uwzględnione | nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń |nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń | 

<sup>1</sup> określone limity dla różnych opcji planu usługi App Service, zobacz [limity planu usługi App Service](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> domyślnie limitu czasu przez środowisko uruchomieniowe 1.x funkcji w ramach planu usługi App Service jest nieograniczona.  
<sup>3</sup> wymaga planu usługi App Service można ustawić [Always On](../articles/azure-functions/functions-scale.md#always-on). Płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> te limity są [ustawiony na hoście](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> rzeczywista liczba aplikacji funkcji, które możesz hostować zależy działanie aplikacji, rozmiaru wystąpienia maszyny i odpowiednie wykorzystanie zasobów.
<sup>6</sup> limit magazynu jest łączny rozmiar zawartości w magazyn tymczasowy dla wszystkich aplikacji w ten sam plan usługi App Service. Plan zużycie używa usługi Azure Files do tymczasowego przechowywania danych.  
<sup>7</sup> kiedy aplikacja funkcji znajduje się w [planu zużycie](../articles/azure-functions/functions-scale.md#consumption-plan), tylko opcja CNAME jest obsługiwana. Dla aplikacji funkcji w [plan w warstwie Premium](../articles/azure-functions/functions-scale.md#premium-plan) lub [planu usługi App Service](../articles/azure-functions/functions-scale.md#app-service-plan), można mapować domenę niestandardową przy użyciu rekordu A lub CNAME.
