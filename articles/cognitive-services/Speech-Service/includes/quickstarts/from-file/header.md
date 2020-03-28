---
title: 'Szybki start: rozpoznawanie mowy z pliku audio — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037781"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku audio. Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy z pliku wymaga tylko kilku kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `AudioConfig` obiekt określający obiekt . Nazwa pliku WAV.
> * Utwórz `SpeechRecognizer` obiekt `SpeechConfig` przy `AudioConfig` użyciu obiektów i obiektów z góry.
> * Za `SpeechRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `SpeechRecognitionResult` zwrócony.
