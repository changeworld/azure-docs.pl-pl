---
title: Tworzenie funkcji uruchamianej zgodnie z harmonogramem na platformie Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć na platformie Azure funkcję uruchamianą zgodnie z określonym harmonogramem.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9c961c06a01bba0b645088a19cd55e72a54396c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Tworzenie funkcji wyzwalanej czasomierzem na platformie Azure

Dowiedz się, jak za pomocą usługi Azure Functions utworzyć funkcję [bezserwerową](https://azure.microsoft.com/overview/serverless-computing/) uruchamianą zgodnie z określonym harmonogramem.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Pomyślnie utworzona aplikacja funkcji.](./media/functions-create-first-azure-function/function-app-create-success.png)

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Tworzenie funkcji wyzwalanej czasomierzem

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **Funkcja niestandardowa**. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.

    ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-scheduled-function/add-first-function.png)

2. W polu wyszukiwania wpisz `timer`, a następnie wybierz żądany język dla szablonu wyzwalacza czasomierza. 

    ![Wybierz szablon funkcji wyzwalanej czasomierzem.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

3. Skonfiguruj nowy wyzwalacz, wprowadzając ustawienia określone w tabeli znajdującej się poniżej obrazu.

    ![Utwórz funkcję wyzwalaną czasomierzem w witrynie Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa** | Domyślne | Określa nazwę funkcji wyzwalanej czasomierzem. |
    | **[Harmonogram](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Składające się z 6 pól [wyrażenie CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) planujące uruchamianie funkcji co minutę. |

2. Kliknij przycisk **Utwórz**. Zostanie utworzona funkcja w wybranym języku uruchamiana co minutę.

3. Zweryfikuj uruchomienie, wyświetlając informacje o śledzeniu zapisane w dziennikach.

    ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Teraz możesz zmienić harmonogram funkcji tak, aby była uruchamiana co godzinę, a nie co minutę. 

## <a name="update-the-timer-schedule"></a>Aktualizowanie harmonogramu czasomierza

1. Rozwiń swoją funkcję i kliknij pozycję **Integracja**. W tym miejscu określa się powiązania danych wejściowych i wyjściowych dla funkcji oraz ustawia harmonogram. 

2. W polu **Harmonogram** wprowadź nową wartość godzinową `0 0 */1 * * *`, a następnie kliknij przycisk **Zapisz**.  

![Harmonogram aktualizowania czasomierza usługi Functions w witrynie Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Funkcja będzie teraz uruchamiana raz na godzinę. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję uruchamianą zgodnie z harmonogramem.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji na temat wyzwalaczy czasomierza, zobacz [Planowanie wykonywania kodu za pomocą usługi Azure Functions](functions-bindings-timer.md).
