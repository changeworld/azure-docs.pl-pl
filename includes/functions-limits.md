---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: d00af77a2d502cb534a4ab4a0db9231e5e8748b7
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "69636448"
---
| Resource | [Plan zużycia](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plan App Service](../articles/azure-functions/functions-scale.md#app-service-plan) <sup>1</sup> |
| --- | --- | --- | --- |
| Skalowanie w poziomie | Sterowane zdarzeniami | Sterowane zdarzeniami | [Ręczne/automatyczne skalowanie](../articles/app-service/manage-scale-up.md) | 
| Maksymalna liczba wystąpień | 200 | 20 | 10-20 |
|Domyślny [czas trwania](../articles/azure-functions/functions-scale.md#timeout) (w min) |5 | 30 |30<sup>2</sup> |
|Maksymalny czas [trwania](../articles/azure-functions/functions-scale.md#timeout) (w min) |10 | Unbounded | niepowiązane<sup>3</sup> |
| Maksymalna liczba połączeń wychodzących (na wystąpienie) | 600 aktywne (łącznie 1200) | Unbounded | Unbounded |
| Maksymalny rozmiar żądania (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maksymalna długość ciągu zapytania<sup>4</sup> | 4096 | 4096 | 4096 |
| Maksymalna długość adresu URL żądania<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) na wystąpienie | 100 | 210-840 | 100-840 |
| Maksymalna ilość pamięci (GB na wystąpienie) | 1,5 | 3,5-14 | 1,75-14 |
| Aplikacje funkcji na plan |100 |100 |niepowiązane<sup>5</sup> |
| [Plany usługi App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 na grupę zasobów |100 na grupę zasobów |
| Magazyn<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domeny niestandardowe na aplikację</a> |500<sup>7</sup> |500 |500 |
| [Obsługa protokołu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) domeny niestandardowej |uwzględniono niepowiązane połączenie SNI SSL | uwzględniono niepowiązane SNI SSL i 1 Połączenie SSL z adresu IP połączeń |uwzględniono niepowiązane SNI SSL i 1 Połączenie SSL z adresu IP połączeń | 

<sup>1</sup> Aby uzyskać określone limity dla różnych opcji planu App Service, zobacz [limity App Service planu](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> domyślnie przekroczenie limitu czasu dla funkcji 1. x środowiska uruchomieniowego w planie App Service jest niepowiązane.  
<sup>3</sup> wymaga, aby plan App Service był ustawiony na wartość [zawsze włączone](../articles/azure-functions/functions-scale.md#always-on). Płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/)standardowych.  
<sup>4</sup> te limity są [ustawiane na hoście](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> rzeczywista liczba aplikacji funkcji, które można hostować, zależy od aktywności aplikacji, rozmiaru wystąpień maszyn i odpowiedniego wykorzystania zasobów.  
<sup>6</sup> limit magazynowania to całkowity rozmiar zawartości w magazynie tymczasowym dla wszystkich aplikacji w ramach tego samego planu App Service. W planie użycia Azure Files tymczasowego magazynu.  
<sup>7</sup> Jeśli aplikacja funkcji jest hostowana w [planie zużycia](../articles/azure-functions/functions-scale.md#consumption-plan), obsługiwana jest tylko opcja CNAME. W przypadku aplikacji funkcji w [planie Premium](../articles/azure-functions/functions-scale.md#premium-plan) lub w [planie App Service](../articles/azure-functions/functions-scale.md#app-service-plan)można zmapować domenę niestandardową przy użyciu rekordu CNAME lub a.
