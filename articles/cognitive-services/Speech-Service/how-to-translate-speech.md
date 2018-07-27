---
title: Tłumaczenie mowy za pomocą usług przetwarzania mowy
description: Dowiedz się, jak używać tłumaczenia mowy w usłudze rozpoznawania mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: d539fe5a1a031c196c0d40e989d83575715b278a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285265"
---
# <a name="translate-speech-using-speech-service"></a>Tłumaczenie mowy przy użyciu usługi mowy

[Zestaw SDK rozpoznawania mowy](speech-sdk.md) jest najprostszym sposobem użycia tłumaczenia mowy w aplikacji. Zestaw SDK udostępnia pełną funkcjonalność usługi. Podstawowy proces podczas wykonywania tłumaczeń mowy dokonywanych obejmuje następujące kroki:

1. Tworzenie fabryki mowy i podaj klucz subskrypcji usługi rozpoznawania mowy i [region](regions.md) lub token autoryzacji. Również skonfigurować języków tłumaczeń źródłowe i docelowe w tym momencie, a także określenie, czy chcesz, aby dane wyjściowe tekstu i mowy.

2. Pobierz aparat rozpoznawania z fabryki. W przypadku tłumaczenia należy wybrać rozpoznawania tłumaczenia. (Związanych z innych aparatów rozpoznawania *zamiana mowy na tekst*.) Istnieją różne podtypy aparatu rozpoznawania tłumaczenia na podstawie źródła audio, którego używasz.

4. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacja odbiera wynik końcowy tłumaczenia.

5. Rozpocznij rozpoznawanie i tłumaczenia.

# <a name="sdk-samples"></a>Przykłady zestawu SDK

Aby uzyskać najnowszy zestaw przykładów, zobacz [Cognitive Services mowy SDK przykładowe repozytorium usługi GitHub](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
