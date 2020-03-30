---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78305041"
---
| Zasób | Bezpłatna | Udostępnione | Podstawowa (Basic) | Standardowa | Premium (v2) | Izolowany </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplikacje sieci Web, urządzeń przenośnych lub interfejsu API](https://azure.microsoft.com/services/app-service/) zgodnie z planem usługi [Azure App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Nieograniczony<sup>2</sup> |Nieograniczony<sup>2</sup> |Nieograniczony<sup>2</sup> |Nieograniczony<sup>2</sup>|
| [Plan usługi aplikacji](../articles/app-service/overview-hosting-plans.md) |10 na region |10 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów|
| Oblicz typ wystąpienia |Udostępnione |Udostępnione |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup></p> |Dedykowane<sup>3</sup>|
| [Skalowanie w poziomie](../articles/app-service/manage-scale-up.md) (maksymalna liczba wystąpień) |1 udostępnione |1 udostępnione |3 dedykowane<sup>3</sup> |10 dedykowany<sup>3</sup> |30 dedykowany<sup>3</sup>|100 dedykowany<sup>4</sup>|
| Przechowywanie<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Czas procesora (5 minut)<sup>6</sup> |3 minuty |3 minuty |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Czas procesora (dzień)<sup>6</sup> |60 min. |240 min. |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczony, płać [według standardowych stawek](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Pamięć (1 godzina) |1024 MB na plan usługi aplikacji |1024 MB na aplikację |Nie dotyczy |Nie dotyczy |Nie dotyczy |Nie dotyczy |
| Przepustowość |165 MB |Nielimitowane, [obowiązują stawki za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) |Nielimitowane, [obowiązują stawki za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) |Nielimitowane, [obowiązują stawki za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) |Nielimitowane, [obowiązują stawki za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) |Nielimitowane, [obowiązują stawki za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Architektura aplikacji |32-bitowa |32-bitowa |32-bitowe/64-bitowe |32-bitowe/64-bitowe |32-bitowe/64-bitowe |32-bitowe/64-bitowe |
| Gniazda sieci Web na wystąpienie<sup>7</sup> |5 |35 |350 |Unlimited (nieograniczony) |Unlimited (nieograniczony) |Unlimited (nieograniczony) |
| Połączenia IP | 600 | 600 | W zależności od rozmiaru wystąpienia<sup>8</sup> | W zależności od rozmiaru wystąpienia<sup>8</sup> | W zależności od rozmiaru wystąpienia<sup>8</sup> | 16 000 |
| [Równoczesne połączenia debugera](../articles/app-service/troubleshoot-dotnet-visual-studio.md) dla aplikacji |1 |1 |1 |5 |5 |5 |
| Certyfikaty usługi app service na subskrypcję<sup>9</sup>| Nieobsługiwane | Nieobsługiwane |10 |10 |10 |10 |
| Domeny niestandardowe na aplikację</a> |0 (tylko azurewebsites.net subdomeny)|500 |500 |500 |500 |500 |
| Obsługa [dysl SSL](../articles/app-service/configure-ssl-certificate.md) domeny niestandardowej |Nie jest obsługiwany certyfikat symboli wieloznacznych dla *.azurewebsites.net jest domyślnie dostępny|Nie jest obsługiwany certyfikat symboli wieloznacznych dla *.azurewebsites.net jest domyślnie dostępny|Nieograniczona liczba połączeń SNI SSL |W zestawie nieograniczone połączenia SSL SNI i 1 PROTOKOŁU IP SSL |W zestawie nieograniczone połączenia SSL SNI i 1 PROTOKOŁU IP SSL | W zestawie nieograniczone połączenia SSL SNI i 1 PROTOKOŁU IP SSL|
| Połączenia hybrydowe na plan | | | 5 | 25 | 200 | 200 |
| Zintegrowany moduł równoważenia obciążenia | |X |X |X |X |X<sup>10</sup> |
| [Zawsze włączone](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/manage-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (ręczne + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczne + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczne + zaplanowane) |
| [Automatyczne skalowanie](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitorowanie punktów końcowych](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Miejsca przejściowe](../articles/app-service/deploy-staging-slots.md) na aplikację| | | |5 |20 |20 |
| Umowa SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1.</sup> Aplikacje i przydziały miejsca są na plan usługi app service, chyba że zaznaczono inaczej.  
<sup>2.</sup> Rzeczywista liczba aplikacji, które można hostować na tych komputerach zależy od aktywności aplikacji, rozmiar wystąpień komputera i odpowiednie wykorzystanie zasobów.  
<sup>3</sup> Dedykowane wystąpienia mogą mieć różne rozmiary. Aby uzyskać więcej informacji, zobacz [Ceny usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Na życzenie dozwolone są większe liczby osób.  
<sup>5</sup> Limit miejsca jest całkowity rozmiar zawartości we wszystkich aplikacjach w tym samym planie usługi aplikacji. Całkowity rozmiar zawartości wszystkich aplikacji we wszystkich planach usługi aplikacji w jednej grupie zasobów i regionie nie może przekraczać 500 GB.  
<sup>6</sup> Zasoby te są ograniczone przez zasoby fizyczne w dedykowanych wystąpień (rozmiar wystąpienia i liczba wystąpień).  
<sup>7</sup> Jeśli skalujesz aplikację w warstwie Podstawowa do dwóch wystąpień, masz 350 równoczesnych połączeń dla każdego z dwóch wystąpień. W warstwie Standardowa i powyżej nie ma teoretycznych ograniczeń dla gniazd sieci web, ale inne czynniki mogą ograniczyć liczbę gniazd internetowych. Na przykład maksymalne równoczesnych żądań `maxConcurrentRequestsPerCpu`dozwolonych (zdefiniowane przez ) to: 7500 na małą maszynę wirtualną, 15 000 na średnią maszynę wirtualną (7500 x 2 rdzenie) i 75 000 na dużą maszynę wirtualną (18 750 x 4 rdzenie).  
<sup>8</sup> Maksymalna liczba połączeń IP przypadana na wystąpienie i zależy od rozmiaru wystąpienia: 1920 na wystąpienie B1/S1/P1V2, 3968 na wystąpienie B2/S2/P2V2, 8064 na wystąpienie B3/S3/P3V2.  
<sup>9</sup> Limit przydziału certyfikatu usługi app service na subskrypcję można zwiększyć za pomocą żądania pomocy technicznej do maksymalnego limitu 200.  
<sup>10</sup> Izolowane jednostki SKU usługi app service mogą być wewnętrznie równoważenia obciążenia (ILB) z modułem równoważenia obciążenia platformy Azure, więc nie ma łączności publicznej z Internetu. W rezultacie niektóre funkcje ILB II usługi aplikacji muszą być używane z komputerów, które mają bezpośredni dostęp do punktu końcowego sieci równoważenia obciążenia.  
<sup>11</sup> Uruchamianie niestandardowych plików wykonywalnych i/lub skryptów na żądanie, zgodnie z harmonogramem lub w sposób ciągły jako zadanie w tle w wystąpieniu usługi App Service. Zawsze włączone jest wymagane do ciągłego wykonywania WebJobs. Nie ma wstępnie zdefiniowanego limitu liczby webjobs, które można uruchomić w wystąpieniu usługi App Service. Istnieją praktyczne limity, które zależą od tego, co próbuje zrobić kod aplikacji.  
