---
title: Sceny, ujęcia i klatki kluczowe indeksatora wideo
titleSuffix: Azure Media Services
description: W tym temacie przedstawiono omówienie scen, ujęć i klatek kluczowych indeksatora wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245943"
---
# <a name="scenes-shots-and-keyframes"></a>Sceny, ujęcia i klatki kluczowe

Indeksator wideo obsługuje segmentowanie filmów na jednostki czasowe na podstawie właściwości strukturalnych i semantycznych. Ta funkcja umożliwia klientom łatwe przeglądanie, zarządzanie i edytowanie zawartości wideo na podstawie różnych ziarnistości. Na przykład na podstawie scen, ujęć i klatek kluczowych opisanych w tym temacie.   

![Sceny, ujęcia i klatki kluczowe](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Wykrywanie sceny  
 
Indeksator wideo określa, kiedy scena zmienia się w filmie na podstawie wskazówek wizualnych. Scena przedstawia pojedyncze zdarzenie i składa się z serii kolejnych ujęć, które są semantycznie związane. Miniatura sceny jest pierwszą klatką kluczową jej podstawowego ujęcia. Indeksator wideo segmentuje film w sceny na podstawie spójności kolorów w kolejnych ujęciach i pobiera czas rozpoczęcia i zakończenia każdej sceny. Wykrywanie sceny jest uważane za trudne zadanie, ponieważ polega na kwantyfikacji semantycznych aspektów filmów.

> [!NOTE]
> Dotyczy filmów zawierających co najmniej 3 sceny.

## <a name="shot-detection"></a>Wykrywanie strzałów

Indeksator wideo określa, kiedy ujęcie zmienia się w filmie na podstawie wskazówek wizualnych, śledząc zarówno nagłe, jak i stopniowe przejścia w schemacie kolorów sąsiednich klatek. Metadane ujęcia zawierają godzinę rozpoczęcia i zakończenia, a także listę klatek kluczowych zawartych w tym zdjęciu. Zdjęcia są kolejnymi klatkami pobranymi z tego samego aparatu w tym samym czasie.

## <a name="keyframe-detection"></a>Wykrywanie klatek kluczowych

Indeksator wideo wybiera klatki, które najlepiej reprezentują każde ujęcie. Klatki kluczowe to reprezentatywne klatki wybrane z całego filmu na podstawie właściwości estetycznych (na przykład kontrastu i stabilności). Indeksator wideo pobiera listę identyfikatorów klatek kluczowych jako część metadanych ujęcia, na podstawie których klienci mogą wyodrębnić klatkę kluczową jako obraz o wysokiej rozdzielczości.  

### <a name="extracting-keyframes"></a>Wyodrębnianie klatek kluczowych

Aby wyodrębnić klatki kluczowe o wysokiej rozdzielczości dla filmu, należy najpierw przesłać i zindeksować film.

![Klatek kluczowych](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Ze stroną video indexer

Aby wyodrębnić klatki kluczowe za pomocą witryny video Indexer, prześlij i zindeksuj film. Po zakończeniu zadania indeksowania kliknij przycisk **Pobierz** i wybierz **opcję Artefakty (ZIP).** Spowoduje to pobranie folderu artefaktów na komputer. 

![Klatek kluczowych](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Rozpaj i otwórz folder. W folderze *_KeyframeThumbnail* znajdziesz wszystkie klatki kluczowe, które zostały wyodrębnione z filmu. 

#### <a name="with-the-video-indexer-api"></a>Z interfejsem API indeksatora wideo

Aby uzyskać klatki kluczowe za pomocą interfejsu API indeksatora wideo, prześlij i zindeksuj film za pomocą połączenia [Przekaż wideo.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Po zakończeniu zadania indeksowania [wywołaj](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)wywołanie get video index . To daje wszystkie spostrzeżenia, że indeksator wideo wyodrębnione z zawartości w pliku JSON.  

Otrzymasz listę identyfikatorów klatek kluczowych jako część metadanych każdego zdjęcia. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Teraz musisz uruchomić każdy z tych identyfikatorów klatek kluczowych w wywołaniu [Pobierz miniatury.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Spowoduje to pobranie każdego z obrazów klatki kluczowej na komputer. 

## <a name="editorial-shot-type-detection"></a>Wykrywanie typu strzału redakcyjnego

Klatki kluczowe są skojarzone z ujęciami w wyjściowym JSON. 

Typ strzału skojarzony z pojedynczym strzałem w szczegółowych informacjach JSON reprezentuje jego typ redakcyjny. Te cechy typu ujęcia mogą okazać się przydatne podczas edycji filmów w klipach, zwiastunach lub podczas wyszukiwania określonego stylu klatki kluczowej do celów artystycznych. Różne typy są określane na podstawie analizy pierwszej klatki kluczowej każdego zdjęcia. Zdjęcia są identyfikowane przez skalę, rozmiar i położenie ścian pojawiających się w pierwszej klatce kluczowej. 

Rozmiar i skala ujęcia są określane na podstawie odległości między kamerą a twarzami wyświetlanymi w kadrze. Korzystając z tych właściwości, indeksator wideo wykrywa następujące typy stręców:

* Szeroki: pokazuje ciało całej osoby.
* Medium: pokazuje górną część ciała i twarz osoby.
* Zbliżenie: pokazuje głównie twarz osoby.
* Ekstremalne zbliżenie: pokazuje twarz osoby wypełniającej ekran. 

Typy śrutowania można również określić na podstawie położenia znaków tematu względem środka ramki. Ta właściwość definiuje następujące typy elementów strzału w indeksatorze wideo:

* Lewa ściana: osoba pojawia się po lewej stronie ramki.
* Ściana środkowa: osoba pojawia się w centralnym regionie ramki.
* Prawa ściana: osoba pojawia się po prawej stronie ramki.
* Na zewnątrz: osoba pojawia się na zewnątrz.
* Wewnątrz: osoba pojawia się w pomieszczeniu.

Dodatkowe cechy:

* Dwa ujęcia: pokazuje twarze dwóch osób średniej wielkości.
* Wiele twarzy: więcej niż dwie osoby.


## <a name="next-steps"></a>Następne kroki

[Sprawdź dane wyjściowe indeksatora wideo wytwarzane przez interfejs API](video-indexer-output-json-v2.md#scenes)
