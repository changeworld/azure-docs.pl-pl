---
title: Dostosowywanie modelu osoby za pomocą interfejsu API indeksatora wideo
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model osoby za pomocą interfejsu API indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127887"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Dostosowywanie modelu osoby za pomocą interfejsu API indeksatora wideo

Video Indexer obsługuje wykrywanie twarzy i rozpoznawanie gwiazd dla treści wideo. Funkcja rozpoznawania gwiazd obejmuje około miliona twarzy na podstawie powszechnie żądanego źródła danych, takiego jak IMDB, Wikipedia i najlepsi influencerzy LinkedIn. Twarze, które nie są rozpoznawane przez funkcję rozpoznawania gwiazd, są wykrywane, ale pozostawione bez nazwy. Po przesłaniu filmu do indeksatora wideo i powrocie wyników możesz wrócić i nazwać twarze, które nie zostały rozpoznane. Po oznaczeniu twarzy nazwą twarz i nazwa stają się dodawane do modelu osoby na koncie. Indeksator wideo rozpozna tę twarz w przyszłych filmach i poprzednich filmach.

Za pomocą interfejsu API indeksatora wideo można edytować twarze wykryte w klipie wideo, zgodnie z opisem w tym temacie. Można również użyć witryny video indexer, zgodnie z opisem w [Dostosuj model osoby za pomocą witryny wideo indeksatora](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Zarządzanie wieloma modelami osób

Indeksator wideo obsługuje wiele modeli osób na konto. Ta funkcja jest obecnie dostępna tylko za pośrednictwem interfejsów API indeksatora wideo.

Jeśli twoje konto obsługuje różne scenariusze przypadków użycia, możesz utworzyć wiele modeli osób na konto. Jeśli na przykład zawartość jest powiązana ze sportem, możesz utworzyć osobny model osoby dla każdego sportu (piłki nożnej, koszykówki, piłki nożnej itd.).

Po utworzeniu modelu można go użyć, podając identyfikator modelu określonego modelu osoby podczas przesyłania/indeksowania lub ponownego indeksowania wideo. Szkolenie nowej twarzy dla wideo aktualizuje określony model niestandardowy, z który został skojarzony.

Każde konto ma limit 50 modeli osób. Jeśli nie potrzebujesz obsługi wielu modeli osób, nie przypisuj identyfikatora modelu osoby do filmu podczas przesyłania/indeksowania lub ponownego indeksowania. W takim przypadku indeksator wideo używa domyślnego niestandardowego modelu osoby na koncie.

## <a name="create-a-new-person-model"></a>Tworzenie nowego modelu osoby

Aby utworzyć nowy model osoby na określonym koncie, należy użyć interfejsu API [modelu osoby.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

Odpowiedź zawiera nazwę i wygenerowany identyfikator modelu osoby, który został właśnie utworzony zgodnie z formatem poniższego przykładu.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Następnie używasz wartości **identyfikatora** dla **parametru personModelId** podczas [przesyłania wideo do indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [ponownego indeksowania filmu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Usuwanie modelu osoby

Aby usunąć niestandardowy model osoby z określonego konta, należy użyć interfejsu API [modelu delete osoby.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Po pomyślnym usunięciu modelu osoby indeks bieżących filmów, które były używane, pozostanie niezmieniony, dopóki nie zostanie ponownie zindeksowany. Po ponownym indeksowaniu twarze, które zostały nazwane w usuniętym modelu, nie zostaną rozpoznane przez indeksator wideo w bieżących filmach wideo, które zostały zindeksowane przy użyciu tego modelu, ale twarze będą nadal wykrywane. Bieżące filmy, które zostały zindeksowane przy użyciu usuniętego modelu, będą teraz korzystać z domyślnego modelu osoby na Twoim koncie. Jeśli twarze z usuniętego modelu są również nazwane w domyślnym modelu konta, te twarze będą nadal rozpoznawane w filmach.

Nie ma zwracanych zawartości, gdy model osoby zostanie pomyślnie usunięty.

## <a name="get-all-person-models"></a>Pobierz wszystkie modele person

Aby uzyskać wszystkie modele osoby na określonym koncie, użyj interfejsu API [modelu osoby.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

Odpowiedź zawiera listę wszystkich modeli osoby na koncie (w tym domyślny model osoby na określonym koncie) oraz każdą z ich nazw i identyfikatorów zgodnie z poniższym przykładem.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Możesz wybrać model, którego chcesz użyć dla `id` filmu wideo, używając `personModelId` wartości modelu Osoby dla parametru podczas [przesyłania wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [ponownego indeksowania wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizowanie twarzy

To polecenie umożliwia zaktualizowanie twarzy w filmie o nazwie przy użyciu identyfikatora wideo i identyfikatora twarzy. Ta akcja następnie aktualizuje model osoby, z którymi film został skojarzony podczas przesyłania/indeksowania lub ponownego indeksowania. Jeśli nie przypisano żadnego modelu osoby, aktualizuje domyślny model osoby na koncie.

Następnie system rozpoznaje wystąpienia tej samej twarzy w innych bieżących filmach, które współużytkuje ten sam model osoby. Rozpoznanie twarzy w innych bieżących filmach może zająć trochę czasu, ponieważ jest to proces wsadowy.

Możesz zaktualizować twarz, którą indeksator wideo rozpoznał jako gwiazdę, o nowej nazwie. Nowa nazwa, którą nadasz, będzie mieć pierwszeństwo przed wbudowanym uznaniem gwiazdy.

Aby zaktualizować twarz, użyj zaktualizowanego interfejsu API [twarzy wideo.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Nazwy są unikatowe dla modeli person, więc jeśli podasz `name` dwie różne ściany w tym samym modelu osoby tej samej wartości parametru, indeksator wideo wyświetla ściany jako tę samą osobę i zbiega je po ponownym wydekgowania wideo.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu osoby za pomocą witryny video Indexer](customize-person-model-with-website.md)
