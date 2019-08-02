---
title: Conversation Learner domyślną konfigurację — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się więcej o domyślnej konfiguracji Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705299"
---
# <a name="default-values-and-boundaries"></a>Wartości domyślne i granice

W tym dokumencie opisano domyślną konfigurację Conversation Learner i granic usługi Key.

## <a name="default-configuration"></a>Konfiguracja domyślna

Parametr | Wartość domyślna
--- | --- 
Domyślny limit czasu sesji | 30 minut

## <a name="boundaries"></a>Granice

Parametr | Limit
--- | --- 
Interfejs API tworzenia, Maksymalna liczba wywołań HTTP miesięcznie | 5 M
Interfejs API tworzenia, Maksymalna liczba wywołań HTTP na sekundę | 25
Interfejs API sesji, Maksymalna liczba wywołań HTTP miesięcznie | 500 000
Interfejs API sesji, Maksymalna liczba wywołań HTTP na sekundę | 10
Maksymalna liczba jednostek niestandardowych (nieprogramistycznych) na model | Zobacz [dokument granic Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); w tej rzeczywistości rzeczywista liczba może być nieco mniejsza
Maksymalna liczba wstępnie utworzonych jednostek na model | Zobacz [dokument granic Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Maksymalna liczba jednostek (łącznie) na model | 100
Maksymalna liczba akcji na model | 32
Maksymalna liczba okien dialogowych pouczenia na model | 1000
Maksymalna liczba włączanych użytkowników na pociąg | 100
Maksymalna liczba okien dialogowych dziennika na model | Brak limitu wstępnego, ale okna dialogowe dzienników są przechowywane przez stały okresy przed odrzuceniem.  Ponadto interfejs użytkownika Conversation Learner wyświetli okna dialogowe dzienników 100 w danym momencie. 
Maksymalna liczba modeli na użytkownika | Brak limitu wstępnego
Maksymalna liczba sekwencyjnych akcji bez oczekiwania | 5 (*)

(*) Po 5 kolejnych akcjach, które nie są oczekiwane, są maskowane wszystkie akcje, które nie są obsługiwane, a Conversation Learner będą wybierać spośród dostępnych akcji oczekiwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do Conversation Learner](./quickstart.md)
