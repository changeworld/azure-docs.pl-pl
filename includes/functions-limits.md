---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198347"
---
| Zasób | [Plan Zużycie](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plan usługi aplikacji](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Skalowanie w poziomie | Sterowanie zdarzeniami | Sterowanie zdarzeniami | [Ręczna/automatyczna skala](../articles/app-service/manage-scale-up.md) | 
| Maksymalna liczba wystąpień | 200 | 100 | 10-20 |
|Domyślny [limit czasu](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Maksymalny [limit czasu](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | bez ograniczeń<sup>8</sup> | bez ograniczeń<sup>3</sup> |
| Maksymalna liczba połączeń wychodzących (na wystąpienie) | 600 aktywnych (łącznie 1200) | Nieograniczony | Nieograniczony |
| Maksymalny rozmiar żądania (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maksymalna długość ciągu zapytania<sup>4</sup> | 4096 | 4096 | 4096 |
| Maksymalna długość adresu URL żądania<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) na wystąpienie | 100 | 210-840 | 100-840 |
| Maksymalna ilość pamięci (GB na wystąpienie) | 1.5 | 3.5-14 | 1.75-14 |
| Aplikacje funkcyjne na plan |100 |100 |bez ograniczeń<sup>5</sup> |
| [Plany usługi App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 na grupę zasobów |100 na grupę zasobów |
| Przechowywanie<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domeny niestandardowe na aplikację</a> |500<sup>7</sup> |500 |500 |
| Obsługa [dysl SSL](../articles/app-service/configure-ssl-bindings.md) domeny niestandardowej |niezwiązane połączenie SNI SSL w zestawie | niezwiązane połączenia SNI SSL i 1 IP SSL w zestawie |niezwiązane połączenia SNI SSL i 1 IP SSL w zestawie | 

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu usługi app service, zobacz [limity planu usługi app service](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Domyślnie limit czasu dla funkcji 1.x środowiska uruchomieniowego w planie usługi app service jest nieograniczony.  
<sup>3</sup> Wymaga, aby plan usługi aplikacji był ustawiony [na Zawsze włączony](../articles/azure-functions/functions-scale.md#always-on). Płać [według stawek](https://azure.microsoft.com/pricing/details/app-service/)standardowych .  
<sup>4</sup> Te limity są [ustawione w hoście](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Rzeczywista liczba aplikacji funkcji, które można hostować zależy od aktywności aplikacji, rozmiar wystąpień komputera i odpowiednie wykorzystanie zasobów.  
<sup>6</sup> Limit miejsca to całkowity rozmiar zawartości w magazynie tymczasowym we wszystkich aplikacjach w tym samym planie usługi App Service. Plan użycia używa usługi Azure Files do magazynu tymczasowego.  
<sup>7</sup> Gdy aplikacja funkcji jest hostowana w [planie zużycia,](../articles/azure-functions/functions-scale.md#consumption-plan)obsługiwana jest tylko opcja CNAME. W przypadku aplikacji funkcyjnych w [planie Premium](../articles/azure-functions/functions-scale.md#premium-plan) lub planie usługi [app service](../articles/azure-functions/functions-scale.md#app-service-plan)można mapować domenę niestandardową przy użyciu cname lub rekordu A.  
<sup>8</sup> Gwarantowane do 60 minut.