---
title: Dostosuj tekst na mowę dane wyjściowe — usługi mowy
titleSuffix: Azure Cognitive Services
description: Włącz rejestrowanie w zestawie SDK rozpoznawania mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 6d602491c66669007ae220c3b8143ce3e805246f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147989"
---
# <a name="fine-tune-text-to-speech-output"></a>Dostosowywanie danych wyjściowych zamiany tekstu na mowę

Usługi mowy platformy Azure umożliwiają dostosowanie szybkości, wymowa, woluminu, pomysłu i rozkład zamiany tekstu na mowę, dane wyjściowe [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md). SSML to język znaczników oparty na formacie XML, który używa tagów, aby poinformować usługi, o jakich funkcji wymaga dostrajania. Komunikat SSML jest następnie wysyłana w treści poszczególnych żądań w usłudze zamiany tekstu na mowę. Aby uprościć proces dostosowywania, teraz oferty usług przetwarzania mowy [dostosowywania głosu](https://aka.ms/voicetuning) w czasie rzeczywistym dane wyjściowe, dzięki któremu można wzrokowo sprawdzić i dostosować zamiany tekstu na mowę.

Narzędzia dostosowywania głosu obsługuje firmy Microsoft [standardowa](language-support.md#standard-voices), [neuronowych](language-support.md#text-to-speech), i [głosów niestandardowych](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Rozpoczynanie pracy przy użyciu narzędzia dostosowywania głosu

Przed rozpoczęciem można dostrajanie zamiany tekstu na mowę danych wyjściowych za pomocą narzędzia dostosowywania głosu, należy wykonać następujące czynności:

1. Tworzenie [bezpłatne konto Microsoft](https://account.microsoft.com/account) Jeśli nie masz jeszcze jeden.
2. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/en-us/free/) Jeśli nie masz jeszcze jeden. Kliknij przycisk **Rozpocznij za darmo**i Utwórz nowe konto platformy Azure przy użyciu konta Microsoft.

3. Utwórz subskrypcję usługi mowy w witrynie Azure portal. Instrukcje krok po kroku dotyczące [sposobu tworzenia zasobu mowy](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) są dostępne.
   >[!NOTE]
   >Po utworzeniu zasobu mowy w witrynie Azure portal, informacje o lokalizacji platformy Azure jest musi być zgodna z regionem głosu TTS. Głos TTS neuronowych obsługuje zestaw sub lokalizacji platformy Azure. Aby uzyskać pełną listę pomocy technicznej, zobacz [regionów](regions.md#text-to-speech).

   >[!NOTE]
   >Musisz mieć F0 lub klucza S0 utworzone w witrynie Azure portal, zanim użyjesz usługi. Głosu dostrajania **nie** obsługuje [30-dniowej bezpłatnej wersji próbnej klucz](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Zaloguj się do [dostosowywania głosu](https://aka.ms/voicetuning) portalu i Połącz z subskrypcją usług przetwarzania mowy. Wybierz pojedynczej subskrypcji usług przetwarzania mowy, a następnie utworzyć projekt.
5. Wybierz **dostrajania nowych**. Następnie wykonaj następujące kroki:

   * Znajdź i zaznacz **wszystkie subskrypcje**.  
   * Wybierz **połączyć z istniejącą subskrypcją**.  
     ![Połącz istniejącą subskrypcję](./media/custom-voice/custom-voice-connect-subscription.png).
   * Wprowadź klucz subskrypcji usług przetwarzania mowy platformy Azure, a następnie wybierz **Dodaj**. Klucze subskrypcji są dostępne w portalu dostosowania mowy [stronę subskrypcji](https://go.microsoft.com/fwlink/?linkid=2090458). Można również pobrać kluczy, w okienku zarządzanie zasobami w [witryny Azure portal](https://portal.azure.com/). 
   * Jeśli masz więcej niż jedną subskrypcję usług przetwarzania mowy, którego chcesz użyć, powtórz te czynności dla każdej subskrypcji.

## <a name="customize-the-text-to-speech-output"></a>Dostosuj tekst na mowę danych wyjściowych

Teraz, po utworzeniu konta i połączone z subskrypcją, możesz rozpocząć dostosowywanie zamiany tekstu na mowę danych wyjściowych.

1. Wybierz głosu.
2. Wprowadź tekst, który chcesz edytować.
3. Przed wprowadzeniem zmiany należy odtworzyć dźwięk, aby można było uzyskać pewne pojęcie dla danych wyjściowych.
4. Wybierz wyraz/zdania, który chcesz dostosować i mogą zacząć eksperymentować z różnych funkcji na podstawie SSML.

>[!TIP]
> Aby uzyskać szczegółowe informacje dotyczące dostosowywania SSML i dostosowywania głosu danych wyjściowych, zobacz [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Ograniczenia

Dostrajanie neuronowych głosowych są nieco inne niż automatycznego dostrajania dla głosów standardowe i niestandardowe.

* Maksimów nie jest obsługiwana dla neuronowych głosów.
* Gęstość i wolumin funkcje działają tylko z postaci kompletnych zdań. Te funkcje nie są dostępne na poziomie programu word.
* Szybkość niektóre neuronowych głosów można dostrojone w oparciu wyrazów, podczas gdy inni wymagają wybrania całych zdań.

> [!TIP]
> Narzędzia dostosowywania głosu zapewnia informacje kontekstowe o funkcjach i dostrajania.

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz zasób mowy na platformie Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Rozpoczęcia strojenia głosu](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Język znaczników synteza mowy (SSML)](speech-synthesis-markup.md)
