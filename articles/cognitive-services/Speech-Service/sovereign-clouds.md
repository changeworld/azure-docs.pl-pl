---
title: Suwerenne chmury — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak korzystać z suwerennych chmur
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228091"
---
# <a name="speech-services-with-sovereign-clouds"></a>Usługi mowy z suwerennymi chmurami

## <a name="azure-government-united-states"></a>Azure Government (Stany Zjednoczone)

Tylko rządy federalne, stanowe, lokalne i plemienne USA oraz ich partnerzy mają dostęp do tego dedykowanego wystąpienia z operacjami kontrolowanymi przez przebadanych obywateli USA.
- Regiony: Us Gov Virginia
- SR w SpeechSDK:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" your-key\>");*
- TTS w SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us",[]()"\<twój klucz\>");*
- Tokeny uwierzytelniania:[]()https ://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Portal Azure:https://portal.azure.us  
- Niestandardowy portal mowy:https://virginia.cris.azure.us/Home/CustomSpeech
- Dostępne jednostki SKU: S0
- Obsługiwane funkcje:
  - Zamiana mowy na tekst
  - Mowa niestandardowa (adaptacja akustyczna/językowa)
  - Zamiana tekstu na mowę
  - Tłumacz mowy
- Nieobsługiwały się funkcje
  - Niestandardowy głos
  - Głosy neuronowe dla zamiany tekstu na mowę
- Obsługiwane ustawienia regionalne: obsługiwane są ustawienia regionalne dla następujących języków.
  - Arabski (ar-*)
  - Chiński (zh-*)
  - Angielski (en-*)
  - Francuski (fr-*)
  - Niemiecki (de-*)
  - Hindi
  - Koreański
  - Rosyjski
  - Hiszpański (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Chiny

Znajduje się w Chinach, centrum danych platformy Azure z bezpośrednim dostępem do China Mobile, China Telecom, China Unicom i innych głównych sieci szkieletowych przewoźnika, aby chińscy użytkownicy zapewniali szybkie i stabilne środowisko dostępu do sieci lokalnej.
- Regiony: Chiny Wschód 2 (Szanghaj)
- SR w SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" twój\>klucz ");*
- TTS w SpeechSDK: *config. FromHost("https://chinaeast2.tts.speech.azure.cn",[]()"\<twój klucz\>");*
- Tokeny uwierzytelniania:[]()https ://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Portal Azure:https://portal.azure.cn
- Niestandardowy portal mowy:https://speech.azure.cn/CustomSpeech
- Dostępne jednostki SKU: S0
- Obsługiwane funkcje:
  - Zamiana mowy na tekst
  - Mowa niestandardowa (adaptacja akustyczna/językowa)
  - Zamiana tekstu na mowę
  - Tłumacz mowy
- Nieobsługiwały się funkcje
  - Niestandardowy głos
  - Głosy neuronowe dla zamiany tekstu na mowę
- Obsługiwane ustawienia regionalne: obsługiwane są ustawienia regionalne dla następujących języków.
  - Arabski (ar-*)
  - Chiński (zh-*)
  - Angielski (en-*)
  - Francuski (fr-*)
  - Niemiecki (de-*)
  - Hindi
  - Koreański
  - Rosyjski
  - Hiszpański (es-*)

