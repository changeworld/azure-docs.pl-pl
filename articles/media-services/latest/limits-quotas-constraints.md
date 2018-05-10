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
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3

W tym temacie opisano, przydziały i ograniczenia dotyczące usługi Azure Media Services w wersji 3.

| Zasób | Limit domyślny | 
| --- | --- | 
| Zasoby dla konta usługi Azure Media Services | 1 000 000|
| JobInputs na zadanie | 100 |
| JobOutputs na zadanie | 30 (stały) |
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>(1)</sup> |
| Zadań na konta usługi Media Services | 50,000<sup>(2)</sup> |
| LiveEvents na konto usługi Media Services |5|
| Konta usługi Media Services w ramach jednej subskrypcji | 25 (stały) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs uruchomiona na LiveEvent |3|
| LiveOutputs w stanie zatrzymania na LiveEvent |50|
| Konta magazynu | 1000<sup>(4)</sup> (rozwiązany) |
| Punkty końcowe przesyłania strumieniowego uruchomiona na koncie usługi Media Services|2|
| Przekształca na konto usługi Media Services | 20 |
| Unikatowy StreamingLocators skojarzony z zasobem w tym samym czasie | 20<sup>(5)</sup> |
  
<sup>1</sup>maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest aktualnie w magazynie obiektów Blob Azure do 5 TB. Jednak dodatkowe ograniczenia mają zastosowanie w usłudze Azure Media Services oparte na rozmiarów maszyn wirtualnych, które są używane przez usługę. Jeśli plik źródłowy jest większy niż 260 GB zadania prawdopodobnie zakończy się niepowodzeniem. Jeśli masz zawartość 4K, który jest większy niż limit 260 GB, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalnych środki zaradcze do obsługi danego scenariusza.

<sup>2</sup> ta liczba uwzględnia również umieszczonych w kolejce, Zakończono active i anulowanych zadań. Nie ma usuniętych zadania. 

Rekord wszystkie zadania w ramach Twojego konta, które są starsze niż 90 dni zostaną automatycznie usunięte, nawet jeśli jest to całkowita liczba rekordów poniżej maksymalny limit przydziału. 

<sup>3</sup> jest ograniczona do 1 000 000 wpisów StreamingPolicy dla różnych zasad usługi Media Services (na przykład w przypadku zasad StreamingLocator lub ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Korzystaj z tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni, uprawnień dostępu itd. 

<sup>4</sup> kont magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>5</sup> StreamingLocators nie są przeznaczone do zarządzania kontroli dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Biletu pomocy technicznej

Dla zasobów, które nie zostały usunięte, może uzyskać przydziały do zostać wywołane, otwierając [obsługuje biletu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Podaj szczegółowe informacje w żądaniu zmiany żądany limit przydziału, scenariusze przypadek użycia i regiony wymagane. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
