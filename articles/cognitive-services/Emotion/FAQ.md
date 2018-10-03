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
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238543"
---
# <a name="emotion-api-frequently-asked-questions"></a>Interfejs API rozpoznawania emocji — często zadawane pytania

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostaną wycofane w dniu 15 lutego 2019 r. Możliwości rozpoznawania emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania emocji na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pytanie**: *jakie typy obrazów okazję wynika z interfejsu API rozpoznawania emocji?*

**Odpowiedź**: Korzystanie z pozbawiony przeszkód, pełny czołowego obrazów twarzy uzyskać jak najlepsze rezultaty. Niezawodność zmniejsza się za pomocą częściowej czołowego twarzy i interfejs API rozpoznawania emocji nie może rozpoznać emocji na obrazach, gdzie twarz obraca się ponad 45 stopni.

-----

**Pytanie**: *emocji ile interfejs API rozpoznawania emocji zidentyfikuje?*

**Odpowiedź**: interfejs API rozpoznawania emocji rozpoznaje ośmiu różnych emocji powszechnie akceptowane:
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

**Pytanie**: *Próbuję jakoś dane binarny w, ale dzięki temu będę: "nieprawidłowy twarzy obrazu.**

**Odpowiedź**: ten komunikat oznacza, że algorytm miał wystąpił problem podczas analizowania obrazu.  
* Formaty obsługiwane obrazu wejściowego obejmują JPEG, PNG, GIF (pierwsza ramka), BMP
* Rozmiar pliku obrazu nie powinien być większy niż 4 MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Nie można wykryć twarzy poza tym zakresem
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, na przykład dużych powierzchni kąty (head ułożenia), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
