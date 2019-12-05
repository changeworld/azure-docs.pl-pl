---
title: 'Szybki Start: synteza mowy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818254"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na mowę. Po spełnieniu kilku wymagań wstępnych, renderowanie mowy do domyślnych głośników obejmuje tylko cztery kroki:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````SpeechSynthesizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Używanie obiektu ````SpeechSynthesizer````, aby mówić do tekstu.
> * Sprawdź ````SpeechSynthesisResult```` zwróconych pod kątem błędów.
