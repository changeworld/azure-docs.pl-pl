---
title: Konfiguracja domyślna uczeń konwersacji — usług Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Więcej informacji na temat domyślnej konfiguracji uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ebdc1e1c100329e95bd19359408cb138d233b1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385441"
---
# <a name="default-values-and-boundaries"></a>Wartości domyślne i granice

W tym dokumencie opisano konfigurację domyślną uczeń konwersacji i granice klucza usługi.

## <a name="default-configuration"></a>Konfiguracja domyślna

Parametr | Wartość domyślna
--- | --- 
Domyślna wartość limitu czasu sesji | 30 minut

## <a name="boundaries"></a>Granice

Parametr | Limit
--- | --- 
Tworzenie interfejsu API, max HTTP wywołania na miesiąc | 5 MIN
Tworzenie interfejsu API, max HTTP wywołań na sekundę | 25
Sesja interfejsu API, max wywołań HTTP na miesiąc | 500 000
Sesja interfejsu API, max wywołania HTTP na sekundę | 10
Maksymalna liczba jednostek niestandardowych (innych niż programowy), na modelu | Zobacz [doc granice LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); w praktyce rzeczywista liczba może być nieco mniejsze.
Maksymalna liczba wstępnie utworzone jednostki na modelu | Zobacz [doc granice usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Maksymalna liczba jednostek (w sumie) na modelu | 100
Maksymalna liczba akcji na modelu | 32
Maksymalna liczba wyświetlanymi w oknach dialogowych train na modelu | 1000
Maksymalna liczba użytkowników zmieni się na szkolenie w oknie dialogowym | 100
Maksymalna liczba wyświetlanymi w oknach dialogowych dziennika na modelu | Nie wstępnie ustalony limit, ale dziennika w oknach dialogowych tylko zostaną zachowane na czas, zanim zostanie usunięty.  Ponadto UI uczeń konwersacji pokaże 100 dziennika okien dialogowych w danym momencie. 
Maksymalna liczba modeli dla każdego użytkownika | Nie wstępnie ustalony limit
Maksymalna liczba kolejnych akcji-wait | 5 (*)

(*) Po kolejnych 5-wait akcji wszystkie akcje-wait są maskowane i wybierze uczeń konwersacji między oczekiwania dostępne akcje.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozpoczynanie pracy z usługą uczeń konwersacji](./quickstart.md)
