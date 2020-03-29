---
title: Informacje o wersji — usługa Custom Vision
titleSuffix: Azure Cognitive Services
description: Uzyskaj najnowsze informacje o nowych wersjach zespołu Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647575"
---
# <a name="custom-vision-service-release-notes"></a>Informacje o wersji usługi Custom Vision

## <a name="may-2-2019-and-may-10-2019"></a>2 maja 2019 r. i 10 maja 2019 r.

- Poprawki błędów i ulepszenia wewnętrznej bazy danych

## <a name="may-23-2019"></a>23 maja 2019 r.

- Ulepszone środowisko użytkownika portalu związane z subskrypcjami platformy Azure ułatwia wybór katalogów platformy Azure.

## <a name="april-18-2019"></a>18 kwietnia 2019 r. 

- Dodano eksport wykrywania obiektów dla zestawu wizyjnego AI Dev Kit.
- UI ui tweaks, w tym wyszukiwania projektu.

## <a name="april-3-2019"></a>3 kwietnia 2019 r.

- Zwiększono limit obwiedni na obraz do 200. 
- Poprawki błędów, w tym znaczna aktualizacja wydajności dla modeli eksportowanych do TensorFlow. 

## <a name="march-26-2019"></a>26 marca 2019 r.

- Usługa Custom Vision service została wprowadzona w ogólną dostępność na platformie Azure!
- Dodano funkcję zaawansowanego szkolenia z nową zapleczem uczenia maszynowego w celu zwiększenia wydajności, szczególnie w przypadku wymagających zestawów danych i szczegółowej klasyfikacji. Dzięki zaawansowanemu szkoleniu można określić budżet czasu obliczeniowego na szkolenia, a usługa Custom Vision eksperymentalnie określi najlepsze ustawienia szkolenia i rozszerzenia. Aby uzyskać szybkie iteracje, można nadal korzystać z istniejącego szybkiego szkolenia.
- Wprowadzono interfejsy API 3.0. Ogłoszono wycofanie interfejsów API przed 3.0 w dniu 1 października 2019 r. Zobacz przewodników szybki start dokumentacji dla [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), lub [Przejdź](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) przykłady, jak rozpocząć.
- Zastąpiono "Domyślne iteracje" publikowania/likwidowania w interfejsach API 3.0.
- Dodano nowe cele eksportu modelu. Eksport pliku dockerfile został uaktualniony do obsługi ARM dla Raspberry Pi 3. Pomoc techniczna dotycząca eksportu została dodana do [zestawu Wizjoner AI Dev Kit.](https://visionaidevkit.com/).
- Zwiększono limit tagów na projekt do 500 dla warstwy S0. Zwiększono limit obrazów na projekt do 100 000 dla warstwy S0.
- Usunięto domenę dla dorosłych. Zamiast tego zaleca się domenę ogólną.
- Ogłoszono [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) ogólnej dostępności.  

## <a name="february-25-2019"></a>25 lutego 2019 r.

- Ogłoszono zakończenie projektów z ograniczoną próbą (projektów niezwiązanych z zasobem platformy Azure), ponieważ usługa Custom Vision zbliża się do zakończenia przejścia do publicznej wersji zapoznawczej platformy Azure. Od 25 marca 2019 r. CustomVision.ai lokacja będzie obsługiwać tylko wyświetlanie projektów skojarzonych z zasobem platformy Azure, takich jak bezpłatny zasób usługi Custom Vision. Do 1 października 2019 r. nadal będziesz mieć dostęp do istniejących ograniczonych projektów próbnych za pośrednictwem interfejsów API usługi Custom Vision. To daje czas na aktualizację kluczy interfejsu API dla wszystkich aplikacji, które zostały napisane za pomocą usługi Custom Vision. Po 1 października 2019 r. wszystkie ograniczone projekty próbne, które nie zostały przeniesione na platformę Azure, zostaną usunięte.

## <a name="january-22-2019"></a>22 stycznia 2019 r.

- Dodano obsługę nowych regionów platformy Azure: Zachodnie stany USA 2, wschodnie stany USA, wschodnie stany USA 2, Europa Zachodnia, Europa Północna, Azja Południowo-Wschodnia, Australia Wschodnia, Indie Środkowe, Południowa Wielka Brytania, Japonia Wschodnia i Północno-środkowe stany USA. Wsparcie jest kontynuowane dla południowo-środkowych stanów USA.

## <a name="december-12-2018"></a>12 grudnia 2018 r.

- Obsługa eksportu dla modeli wykrywania obiektów (wprowadzono domenę kompaktową do wykrywania obiektów).
- Naprawiono szereg problemów z dostępnością w celu poprawy obsługi czytnika ekranu i nawigacji klawiatury.
- Aktualizacje środowiska użytkownika dla przeglądarki obrazów i ulepszone wykrywanie obiektów w celu szybszego tagowania.  
- Zaktualizowano model podstawowy domeny wykrywania obiektów w celu lepszej jakości wykrywania obiektów.
- Poprawki.

## <a name="november-6-2018"></a>6 listopada 2018 r.

- Dodano obsługę domeny logo w wykrywaniu obiektów.

## <a name="october-9-2018"></a>9 października 2018 r.

- Wykrywanie obiektów wchodzi płatny podgląd. Teraz można tworzyć projekty wykrywania obiektów za pomocą zasobu platformy Azure.
- Dodano funkcję "Przenieś na platformę Azure" do witryny sieci Web, aby ułatwić uaktualnienie projektu z limitowanej wersji próbnej w celu połączenia z platformą Azure. projekt połączony z zasobami (F0 lub S0.) Można to znaleźć na stronie Ustawienia produktu.  
- Dodano eksport do ONNX 1.2, aby obsługiwać wersję systemu Windows ML systemu Windows 2018 October Update.
Poprawki błędów, w tym dla eksportu ONNX ze znakami specjalnymi.

## <a name="august-14-2018"></a>14 sierpnia 2018 r.

- Dodano widżet "Wprowadzenie", aby customvision.ai witrynę, aby poprowadzić użytkowników przez szkolenie projektowe.
- Dalsze ulepszenia potoku uczenia maszynowego w celu skorzystania z projektów wielolabelowych (nowa warstwa strat).

## <a name="june-28-2018"></a>28 czerwca 2018 r.

- Poprawki błędów & ulepszenia zaplecza.
- Włączono klasyfikację wieloklasową dla projektów, w których obrazy mają dokładnie jedną etykietę. W prognozach dla trybu wieloklasowego prawdopodobieństwa zsumują się do jednego (wszystkie obrazy są klasyfikowane między określonymi tagami).

## <a name="june-13-2018"></a>13 czerwca 2018 r.

- UX refresh, koncentruje się na łatwość obsługi i dostępności.
- Ulepszenia potoku uczenia maszynowego, aby korzystać z projektów wielolabelowych z dużą liczbą tagów.
- Naprawiono błąd w eksporcie TensorFlow. Włączone eksportowane przechowywanie wersji modelu, dzięki czemu iteracje mogą być eksportowane więcej niż jeden raz.

## <a name="may-7-2018"></a>7 maja 2018 r.

- Wprowadzono funkcję wykrywania obiektów w wersji zapoznawczej dla projektów w ograniczonej wersji próbnej.
- Uaktualnienie do interfejsów API 2.0
- Warstwa S0 rozwinięta do 250 tagów i 50 000 obrazów.
- Znaczne ulepszenia zaplecza w potoku uczenia maszynowego na potrzeby projektów klasyfikacji obrazów. Projekty szkolone po 27 kwietnia 2018 r. skorzystają z tych aktualizacji.
- Dodano eksportowanie modeli do ONNX na potrzeby użycia z uczeniem maszynowym systemu Windows.
- Dodano eksportowanie modeli do pliku Dockerfile. Dzięki temu można pobierać artefakty, aby tworzyć własne kontenery systemu Windows lub Linux, w tym DockerFile, model TensorFlow i kod usługi.
- W przypadku nowo przeszkolonych modeli eksportowanych do TensorFlow w domenach ogólnych (kompaktowych) i punktowych (kompaktowych) [wartości średnie są teraz (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)dla spójności we wszystkich projektach.

## <a name="march-1-2018"></a>1 marca 2018 r.

- Wprowadzono płatny podgląd i wbudowany w witrynę Azure portal. Projekty można teraz dołączać do zasobów Azure przy użyciu warstwy F0 (Bezpłatna) lub S0 (Standardowa). Wprowadzono projekty warstwy S0, które umożliwiają użycie do 100 tagów i 25 000 obrazów.
- Zmiany w zapleczu potoku uczenia maszynowego/parametru normalizacji. Dzięki temu klienci zyskują lepszą kontrolę nad kompromisami precyzji-przywołania podczas dostosowywania progu prawdopodobieństwa. W ramach tych zmian domyślny próg prawdopodobieństwa w portalu CustomVision.ai został ustawiony na 50%.

## <a name="december-19-2017"></a>19 grudnia 2017 r.

- Eksport do systemu Android (TensorFlow) dodano, oprócz wcześniej wydanego eksportu do iOS (CoreML).Export to Android (TensorFlow) added, in addition to previously released export to iOS (CoreML.) Umożliwia to eksport uczonego modelu kompaktowego do uruchomienia w trybie offline w aplikacji.
- Dodano „kompaktowe” domeny Retail i Landmark umożliwiające eksportowanie modeli do tych domen.
- Wydano wersję [1.2 interfejsu API szkolenia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) oraz [1.1 interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Zaktualizowane interfejsy API obsługują eksportowanie modeli, nową operację przewidywania, która nie zapisuje obrazów w katalogu „Prognozy”, a także wprowadzono operacje wsadowe w interfejsie API szkolenia.
- Ulepszenia środowiska użytkownika, w tym możliwość wyświetlania domen, które zostały użyte do szkolenia iteracji.
- Zaktualizowano [zestaw SDK języka C# i dane przykładowe](https://github.com/Microsoft/Cognitive-CustomVision-Windows).