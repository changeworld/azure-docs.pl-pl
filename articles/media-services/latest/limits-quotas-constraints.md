---
title: Przydziały i ograniczenia w usłudze Azure Media Services w wersji 3 | Dokumenty firmy Microsoft
description: W tym temacie opisano przydziały i ograniczenia w usłudze Microsoft Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888431"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Przydziały i ograniczenia w usłudze Azure Media Services w wersji 3

W tym artykule opisano przydziały i ograniczenia w usłudze Azure Media Services w wersji 3.

| Zasób | Limit domyślny | 
| --- | --- | 
| Elementy zawartości na konto usługi Azure Media Services | 1 000 000|
| Filtry manifestów dynamicznych|100|
| JobInputs na zadanie | 50 (stała)|
| Zlecenia na zadanie | 20 (stała) |
| TransformOutputs w transformacji | 20 (stała) |
| Pliki na jobinput|10 (stała)|
| Rozmiar pliku| W niektórych scenariuszach istnieje limit maksymalnego rozmiaru pliku obsługiwanego do przetwarzania w umiaźnym programie Media Services. <sup>(1)</sup> |
| Zadania na konto usługi Media Services | 500 000 <sup>(2)</sup> (stałe)|
| Wydarzenia na żywo na konto usługi Media Services |5|
| Konta usługi Media Services w jednej subskrypcji | 25 (stały) |
| Wyjścia na żywo na wydarzenie na żywo |3 <sup>(3)</sup> |
| Maksymalny czas trwania wyjścia na żywo | 25 godzin |
| Konta magazynu | 100<sup>(4)</sup> (stałe) |
| Przesyłanie strumieniowe punktów końcowych (zatrzymane lub uruchomione) na konto usługi Media Services|2 (stała)|
| Zasady przesyłania strumieniowego | 100 <sup>(5)</sup> |
| Przekształcenia na konto usługi Media Services | 100 (stała)|
| Unikatowe lokalizatory przesyłania strumieniowego skojarzone z zasobem jednocześnie | 100<sup>(6)</sup> (stałe) |
| Opcje dla zasad klucza zawartości |30 | 
| Licencje miesięcznie dla każdego typu DRM w usłudze dostarczania kluczy usługi Media Services na konto|1 000 000|

<sup>1</sup> Maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob wynosi obecnie do 5 TB w usłudze Azure Blob Storage. Dodatkowe limity mają zastosowanie w usłudze Media Services na podstawie rozmiarów maszyn wirtualnych, które są używane przez usługę. Limit rozmiaru ma zastosowanie do przekazywalnych plików, a także plików, które są generowane w wyniku przetwarzania usługi Media Services (kodowania lub analizowania). Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity dla jednostek zarezerwowanych nośników S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli kodujesz źródła rozdzielczości 4K o długim czasie trwania, musisz używać jednostek zarezerwowanych nośników S3, aby osiągnąć wymaganą wydajność. Jeśli masz zawartość 4K, która jest większa niż limit 260 GB w jednostkach zarezerwowanych nośników S3, otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej nośnika   |Maksymalny rozmiar wejścia (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. 

Każdy rekord zadania na koncie starszym niż 90 dni zostanie automatycznie usunięty, nawet jeśli całkowita liczba rekordów jest niższa od maksymalnego limitu. 

<sup>3</sup> Wyjścia na żywo rozpoczynają się podczas tworzenia i zatrzymują się po usunięciu.

<sup>4</sup> Konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>5</sup> Podczas korzystania z niestandardowych [zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies)należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie użyć ich dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje szyfrowania i protokoły. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>6</sup> Lokalizatory przesyłania strumieniowego nie są przeznaczone do zarządzania kontrolą dostępu dla użytkownika. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Bilet pomocy technicznej

W przypadku zasobów, które nie są stałe, można poprosić o podniesione przydziały, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz szczegółowe informacje w żądaniu na żądane zmiany przydziału, scenariusze przypadków użycia i regionów wymaganych. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Następne kroki

[Przegląd](media-services-overview.md)
