---
title: Często zadawane pytania — interfejs API rozpoznawania emocji
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API rozpoznawania emocji.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: d4e074a789a23d18349a32d65f7a4e9a6f41f6e8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860035"
---
# <a name="emotion-api-frequently-asked-questions"></a>Interfejs API rozpoznawania emocji — często zadawane pytania

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania emocji na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pytanie**: *Jakie typy obrazów uzyskać najlepsze wyniki z interfejsu API rozpoznawania emocji?*

**Odpowiedź**: Użyj pozbawiony przeszkód, pełny czołowego obrazy twarzy uzyskać jak najlepsze rezultaty. Niezawodność zmniejsza się za pomocą częściowej czołowego twarzy i interfejs API rozpoznawania emocji nie może rozpoznać emocji na obrazach, gdzie twarz obraca się ponad 45 stopni.

-----

**Pytanie**: *Jak wiele emocji można zidentyfikować interfejsu API rozpoznawania emocji*

**Odpowiedź**: Interfejs API rozpoznawania emocji rozpoznaje ośmiu różnych emocji powszechnie akceptowane:
* Szczęście
* Smutek
* Zaskoczenie
* Gniew
* Strach
* Pogarda
* Obrzydzenie
* Obojętność

-----

**Pytanie**: *Czy istnieje sposób strumienia wideo na żywo do interfejsu API i jednocześnie uzyskać wynik?*

**Odpowiedź**: Użyj obrazu na podstawie interfejs API rozpoznawania emocji i wywołać go na każdej ramce lub pominąć ramki dla wydajności.  Wideo próbki analizy klatka po klatce są dostępne.

-----

**Pytanie**: *Próbuję jakoś dane binarny w, ale dzięki temu będę: "Nieprawidłowa twarzy obrazu.**

**Odpowiedź**: Ten komunikat oznacza, że algorytm miał wystąpił problem podczas analizowania obrazu.  
* Formaty obsługiwane obrazu wejściowego obejmują JPEG, PNG, GIF (pierwsza ramka), BMP
* Rozmiar pliku obrazu nie powinien być większy niż 4 MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Nie można wykryć twarzy poza tym zakresem
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, na przykład dużych powierzchni kąty (head ułożenia), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
