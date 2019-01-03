---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 6bb7db5d57508d448f88a30623b7f30e88a0a821
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736755"
---
| Zasób | Bezpłatna | Udostępniona | Podstawowa | Standardowa (Standard) | Premium (wersja 2) | Izolowane </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplikacje internetowe, mobilne i interfejsu API](https://azure.microsoft.com/services/app-service/) na [planu usługi App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Nieograniczona liczba<sup>2</sup> |Nieograniczona liczba<sup>2</sup> |Nieograniczona liczba<sup>2</sup> |Nieograniczona liczba<sup>2</sup>|
| [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) |1 na region |10 dla grupy zasobów |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów |100 dla każdej grupy zasobów|
| Typ wystąpienia obliczeniowe |Udostępniona |Udostępniona |W wersji dedykowanej<sup>3</sup> |W wersji dedykowanej<sup>3</sup> |W wersji dedykowanej<sup>3</sup></p> |W wersji dedykowanej<sup>3</sup>|
| [Skalowalny w poziomie](../articles/app-service/web-sites-scale.md) (maksymalna liczba wystąpień) |1 udostępnionego |1 udostępnionego |3 w wersji dedykowanej<sup>3</sup> |10 w wersji dedykowanej<sup>3</sup> |20 w wersji dedykowanej<sup>3</sup>|w wersji dedykowanej 100<sup>4</sup>|
| Magazyn<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Czas Procesora (5 min)<sup>6</sup> |3 minuty |3 minuty |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Czas Procesora (dzień)<sup>6</sup> |60 minut |240 minut |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |Nieograniczone, płacić zgodnie ze standardowymi [stawki](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Pamięć (1 godzina) |1024 MB na plan usługi App Service |1024 MB dla aplikacji |ND |ND |ND |ND |
| Przepustowość |165 MB |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |Nieograniczona [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) zastosowania |
| Architektura aplikacji |32-bitowa |32-bitowa |32-bitowy/64-bitowy |32-bitowy/64-bitowy |32-bitowy/64-bitowy |32-bitowy/64-bitowy |
| Za wystąpienie usługi sieci Web Sockets<sup>7</sup> |5 |35 |350 |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
| Współbieżne [debugera połączeń](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikację |1 |1 |1 |5 |5 |5 |
| Certyfikaty usługi App Service na subskrypcję<sup>10</sup>| Nieobsługiwane | Nieobsługiwane |10 |10 |10 |10 |
| Domeny niestandardowe na aplikację</a> |0 (tylko w przypadku poddomena azurewebsites.net)|500 |500 |500 |500 |500 |
| Domena niestandardowa [Obsługa protokołu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Nieobsługiwane. Certyfikat wieloznaczny dla *. azurewebsites.net domyślnie dostępne.|Nieobsługiwane. Certyfikat wieloznaczny dla *. azurewebsites.net domyślnie dostępne.|Nieograniczone połączenia SNI SSL |Nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń |Nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń | Nieograniczone połączenia SNI SSL i 1 połączenie IP SSL połączeń|
| Zintegrowana usługa równoważenia obciążenia | |X |X |X |X |X<sup>9</sup> |
| [Zawsze włączone](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/manage-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (Ręczne + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (Ręczne + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (Ręczne + zaplanowane) |
| [Automatyczne skalowanie](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [Zadania Webjob](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Usługa Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomocy technicznej | |X |X |X |X |X |
| [Monitorowanie punktu końcowego](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Miejsca przejściowe](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Umowa SLA | |  |99,9% |99.95%|99.95%|99.95%|  

<sup>1</sup> aplikacji i limitami przydziału magazynów są na plan usługi App Service, chyba że zaznaczono inaczej.  
<sup>2</sup> rzeczywista liczba aplikacje obsługujące na tych maszynach zależy działanie aplikacji, rozmiaru wystąpienia maszyny i odpowiednie wykorzystanie zasobów.  
<sup>3</sup> dedykowane wystąpienia mogą mieć różne rozmiary. Zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/) Aby uzyskać więcej informacji.  
<sup>4</sup> możliwość zwiększenia na żądanie.  
<sup>5</sup> limit magazynu jest łączny rozmiar zawartości dla wszystkich aplikacji w ten sam plan usługi App Service.  
<sup>6</sup> te zasoby są ograniczone przez zasoby fizyczne na dedykowanych wystąpieniach (rozmiar wystąpienia i liczby wystąpień).  
<sup>7</sup> skalowanie aplikacji w warstwie podstawowa do dwóch wystąpień, masz 350 równoczesnych połączeń dla każdego z dwóch wystąpień.  
<sup>8</sup> uruchamiaj niestandardowe pliki wykonywalne i/lub skrypty na żądanie, według harmonogramu albo w sposób ciągły jako zadanie w tle w ramach usługi App Service wystąpienia usługi. Do ciągłego wykonywania zadań w sieci Web jest wymagana opcja Zawsze włączona. Do wykonywania zadań w sieci Web według harmonogramu jest wymagana usługa Azure Scheduler w warstwie Bezpłatna lub Standardowa. Nie ma wstępnie zdefiniowanego limitu liczby zadań Webjob, która może działać w wystąpieniu usługi App Service, ale istnieją praktycznych limitów, które są zależne od kodu aplikacji próbuje zrobić.  
<sup>9</sup> jednostek SKU izolowanej usługi app mają możliwość wewnętrznie można załadować o zrównoważonym obciążeniu (ILB) za pomocą usługi Azure Load Balancer, co oznacza brak łączności publicznej z Internetu. Z tego powodu niektóre funkcje izolowanej usługi App Service z obciążeniem równoważonym wewnętrznie muszą być używane z poziomu maszyn mających bezpośredni dostęp do punktu końcowego sieci modułu wewnętrznego równoważenia obciążenia.  
<sup>10</sup> za pośrednictwem żądania pomocy technicznej, aby maksymalny limit 200 można zwiększyć limit przydziału certyfikatu usługi App Service na subskrypcję.  