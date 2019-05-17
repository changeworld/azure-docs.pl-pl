---
title: Informacje o wersji — Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 4b4e449b9260debbe90523141cacc4ed76219490
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787947"
---
# <a name="custom-vision-service-release-notes"></a>Informacje o wersji usługi Custom Vision

## <a name="may-2-2019-and-may-10-2019"></a>2 maja 2019 r i 10 maja 2019 r.

- Ulepszenia poprawki błędów i wewnętrznej bazy danych

## <a name="may-23-2019"></a>23 maja 2019 r.

- Ulepszony portal środowisko środowiska użytkownika związane z subskrypcjami platformy Azure, co ułatwia wybieranie katalogów platformy Azure.

## <a name="april-18-2019"></a>18 kwietnia 2019 r. 

- Dodano wykrywanie obiektów eksportu sztucznej Inteligencji wizji deweloperski.
- Ulepszenia interfejsu użytkownika, w tym projekcie wyszukiwania.

## <a name="april-3-2019"></a>3 kwietnia 2019 r.

- Zwiększono limit liczby obwiedni na obraz do 200. 
- Poprawki błędów, w tym aktualizacja wydajności istotne dla modeli wyeksportowane do TensorFlow. 

## <a name="march-26-2019"></a>26 marca 2019 r.

- Usługa Custom Vision Service została wprowadzona ogólnej dostępności na platformie Azure!
- Dodano funkcję Zaawansowane szkolenie przy użyciu nowego zaplecza w celu zwiększenia wydajności, szczególnie na trudne zestawów danych i szczegółowych klasyfikacji uczenia maszynowego. Za pomocą zaawansowanych szkoleń, można określić budżet godziny obliczeń dla szkolenia i Custom Vision doświadczalnie zidentyfikuje najlepsze ustawienia szkolenia i rozszerzenia. Dla szybkie iteracje możesz w dalszym ciągu używać istniejących szybkie szkolenia.
- Wprowadzono 3.0 interfejsów API. Ogłoszono najbliższych amortyzacja interfejsów API pre 3.0 na 1 października 2019 r. Zobacz dokumentację Szybki Start dla [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [węzła](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), lub [Przejdź](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) przykłady o tym, jak rozpocząć pracę.
- Zastąpione "Domyślne iteracji" Publikowanie/Cofnij publikowanie w 3.0 interfejsów API.
- Dodano nowe obiekty docelowe eksportu modelu. Eksportu plik Dockerfile został uaktualniony do obsługi ARM dla urządzenia Raspberry Pi 3. Dodano obsługę eksportowania [zestaw deweloperski sztucznej Inteligencji wizji.](https://visionaidevkit.com/).
- Zwiększone ograniczenie tagów w projektu do 500 dla warstwy S0. Zwiększona limit obrazy na projekt, do 100 000 dla warstwy S0.
- Usunięto treści dla dorosłych w domenie. Zamiast tego zaleca się domeny głównej.
- Ogłoszono [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) za usługi ogólnie dostępne.  

## <a name="february-25-2019"></a>25 lutego 2019 r.

- Ogłoszono koniec ograniczonej wersji próbnej projekty (projekty, nie jest skojarzony z zasobem platformy Azure), ponieważ Custom Vision zbliżą się do ukończenia jej przeniesienie do publicznej platformy Azure w wersji zapoznawczej. Począwszy od 25 marca 2019 r lokacji CustomVision.ai obejmie tylko projekty wyświetlania skojarzone z zasobem platformy Azure, takich jak bezpłatny zasób Custom Vision. Do 1 października 2019 r nadal będzie mogli korzystać z istniejących projektów ograniczonej wersji próbnej poprzez niestandardowe interfejsy API przetwarzania. Umożliwi to czas aktualizacji kluczy interfejsu API dla aplikacji napisanych przy użyciu niestandardowego przetwarzania. Po 1 października 2019 r zostaną usunięte żadne ograniczone próbne projekty, które nie zostały przeniesione do platformy Azure.

## <a name="january-22-2019"></a>22 stycznia 2019 r.

- Pomoc techniczna dodane dla nowych regionów systemu Azure: Zachodnie stany USA 2, wschodnie stany USA, wschodnie stany USA 2, Europa Zachodnia, Europa Północna, Azja południowo-wschodnia, Australia Wschodnia, Indie środkowe, południowe Zjednoczone Królestwo, Japonia, część wschodnia i Północnośrodkowa. Obsługa jest powtarzany południowo-środkowe stany USA.

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

- Poprawki błędów i ulepszenia w wewnętrznej bazie danych.
- Włączone wieloklasowej klasyfikacji, w przypadku projektów, których obrazy mają dokładnie jedną etykietę. W prognozy dla trybu wieloklasowej prawdopodobieństwa obliczymy sumę do jednego (wszystkie obrazy są klasyfikowane między z określonymi tagami).

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

- Wprowadzony płatna wersja zapoznawcza i dołączona do witryny Azure portal. Projekty można teraz dołączać do zasobów Azure przy użyciu warstwy F0 (Bezpłatna) lub S0 (Standardowa). Wprowadzono projekty warstwy S0, które umożliwiają użycie do 100 tagów i 25 000 obrazów.
- Zmiany w zapleczu potoku uczenia maszynowego/parametru normalizacji. Dzięki temu klienci zyskują lepszą kontrolę nad kompromisami precyzji-przywołania podczas dostosowywania progu prawdopodobieństwa. W ramach tych zmian domyślny próg prawdopodobieństwa w portalu CustomVision.ai został ustawiony na 50%.

## <a name="december-19-2017"></a>19 grudnia 2017 r.

- Dodano eksportowanie do systemu Android (TensorFlow) poza wcześniej wydanym eksportowaniem do systemu iOS (CoreML). Dzięki temu można uruchomić eksportowanie przeszkolonego modelu kompaktowego offline w aplikacji.
- Dodano „kompaktowe” domeny Retail i Landmark umożliwiające eksportowanie modeli do tych domen.
- Wydano wersję [1.2 interfejsu API szkolenia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) oraz [1.1 interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Zaktualizowane interfejsy API obsługują eksportowanie modeli, nową operację przewidywania, która nie zapisuje obrazów w katalogu „Prognozy”, a także wprowadzono operacje wsadowe w interfejsie API szkolenia.
- Ulepszenia środowiska użytkownika, w tym możliwość wyświetlania domen, które zostały użyte do szkolenia iteracji.
- Zaktualizowano [zestaw SDK języka C# i dane przykładowe](https://github.com/Microsoft/Cognitive-CustomVision-Windows).