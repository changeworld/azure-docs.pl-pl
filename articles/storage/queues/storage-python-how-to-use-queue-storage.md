---
title: Jak używać magazynu kolejek w języku Python — Azure Storage
description: Dowiedz się, w jaki sposób używać usługa kolejki platformy Azure z poziomu języka Python do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/14/2018
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 1ed084bfa0cf6879983e38ac6a8c5ab57e8948a8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721352"
---
# <a name="how-to-use-queue-storage-from-python"></a>Jak używać Magazynu kolejek w języku Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze za pomocą usługi Azure queue storage. Przykłady są zapisywane w języku Python i używają [Zestaw SDK Microsoft Azure Storage dla języka Python]. Omówione scenariusze obejmują **Wstawianie**, **wgląd**, **pobieranie**i **usuwanie** komunikatów w kolejce, a także **Tworzenie i usuwanie kolejek**. Aby uzyskać więcej informacji o kolejkach, zapoznaj się z sekcją [następne kroki].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i Zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python 2,7, 3,3, 3,4, 3,5 lub 3,6.
 
### <a name="install-via-pypi"></a>Zainstaluj za pomocą PyPi

Aby zainstalować program za pośrednictwem indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0,36 lub starszej, Odinstaluj starszy `pip uninstall azure-storage` zestaw SDK przy użyciu programu przed zainstalowaniem najnowszego pakietu.

Aby zapoznać się z alternatywnymi metodami instalacji, zobacz [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji

Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z kolejkami [platformy Azure, zobacz Azure Storage: Wprowadzenie z kolejkami platformy Azure](https://github.com/Azure-Samples/storage-queue-python-getting-started)w języku Python. 

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano `azure-storage-queue` pakiety `azure-storage-common` i.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki

Obiekt **QueueService** umożliwia korzystanie z kolejek. Poniższy kod tworzy obiekt **QueueService** . Dodaj następujące elementy w górnej części każdego pliku języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage:

```python
from azure.storage.queue import QueueService
```

Poniższy kod tworzy obiekt **QueueService** przy użyciu nazwy konta magazynu i klucza konta. Zastąp element "Moje konto" i "klucze" nazwą konta i kluczem.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, użyj metody **Put\_komunikat** , aby utworzyć nową wiadomość i dodać ją do kolejki.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: Wgląd w następny komunikat
Możesz uzyskać wgląd w komunikat z przodu kolejki bez usuwania go z kolejki, wywołując metodę **wglądu\_wiadomości** . Domyślnie funkcja **wglądu\_w wiadomości** jest wglądowa w jeden komunikat.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Instrukcje: Dequeueing messages
Kod usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu metody **get\_messages**domyślnie otrzymujesz następny komunikat w kolejce. Komunikat zwrócony z **komunikatów get\_** jest niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać opcję **Usuń\_komunikat**. Ten dwuetapowy proces usuwania komunikatu gwarantuje, że gdy kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje **komunikat usuwania\_** bezpośrednio po przetworzeniu komunikatu.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.
Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu używa metody **get\_messages** , aby pobrać 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: Zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod używa metody **Update\_Message** do zaktualizowania wiadomości. Limit czasu widoczności jest ustawiony na 0, co oznacza, że komunikat natychmiast pojawia się, a zawartość jest aktualizowana.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **metadanych\_get\_Queue** prosi usługę kolejki o zwrócenie metadanych dotyczących kolejki oraz **approximate_message_count**. Wynik jest przybliżony, ponieważ komunikaty mogą być dodawane lub usuwane po udzieleniu odpowiedzi przez usługę kolejki na żądanie.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj metodę **delete\_Queue** .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy magazynu kolejek, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Zestaw SDK Microsoft Azure Storage dla języka Python]: https://github.com/Azure/azure-storage-python
