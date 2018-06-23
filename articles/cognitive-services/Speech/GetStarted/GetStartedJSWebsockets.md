---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku JavaScript | Dokumentacja firmy Microsoft
description: Za pomocą interfejsu API rozpoznawania mowy firmy Microsoft w usługach kognitywnych programować aplikacje, które stale przekonwertowana na tekst rozmowy audio.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 56c41fd7f6a00d80bc6bccd61894654e057e926e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347409"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Rozpoczynanie pracy z interfejsem API rozpoznawania mowy w języku JavaScript

Można programować aplikacje, które Konwertuj rozmowy audio na tekst za pomocą interfejsu API rozpoznawania mowy. Korzysta z biblioteki klienckiej JavaScript [protokół WebSocket usługi mowy](../API-Reference-REST/websocketprotocol.md), co pozwala na rozmowy i otrzymywać przetłumaczone tekstu jednocześnie. Ten artykuł ułatwia szybkie wprowadzenie do interfejsu API rozpoznawania mowy w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API rozpoznawania mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych. Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

> [!IMPORTANT]
> Pobierz klucz subskrypcji. Zanim będzie można użyć bibliotek klienckich mowy, musisz mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Rozpoczęcie pracy

W tej sekcji firma Microsoft przeprowadzi Cię przez kroki niezbędne do załadowania strony HTML próbki. Przykładowa zawartość pliku znajduje się w naszej [repozytorium github](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Możesz **bezpośrednio otworzyć próbki** z repozytorium, lub **Otwórz próbki z lokalną kopią** repozytorium. 

> [!NOTE]
> Niektóre przeglądarki blokować dostęp mikrofon w źródle cofnąć zabezpieczeń. Dlatego zaleca się przykład hosta / "aplikacji" w protokole https, aby przygotować go do pracy na wszystkich obsługiwanych przeglądarkach. 

### <a name="open-the-sample-directly"></a>Otwórz bezpośrednio próbki

Uzyskać klucz subskrypcji, jak opisano powyżej. Następnie otwórz [łączy się z przykładem](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Spowoduje to załadowanie strony w domyślnej przeglądarce (renderowany przy użyciu [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otwórz próbki z lokalną kopią

Aby wypróbować próbki lokalnie, Klonuj to repozytorium:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

kompilowanie źródeł TypeScript i pakietu/browserfy ich w jednym pliku JavaScript ([npm](https://www.npmjs.com/) musi być zainstalowany na tym komputerze). Zmiany w folderze głównym repozytorium sklonowany, a następnie uruchom polecenia:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otwórz `samples\browser\Sample.html` w ulubionej przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat do nich zestawu SDK do własnych strony sieci Web jest dostępna [tutaj](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Uwagi

- Interfejs API rozpoznawania mowy obsługuje trzy [tryby rozpoznawania](../concepts.md#recognition-modes). Możesz przełączyć tryb aktualizując **Setup()** znaleźć w pliku Sample.html funkcji. Przykład ustawia tryb `Interactive` domyślnie. Aby zmienić tryb, należy zaktualizować parametr `SR.RecognitionMode.Interactive` do innego trybu. Na przykład zmienić parametru `SR.RecognitionMode.Conversation`.
- Aby uzyskać pełną listę obsługiwanych języków, zobacz [obsługiwanych języków](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Powiązane tematy

- [Repozytorium przykładowy interfejs API rozpoznawania mowy JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Wprowadzenie do interfejsu API REST](GetStartedREST.md)
