---
title: Dostosuj dane wyjściowe zamiany tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Włącz rejestrowanie w zestawie mowy SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562741"
---
# <a name="fine-tune-text-to-speech-output"></a>Dostosowywanie danych wyjściowych zamiany tekstu na mowę

Usługi Azure Speech Services umożliwiają dostosowanie szybkości, wymowy, objętości, skoku i rozkładu danych wyjściowych zamiany tekstu na mowę przy użyciu [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md). SSML to język znaczników oparty na języku XML, który używa tagów do informowania o tym, co funkcja wymaga dostrajania. Komunikat SSML jest następnie wysyłany w treści każdego żądania do usługi zamiany tekstu na mowę. W celu uproszczenia procesu dostosowywania usługi mowy oferują teraz narzędzie do [dostrajania głosu](https://aka.ms/voicetuning) , które umożliwia wizualne badanie i Dostosowywanie wyników zamiany tekstu na mowę w czasie rzeczywistym.

Narzędzie strojenia głosu obsługuje [standardowe](language-support.md#standard-voices), [neuronowych](language-support.md#text-to-speech)i niestandardowe [głosy](how-to-customize-voice-font.md)firmy Microsoft.

## <a name="get-started-with-the-voice-tuning-tool"></a>Wprowadzenie do narzędzia dostrajania głosu

Przed rozpoczęciem dokładnego dostrajania danych wyjściowych zamiany tekstu na mowę za pomocą narzędzia do dostrajania głosu należy wykonać następujące czynności:

1. Utwórz [bezpłatny konto Microsoft](https://account.microsoft.com/account) , jeśli jeszcze go nie masz.
2. Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz. Kliknij przycisk **Rozpocznij bezpłatnie**i Utwórz nowe konto platformy Azure przy użyciu konto Microsoft.

3. Utwórz subskrypcję usługi Speech Services w Azure Portal. Instrukcje krok po kroku dotyczące [tworzenia zasobów mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) są dostępne.
   >[!NOTE]
   >Podczas tworzenia zasobu mowy w Azure Portal informacje o lokalizacji platformy Azure muszą być zgodne z regionem głosu TTS. Głos neuronowych TTS obsługuje podzbiór lokalizacji platformy Azure. Aby zapoznać się z pełną listą pomocy technicznej, zobacz [regiony](regions.md#text-to-speech).

   >[!NOTE]
   >Aby można było korzystać z usługi, w Azure Portal należy utworzyć klucz F0 lub S0. Dostrajanie głosu **nie** obsługuje [30-dniowego bezpłatnego klucza wersji próbnej](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Zaloguj się do portalu [strojenia głosu](https://aka.ms/voicetuning) i Połącz swoją subskrypcję usługi Speech Services. Wybierz pojedynczą subskrypcję usługi Speech Services, a następnie utwórz projekt.
5. Wybierz pozycję **Nowy dostrajanie**. Następnie wykonaj następujące kroki:

   * Znajdź i wybierz pozycję **wszystkie subskrypcje**.  
   * Wybierz **połączyć z istniejącą subskrypcją**.  
     ![Połącz istniejącą subskrypcję](./media/custom-voice/custom-voice-connect-subscription.png).
   * Wprowadź klucz subskrypcji usługi Azure Speech Services, a następnie wybierz pozycję **Dodaj**. Twoje klucze subskrypcji są dostępne w portalu dostosowywania mowy na [stronie subskrypcji](https://go.microsoft.com/fwlink/?linkid=2090458). Klucze można także pobrać z okienka zarządzanie zasobami w [Azure Portal](https://portal.azure.com/).
   * Jeśli masz więcej niż jedną subskrypcję usługi Speech Services, której planujesz użyć, Powtórz te kroki dla każdej subskrypcji.

## <a name="customize-the-text-to-speech-output"></a>Dostosowywanie danych wyjściowych zamiany tekstu na mowę

Teraz, gdy utworzono konta i połączono swoją subskrypcję, możesz zacząć dostrajać dane wyjściowe zamiany tekstu na mowę.

1. Wybierz głos.
2. Wprowadź tekst, który chcesz edytować.
3. Przed rozpoczęciem wprowadzania zmian Odtwórz dźwięk, aby uzyskać efekt dla danych wyjściowych.
4. Wybierz słowo/zdanie, które chcesz udoskonalić, i zacznij eksperymentować z różnymi funkcjami opartymi na SSML.

>[!TIP]
> Aby uzyskać szczegółowe informacje na temat dostosowywania SSML i dostrajania danych wyjściowych, zobacz [Speech synteza języka Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Ograniczenia

Dostrajanie głosu neuronowych jest nieco inne niż dostrajanie dla standardowych i niestandardowych głosów.

* Intonation nie jest obsługiwana dla głosów neuronowych.
* Funkcje skoku i głośności działają tylko z pełnymi zdaniami. Te funkcje nie są dostępne na poziomie wyrazu.
* W przypadku stawki niektóre głosy neuronowych można dostrajać na podstawie wyrazów, a inne wymagają wybrania całych zdań.

> [!TIP]
> Narzędzie strojenia głosu zawiera kontekstowe informacje o funkcjach i dostrajania.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie zasobu mowy na platformie Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Rozpocznij dostrajanie głosu](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Język znaczników syntezy mowy (SSML)](speech-synthesis-markup.md)
