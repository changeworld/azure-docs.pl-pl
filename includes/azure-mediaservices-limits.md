---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334807"
---
>[!NOTE]
>W przypadku zasobów, które nie są stałe, otwórz bilet pomocy technicznej, aby poprosić o zwiększenie przydziałów. Nie należy tworzyć dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

| Zasób | Limit | 
| --- | --- | 
| Konta usługi Azure Media Services w ramach jednej subskrypcji | 25 (stały) |
| Jednostki zarezerwowane nośników na konto usługi Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Zadania na konto usługi Media Services | 50 000<sup>2</sup> |
| Łańcuchowe zadania podrzędne na zadanie | 30 (stały) |
| Zasoby na konto usługi Media Services | 1 000 000|
| Elementy zawartości na zadanie podrzędne | 50 |
| Elementy zawartości na zadanie | 100 |
| Unikatowe lokalizatory jednocześnie skojarzone z elementem zawartości | 5<sup>4</sup> |
| Kanały na żywo na konto usługi Media Services |5|
| Programy w stanie zatrzymania na kanał |50|
| Programy w stanie działania na kanał |3|
| Przesyłanie strumieniowe punktów końcowych, które są zatrzymane lub uruchomione na koncie usługi Media Services|2|
| Jednostki przesyłania strumieniowego na punkt końcowy przesyłania strumieniowego |10 |
| Konta magazynu | 1000<sup>5</sup> (stała) |
| Zasady | 1 000 000<sup>6</sup> |
| Rozmiar pliku| W niektórych scenariuszach istnieje limit maksymalnego rozmiaru pliku obsługiwanego do przetwarzania w umiaźnym umiań. <sup>7</sup> |

<sup>1.</sup> Jeśli zmienisz typ, na przykład z S2 na S1, maksymalne limity jednostek zarezerwowanych zostaną zresetowane.

<sup>2.</sup> Liczba ta obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. Stare zadania można usunąć za pomocą **programu IJob.Delete** lub żądania **DELETE** HTTP.

Od 1 kwietnia 2017 r. każdy rekord zadania na koncie starszym niż 90 dni jest automatycznie usuwany wraz z powiązanymi rekordami zadań. Automatyczne usuwanie występuje, nawet jeśli całkowita liczba rekordów jest niższa od maksymalnego przydziału. Aby zarchiwizować informacje o zadaniu i zadaniu, użyj kodu opisanego w programie Zarządzanie zasobami za pomocą pliku [Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Podczas składania żądania do listy jednostek zadań, maksymalnie 1000 zadań jest zwracany na żądanie. Aby śledzić wszystkie przesłane zadania, użyj kwerend u góry lub pomiń, jak opisano w [opcjach kwerend systemowych OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Lokalizatory nie są przeznaczone do zarządzania kontrolą dostępu dla użytkownika. Aby przyznać różne prawa dostępu poszczególnym użytkownikom, należy korzystać z rozwiązań do zarządzania prawami cyfrowymi (DRM). Aby uzyskać więcej informacji, zobacz [Ochrona zawartości za pomocą usługi Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> Konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>6</sup> Istnieje limit 1 000 000 zasad dla różnych zasad usługi Media Services. Przykładem jest zasady lokalizatora lub ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Jeśli zawsze używasz tych samych dni i uprawnień dostępu, użyj tego samego identyfikatora zasad. Aby uzyskać informacje i przykład, zobacz [Zarządzanie zasobami za pomocą sdk .NET usługi Media Services](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob wynosi obecnie do 5 TB w usłudze Azure Blob Storage. Dodatkowe limity mają zastosowanie w usłudze Media Services na podstawie rozmiarów maszyn wirtualnych, które są używane przez usługę. Limit rozmiaru ma zastosowanie do przekazywalnych plików, a także plików, które są generowane w wyniku przetwarzania usługi Media Services (kodowania lub analizowania). Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity dla jednostek zarezerwowanych nośników S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli kodujesz źródła rozdzielczości 4K o długim czasie trwania, musisz używać jednostek zarezerwowanych nośników S3, aby osiągnąć wymaganą wydajność. Jeśli masz zawartość 4K, która jest większa niż limit 260 GB w jednostkach zarezerwowanych nośników S3, otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej nośnika    |Maksymalny rozmiar wejścia (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
