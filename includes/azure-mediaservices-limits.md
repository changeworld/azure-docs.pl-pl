---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183612"
---
>[!NOTE]
>Dla zasobów, które nie są rozwiązywane Otwórz bilet pomocy technicznej, aby poprosić o zwiększenie limitów przydziału. Nie twórz dodatkowych kont usługi Azure Media Services w celu podjęcia próby uzyskania wyższych limitów.

| Resource | Limit domyślny | 
| --- | --- | 
| Konta platformy Azure Media Services w ramach jednej subskrypcji | 25 (stały) |
| Zarezerwowane multimediów jednostek na konto usługi Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Liczba zadań na konto usługi Media Services | 50 000<sup>2</sup> |
| Łańcuchowe zadania podrzędne na zadanie | 30 (stały) |
| Elementy zawartości na konto usługi Media Services | 1 000 000|
| Elementy zawartości na zadanie podrzędne | 50 |
| Elementy zawartości na zadanie | 100 |
| Unikatowe lokalizatory jednocześnie skojarzone z elementem zawartości | 5<sup>4</sup> |
| Kanały na żywo dla konta usługi Media Services |5|
| Programy w stanie zatrzymania na kanał |50|
| Programy w stanie działania na kanał |3|
| Punkty końcowe, które zostały zatrzymane przesyłania strumieniowego lub uruchomione na konto usługi Media Services|2|
| Jednostki przesyłania strumieniowego na punkt końcowy przesyłania strumieniowego |10 |
| Konta magazynu | 1000<sup>5</sup> (stały) |
| Zasady | 1 000 000<sup>6</sup> |
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>7</sup> |

<sup>1</sup>Jeśli zmienisz typ, na przykład z S2 do S1, limitów maksymalnej liczby jednostek zarezerwowanych zostaną zresetowane.

<sup>2</sup>ta liczba obejmuje zadania umieszczonych w kolejce, zakończone, aktywne i anulowane. Go nie obejmuje usuniętych zadań. Można usunąć stare zadania za pomocą **IJob.Delete** lub **Usuń** żądania HTTP.

Od 1 kwietnia 2017 r. wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni jest automatycznie usuwana, wraz z jego rekordów skojarzonych zadań. Automatyczne wykrywanie występuje nawet, jeśli łączna liczba rekordów jest poniżej maksymalny limit przydziału. Aby archiwizację informacji i zadań, należy użyć kodu opisanego w [zarządzanie zasobami za pomocą zestawu SDK .NET usługi Media Services](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>po wprowadzeniu żądania do listy zadań jednostek maksymalnie 1000 zadania jest zwracana na żądanie. Aby zachować śledzić wszystkie przesłane zadania, Użyj górnej i pominąć zapytań, zgodnie z opisem w [opcje zapytania systemu OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>lokalizatory nie są przeznaczone do zarządzania kontroli dostępu poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania (prawami cyfrowymi DRM) prawami cyfrowymi. Aby uzyskać więcej informacji, zobacz [chronić zawartość przy użyciu usługi Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>6</sup>jest ograniczona do 1 000 000 zasad dla różnych zasad usługi Media Services. To na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Jeśli zawsze używasz tych samych dni i uprawnień dostępu, użyj tego samego identyfikatora zasad. Aby uzyskać informacje i obejrzeć przykład, zobacz [zarządzanie zasobami za pomocą zestawu SDK .NET usługi Media Services](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest obecnie maksymalnie 5 TB w usłudze Azure Blob Storage. Dodatkowe limity mają zastosowanie w usłudze Media Services, w oparciu o rozmiarów maszyn wirtualnych, które są używane przez usługę. Limit rozmiaru ma zastosowanie do plików, które można przekazać, a także plików, które wygenerowanie wyniku usługi Media Services przetwarzania (kodowanie lub analizowanie). Jeśli plik źródłowy jest większy niż 260 GB, zadanie prawdopodobnie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity na media, zarezerwowane jednostki S1, S2 i S3. Plik źródłowy jest większy niż limitu zdefiniowanego w tabeli, kodowania zadania nie powiedzie się. Kodowanie 4K rozwiązania źródeł czas trwania długiej musisz użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć wydajność konieczne. Jeśli zawartość 4K, który jest większy niż limit 260 GB jednostek zarezerwowanych multimediów S3, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalne środki zaradcze w celu obsługi danego scenariusza.

|Typ jednostki zarezerwowane multimediów   |Maksymalny rozmiar danych wejściowych (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
