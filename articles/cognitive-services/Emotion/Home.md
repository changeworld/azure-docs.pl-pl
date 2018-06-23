---
title: Omówienie emocji interfejsu API | Dokumentacja firmy Microsoft
description: Tworzenie bardziej spersonalizowanych aplikacji, za pomocą interfejsu API rozpoznawania emocji — warstwa kognitywnych usługami przy użyciu algorytmu rozpoznawania emocji najnowocześniejsze, oparte na chmurze firmy Microsoft.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347660"
---
# <a name="what-is-emotion-api"></a>Co to jest interfejs API rozpoznawania emocji — warstwa?

> [!IMPORTANT]
> Interfejs API emocji została uznana za przestarzałą na 30 października 2017 r. Funkcjonalność jest teraz częścią [powierzchni interfejsu API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Witamy w interfejsie API rozpoznawania emocji — warstwa firmy Microsoft, co pozwala na tworzenie bardziej spersonalizowanych aplikacji za pomocą algorytmu rozpoznawania emocji chmurowych firmy Microsoft.

### <a name="emotion-recognition"></a>Rozpoznawania emocji

Beta API rozpoznawania emocji — warstwa przyjmuje jako dane wejściowe obrazu i zwraca zaufania zestawu emocji dla każdej powierzchni obrazu, a także obwiedni krój z powierzchni interfejsu API. Emocji wykryto są szczęście, sadness, niespodziewanego, gniew, obawy, contempt, wstręt lub neutralna. Te emocji są przekazywane cross-culturally i powszechnie za pomocą tego samego podstawowego twarzy, jeśli są identyfikowane za pomocą interfejsu API rozpoznawania emocji — warstwa. 

**Interpretowanie wyników:** 

Przy interpretacji wyników z interfejsu API rozpoznawania emocji — warstwa, emocji wykryto powinny być rozumiane jako emocji z najwyższym wynik, zgodnie z znormalizowanych są wyniki do zsumowania do jednego. Użytkownicy mogą wybrać ustawić wyższy próg zaufania w swojej aplikacji, w zależności od potrzeb. 

Aby uzyskać więcej informacji na temat wykrywania emocji zobacz dokumentację interfejsu API: 
  * Podstawowe: Jeśli użytkownik została już wywołana powierzchni interfejsu API, mogą przesłać prostokąt krój jako dane wejściowe i używać warstwy podstawowa. [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standardowy: Jeśli użytkownik nie przedstawi prostokąt krój, powinny używać Tryb standardowy.  [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Przykładowy sposób interpretowania przesyłania strumieniowego wideo z interfejsem API rozpoznawania emocji — warstwa [sposobu analizowania wideo w czasie rzeczywistym](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
