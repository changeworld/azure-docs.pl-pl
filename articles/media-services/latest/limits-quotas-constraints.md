---
title: Limity przydziału i ograniczenia dotyczące usługi Azure Media Services v3 | Dokumentacja firmy Microsoft
description: W tym temacie opisano przydziały i ograniczenia dotyczące usługi Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: d5fc14adab956fae23aad24fa7bc488c8c2041e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322570"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Limity przydziału i ograniczenia dotyczące usługi Azure Media Services v3

W tym artykule opisano przydziały i ograniczenia dotyczące usługi Azure Media Services v3.

| Resource | Limit domyślny | 
| --- | --- | 
| Elementy zawartości na konto usługi Azure Media Services | 1 000 000|
| Filtry manifestów dynamicznych|100|
| JobInputs na zadanie | 50 (stałe)|
| JobOutputs na zadanie | 20 (stałe) |
| TransformOutputs w transformacji | 20 (stałe) |
| Pliki na JobInput|10 (stałe)|
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>(1)</sup> |
| Liczba zadań na konto usługi Media Services | 500 000 <sup>(2)</sup> (stałe)|
| Wyświetlanie listy przekształceń|Dzielenie odpowiedzi na strony, 1000 przekształceń na stronie|
| Wyświetlanie listy zadań|Dzielenie odpowiedzi na strony, 500 zadań na stronie|
| Wydarzenia na żywo na konto usługi Media Services |5|
| Konta usługi Media Services w ramach jednej subskrypcji | 25 (stały) |
| Na żywo dane wyjściowe w stanie działania na wydarzenie na żywo |3|
| Czas trwania Max na żywo w danych wyjściowych | 25 godzin |
| Konta magazynu | 100<sup>(4)</sup> (stałe) |
| Punkty końcowe przesyłania strumieniowego (zatrzymana lub uruchomiona) na konto usługi Media Services|2 (stałe)|
| Zasady przesyłania strumieniowego | 100 <sup>(3)</sup> |
| Przekształcenia na konto usługi Media Services | 100 (stałe)|
| Unikatowe Lokalizatory przesyłania strumieniowego, w tym samym czasie skojarzony z elementem zawartości | 100<sup>(5)</sup> (stałe) |
| Zasady dotyczące klucza zawartości |30 | 

<sup>1</sup> maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest obecnie maksymalnie 5 TB w usłudze Azure Blob Storage. Jednak dodatkowe limity mają zastosowanie w usłudze Azure Media Services, oparte na rozmiary maszyn wirtualnych, które są używane przez usługę. Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. Jeśli zawartość 4K, który jest większy niż limit 260 GB, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalne środki zaradcze w celu obsługi danego scenariusza.

<sup>2</sup> ta liczba obejmuje zadania umieszczonych w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. 

Wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni zostaną automatycznie usunięte, nawet jeśli łączna liczba rekordów jest poniżej maksymalny limit przydziału. 

<sup>3</sup> podczas korzystania z niestandardowej [przesyłania strumieniowego zasad](https://docs.microsoft.com/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby usługi StreamingLocators za każdym razem szyfrowania tych samych opcji i protokołów są wymagane. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>4</sup> konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>5</sup> Lokalizatory przesyłania strumieniowego nie są przeznaczone do zarządzania kontroli dostępu dla użytkownika. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Bilet pomocy technicznej

Dla zasobów, które nie zostały usunięte, możesz poprosić o zwiększenie limitów przydziału, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). W żądaniu zmiany żądany limit przydziału, scenariuszy przypadków użycia i wymaganych regionach, należy uwzględnić szczegółowe informacje. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
