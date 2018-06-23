---
title: Często zadawane pytania emocji interfejsu API | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące interfejsu API rozpoznawania emocji — warstwa w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347001"
---
# <a name="emotion-api-frequently-asked-questions"></a>Emocji interfejsu API często zadawane pytania
 
> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności API rozpoznawania emocji — warstwa na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z pomocy i obsługi technicznej na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pytanie**: *jakie typy obrazów uzyskać najlepsze wyniki z interfejsu API rozpoznawania emocji — warstwa?*

**Odpowiedzi**: Użyj pozbawiony przeszkód, pełną czołowego obrazy twarzy dla uzyskania najlepszych wyników. Niezawodność zmniejsza z częściowa kroje czołowego i interfejsu API rozpoznawania emocji — warstwa nie może rozpoznać emocji w przypadku kroju obrócony o 45 stopni lub więcej obrazów.

-----

**Pytanie**: *emocji ile API rozpoznawania emocji — warstwa zidentyfikuje?*

**Odpowiedzi**: interfejs API rozpoznawania emocji — warstwa rozpoznaje ośmiu różnych emocji powszechnie zaakceptowany: 
* Szczęście
* Smutek
* Zaskoczenie
* Gniew
* Strach
* Pogarda
* Obrzydzenie 
* Obojętność 

-----

**Pytanie**: *czy istnieje sposób przekazywania strumienia wideo na żywo do interfejsu API w celu uzyskania wynik równocześnie?*

**Odpowiedzi**: Użyj emocji opartej na obrazie interfejsu API i wywołać go w każdej ramce lub pominąć ramki wydajności.  Przykłady analizy przez klatka wideo są dostępne.

-----

**Pytanie**: *am I przekazywanie danych binarnych obrazu w, ale udostępnia mnie: "krój nieprawidłowy obraz.**

**Odpowiedzi**: oznacza to, że algorytm wystąpiły problemy podczas analizowania obrazu.  
* Formaty obrazu wejściowego obsługiwanych obejmuje JPEG, PNG, GIF (pierwszej ramki), BMP. 
* Rozmiar pliku obrazu nie powinna być większa niż 4MB
* Zakres rozmiaru wykrywalny krój to 36 x 36 do 4096 x 4096 pikseli. Nie są wykrywane kroje poza tym zakresem.
* Niektóre kroje mogą nie być wykrywane ze względu na problemy techniczne, np. w przypadku bardzo dużych kąty przedniej (ułożenia head), duża okluzji. Kroje czołowego i niemal czołowego ma uzyskania najlepszych wyników

-----
