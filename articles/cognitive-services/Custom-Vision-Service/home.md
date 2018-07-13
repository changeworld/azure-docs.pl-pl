---
title: Omówienie uczenia maszynowego usługi Custom Vision Service — Azure Cognitive Services | Microsoft Docs
description: Usługa Custom Vision Service to usługa Microsoft Cognitive Service umożliwiająca tworzenie niestandardowych klasyfikatorów obrazów na platformie Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087805"
---
# <a name="what-is-the-custom-vision-service"></a>Czym jest usługa Custom Vision Service?

Usługa Custom Vision Service to usługa Microsoft Cognitive Service umożliwiająca tworzenie niestandardowych klasyfikatorów obrazów. Dzięki usłudze można prościej i szybciej tworzyć, wdrażać i poprawiać klasyfikatory obrazów. Usługa Custom Vision Service zapewnia interfejs API REST oraz interfejs internetowy na potrzeby przekazywania obrazów i uczenia klasyfikatora.

## <a name="what-does-custom-vision-service-do-well"></a>Co usługa Custom Vision Service robi dobrze?

Usługa Custom Vision Service działa najlepiej, gdy element do sklasyfikowania jest wyraźnie widoczny na obrazie. 

Aby utworzyć klasyfikator lub detektor, potrzeba kilku obrazów. 50 obrazów na klasę to wystarczająco dużo, aby uruchomić prototyp. Metody używane przez usługę Custom Vision Service są odporne na różnice, więc możesz rozpocząć tworzenie prototypów z tak małą ilością danych. Oznacza to, że usługa Custom Vision Service nie jest dobrym rozwiązaniem w przypadku scenariuszy, w których chcesz wykrywać subtelne różnice. Na przykład w przypadku wykrywania niewielkich rys lub wgnieceń w scenariuszach związanych z zapewnianiem jakości.

## <a name="release-notes"></a>Informacje o wersji

### <a name="may-7-2018"></a>7 maja 2018 r.
- Wprowadzono funkcję wykrywania obiektów w wersji zapoznawczej dla projektów w ograniczonej wersji próbnej.
- Uaktualnienie do interfejsów API 2.0
- Warstwa S0 rozwinięta do 250 tagów i 50 000 obrazów. 
- Znaczne ulepszenia zaplecza w potoku uczenia maszynowego na potrzeby projektów klasyfikacji obrazów. Projekty szkolone po 27 kwietnia 2018 r. skorzystają z tych aktualizacji.
- Dodano eksportowanie modeli do ONNX na potrzeby użycia z uczeniem maszynowym systemu Windows.
- Dodano eksportowanie modeli do pliku Dockerfile. Dzięki temu można pobierać artefakty, aby tworzyć własne kontenery systemu Windows lub Linux, w tym DockerFile, model TensorFlow i kod usługi. 
- W przypadku nowo szkolonych modeli wyeksportowanych do TensorFlow w domenach General (kompaktowa) i Landmark (kompaktowa) [średnie wartości wynoszą teraz (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), aby zapewnić spójność ze wszystkimi projektami. 

### <a name="march-1-2018"></a>1 marca 2018 r.
- Wprowadzono płatną wersję zapoznawczą i dołączono ją do witryny Azure Portal. Projekty można teraz dołączać do zasobów Azure przy użyciu warstwy F0 (Bezpłatna) lub S0 (Standardowa). Wprowadzono projekty warstwy S0, które umożliwiają użycie do 100 tagów i 25 000 obrazów. 
- Zmiany w zapleczu potoku uczenia maszynowego/parametru normalizacji. Dzięki temu klienci zyskują lepszą kontrolę nad kompromisami precyzji-przywołania podczas dostosowywania progu prawdopodobieństwa. W ramach tych zmian domyślny próg prawdopodobieństwa w portalu CustomVision.ai został ustawiony na 50%.

### <a name="december-19-2017"></a>19 grudnia 2017 r.

- Dodano eksportowanie do systemu Android (TensorFlow) poza wcześniej wydanym eksportowaniem do systemu iOS (CoreML). Dzięki temu można uruchomić eksportowanie przeszkolonego modelu kompaktowego offline w aplikacji.
- Dodano „kompaktowe” domeny Retail i Landmark umożliwiające eksportowanie modeli do tych domen.
- Wydano wersję [1.2 interfejsu API szkolenia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) oraz [1.1 interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Zaktualizowane interfejsy API obsługują eksportowanie modeli, nową operację przewidywania, która nie zapisuje obrazów w katalogu „Prognozy”, a także wprowadzono operacje wsadowe w interfejsie API szkolenia.
- Ulepszenia środowiska użytkownika, w tym możliwość wyświetlania domen, które zostały użyte do szkolenia iteracji.
- Zaktualizowano [zestaw SDK języka C# i dane przykładowe](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak utworzyć klasyfikator](getting-started-build-a-classifier.md)
