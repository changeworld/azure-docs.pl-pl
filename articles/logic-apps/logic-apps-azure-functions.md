---
title: Niestandardowy kod dla usługi Azure Logic Apps w środowisku Azure Functions | Dokumentacja firmy Microsoft
description: Tworzenie i uruchamianie niestandardowych kodów dla usługi Azure Logic Apps w środowisku Azure Functions
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: befdd7af0a91623af4c5ea0fd957141cad84eb53
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297873"
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Dodaj i uruchomienia niestandardowego kodu dla usługi logic apps za pomocą usługi Azure Functions

Aby uruchomić niestandardowe fragmenty kodu języka C# lub node.js w aplikacji logiki, można utworzyć funkcji niestandardowych za pomocą usługi Azure Functions. 
[Środowisko Azure Functions](../azure-functions/functions-overview.md) oferuje wolny od serwera przetwarzania danych w Microsoft Azure i są użyteczne do wykonywania tych zadań:

* Zaawansowane, formatowanie lub obliczeniowych pól w aplikacji logiki
* Wykonywanie obliczeń w przepływie pracy.
* Rozszerzanie funkcjonalności aplikacji logiki z funkcjami, które są obsługiwane w języku C# lub node.js

## <a name="create-custom-functions-for-your-logic-apps"></a>Tworzenie funkcji niestandardowych dla aplikacji logiki

Zaleca się utworzenie funkcji w portalu Azure Functions z **ogólny element Webhook - węzła** lub **ogólny element Webhook - C#** szablonów. Wynik tworzy wypełniana automatycznie szablon, który akceptuje `application/json` z aplikacji logiki. Funkcje, które utworzono z tych szablonów są wykrywane automatycznie i są wyświetlane w Projektancie aplikacji logiki w obszarze **usługi Azure Functions w moim regionie.**

W portalu Azure na **integracji** okienku dla funkcji szablon powinien pokazują, że **tryb** ustawioną **Webhook** i **typu elementu Webhook**ma ustawioną wartość **ogólnego JSON**. 

Funkcje elementu Webhook zaakceptować żądania i przekaż go do metody za pomocą `data` zmiennej. Uzyskać dostęp do właściwości z ładunku, używając zapisu kropkowego jak `data.function-name`. Na przykład prostych funkcji JavaScript, który konwertuje wartości daty i godziny do ciągu daty wygląda następująco:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Wywołanie usługi Azure Functions w aplikacjach logic apps

Aby wyświetlić kontenery w ramach subskrypcji i wybierz funkcję, która ma zostać wywołana w Projektancie aplikacji logiki, kliknij przycisk **akcje** menu, a następnie wybierz z **usługi Azure Functions w moim regionie**.

Po wybraniu funkcji, należy określić obiekt wprowadzony ładunek. Ten obiekt jest komunikatów wysyła do funkcji i musi być obiektem JSON aplikacji logiki. Na przykład, jeśli mają być przekazywane **ostatniej modyfikacji** daty z wyzwalacza Salesforce ładunku funkcja wygląda w tym przykładzie:

![Data ostatniej modyfikacji][1]

## <a name="trigger-logic-apps-from-a-function"></a>Aplikacje logiki wyzwalacza w funkcji

Możesz wyzwolić aplikacji logiki z wewnątrz funkcji. Zobacz [logiki aplikacji jako punkty końcowe można wywołać](logic-apps-http-endpoint.md). Tworzenie aplikacji logiki, która zawiera wyzwalacz ręcznego, a następnie z wewnątrz funkcji, generowanie HTTP POST do ręcznego wyzwalacza, adres URL z ładunku, który ma wysłany do aplikacji logiki.

### <a name="create-a-function-from-logic-app-designer"></a>Tworzenie funkcji przy użyciu projektanta aplikacji logiki

Można również utworzyć funkcję webhook node.js przy użyciu projektanta. Najpierw wybierz **usługi Azure Functions w moim regionie** , a następnie wybierz kontener dla funkcji. Jeśli jeszcze nie masz kontenera, należy utworzyć jeden z [portalu Azure Functions](https://functions.azure.com/signin). Następnie wybierz **Utwórz nowy**.  

Aby wygenerować szablon na podstawie danych, który ma zostać obliczeniowe, należy określić obiekt kontekstu, który chcesz przekazać do funkcji. Ten obiekt musi być obiektem JSON. Na przykład jeśli przekazać zawartość pliku z akcji FTP, ładunku kontekstu wygląda w tym przykładzie:

![Kontekst ładunku][2]

> [!NOTE]
> Ponieważ ten obiekt nie był rzutowany jako ciąg, zawartość jest dodawana bezpośrednio do ładunek JSON. Jednak występuje błąd, jeśli obiekt nie jest tokenem JSON (to znaczy, że ciąg lub JSON/Tablica obiektów). Można rzutować obiektu jako ciąg, Dodaj cudzysłowy, jak pokazano na pierwszym rysunku, w tym artykule.
> 

Projektant następnie generuje szablonu funkcji, możesz utworzyć wbudowany. Zmienne wstępnie są tworzone na podstawie w kontekście, który chcesz przekazać do funkcji.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
