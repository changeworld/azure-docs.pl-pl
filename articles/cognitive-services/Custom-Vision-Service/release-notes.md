---
title: Informacje o wersji — Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: a4b6b1d267e9c86a03644129ee639b74ed214d88
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408361"
---
# <a name="custom-vision-service-release-notes"></a>Informacje o wersji usługi Custom Vision


## <a name="december-12-2018"></a>12 grudnia 2018 r.
- Obsługuje eksportowania dla modeli wykrywanie obiektów (wprowadzono obiekt wykrywania Compact domeny).
- Rozwiązano szereg problemów dotyczących ułatwień dostępu dla czytnika zawartości ekranu ulepszone i obsługa nawigacji klawiatury. 
- Aktualizacje środowiska użytkownika Przeglądarka obrazów i wykrywania obiektu ulepszone znakowanie umożliwiający znakowanie szybciej.  
- Zaktualizowano podstawowy model dla domeny wykrywania obiektu lepsze wykrywanie obiektów jakości. 
- Poprawki błędów.

## <a name="november-6-2018"></a>6 listopada 2018 r.
- Dodano obsługę dla domeny Logo w wykrywanie obiektów.

## <a name="october-9-2018"></a>9 października 2018 r.
- Wykrywanie obiektów wprowadza płatna wersja zapoznawcza. Można teraz tworzyć projekty wykrywanie obiektów z zasobów platformy Azure.
- Dodano funkcję "Przenieś na platformie Azure" do witryny sieci Web, aby ułatwić uaktualnienie projektu ograniczonej wersji próbnej, aby utworzyć łącze do platformy Azure. Projekt połączonych zasobów (F0 lub S0). To na stronie Ustawienia można znaleźć na produkt.  
- Dodano Eksport do 1.2 ONNX, obsługi wersji aktualizacji października 2018 Windows Windows ML.
Eksportowanie poprawki błędów, w tym ONNX przy użyciu znaków specjalnych. 

## <a name="august-14-2018"></a>14 sierpnia 2018 r.
- Dodano widżet "Wprowadzenie" do lokacji customvision.ai prowadzą użytkowników przez szkolenia dotyczącego projektu. 
- Dodatkowe ulepszenia potoku uczenia maszynowego korzystną multilabel projektów (nową warstwę straty).

## <a name="june-28-2018"></a>28 czerwca 2018 r.
- Ulepszenia poprawki błędów i wewnętrznej bazy danych.
- Włączone Wieloklasowej klasyfikacji, w przypadku projektów, których obrazy mają dokładnie jedną etykietę. W prognozy dla trybu wieloklasowej prawdopodobieństwa obliczymy sumę do jednego (wszystkie obrazy są klasyfikowane między z określonymi tagami).

## <a name="june-13-2018"></a>13 czerwca 2018 r.
- Odświeżanie UX koncentruje się na łatwość użycia i ułatwienia dostępu. 
- Ulepszenia potoku uczenia maszynowego korzystną multilabel projektów z dużą liczbą tagów.
- Usunięto usterkę występującą podczas eksportowania TensorFlow. Włączone modelu eksportowanych funkcji przechowywania wersji, więc iteracji można wyeksportować więcej niż jeden raz. 

## <a name="may-7-2018"></a>7 maja 2018 r.
- Wprowadzono funkcję wykrywania obiektów w wersji zapoznawczej dla projektów w ograniczonej wersji próbnej.
- Uaktualnienie do interfejsów API 2.0
- Warstwa S0 rozwinięta do 250 tagów i 50 000 obrazów. 
- Znaczne ulepszenia zaplecza w potoku uczenia maszynowego na potrzeby projektów klasyfikacji obrazów. Projekty szkolone po 27 kwietnia 2018 r. skorzystają z tych aktualizacji.
- Dodano eksportowanie modeli do ONNX na potrzeby użycia z uczeniem maszynowym systemu Windows.
- Dodano eksportowanie modeli do pliku Dockerfile. Dzięki temu można pobierać artefakty, aby tworzyć własne kontenery systemu Windows lub Linux, w tym DockerFile, model TensorFlow i kod usługi. 
- Nowo wykwalifikowani modeli wyeksportowane do TensorFlow w ogóle (CD) i domen charakterystycznych elementów krajobrazu (CD) [wartości oznaczają są teraz (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), w celu zachowania spójności we wszystkich projektach. 

## <a name="march-1-2018"></a>1 marca 2018 r.
- Wprowadzono płatną wersję zapoznawczą i dołączono ją do witryny Azure Portal. Projekty można teraz dołączać do zasobów Azure przy użyciu warstwy F0 (Bezpłatna) lub S0 (Standardowa). Wprowadzono projekty warstwy S0, które umożliwiają użycie do 100 tagów i 25 000 obrazów. 
- Zmiany w zapleczu potoku uczenia maszynowego/parametru normalizacji. Dzięki temu klienci zyskują lepszą kontrolę nad kompromisami precyzji-przywołania podczas dostosowywania progu prawdopodobieństwa. W ramach tych zmian domyślny próg prawdopodobieństwa w portalu CustomVision.ai został ustawiony na 50%.

## <a name="december-19-2017"></a>19 grudnia 2017 r.

- Dodano eksportowanie do systemu Android (TensorFlow) poza wcześniej wydanym eksportowaniem do systemu iOS (CoreML). Dzięki temu można uruchomić eksportowanie przeszkolonego modelu kompaktowego offline w aplikacji.
- Dodano „kompaktowe” domeny Retail i Landmark umożliwiające eksportowanie modeli do tych domen.
- Wydano wersję [1.2 interfejsu API szkolenia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) oraz [1.1 interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Zaktualizowane interfejsy API obsługują eksportowanie modeli, nową operację przewidywania, która nie zapisuje obrazów w katalogu „Prognozy”, a także wprowadzono operacje wsadowe w interfejsie API szkolenia.
- Ulepszenia środowiska użytkownika, w tym możliwość wyświetlania domen, które zostały użyte do szkolenia iteracji.
- Zaktualizowano [zestaw SDK języka C# i dane przykładowe](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

