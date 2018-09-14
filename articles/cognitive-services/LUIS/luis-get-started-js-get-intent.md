---
title: Analizowanie tekstu w języku naturalnym w usłudze Language Understanding (LUIS) przy użyciu języka JavaScript — Cognitive Services — Azure Cognitive Services | Microsoft Docs
description: W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Przy użyciu języka JavaScript wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771713"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Szybki start: analiza tekstu przy użyciu języka JavaScript

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analiza tekstu przy użyciu przeglądarki

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Analiza tekstu przy użyciu języka JavaScript 

Aby uzyskać dostęp do tego samego wyniku, który został wyświetlony w oknie przeglądarki w poprzednim kroku, możesz użyć języka JavaScript. 

1. Skopiuj poniższy kod i zapisz go w pliku HTML:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Otwórz plik w przeglądarce. Wprowadź klucz punktu końcowego usługi LUIS w formularzu i wybierz pozycję **Prześlij**.

    ![Przykład kodu HTML wyświetlanego w przeglądarce z wynikami usługi LUIS dla aplikacji Home Automation](./media/luis-get-started-js-get-intent/html-results.png)

    Pod formularzem zostaną wyświetlone wyniki. 

## <a name="luis-keys"></a>Klucze usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń plik JavaScript.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-javascript-add-utterance.md)
