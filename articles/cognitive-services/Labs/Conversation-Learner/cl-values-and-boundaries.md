---
title: Konfiguracja domyślna uczeń konwersacji — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Więcej informacji na temat konfiguracji domyślnej uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348584"
---
# <a name="default-values-and-boundaries"></a>Wartości domyślne i granice

Ten dokument zawiera opis domyślną konfigurację uczeń konwersacji i granice klucza usługi.

## <a name="default-configuration"></a>Domyślna konfiguracja

Parametr | Wartość domyślna
--- | --- 
Domyślny limit czasu sesji | 30 minut

## <a name="boundaries"></a>Granice

Parametr | Limit
--- | --- 
Tworzenie interfejsu API, max HTTP wywołania miesięcznie | 5M
Tworzenie interfejsu API, max HTTP wywołania na sekundę | 25
Sesja interfejsu API, max HTTP wywołania miesięcznie | 500 000
Sesja interfejsu API, max HTTP wywołania na sekundę | 10
Maksymalna liczba niestandardowych obiektów (z systemem innym niż — programowe) na aplikację | Zobacz [doc granice LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries); w praktyce, rzeczywista liczba może być nieco mniejsze.
Maksymalna liczba jednostek wbudowanych według aplikacji | Zobacz [LUIS granice dokumentu](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Maksymalna liczba jednostek (łącznie) na aplikację | 100
Maksymalna liczba akcji na aplikację | 32
Maksymalna liczba okien dialogowych train według aplikacji | 1000
Maksymalna liczba użytkowników włącza na train okna dialogowego | 100
Maksymalna liczba okien dialogowych dziennika na aplikację | Nie wstępnie ustawiony limit, ale okien dialogowych dziennika tylko zostaną zachowane na czas, zanim zostanie usunięty.  Ponadto interfejsu użytkownika uczeń konwersacji wyświetli 100 dziennika okien dialogowych w czasie. 
Maksymalna liczba aplikacji na użytkownika | Brak limitu wstępnie ustawiona
Maksymalna liczba kolejnych akcji bez oczekiwania | 5 (*)

(*) Po 5 sekwencyjnych akcji bez oczekiwania są maskowane wszystkich akcji bez oczekiwania i wybierze uczeń konwersację między oczekiwania dostępne akcje.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozpoczynanie pracy z uczeń konwersacji](./quickstart.md)
