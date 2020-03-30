---
title: Tworzenie funkcji na platformie Azure wyzwalanych przez wiadomości kolejki
description: Utwórz za pomocą usługi Azure Functions funkcję niewymagającą użycia serwera wywoływaną za pomocą komunikatów przesyłanych do kolejki usługi Azure Storage.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: quickstart
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3d4cfc40f1849ecd2745b1d662973c7f64a0a60c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75769255"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Tworzenie funkcji wyzwalanej przez usługę Azure Queue Storage

Dowiedz się, jak utworzyć funkcję, która jest wyzwalana w momencie przesłania komunikatów do kolejki usługi Azure Storage.

![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Pobrać i zainstalować program [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Pomyślnie utworzona aplikacja funkcji.](./media/functions-create-first-azure-function/function-app-create-success.png)

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Tworzenie funkcji wyzwalanej przez kolejkę

1. Rozwiń aplikację funkcji **+** i kliknij przycisk obok **pozycji Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku trzeciego.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. W polu wyszukiwania wpisz `queue`, a następnie wybierz szablon **Wyzwalacz kolejki**.

1. Jeśli zostanie wyświetlony monit, wybierz **pozycję Zainstaluj,** aby zainstalować rozszerzenie usługi Azure Storage i wszelkie zależności w aplikacji funkcji. Po pomyślnym zakończeniu instalacji wybierz pozycję **Kontynuuj**.

    ![Instalowanie rozszerzeń powiązania](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

1. Użyj ustawień w sposób określony w tabeli znajdującej się poniżej obrazu.

    ![Konfigurowanie funkcji wyzwalanej przez usługę Queue Storage.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa** | Unikatowa w obrębie aplikacji funkcji | Nazwa funkcji wyzwalanej przez kolejkę. |
    | **Nazwa kolejki**   | myqueue-items    | Nazwa kolejki, z którą zostanie nawiązane połączenie na koncie magazynu. |
    | **Połączenie konta magazynu** | AzureWebJobsStorage | Możesz skorzystać z połączenia konta magazynu już używanego przez aplikację funkcji lub utworzyć nowe.  |    

1. Kliknij przycisk **Utwórz**, aby utworzyć funkcję.

Następnie nawiąż połączenie z kontem usługi Azure Storage i utwórz kolejkę magazynu **myqueue-items**.

## <a name="create-the-queue"></a>Tworzenie kolejki

1. W funkcji kliknij pozycję **Integracja**, rozwiń pozycję **Dokumentacja** i skopiuj wartości pól **Nazwa konta** oraz **Klucz konta**. Te poświadczenia służą do nawiązywania połączenia z kontem magazynu w Eksploratorze usługi Azure Storage. Jeśli już nawiązano połączenie z kontem magazynu, przejdź do kroku 4.

    ![Uzyskiwanie poświadczeń połączenia konta magazynu.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Uruchom narzędzie [Microsoft Azure Storage Explorer](https://storageexplorer.com/), kliknij ikonę połączenia po lewej stronie, wybierz pozycję **Użyj klucza i nazwy konta magazynu** i kliknij przycisk **Dalej**.

    ![Uruchamianie narzędzia Storage Account Explorer.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Wprowadź wartości **Nazwa konta** i **Klucz konta** z kroku 1, kliknij przycisk **Dalej**, a następnie przycisk **Połącz**.

    ![Wprowadzanie poświadczeń magazynu i nawiązywanie połączenia.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Rozwiń dołączone konto magazynu, kliknij prawym przyciskiem myszy `myqueue-items` **pozycję Kolejki,** kliknij polecenie **Utwórz kolejkę**, a następnie naciśnij klawisz Enter.

    ![Tworzenie kolejki magazynu.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Teraz, gdy masz już kolejkę magazynu, możesz przetestować funkcję, dodając komunikat do kolejki.

## <a name="test-the-function"></a>Testowanie funkcji

1. W portalu Azure przejdź do funkcji, rozwiń **dzienniki** u dołu strony i upewnij się, że przesyłanie strumieniowe dziennika nie jest wstrzymane.

1. W Eksploratorze magazynu rozwiń konto magazynu, **kolejki**i **elementy myszy,** a następnie kliknij pozycję **Dodaj wiadomość**.

    ![Dodawanie komunikatu do kolejki.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Wpisz komunikat „Hello World!” w polu **Message text** (Tekst komunikatu) i kliknij przycisk **OK**.

1. Poczekaj kilka sekund, a następnie wróć do dzienników funkcji i sprawdź, czy nowy komunikat został odczytany z kolejki.

    ![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Wróć do programu Storage Explorer, kliknij pozycję **Refresh** (Odśwież) i sprawdź, czy komunikat został przetworzony i nie ma go już w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję, która jest uruchamiana w momencie dodania komunikatu do kolejki magazynu. Aby uzyskać więcej informacji na temat wyzwalaczy usługi Queue Storage, zobacz [Powiązania usługi Queue Storage w usłudze Azure Functions](functions-bindings-storage-queue.md).

Teraz, gdy masz już utworzoną swoją pierwszą funkcję, dodajmy do funkcji powiązanie danych wyjściowych, które zapisuje komunikat z powrotem do innej kolejki.

> [!div class="nextstepaction"]
> [Dodawanie komunikatów do kolejki usługi Azure Storage przy użyciu funkcji](functions-integrate-storage-queue-output-binding.md)
