---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133784"
---
Następujące limity dotyczą każdego obszaru roboczego usługi Log Analytics, w bieżącym na podstawie użycia warstwy cenowej wprowadzonych w kwietniu 2018 r.:

|     | Za GB 2018 r. |
| --- | --- | 
| Ilość danych zebranych na dzień | Brak |
| Okres przechowywania danych | 30 do 730 dni<sup>1</sup> |

Następujące limity dotyczą każdego obszaru roboczego usługi Log Analytics najnowszych starszej wersji warstwy cenowe:

|  | Wolne | Autonomiczna (za GB) | Na węzeł (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Ilość danych zebranych na dzień |500 MB<sup>2</sup> |Brak |Brak |
| Okres przechowywania danych |7 dni | 30 do 730 dni<sup>1</sup> | 30 do 730 dni<sup>1</sup> |

Następujące limity dotyczą każdego obszaru roboczego usługi Log Analytics najstarsze starszej wersji warstwy cenowe:

|  | Standardowa (Standard) | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| Ilość danych zebranych na dzień | Brak | Brak | 
| Okres przechowywania danych |30 dni | 365 dni |

<sup>1</sup>przechowywanie danych dłużej niż 31 dni jest dostępna dla dodatkowych opłat. Dowiedz się więcej o [cennika usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>gdy obszar roboczy osiągnie 500 MB limit dziennego transferu danych, analiza danych zatrzymuje, a następnie wznowiona na początku następnego dnia. Dzień jest oparty na czasie UTC.

>[!NOTE]
>W zależności od tego, jak długo używasz usługi Log Analytics mogą mieć dostęp do starszych warstw cenowych. Dowiedz się więcej o [starszej wersji usługi Log Analytics warstw cenowych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

Następujące limity dotyczą zasobów usługi Azure Log Analytics (obszary robocze) na subskrypcję.

| Warstwa cenowa    | Liczba obszarów roboczych na subskrypcję | Komentarze
| --- | --- | --- |
| Warstwa bezpłatna  | 10 | Nie można zwiększyć ten limit. |
| Wszystkie warstwy innych niż bezpłatna | ND | Ograniczeniem liczby zasobów w grupie zasobów i liczba grup zasobów na subskrypcję. | 

Poniższe limity mają zastosowanie do interfejsów API analizy dziennika:

| Category | Limits | Komentarze
| --- | --- | --- |
| Interfejs API modułu zbierającego dane | Maksymalny rozmiar pojedynczego wpisu to 30 MB.<br>Maksymalny rozmiar wartości pól to 32 KB. | Dziel większe woluminy na wiele wpisów.<br>Pola dłuższe niż 32 KB są obcinane. |
| Interfejs API wyszukiwania | 5000 rekordów zwracanych dla danych niezagregowanych.<br>500 000 rekordów dla danych zagregowanych. | Zagregowane dane to wyszukiwanie obejmujące `summarize` polecenia.
 
