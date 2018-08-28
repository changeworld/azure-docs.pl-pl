---
title: Limity przydziału i ograniczenia dotyczące usługi Azure Media Services v3 | Dokumentacja firmy Microsoft
description: W tym temacie opisano przydziały i ograniczenia dotyczące usługi Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/26/2018
ms.author: juliako
ms.openlocfilehash: 49b834325ce819f20978e06d85ee308955510ac1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051113"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Limity przydziału i ograniczenia dotyczące usługi Azure Media Services v3

W tym artykule opisano przydziały i ograniczenia dotyczące usługi Azure Media Services v3.

| Zasób | Limit domyślny | 
| --- | --- | 
| Elementy zawartości na konto usługi Azure Media Services | 1 000 000|
| Filtry manifestów dynamicznych|100|
| JobInputs na zadanie | 50 (stałe)|
| JobOutputs za zadanie/TransformOutputs w transformacji | 20 (stałe) |
| Pliki na JobInput|10 (stałe)|
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>(1)</sup> |
| Liczba zadań na konto usługi Media Services | 500 000 <sup>(2)</sup> (stałe)|
| Lista przekształceń|Stronicowanie odpowiedzi z 1000 przekształceń na stronę|
| Wyświetlanie listy zadań|Stronicowanie odpowiedzi 500 zadań na stronie|
| LiveEvents na konto usługi Media Services |5|
| Konta usługi Media Services w ramach jednej subskrypcji | 25 (stały) |
| LiveOutputs w stanie działania na element LiveEvent |3|
| Konta magazynu | 100<sup>(4)</sup> (stałe) |
| Punkty końcowe przesyłania strumieniowego w stanie działania na konto usługi Media Services|2|
| StreamingPolicies | 100 <sup>(3)</sup> |
| Przekształcenia na konto usługi Media Services | 100 (stałe)|
| Unikatowe StreamingLocators skojarzone z elementem zawartości w tym samym czasie | 100<sup>(5)</sup> (stałe) |

<sup>1</sup> maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest obecnie maksymalnie 5 TB w usłudze Azure Blob Storage. Jednak dodatkowe limity mają zastosowanie w usłudze Azure Media Services, oparte na rozmiary maszyn wirtualnych, które są używane przez usługę. Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. Jeśli zawartość 4K, który jest większy niż limit 260 GB, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalne środki zaradcze w celu obsługi danego scenariusza.

<sup>2</sup> ta liczba obejmuje zadania umieszczonych w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. 

Wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni zostaną automatycznie usunięte, nawet jeśli łączna liczba rekordów jest poniżej maksymalny limit przydziału. 

<sup>3</sup> podczas korzystania z niestandardowej [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby usługi StreamingLocators za każdym razem szyfrowania tych samych opcji i protokołów są wymagane. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

<sup>4</sup> konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>5</sup> StreamingLocators nie są przeznaczone do zarządzania kontroli dostępu poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Bilet pomocy technicznej

Dla zasobów, które nie zostały usunięte, możesz poprosić o zwiększenie limitów przydziału, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). W żądaniu zmiany żądany limit przydziału, scenariuszy przypadków użycia i wymaganych regionach, należy uwzględnić szczegółowe informacje. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
