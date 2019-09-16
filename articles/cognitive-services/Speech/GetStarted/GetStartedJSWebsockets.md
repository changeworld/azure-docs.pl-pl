---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania rozpoznawanie mowy Bing w języku JavaScript | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API rozpoznawania rozpoznawanie mowy Bing w Cognitive Services do tworzenia aplikacji, które ciągle konwertują dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965812"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Wprowadzenie do interfejsu API rozpoznawania mowy w języku JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Można opracowywać aplikacje, które konwertują dźwięk mówiony na tekst za pomocą interfejsu API rozpoznawania mowy. Biblioteka kliencka języka JavaScript korzysta z [protokołu WebSocket usługi rozpoznawania mowy](../API-Reference-REST/websocketprotocol.md), który umożliwia rozmowę i odbieranie tekstu uzyskanego jednocześnie. Ten artykuł pomoże Ci rozpocząć pracę z interfejsem API rozpoznawania mowy w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie interfejsu API rozpoznawania mowy i pobieranie klucza subskrypcji bezpłatnej wersji próbnej

Speech API jest częścią Cognitive Services. Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

> [!IMPORTANT]
> Pobierz klucz subskrypcji. Aby można było korzystać z bibliotek klienckich mowy, należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Wprowadzenie

W tej sekcji przeprowadzimy Cię przez kroki niezbędne do załadowania przykładowej strony HTML. Przykład znajduje się w naszym [repozytorium GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Możesz **otworzyć przykład bezpośrednio** z repozytorium lub **otworzyć przykład z lokalnej kopii** repozytorium.

> [!NOTE]
> Niektóre przeglądarki blokują dostęp do mikrofonu przy niezabezpieczonym pochodzeniu. Dlatego zaleca się hostowanie "przykładowej"/"aplikacji" przy użyciu protokołu HTTPS w celu umożliwienia pracy z wszystkimi obsługiwanymi przeglądarkami.

### <a name="open-the-sample-directly"></a>Otwórz próbkę bezpośrednio

Uzyskaj klucz subskrypcji zgodnie z powyższym opisem. Następnie otwórz [link do przykładu](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Spowoduje to załadowanie strony do domyślnej przeglądarki (renderowane przy użyciu [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otwórz przykład z kopii lokalnej

Aby wypróbować przykład lokalnie, Sklonuj to repozytorium:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Skompiluj źródła TypeScript i podziel je na jeden plik JavaScript ([npm](https://www.npmjs.com/) musi być zainstalowany na maszynie). Przejdź do katalogu głównego sklonowanego repozytorium i uruchom polecenia:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otwórz `samples\browser\Sample.html` w ulubionej przeglądarce.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat sposobu dołączania zestawu SDK do własnej strony sieci Web jest dostępna [tutaj](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Uwagi

- Interfejs API rozpoznawania mowy obsługuje trzy [tryby rozpoznawania](../concepts.md#recognition-modes). Tryb można przełączyć, aktualizując funkcję **instalacji ()** , która znajduje się w pliku Sample. html. Przykład domyślnie ustawia tryb na `Interactive` . Aby zmienić tryb, zaktualizuj parametr `SR.RecognitionMode.Interactive` do innego trybu. Na przykład zmień parametr na `SR.RecognitionMode.Conversation`.
- Aby zapoznać się z pełną listą obsługiwanych języków, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Tematy pokrewne

- [Przykładowe repozytorium interfejsu API rozpoznawania mowy JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Wprowadzenie do interfejsu API REST](GetStartedREST.md)
