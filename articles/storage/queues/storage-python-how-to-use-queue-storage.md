---
title: Jak używać magazynu kolejek w języku Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi kolejek platformy Azure w języku Python do tworzenia i usuwania kolejki, Wstaw, Pobierz i usunąć wiadomości.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2017
ms.locfileid: "25986753"
---
# <a name="how-to-use-queue-storage-from-python"></a>Jak używać Magazynu kolejek w języku Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi magazynu kolejek Azure. Próbki są napisane w języku Python i użyj [Microsoft Azure magazynu SDK dla języka Python]. Omówione scenariusze obejmują **wstawianie**, **wybierania**, **pobierania**, i **usuwanie** kolejki komunikatów, a także **tworzenie i usuwanie kolejek**. Aby uzyskać więcej informacji dotyczących kolejek można znaleźć w sekcji [następne kroki].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i zainstaluj magazynu Azure SDK dla języka Python

Azure Storage SDK for Python wymaga Python 2.7, 3.3, 3.4, 3.5 lub 3,6 i składa się z 4 pakietów różnych: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` i `azure-storage-queue`. W tym samouczku zamierzamy użyj `azure-storage-queue` pakietu.
 
### <a name="install-via-pypi"></a>Zainstaluj za pośrednictwem PyPi

Aby zainstalować za pomocą indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Magazyn Azure dla języka Python w wersji 0.36 lub starszej, najpierw musisz ją odinstalować przy użyciu `pip uninstall azure-storage` jako już nie udostępnimy zestawu SDK usługi Magazyn dla języka Python w jednym pakiecie.
> 
> 

Dla metod instalacji alternatywny, odwiedź stronę [zestawu SDK usługi Magazyn Azure dla języka Python w usłudze Github](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Porady: Tworzenie kolejki
**QueueService** obiektu umożliwia pracę z kolejki. Poniższy kod tworzy **QueueService** obiektu. Dodaj następujący kod w górnej części każdego pliku Python, w którym chcesz uzyskania programowego dostępu do magazynu Azure:

```python
from azure.storage.queue import QueueService
```

Poniższy kod tworzy **QueueService** przy użyciu klucza nazwy i konta konta magazynu. Zamień "myaccount" i "klucze" Nazwa konta i klucz.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Porady: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, użyj **put\_komunikat** metodę, aby utworzyć nową wiadomość i dodaj go do kolejki.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Porady: Podgląd kolejnego komunikatu
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując **peek\_wiadomości** metody. Domyślnie **peek\_wiadomości** wglądu w pojedynczym komunikacie.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Porady: Usuwania z kolejki komunikatów
Twój kod usuwa komunikat z kolejki w dwóch etapach. Podczas wywoływania **uzyskać\_wiadomości**, Pobierz następnej wiadomości w kolejce domyślnie. Komunikat zwrócony z **uzyskać\_wiadomości** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **usunąć\_komunikat**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **usunąć\_komunikat** natychmiast po przetworzeniu komunikatu.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.
Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje **uzyskać\_wiadomości** metodę, aby pobrać 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Porady: Zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Kod poniżej używa **aktualizacji\_komunikat** metodę aktualizowania wiadomości. Limitu czasu widoczności jest równa 0, co oznacza komunikat jest wyświetlany natychmiast i aktualizacji zawartości.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Porady: Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **Uzyskać\_kolejki\_metadanych** metody prosi usługę kolejki do zwracania metadanych dotyczących kolejki oraz **approximate_message_count**. Wynik jest tylko przybliżoną, ponieważ komunikaty mogą dodane lub usunięte po usługa kolejki odpowiada na żądania.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Porady: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **usunąć\_kolejki** metody.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy magazynu kolejek, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](/develop/python/)
* [Interfejs API REST usług Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage]
* [Microsoft Azure magazynu SDK dla języka Python]

[Blog zespołu odpowiedzialnego za usługę Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure magazynu SDK dla języka Python]: https://github.com/Azure/azure-storage-python