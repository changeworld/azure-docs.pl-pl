---
title: 'Szybki start: biblioteka magazynu kolejki platformy Azure w wersji 12 — Python'
description: Dowiedz się, jak utworzyć kolejkę i dodać wiadomości do kolejki za pomocą biblioteki Języka Python w wersji 12 kolejki azure. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199771"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Szybki start: biblioteka klienta magazynu usługi Azure Queue w wersji 12 dla języka Python

Wprowadzenie do biblioteki klienta usługi Azure Queue storage w wersji 12 dla języka Python. Usługa Azure Queue storage to usługa do przechowywania dużej liczby wiadomości do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta magazynu usługi Azure Queue w wersji 12 dla języka Python, aby:

* Tworzenie kolejki
* Dodawanie wiadomości do kolejki
* Wgląd do wiadomości w kolejce
* Aktualizowanie wiadomości w kolejce
* Odbieranie wiadomości z kolejki
* Usuwanie wiadomości z kolejki
* Usuwanie kolejki

[API reference documentation](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [Przykłady](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples) kodu źródłowego | pakietu[(Indeks pakietu Python)](https://pypi.org/project/azure-storage-queue/) | [biblioteki referencyjnej](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)interfejsu API

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) dla twojego systemu operacyjnego - 2.7, 3.5 lub więcej

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta usługi Azure Queue storage w wersji 12 dla języka Python.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację języka Python o nazwie *queues-quickstart-v12*.

1. W oknie konsoli (takich jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przełącz się do nowo utworzonego katalogu *kolejki-szybki start-v12.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Zainstaluj bibliotekę klienta magazynu obiektów Blob `pip install` platformy Azure dla pakietu Języka Python za pomocą polecenia.

```console
pip install azure-storage-queue
```

To polecenie instaluje bibliotekę klienta magazynu usługi Azure Queue dla pakietu Python i wszystkie biblioteki, od których zależy. W takim przypadku jest to tylko biblioteka podstawowa platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Otwieranie nowego pliku tekstowego w edytorze kodu
1. Dodawanie `import` instrukcji
1. Tworzenie struktury programu, w tym bardzo podstawowa obsługa wyjątków

    Oto kod:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Zapisz nowy plik jako *queues-quickstart-v12.py* w katalogu *kolejki-szybki start-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie. Magazyn kolejek oferuje trzy typy zasobów:

* Konto magazynu
* Kolejka na koncie magazynu
* Wiadomości w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejek](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka Python do interakcji z tymi zasobami:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
* [QueueClient:](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) `QueueClient` Klasa umożliwia zarządzanie i manipulowanie poszczególnych kolejek i jej wiadomości.
* [QueueMessage:](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) `QueueMessage` Klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą biblioteki klienta magazynu usługi Azure Queue dla języka Python:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kolejki](#create-a-queue)
* [Dodawanie wiadomości do kolejki](#add-messages-to-a-queue)
* [Wgląd do wiadomości w kolejce](#peek-at-messages-in-a-queue)
* [Aktualizowanie wiadomości w kolejce](#update-a-message-in-a-queue)
* [Odbieranie wiadomości z kolejki](#receive-messages-from-a-queue)
* [Usuwanie wiadomości z kolejki](#delete-messages-from-a-queue)
* [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu. Parametry połączenia są przechowywane zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `try` wewnątrz bloku:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Zdecyduj o nazwie nowej kolejki. Poniższy kod dołącza wartość UUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się od litery lub liczby. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi również zawierać od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazywania kolejek, zobacz [Nazywanie kolejek i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie klasy [QueueClient.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) Następnie zadzwoń do [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metody, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na `try` końcu bloku:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Dodawanie wiadomości do kolejki

Poniższy fragment kodu dodaje wiadomości do kolejki, wywołując [metodę send_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) Zapisuje również [QueueMessage zwrócony](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) `send_message` z trzeciego wywołania. Jest `saved_message` używany do aktualizacji zawartości wiadomości w dalszej części programu.

Dodaj ten kod na `try` końcu bloku:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd do wiadomości w kolejce

Zajrzeć do wiadomości w kolejce, wywołując [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metody. Metoda `peek_messages` pobiera jeden lub więcej wiadomości z przodu kolejki, ale nie zmienia widoczności wiadomości.

Dodaj ten kod na `try` końcu bloku:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie wiadomości w kolejce

Zaktualizuj zawartość wiadomości, wywołując [metodę update_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) Metoda `update_message` może zmienić limit czasu widoczności wiadomości i zawartość. Zawartość wiadomości musi być ciągiem zakodowanym w uiścić UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością należy przekazać wartości z wiadomości, która została zapisana wcześniej w kodzie. Wartości `saved_message` identyfikują komunikat, który ma być aktualizowany.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki

Pobierz wcześniej dodane wiadomości, wywołując [metodę receive_messages.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)

Dodaj ten kod na `try` końcu bloku:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie wiadomości z kolejki

Usuń wiadomości z kolejki po ich odebraniu i przetworzyniu. W takim przypadku przetwarzanie jest po prostu wyświetlanie komunikatu na konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `input` przed procesami i usunięciem wiadomości. Sprawdź w [witrynie Azure portal,](https://portal.azure.com) czy zasoby zostały utworzone poprawnie, zanim zostaną usunięte. Wszystkie wiadomości, które nie zostały jawnie usunięte, po pewnym czasie staną się ponownie widoczne w kolejce, aby uzyskać kolejną szansę na ich przetworzenie.

Dodaj ten kod na `try` końcu bloku:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając kolejkę przy użyciu [metody delete_queue.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)

Dodaj ten kod na `try` końcu bloku i zapisz plik:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę wiadomości w kolejce, a następnie pobiera je i usuwa, zanim na koniec usunie kolejkę.

W oknie konsoli przejdź do katalogu zawierającego plik *queues-quickstart-v12.py,* a następnie `python` wykonaj następujące polecenie, aby uruchomić aplikację.

```console
python queues-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Gdy aplikacja wstrzymuje się przed odbierania wiadomości, sprawdź swoje konto magazynu w [witrynie Azure portal](https://portal.azure.com). Sprawdź, czy wiadomości znajdują się w kolejce.

Naciśnij klawisz **Enter,** aby odbierać i usuwać wiadomości. Po wyświetleniu monitu naciśnij ponownie klawisz **Enter,** aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć kolejkę i dodać do niej wiadomości za pomocą kodu Pythona. Następnie nauczyłeś się podglądać, pobierać i usuwać wiadomości. Na koniec dowiedziałeś się, jak usunąć kolejkę wiadomości.

Aby uzyskać samouczki, przykłady, szybkie uruchamianie i inną dokumentację, odwiedź:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów języka Python](https://docs.microsoft.com/azure/python/)

* Aby dowiedzieć się więcej, zobacz [biblioteki usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
* Aby wyświetlić więcej przykładowych aplikacji magazynu kolejki platformy Azure, przejdź do [przykładów biblioteki klienta usługi Azure Queue storage w wersji 12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
