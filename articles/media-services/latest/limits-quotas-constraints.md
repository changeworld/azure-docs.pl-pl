---
title: Przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3 | Dokumentacja firmy Microsoft
description: W tym temacie opisano przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: juliako
ms.openlocfilehash: 14779306815681c368a98d698a6688d528a6c747
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294033"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3

W tym artykule opisano, przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3.

| Zasób | Limit domyślny | 
| --- | --- | 
| Zasoby dla konta usługi Azure Media Services | 1 000 000|
| JobInputs na zadanie | 50 (rozwiązany)|
| JobOutputs na zadania/TransformOutputs w transformacji | 20 (rozwiązany) |
| Pliki na JobInput|10 (rozwiązany)|
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>(1)</sup> |
| Zadań na konta usługi Media Services | 500 000 <sup>(2)</sup> (rozwiązany)|
| Lista transformacji|Odpowiedź z 1000 przekształcenia na stronie z podziałem na strony|
| Lista zadań|Odpowiedź, 500 zadań na stronie z podziałem na strony|
| LiveEvents na konto usługi Media Services |5|
| Konta usługi Media Services w ramach jednej subskrypcji | 25 (stały) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs uruchomiona na LiveEvent |3|
| LiveOutputs w stanie zatrzymania na LiveEvent |50|
| Konta magazynu | 100<sup>(4)</sup> (rozwiązany) |
| Punkty końcowe przesyłania strumieniowego uruchomiona na koncie usługi Media Services|2|
| Przekształca na konto usługi Media Services | 100 (rozwiązany)|
| Unikatowy StreamingLocators skojarzony z zasobem w tym samym czasie | 20<sup>(5)</sup> |

<sup>1</sup> maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest aktualnie w magazynie obiektów Blob Azure do 5 TB. Jednak dodatkowe ograniczenia mają zastosowanie w usłudze Azure Media Services oparte na rozmiarów maszyn wirtualnych, które są używane przez usługę. Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. Jeśli masz zawartość 4K, który jest większy niż limit 260 GB, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalnych środki zaradcze do obsługi danego scenariusza.

<sup>2</sup> ta liczba uwzględnia również umieszczonych w kolejce, Zakończono active i anulowanych zadań. Nie ma usuniętych zadania. 

Rekord wszystkie zadania w ramach Twojego konta, które są starsze niż 90 dni zostaną automatycznie usunięte, nawet jeśli jest to całkowita liczba rekordów poniżej maksymalny limit przydziału. 

<sup>3</sup> przy użyciu niestandardowego [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media i ponownie ich używać do Twojej StreamingLocators tego samego szyfrowania opcje i protokołów są wymagane. Nie należy tworzyć nowego elementu StreamingPolicy dla każdego obiektu StreamingLocator.

<sup>4</sup> kont magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>5</sup> StreamingLocators nie są przeznaczone do zarządzania kontroli dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Biletu pomocy technicznej

Dla zasobów, które nie zostały usunięte, może uzyskać przydziały do zostać wywołane, otwierając [obsługuje biletu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Zawierają szczegółowe informacje w żądaniu zmiany żądany limit przydziału, scenariusze przypadek użycia i regiony wymagane. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
