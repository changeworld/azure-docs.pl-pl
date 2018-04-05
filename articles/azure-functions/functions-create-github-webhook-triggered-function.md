---
title: Tworzenie funkcji na platformie Azure wyzwalanej przez element webhook GitHub | Microsoft Docs
description: Użyj usługi Azure Functions, aby utworzyć funkcję niewymagającą użycia serwera wywoływaną za pomocą elementu webhook GitHub.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: cd446c64b8e7627db1c76d652ea1ab6f496eee65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Tworzenie funkcji wyzwalanej przez element webhook GitHub

Dowiedz się, jak utworzyć funkcję wyzwalaną przez żądanie elementu webhook protokołu HTTP z ładunkiem specyficznym dla usługi GitHub.

![Funkcja wyzwalana przez element webhook GitHub w witrynie Azure Portal](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Wymagania wstępne

+ Konto w usłudze GitHub z przynajmniej jednym projektem.
+ Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Pomyślnie utworzona aplikacja funkcji.](./media/functions-create-first-azure-function/function-app-create-success.png)

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Tworzenie funkcji wyzwalanej przez element webhook GitHub

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **Funkcja niestandardowa**. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.

    ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. W polu wyszukiwania wpisz `github`, a następnie wybierz żądany język dla szablonu wyzwalacza elementu webhook GitHub. 

     ![Wybieranie szablonu wyzwalacza elementu webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Wpisz **nazwę** funkcji, a następnie wybierz pozycję **Utwórz**. 

     ![Konfigurowanie funkcji wyzwalanej przez element webhook GitHub w witrynie Azure portal](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji**, po czym skopiuj i zapisz wartości. Powtórz te czynności po kliknięciu pozycji **</> Pobierz wpis tajny usługi GitHub**. Wartości te będą potrzebne podczas konfigurowania elementu webhook w usłudze GitHub.

    ![Sprawdzanie kodu funkcji](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

W następnym kroku zostanie utworzony element webhook w repozytorium GitHub.

## <a name="configure-the-webhook"></a>Konfigurowanie elementu webhook

1. W usłudze GitHub przejdź do repozytorium, którego jesteś właścicielem. Możesz też użyć dowolnego rozwidlonego repozytorium. Jeśli potrzebujesz rozwidlić repozytorium, zobacz <https://github.com/Azure-Samples/functions-quickstart>.

2. Wybierz kolejno pozycje **Ustawienia** > **Opcje** i upewnij się, że ustawienie **Problemy** zostało włączone w obszarze **Funkcje**.

   ![Włączanie problemów](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. W obszarze **Ustawienia** wybierz kolejno pozycje **Elementy webhook** > **Dodaj element webhook**.

    ![Dodawanie elementu webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Użyj ustawień określonych w następującej tabeli, a następnie kliknij pozycję **Dodaj element webhook**:

    ![Ustawianie adresu URL i wpisu tajnego elementu webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Ustawienie | Sugerowana wartość | Opis |
|---|---|---|
| **Adres URL ładunku** | Skopiowana wartość | Użyj wartości zwróconej po kliknięciu pozycji **</> Pobierz adres URL funkcji**. |
| **Typ zawartości** | application/json | Funkcja oczekuje ładunku JSON. |
| **Wpis tajny**   | Skopiowana wartość | Użyj wartości zwróconej po kliknięciu pozycji **</> Pobierz wpis tajny usługi GitHub**. |
| Wyzwalacze zdarzeń | Pozwól mi wybrać pojedyncze zdarzenia | Wyzwalacz ma być uruchamiany tylko w przypadku zdarzeń z komentarzami dotyczącymi problemów.  |
| | Komentarz dotyczący problemu |  |

Element webhook został skonfigurowany do wyzwolenia funkcji po dodaniu nowego komentarza dotyczącego problemu.

## <a name="test-the-function"></a>Testowanie funkcji

1. W swoim repozytorium GitHub otwórz kartę **Problemy** w nowym oknie przeglądarki.

1. W nowym oknie kliknij pozycję **Nowy problem**, wpisz tytuł, a następnie kliknij pozycję **Prześlij nowy problem**.

1. W obszarze problemu wpisz komentarz i kliknij pozycję **Komentarz**.

    ![Dodawanie komentarza dotyczącego problemu w usłudze GitHub.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Wróć do portalu i wyświetl dzienniki. Powinien zostać wyświetlony wpis śledzenia z nowym tekstem komentarza.

     ![Wyświetlanie tekstu komentarza w dziennikach.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję wyzwalaną w momencie otrzymania żądania od elementu webhook usługi GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji na temat wyzwalaczy elementów webhook, zobacz temat [Powiązania protokołu HTTP i elementów webhook w usłudze Azure Functions](functions-bindings-http-webhook.md).
