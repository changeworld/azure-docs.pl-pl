---
title: Tworzenie pierwszej funkcji z poziomu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/05/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 31437360396e220de24789fdff9d2602af385053
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Tworzenie pierwszej funkcji w witrynie Azure Portal

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/overview/serverless-computing/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej. W tym temacie opisano tworzenie funkcji „hello world” w witrynie Azure Portal przy użyciu usługi Functions.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <http://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal-experiment.md)]

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

1. Rozwiń nową aplikację funkcji, a następnie kliknij przycisk **+** obok pozycji **Funkcje**.

2.  Na stronie **Szybkie rozpoczynanie pracy** wybierz pozycję **Element webhook i interfejs API**, **wybierz język** funkcji i kliknij pozycję **Utwórz tę funkcję**. 
   
    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona w wybranym języku i przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP. W tym temacie przedstawiono funkcję skryptu języka C# w portalu, ale funkcję możesz utworzyć w dowolnym [obsługiwanym języku](supported-languages.md). 

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniżej przedstawiono przykład odpowiedzi w przeglądarce Edge (odpowiedź w innych przeglądarkach może zawierać kod XML):

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.   

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**. 

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP i elementów webhook w usłudze Azure Functions](functions-bindings-http-webhook.md).



