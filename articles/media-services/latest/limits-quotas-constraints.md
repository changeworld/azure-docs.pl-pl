---
title: Limity przydziału i ograniczenia w Azure Media Services v3 | Microsoft Docs
description: W tym temacie opisano limity przydziału i ograniczenia w programie Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 819548d784e5cba9fcec6b2110137d91bf28e03d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296908"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Limity przydziału i ograniczenia w Azure Media Services v3

W tym artykule opisano limity przydziału i ograniczenia w programie Azure Media Services v3.

| Zasób | Limit domyślny | 
| --- | --- | 
| Elementy zawartości na konto usługi Azure Media Services | 1 000 000|
| Filtry manifestów dynamicznych|100|
| JobInputs na zadanie | 50 (stałe)|
| JobOutputs na zadanie | 20 (stałe) |
| TransformOutputs w transformacji | 20 (stałe) |
| Pliki na JobInput|10 (stałe)|
| Rozmiar pliku| W niektórych scenariuszach obowiązuje limit maksymalnego rozmiaru pliku, który jest obsługiwany przez przetwarzanie w Media Services. <sup>jedno</sup> |
| Zadania na konto Media Services | 500 000 <sup>(2)</sup> (stałe)|
| Wydarzenia na żywo na konto usługi Media Services |5|
| Media Services kont w ramach jednej subskrypcji | 25 (stały) |
| Dane wyjściowe na żywo na żywo |3 <sup>(3)</sup> |
| Maksymalny czas trwania danych wyjściowych na żywo | 25 godzin |
| Konta magazynu | 100<sup>(4)</sup> (stałe) |
| Punkty końcowe przesyłania strumieniowego (zatrzymane lub uruchomione) dla konta Media Services|2 (stałe)|
| Zasady przesyłania strumieniowego | 100 <sup>(5)</sup> |
| Przekształcenia na konto Media Services | 100 (stałe)|
| Unikatowe lokalizatory przesyłania strumieniowego skojarzone z elementem zawartości w jednym momencie | 100<sup>(6)</sup> (stałe) |
| Opcje dla zasad klucza zawartości |30 | 
| Licencje na miesiąc dla każdego z typów DRM w usłudze Media Services Key Delivery na konto|1 000 000|

<sup>1</sup> maksymalny rozmiar obsługiwany przez pojedynczy obiekt BLOB jest obecnie do 5 TB na platformie Azure Blob Storage. Dodatkowe limity są stosowane w Media Services na podstawie rozmiarów maszyn wirtualnych używanych przez usługę. Limit rozmiaru dotyczy przekazywanych plików, a także plików, które są generowane w wyniku przetwarzania Media Services (kodowanie lub analizowanie). Jeśli rozmiar pliku źródłowego przekracza 260 GB, prawdopodobnie zadanie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity jednostek zarezerwowanych multimediów S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli zakodujesz źródła rozpoznawania 4 KB o długim czasie trwania, musisz użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć potrzebną wydajność. Jeśli masz zawartość 4K o rozmiarze większym niż limit 260 GB w jednostkach zarezerwowanych multimediów S3, skontaktuj się z nami pod adresem amshelp@microsoft.com, aby umożliwić obsługę tego scenariusza.

|Typ jednostki zarezerwowanej multimediów   |Maksymalny rozmiar danych wejściowych (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje ono usuniętych zadań. 

Każdy rekord zadania na koncie starszej niż 90 dni zostanie automatycznie usunięty, nawet jeśli łączna liczba rekordów jest mniejsza niż maksymalny limit przydziału. 

<sup>3</sup> dane wyjściowe na żywo zaczynają się po utworzeniu i Zatrzymaj po usunięciu.

<sup>4</sup> konta magazynu muszą znajdować się w tej samej subskrypcji platformy Azure.

<sup>5</sup> w przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies)należy zaprojektować ograniczony zestaw zasad dla konta usługi Media Service i ponownie użyć ich do StreamingLocators za każdym razem, gdy potrzebne są te same opcje szyfrowania i protokoły. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>6</sup> lokalizatory przesyłania strumieniowego nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="support-ticket"></a>Bilet pomocy technicznej

W przypadku zasobów, które nie zostały naprawione, możesz poprosił o wypróbowanie przydziałów, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz szczegółowe informacje w żądaniu dotyczące żądanych zmian przydziału, scenariuszy przypadków użycia i wymaganych regionów. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Następne kroki

[Omówienie](media-services-overview.md)
