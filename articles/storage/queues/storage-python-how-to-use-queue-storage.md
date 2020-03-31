---
title: Jak korzystać z usługi Azure Queue storage w wersji 2.1 z języka Python — Usługa Azure Storage
description: Dowiedz się, jak używać usługi Kolejki platformy Azure w wersji 2.1 z języka Python do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania wiadomości.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060919"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Jak korzystać z usługi Azure Queue storage w wersji 2.1 z języka Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

W tym artykule przedstawiono typowe scenariusze przy użyciu usługi Azure Queue storage. Scenariusze obejmują wstawianie, wgląd, uzyskiwanie i usuwanie wiadomości kolejki oraz tworzenie i usuwanie kolejek.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

Przykłady w tym artykule są napisane w języku Python i używają [microsoft azure storage SDK dla języka Python]. Aby uzyskać więcej informacji na temat kolejek, zobacz [sekcję Następne kroki.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobieranie i instalowanie pakietu Azure Storage SDK dla języka Python

Zestaw [SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python w wersji 2.7, 3.3 lub nowszej.
 
### <a name="install-via-pypi"></a>Zainstaluj przez PyPi

Aby zainstalować za pomocą indeksu pakietów Pythona (PyPI), wpisz:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu Azure Storage SDK dla języka Python w wersji 0.36 lub starszej, odinstaluj starszy zestaw SDK przed `pip uninstall azure-storage` zainstalowaniem najnowszego pakietu.

Aby uzyskać alternatywne metody instalacji, zobacz [Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji

Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z kolejkami platformy Azure, zobacz Usługa Azure Storage: Wprowadzenie do [kolejek platformy Azure w języku Python.](https://github.com/Azure-Samples/storage-queue-python-getting-started) 

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano zarówno pakiety, `azure-storage-queue` jak i `azure-storage-common` pakiety.

## <a name="create-a-queue"></a>Tworzenie kolejki

[Obiekt QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) umożliwia pracę z kolejkami. Poniższy kod `QueueService` tworzy obiekt. Dodaj następujące informacje w górnej części dowolnego pliku języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage:

```python
from azure.storage.queue import QueueService
```

Poniższy kod `QueueService` tworzy obiekt przy użyciu nazwy konta magazynu i klucza konta. Zastąp *myaccount* i *mykey* nazwą i kluczem do konta.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Wstawianie komunikatu do kolejki

Aby wstawić wiadomość do kolejki, użyj [metody put_message,](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) aby utworzyć nową wiadomość i dodać ją do kolejki.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Wiadomości kolejki platformy Azure są przechowywane jako tekst. Jeśli chcesz przechowywać dane binarne, skonfiguruj funkcje kodowania i dekodowania bazy Base64 w obiekcie usługi kolejki przed umieszczeniem wiadomości w kolejce.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu

Można zajrzeć do wiadomości z przodu kolejki bez usuwania go z kolejki, wywołując [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) metody. Domyślnie `peek_messages` zagląda do pojedynczej wiadomości.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Usuwanie wysuwu wiadomości

Kod usuwa wiadomość z kolejki w dwóch krokach. Podczas wywoływania [get_messages,](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)domyślnie otrzymujesz następną wiadomość w kolejce. Wiadomość zwrócona `get_messages` staje się niewidoczna dla innych wiadomości odczytu kodu z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie wiadomości z kolejki, należy również wywołać [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Ten dwuetapowy proces usuwania wiadomości zapewnia, że gdy kod nie może przetworzyć wiadomości z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Wywołanie `delete_message` kodu zaraz po przetworze wiadomości.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu `get_messages` używa metody, aby uzyskać 16 wiadomości w jednym wywołaniu. Następnie przetwarza każdą wiadomość za pomocą for pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod używa [metody update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) do aktualizacji wiadomości. Limit czasu widoczności jest ustawiony na 0, co oznacza, że komunikat pojawia się natychmiast, a zawartość jest aktualizowana.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) prosi usługę kolejki o zwrócenie metadanych dotyczących `approximate_message_count`kolejki, a także o . Wynik jest tylko przybliżony, ponieważ wiadomości można dodać lub usunąć po usługa kolejki odpowiada na żądanie.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie zawarte w niej wiadomości, należy wywołać metodę [delete_queue.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy poznasz podstawy przechowywania kolejek, skorzystaj z tych łączy, aby dowiedzieć się więcej.

* [Odwołanie do interfejsu API języka Azure Queues Python](/python/api/azure-storage-queue)
* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Zestaw SDK magazynu platformy Microsoft Azure dla języka Python]: https://github.com/Azure/azure-storage-python
