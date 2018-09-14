---
title: Omówienie interfejsu API rozpoznawania emocji | Dokumentacja firmy Microsoft
description: Algorytm rozpoznawania emocji zaawansowane, oparte na chmurze firmy Microsoft umożliwiają tworzenie bardziej spersonalizowanych aplikacji za pomocą interfejsu API rozpoznawania emocji w usługach Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 210990b0f436fd75cb36e71ea28928c457a5232e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573558"
---
# <a name="what-is-emotion-api"></a>Czym jest interfejs API rozpoznawania emocji?

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji została zakończona w dniu 30 października 2017 r. Funkcje są teraz częścią [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

Interfejs API rozpoznawania emocji firmy Microsoft, co pozwala na tworzenie bardziej spersonalizowanych aplikacji przy użyciu algorytmu rozpoznawania emocji w chmurze firmy Microsoft — Zapraszamy!

### <a name="emotion-recognition"></a>Rozpoznawanie emocji

Interfejs API rozpoznawania emocji w wersji beta obrazu jako dane wejściowe przyjmuje i zwraca poziom pewności dla zestawu emocji dla każdej twarzy na obrazie, a także pole ograniczenia dla twarzy z interfejsu API rozpoznawania twarzy. Wykrywane emocje to szczęście, smutek, Zaskoczenie, gniew, strach, pogarda, obrzydzenie lub neutralna. Te emocje są przekazywane międzykulturowe i powszechnie za pomocą tego samego podstawowego twarzy, gdzie są identyfikowane za pomocą interfejsu API rozpoznawania emocji. 

**Interpretowanie wyników:** 

W interpretacji wyników z interfejsu API rozpoznawania emocji, emocji wykryto powinno być interpretowane jako emocji z najwyższym wynikiem, ponieważ wyniki są znormalizowane zgodnie do zsumowania do jednego. Użytkownicy mogą zdecydować się na Ustaw wyższy próg zaufania w swoich aplikacjach, w zależności od ich potrzeb. 

Aby uzyskać więcej informacji na temat wykrywanie emocji na zobacz dokumentacja interfejsu API: 
  * Podstawowe: Jeśli użytkownik wywołał już interfejs API rozpoznawania twarzy, mogą przesłać prostokąt twarzy jako dane wejściowe i korzystać z warstwy podstawowa. [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Jeśli użytkownik nie przesłać prostokąt twarzy, powinny używać Tryb standardowy.  [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Przykładowy sposób interpretowania przesyłanie strumieniowe filmów wideo za pomocą interfejsu API rozpoznawania emocji [jak analizować filmy wideo w czasie rzeczywistym](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
