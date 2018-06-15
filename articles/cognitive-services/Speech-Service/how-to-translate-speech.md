---
title: Tłumaczenie mowy przy użyciu usług mowy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tłumaczenia mowy w usłudze rozpoznawania mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356284"
---
# <a name="translate-speech-using-speech-service"></a>Tłumaczenie mowy przy użyciu usługi mowy

[Mowy SDK](speech-sdk.md) jest najprostszym sposobem użycia tłumaczenia mowy w aplikacji. Zestaw SDK zawiera wszystkie funkcje usługi. Podstawowy proces podczas przeprowadzania translacji mowy obejmuje następujące kroki:

1. Tworzenie fabryki mowy i podaj klucz mowy usługi subskrypcji lub token autoryzacji. Również skonfigurować języków tłumaczeń źródłowe i docelowe w tym momencie, a także określenie, czy ma tekst lub mowy danych wyjściowych.

2. Pobierz aparat rozpoznawania z fabryki. W tłumaczeniu wybierz tłumaczenia aparat rozpoznawania. (Dotyczy innych aparatów rozpoznawania *mowy na tekst*.) Brak różnych odmian rozpoznawania translacji na podstawie audio źródła, którego używasz.

4. W razie potrzeby pochłaniać zdarzenia dla operacji asynchronicznej. Aparat rozpoznawania wywołuje programu obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacja odbiera wynik końcowy tłumaczenia.

5. Uruchom rozpoznawanie i translacji.

# <a name="sdk-samples"></a>Przykłady zestawu SDK

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
