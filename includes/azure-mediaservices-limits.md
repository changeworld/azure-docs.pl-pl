---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 1e1316ef568cbc6409a8653022d9acff9837b59d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279825"
---
>[!NOTE]
>W przypadku zasobów, których ilość nie jest stała, możesz poprosić o zwiększenie limitów przydziału, otwierając bilet pomocy technicznej. **Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

| Zasób | Limit domyślny | 
| --- | --- | 
| Konta usługi Azure Media Services (AMS) w jednej subskrypcji | 25 (stały) |
| Jednostki zarezerwowane multimediów na konto AMS |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Zadania na konto AMS | 50,000<sup>(2)</sup> |
| Łańcuchowe zadania podrzędne na zadanie | 30 (stały) |
| Elementy zawartości na konto AMS | 1 000 000|
| Elementy zawartości na zadanie podrzędne | 50 |
| Elementy zawartości na zadanie | 100 |
| Unikatowe lokalizatory jednocześnie skojarzone z elementem zawartości | 5<sup>(4)</sup> |
| Kanały na żywo dla każdego konta AMS |5|
| Programy w stanie zatrzymania na kanał |50|
| Programy w stanie działania na kanał |3|
| Punkty końcowe przesyłania strumieniowego w stanie działania na konto AMS|2|
| Jednostki przesyłania strumieniowego na punkt końcowy przesyłania strumieniowego |10 |
| Konta magazynu | 1000<sup>(5)</sup> (stały) |
| Zasady | 1 000 000<sup>(6)</sup> |
| Rozmiar pliku| W niektórych scenariuszach istnieje limit na maksymalny obsługiwany rozmiar pliku do przetwarzania w usłudze Media Services. <sup>7</sup> |
  
<sup>1</sup> Jeśli zmienisz typ (na przykład z S2 do S1), są resetowane maksymalnych limitów jednostek RU.

<sup>2</sup> Ta liczba obejmuje zadania umieszczone w kolejce, zakończone, aktywne i anulowane. Nie obejmuje usuniętych zadań. Stare zadania możesz usunąć za pomocą funkcji **IJob.Delete** lub żądania HTTP **DELETE**.

Od 1 kwietnia 2017 r. wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni zostaną automatycznie usunięte, wraz ze skojarzonymi rekordami zadań podrzędnych nawet, jeśli łączna liczba rekordów jest poniżej maksymalny limit przydziału. Jeśli chcesz zarchiwizować informacje dotyczące zadania lub zadania podrzędnego, możesz użyć kodu opisanego [tutaj](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> żądania do listy zadań jednostek, maksymalnie 1000 zadania jest zwracana na żądanie. Jeśli potrzebujesz śledzić wszystkie przesłane zadania, możesz użyć opcji top/skip zgodnie z opisem w temacie [OData system query options](https://msdn.microsoft.com/library/gg309461.aspx) (Opcje zapytań systemu OData).

<sup>4</sup> Lokalizatory nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management). Więcej informacji znajduje się w [tej](../articles/media-services/previous/media-services-content-protection-overview.md) sekcji.

<sup>5</sup> Konta magazynu muszą pochodzić z tej samej subskrypcji platformy Azure.

<sup>6</sup> Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Korzystaj z tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni, uprawnień dostępu itd. Informacje i przykład możesz znaleźć w [tej](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies) sekcji.

<sup>7</sup>Jeśli przekazujesz zawartość do elementu zawartości w usłudze Azure Media Services do przetworzenia jej przy użyciu jednego z procesorów multimediów w usłudze (czyli koderów, np. Media Encoder Standard i Media Encoder Premium Workflow lub analiza aparatów np. Face Detector), następnie należy mieć świadomość ograniczenia dla rozmiarów maksymalny obsługiwany. 

Maksymalny rozmiar obsługiwany dla pojedynczego obiektu blob jest obecnie maksymalnie 5 TB w usłudze Azure Blob Storage. Jednak dodatkowe limity mają zastosowanie w usłudze Azure Media Services, oparte na rozmiary maszyn wirtualnych, które są używane przez usługę. W poniższej tabeli przedstawiono limity na każdym z jednostek zarezerwowanych multimediów (S1, S2, S3) Plik źródłowy jest większy niż limitu zdefiniowanego w tabeli, kodowania zadanie zakończy się niepowodzeniem. Kodujesz 4K rozwiązania źródeł długi czas, należy użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć wydajność konieczne. Jeśli zawartość 4K, który jest większy niż limit 260 GB jednostek zarezerwowanych multimediów S3, skontaktuj się z nami pod adresem amshelp@microsoft.com dla potencjalne środki zaradcze w celu obsługi danego scenariusza.

| Typ jednostki zarezerwowanej multimediów | Maksymalny rozmiar danych wejściowych (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
