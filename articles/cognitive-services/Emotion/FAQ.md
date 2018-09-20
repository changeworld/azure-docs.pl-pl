---
title: Często zadawane pytania — interfejs API rozpoznawania emocji
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API rozpoznawania emocji.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 56b3f2899f1e77c1a2b840285e2c69bdb8987ff4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363092"
---
# <a name="emotion-api-frequently-asked-questions"></a>Interfejs API rozpoznawania emocji — często zadawane pytania
 
> [!IMPORTANT]
> Wersja zapoznawcza interfejsu API wideo zakończy się 30 października 2017 r. Wypróbuj nowy [wersji zapoznawczej interfejsu API indeksatora wideo](https://azure.microsoft.com/services/cognitive-services/video-indexer/) łatwe prowadzenie analiz materiałów wideo i ulepszanie środowiska odnajdywania zawartości, takie jak wyniki wyszukiwania oparte na wykrywaniu wypowiedzianych słów, twarzy, znaki i emocje. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania emocji na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pytanie**: *jakie typy obrazów okazję wynika z interfejsu API rozpoznawania emocji?*

**Odpowiedź**: Korzystanie z pozbawiony przeszkód, pełny czołowego obrazów twarzy uzyskać jak najlepsze rezultaty. Niezawodność zmniejsza się za pomocą częściowej czołowego twarzy i interfejs API rozpoznawania emocji nie może rozpoznać emocji na obrazach, gdzie twarz jest obracany 45 stopni lub więcej.

-----

**Pytanie**: *emocji ile interfejs API rozpoznawania emocji zidentyfikuje?*

**Odpowiedź**: interfejs API rozpoznawania emocji rozpoznaje emocje osiem powszechnie akceptowane różnych: 
* Szczęście
* Smutek
* Zaskoczenie
* Gniew
* Strach
* Pogarda
* Obrzydzenie 
* Obojętność 

-----

**Pytanie**: *czy istnieje sposób strumienia wideo na żywo do interfejsu API i jednocześnie uzyskać wynik?*

**Odpowiedź**: Użyj interfejsu API rozpoznawania emocji na podstawie obrazu i wywołać go w każdej klatce lub od razu przejść ramki dla wydajności.  Wideo próbki analizy klatka po klatce są dostępne.

-----

**Pytanie**: *am I przekazywanie danych obrazów binarnych w, ale dzięki temu będę: "nieprawidłowy twarzy obrazu.**

**Odpowiedź**: oznacza to, że algorytm miał wystąpił problem podczas analizowania obrazu.  
* Formaty obsługiwane obrazu wejściowego obejmuje JPEG, PNG, GIF (pierwsza ramka), BMP. 
* Rozmiar pliku obrazu nie powinien być większy niż 4MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Twarze poza tym zakresem nie zostanie wykryty.
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, np. w przypadku bardzo dużych powierzchni kąty (poza główne), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
