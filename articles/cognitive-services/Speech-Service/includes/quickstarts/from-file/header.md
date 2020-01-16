---
title: 'Szybki Start: Rozpoznawanie mowy z pliku audio — usługa mowy'
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
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037781"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku dźwiękowego. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z pliku obejmuje tylko kilka kroków:
> [!div class="checklist"]
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt `AudioConfig`, który określa. Nazwa pliku WAV.
> * Utwórz obiekt `SpeechRecognizer` przy użyciu `SpeechConfig` i `AudioConfig` obiektów z powyższych.
> * Przy użyciu obiektu `SpeechRecognizer` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą `SpeechRecognitionResult`.
