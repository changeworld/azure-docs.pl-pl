---
title: Niesuwerenne chmury — usługa mowy
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
ms.openlocfilehash: 00617fc09f471eaf3dc13a5aa691c4aab2e1e2ec
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77424919"
---
# <a name="speech-services-with-sovereign-clouds"></a>Usługi mowy z niesuwerennymi chmurami

## <a name="azure-government-united-states"></a>Azure Government (Stany Zjednoczone)

Tylko Federalne Stany USA, Stany, lokalne i plemienne rządy oraz ich partnerzy mają dostęp do tego dedykowanego wystąpienia z operacjami kontrolowanymi przez obywateli Stanów Zjednoczonych.
- Regiony: US Gov Wirginia
- Wirtualizacja SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us", "\<klucz\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us", "\<klucz\>");*
- Tokeny uwierzytelniania:[]()https://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Witryna Azure Portal: https://portal.azure.us  
- Portal Custom Speech: https://virginia.cris.azure.us/Home/CustomSpeech
- Dostępne jednostki SKU: S0
- Obsługiwane funkcje:
  - Zamiana mowy na tekst
  - Custom Speech (adaptacja akustyczna/językowa)
  - Zamiana tekstu na mowę
  - Translator mowy
- Nieobsługiwane funkcje
  - Niestandardowy głos
  - Neuronowych głosy dla zamiany tekstu na mowę
- Obsługiwane ustawienia regionalne: obsługiwane są ustawienia regionalne dla następujących języków.
  - Arabski (AR-*)
  - Chiński (ZH-*)
  - Angielski (EN-*)
  - Francuski (fr-*)
  - Niemiecki (de-*)
  - Hindi
  - Koreański
  - Rosyjski
  - Hiszpański (ES-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Chiny

W Chinach centrum danych platformy Azure z bezpośrednim dostępem do urządzeń przenośnych w Chinach, z Chin Telecom, Chin Unicom i innych głównych sieci szkieletowych przewoźników, w przypadku chińskich użytkowników w celu zapewnienia wysokiego i stabilnego lokalnego środowiska dostępu do sieci.
- Regiony: Chiny Wschodnie 2 (Szanghaj)
- Wirtualizacja SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.CN", "\<klucz\>");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.CN", "\<klucz\>");*
- Tokeny uwierzytelniania:[]()https://chinaeast2.API.Cognitive.Azure.CN/STS/v1.0/issueToken
- Witryna Azure Portal: https://portal.azure.cn
- Portal Custom Speech: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Dostępne jednostki SKU: S0
- Obsługiwane funkcje:
  - Zamiana mowy na tekst
  - Custom Speech (adaptacja akustyczna/językowa)
  - Zamiana tekstu na mowę
  - Translator mowy
- Nieobsługiwane funkcje
  - Niestandardowy głos
  - Neuronowych głosy dla zamiany tekstu na mowę
- Obsługiwane ustawienia regionalne: obsługiwane są ustawienia regionalne dla następujących języków.
  - Arabski (AR-*)
  - Chiński (ZH-*)
  - Angielski (EN-*)
  - Francuski (fr-*)
  - Niemiecki (de-*)
  - Hindi
  - Koreański
  - Rosyjski
  - Hiszpański (ES-*)

