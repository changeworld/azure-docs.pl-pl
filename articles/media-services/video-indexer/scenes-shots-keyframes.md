---
title: Video Indexer scen, zrzutów i klatek kluczowych
titleSuffix: Azure Media Services
description: Ten temat zawiera omówienie Video Indexer scen, zrzutów i klatek kluczowych.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860230"
---
# <a name="scenes-shots-and-keyframes"></a>Sceny, ujęcia i klatki kluczowe

Video Indexer obsługuje segmentacji wideo na jednostki czasowe na podstawie właściwości strukturalnych i semantycznych. Ta funkcja umożliwia klientom łatwe przeglądanie i Edytowanie zawartości wideo w oparciu o różne stopnia szczegółowości. Na przykład na podstawie scen, zrzutów i klatek kluczowych opisanych w tym temacie.   

![Sceny, ujęcia i klatki kluczowe](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Wykrywanie sceny  
 
Video Indexer określa, kiedy scena zmienia się w wideo na podstawie podpowiedzi wizualizacji. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzutów, które są semantycznie powiązane. Miniatura sceny to pierwsza klatka kluczowa podstawowego zrzutu. Indeksator wideo segmentuje wideo w scenach na podstawie spójności kolorów dla kolejnych zrzutów i pobiera godzinę rozpoczęcia i zakończenia każdej sceny. Wykrywanie sceny jest uznawane za trudne zadanie, ponieważ obejmuje to obliczanie ilości semantycznych aspektów wideo.

> [!NOTE]
> Dotyczy wideo, które zawiera co najmniej 3 sceny.

## <a name="shot-detection"></a>Wykrywanie ujęć

Video Indexer określa, kiedy zrzut zmienia się w filmie wideo na podstawie podpowiedzi wizualizacji, śledząc jednocześnie przekroczenia i stopniowe przejścia w schemacie kolorów sąsiadujących ramek. Metadane zrzutu obejmują godzinę początkową i końcową oraz listę klatek kluczowych zawartych w tym zrzucie. Zrzuty to kolejne ramki pobierane z tego samego aparatu w tym samym czasie.

## <a name="keyframe-detection"></a>Wykrywanie klatek kluczowych

Wybiera ramki, które najlepiej reprezentują zrzut. Klatki kluczowe to reprezentatywne ramki wybrane z całego wideo w oparciu o właściwości estetyczne (na przykład kontrast i trwałość). Video Indexer pobiera listę identyfikatorów klatek kluczowych jako część metadanych zrzutu, na podstawie których klienci mogą wyodrębnić miniaturę klatki kluczowej. 

Ramki kluczowe są skojarzone ze zrzutami w wyjściowym kodzie JSON. 

## <a name="editorial-shot-type-detection"></a>Wykrywanie typu zrzutu redakcyjnego

Typ zrzutu skojarzony z pojedynczym zdjęciem w formacie JSON usługi Insights reprezentuje jego typ redakcyjny. Te cechy typów zastrzelonych mogą być przydatne podczas edytowania wideo do klipów, przyczep lub podczas wyszukiwania określonego stylu klatki kluczowej dla celów artystycznych. Różne typy są określane na podstawie analizy pierwszej klatki kluczowej każdego z nich. Zrzuty są identyfikowane przez skalę, rozmiar i lokalizację powierzchni występujących w pierwszej klatce kluczowej. 

Rozmiar i skala zrzutu są ustalane na podstawie odległości między kamerą i powierzchnią występującą w ramce. Korzystając z tych właściwości, Video Indexer wykrywa następujące typy zrzutów:

* Szeroki: pokazuje treść całej osoby.
* Średni: pokazuje górną treść osoby i jej głowę.
* Zamknij: głównie pokazuje głowę osoby.
* Skrajne Zamknięcie: pokazuje głowę osoby wypełniającej ekran. 

Typy zrzutów można także określić według lokalizacji znaków tematu w odniesieniu do środka ramki. Ta właściwość definiuje następujące typy zrzutów w Video Indexer:

* Lewa strona: osoba pojawia się w lewej części ramki.
* Wyśrodkuj na środku: osoba pojawia się w centralnym regionie ramki.
* Prawa strona: osoba pojawia się po prawej stronie ramki.
* Na zewnątrz: osoba zostanie wyświetlona w ustawieniu na zewnątrz.
* Pozostała: osoba zostanie wyświetlona w ustawieniu pomieszczeń.

Dodatkowe cechy:

* Dwa zrzuty: pokazuje powierzchnie dwóch osób o średnim rozmiarze.
* Wiele twarzy: więcej niż dwie osoby.

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie Video Indexer danych wyjściowych wytwarzanych przez interfejs API](video-indexer-output-json-v2.md#scenes)
