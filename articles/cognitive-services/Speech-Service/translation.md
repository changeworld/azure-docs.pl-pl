---
title: Przykładowe translacji | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Poniżej przedstawiono przykładowe translacji mowy.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: a02a89de1d05ec5388a92f14a0fd206e0ee966a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349697"
---
# <a name="sample-for-translation"></a>Próbka do tłumaczenia

> [!NOTE]
> Aby uzyskać instrukcje dotyczące pobierania w tym przykładzie i innych użytkowników, zobacz [przykłady dotyczące zestawu SDK mowy](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Dla wszystkich przykłady poniżej następujące deklaracje najwyższego poziomu powinny być stosowane:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Tłumaczenie przy użyciu mikrofon

Poniższy fragment kodu przedstawia sposób Przetłumacz wejście mowy z języka angielskiego na język niemiecki i również uzyskać dane wyjściowe głosu przetłumaczony tekst. Używa ona mikrofon.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Tłumaczenie przy użyciu pliku danych wejściowych

Poniższy fragment kodu przedstawia sposób tłumaczenie mowy danych wejściowych z angielski, niemiecki i francuski.
Używa pliku jako dane wejściowe.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Przykładowy kod źródłowy

Najnowszą wersję przykłady i jeszcze bardziej zaawansowane przykłady znajdują się w dedykowanej [repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie mowy](./speech-to-text-sample.md)

- [Rozpoznawanie konwersji](./intent.md)
