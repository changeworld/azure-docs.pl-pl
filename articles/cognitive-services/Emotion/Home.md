---
title: Co to jest interfejs API rozpoznawania emocji?
titlesuffix: Azure Cognitive Services
description: Używaj algorytmów rozpoznawania emocji opartych na chmurze, aby tworzyć bardziej spersonalizowane aplikacje.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: abf94e0ab6ebb3df649b1958503c086feb4fa19e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237130"
---
# <a name="what-is-the-emotion-api"></a>Co to jest interfejs API rozpoznawania emocji?

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

Witamy w interfejsie API rozpoznawania emocji firmy Microsoft, który pozwala na tworzenie bardziej spersonalizowanych aplikacji przy użyciu opartego na chmurze algorytmu rozpoznawania emocji firmy Microsoft.

### <a name="emotion-recognition"></a>Rozpoznawanie emocji

Interfejs API rozpoznawania emocji w wersji beta jako dane wejściowe bierze obraz i zwraca poziom pewności dla zestawu emocji w przypadku każdej twarzy na obrazie, a także pole ograniczenia z interfejsu API rozpoznawania twarzy dla każdej twarzy. Wykrywane emocje to szczęście, smutek, zaskoczenie, gniew, strach, pogarda, obrzydzenie lub obojętność. Te emocje mają charakter uniwersalny, są czytelne w różnych kulturach i komunikowane tą samą podstawową mimiką, która jest rozpoznawana przez interfejs API rozpoznawania emocji.

**Interpretowanie wyników:**

Przy interpretacji wyników z interfejsu API rozpoznawania emocji za wykrytą emocję powinna być uznawana ta z najwyższą oceną, ponieważ wyniki są normalizowane tak, aby sumowały się do jednego. Zależnie od potrzeb użytkownicy mogą zdecydować się na ustawienie wyższego progu ufności w swoich aplikacjach.

Aby uzyskać więcej informacji na temat wykrywania emocji, zobacz dokumentację interfejsu API:
  * Tryb podstawowy: jeśli użytkownik wywołał już interfejs API rozpoznawania twarzy, może przesłać prostokąt twarzy jako dane wejściowe i skorzystać z warstwy podstawowej. [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Tryb standardowy: jeśli użytkownik nie przesłał prostokąta twarzy, powinien użyć trybu standardowego.  [Dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Aby zapoznać się z przykładem interpretowania wideo przesyłanego strumieniowo za pomocą interfejsu API rozpoznawania emocji, zobacz [Jak analizować wideo w czasie rzeczywistym](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
