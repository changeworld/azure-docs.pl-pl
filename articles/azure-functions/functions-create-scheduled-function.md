---
title: Tworzenie funkcji uruchamianej zgodnie z harmonogramem na platformie Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć na platformie Azure funkcję uruchamianą zgodnie z określonym harmonogramem.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: a070a7da48af8abe138900dd0221bace2149aa20
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469241"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Tworzenie funkcji wyzwalanej czasomierzem na platformie Azure

Dowiedz się, jak za pomocą Azure Functions utworzyć funkcję [bezserwerową](https://azure.microsoft.com/solutions/serverless/) , która jest uruchamiana na podstawie zdefiniowanego harmonogramu.

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

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku 3.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/functions-create-scheduled-function/add-first-function.png)

3. W polu wyszukiwania wpisz ciąg `timer` i skonfiguruj nowy wyzwalacz, wprowadzając ustawienia określone w tabeli znajdującej się poniżej obrazu.

    ![Utwórz funkcję wyzwalaną czasomierzem w witrynie Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa** | Domyślne | Określa nazwę funkcji wyzwalanej czasomierzem. |
    | **Harmonogram** | 0 \*/1 \* \* \* \* | Składające się z 6 pól [wyrażenie CRON](functions-bindings-timer.md#ncrontab-expressions) planujące uruchamianie funkcji co minutę. |

4. Kliknij pozycję **Utwórz**. Zostanie utworzona funkcja w wybranym języku uruchamiana co minutę.

5. Zweryfikuj uruchomienie, wyświetlając informacje o śledzeniu zapisane w dziennikach.

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

Utworzono funkcję uruchamianą zgodnie z harmonogramem. Aby uzyskać więcej informacji na temat wyzwalaczy czasomierza, zobacz [Planowanie wykonywania kodu za pomocą usługi Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
