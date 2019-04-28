---
title: Jak używać magazynu kolejek w języku Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi kolejek platformy Azure za pomocą języka Python do tworzenia i usuwania kolejki oraz wstawiania, pobieranie i usuwanie wiadomości.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: afa0b6d4f87c4325f116463242c15df9d9b6e7c4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108808"
---
# <a name="how-to-use-queue-storage-from-python"></a>Jak używać Magazynu kolejek w języku Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą usługi Azure Queue storage. Przykłady są zapisywane w języku Python i użyj [Microsoft Azure Storage SDK dla języka Python]. Omówione scenariusze obejmują **wstawianie**, **wgląd do**, **wprowadzenie**, i **usuwanie** kolejki komunikatów, a także  **Tworzenie i usuwanie kolejek**. Więcej informacji na temat kolejek można znaleźć w sekcji [następne kroki].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestawu SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga Python 2.7 3.3, 3.4, 3.5 i 3.6.
 
### <a name="install-via-pypi"></a>Instalowanie za pomocą PyPi

Aby zainstalować, za pomocą indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0.36 lub starszej, należy odinstalować starsze przy użyciu zestawu SDK `pip uninstall azure-storage` przed zainstalowaniem najnowszy pakiet.

Metody instalacji alternatywny temacie [zestawu SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetl przykładowej aplikacji

Do wyświetlania i uruchamiania aplikacji przykładowej, który pokazuje, jak używać języka Python za pomocą kolejek systemu Azure, zobacz [usługi Azure Storage: Wprowadzenie do kolejek platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Aby uruchomić przykładową aplikację, upewnij się, zainstalowano zarówno `azure-storage-queue` i `azure-storage-common` pakietów.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki

**QueueService** obiekt umożliwia pracę z kolejki. Poniższy kod tworzy **QueueService** obiektu. Dodaj następujący kod w górnej części każdego pliku Python, w której chcesz uzyskać programowy dostęp do usługi Azure Storage:

```python
from azure.storage.queue import QueueService
```

Poniższy kod tworzy **QueueService** przy użyciu konta nazwy i klucza konta magazynu. Zamień "myaccount" i "klucze" klucza i nazwy konta.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, należy użyć **umieścić\_komunikat** metodę, aby utworzyć nową wiadomość i dodać go do kolejki.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: Podgląd kolejnego komunikatu
Można wglądu do wiadomości uzyskać kolejki, bez usuwania go z kolejki, wywołując **podglądu\_wiadomości** metody. Domyślnie **podglądu\_wiadomości** wglądu w pojedynczym komunikacie.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Instrukcje: Usuń z kolejki komunikatów
Twój kod usuwa komunikat z kolejki w dwóch etapach. Gdy wywołujesz **uzyskać\_wiadomości**, uzyskasz następny komunikat w kolejce domyślnie. Komunikat zwrócony z **uzyskać\_wiadomości** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **Usuń\_komunikat**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **Usuń\_komunikat** natychmiast po przetworzeniu komunikatu.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.
Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje **uzyskać\_wiadomości** metodę, aby uzyskać 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: Zmień zawartość komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Kod poniżej został użyty **aktualizacji\_komunikat** metodę, aby zaktualizować wiadomości. Limit czasu widoczności jest równa 0, co oznacza, natychmiast pojawi się komunikat, a zawartość jest aktualizowana.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **Uzyskać\_kolejki\_metadanych** metoda prosi usługę kolejki do zwracania metadanych dotyczących tej kolejki, a **approximate_message_count**. Wynik jest przybliżona tylko w przypadku, ponieważ komunikaty mogą dodany lub usunięty, gdy usługa kolejki odpowiada na żądania.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **Usuń\_kolejki** metody.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Queue storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK dla języka Python]: https://github.com/Azure/azure-storage-python
