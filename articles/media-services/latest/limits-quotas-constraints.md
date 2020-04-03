---
title: Przydziały i limity w usłudze Azure Media Services
description: W tym temacie opisano przydziały i limity w usługach Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: 2d4f5f83335f6f115362bcf66cf69d7f9de7eaa5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582317"
---
# <a name="azure-media-services-quotas-and-limits"></a>Przydziały i limity usługi Azure Media Services

W tym artykule wymieniono niektóre z najczęstszych limitów usług Microsoft Azure Media Services, które są czasami nazywane przydziałami.

> [!NOTE]
> W przypadku zasobów, które nie są stałe, otwórz bilet pomocy technicznej, aby poprosić o zwiększenie przydziałów. Nie należy tworzyć dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="account-limits"></a>Limity kont

| Zasób | Limit domyślny | 
| --- | --- | 
| [Konta usługi Media Services](media-services-account-concept.md) w jednej subskrypcji | 25 (stały) |

## <a name="asset-limits"></a>Limity aktywów

| Zasób | Limit domyślny | 
| --- | --- | 
| [Zasoby](assets-concept.md) na konto usługi Media Services | 1 000 000|

## <a name="storage-limits"></a>Limity magazynowania

| Zasób | Limit domyślny | 
| --- | --- | 
| Rozmiar pliku| W niektórych scenariuszach istnieje limit maksymalnego rozmiaru pliku obsługiwanego do przetwarzania w umiaźnym programie Media Services. <sup>(1)</sup> |
| [Konta magazynu](storage-account-concept.md) | 100<sup>(2)</sup> (stałe) |

<sup>1</sup> Maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob wynosi obecnie do 5 TB w usłudze Azure Blob Storage. Dodatkowe limity mają zastosowanie w usłudze Media Services na podstawie rozmiarów maszyn wirtualnych, które są używane przez usługę. Limit rozmiaru ma zastosowanie do przekazywalnych plików, a także plików, które są generowane w wyniku przetwarzania usługi Media Services (kodowania lub analizowania). Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity dla jednostek zarezerwowanych nośników S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli kodujesz źródła rozdzielczości 4K o długim czasie trwania, musisz używać jednostek zarezerwowanych nośników S3, aby osiągnąć wymaganą wydajność. Jeśli masz zawartość 4K, która jest większa niż limit 260 GB w jednostkach zarezerwowanych nośników S3, otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej nośnika|Maksymalny rozmiar wejścia (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limity zadań (kodowanie & analizowanie)

| Zasób | Limit domyślny | 
| --- | --- | 
| [Zadania](transforms-jobs-concept.md) na konto usługi Media Services | 500 000 <sup>(3)</sup> (stałe)|
| Dane wejściowe zlecenia na zadanie | 50 (stała)|
| Wyjścia zlecenia na zadanie | 20 (stała) |
| [Przekształcenia](transforms-jobs-concept.md) na konto usługi Media Services | 100 (stała)|
| Przekształcanie wyjść w przekształcaniu | 20 (stała) |
| Pliki na dane wejściowe zadania|10 (stała)|

<sup>3</sup> Ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. 

Każdy rekord zadania na koncie starszym niż 90 dni zostanie automatycznie usunięty, nawet jeśli całkowita liczba rekordów jest niższa od maksymalnego limitu. 

## <a name="live-streaming-limits"></a>Limity transmisji na żywo

| Zasób | Limit domyślny | 
| --- | --- | 
| [Wydarzenia na żywo](live-events-outputs-concept.md) <sup>(4)</sup> na konto usługi Media Services |5|
| Wyjścia na żywo na wydarzenie na żywo |3 <sup>(5)</sup> |
| Maksymalny czas trwania wyjścia na żywo | 25 godzin |

<sup>4</sup> Aby uzyskać szczegółowe informacje na temat limitów zdarzeń na żywo, zobacz [porównanie typów zdarzeń na żywo i limity](live-event-types-comparison.md).

<sup>5</sup> Wyjścia na żywo rozpoczynają się podczas tworzenia i zatrzymują się po usunięciu.

## <a name="packaging--delivery-limits"></a>Limity dostaw opakowań &

| Zasób | Limit domyślny | 
| --- | --- | 
| [Przesyłanie strumieniowe punktów końcowych (zatrzymane](streaming-endpoint-concept.md) lub uruchomione) na konto usługi Media Services|2 |
| [Filtry manifestów dynamicznych](filters-dynamic-manifest-overview.md)|100|
| [Zasady przesyłania strumieniowego](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Unikatowe [lokalizatory przesyłania strumieniowego skojarzone](streaming-locators-concept.md) z zasobem jednocześnie | 100<sup>(7)</sup> (stała) |

<sup>6</sup> Podczas korzystania z niestandardowych [zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies)należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie użyć ich dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje szyfrowania i protokoły. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>7</sup> Lokalizatory przesyłania strumieniowego nie są przeznaczone do zarządzania kontrolą dostępu dla użytkownika. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="protection-limits"></a>Limity ochrony

| Zasób | Limit domyślny | 
| --- | --- | 
| Opcje dla [zasad klucza zawartości](content-key-policy-concept.md) |30 | 
| Licencje miesięcznie dla każdego typu DRM w usłudze dostarczania kluczy usługi Media Services na konto|1 000 000|

## <a name="support-ticket"></a>Bilet pomocy technicznej

W przypadku zasobów, które nie są stałe, można poprosić o podniesione przydziały, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz szczegółowe informacje w żądaniu na żądane zmiany przydziału, scenariusze przypadków użycia i regionów wymaganych. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Następne kroki

[Przegląd](media-services-overview.md)
