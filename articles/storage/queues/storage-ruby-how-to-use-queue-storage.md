---
title: Jak korzystać z magazynu kolejek z Ruby - Azure Storage
description: Dowiedz się, jak używać usługi Kolejka platformy Azure do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania wiadomości. Próbki napisane w Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721280"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Jak używać Magazynu kolejek w języku Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Microsoft Azure Queue Storage. Przykłady są zapisywane przy użyciu interfejsu API platformy Azure Ruby.
Scenariusze obejmują **wstawianie,** **podglądanie,** **uzyskiwanie**i **usuwanie** wiadomości kolejki, a także **tworzenie i usuwanie kolejek.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Tworzenie aplikacji Ruby
Utwórz aplikację Ruby. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji Ruby w usłudze App Service w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji do uzyskiwania dostępu do magazynu
Aby korzystać z usługi Azure Storage, należy pobrać i używać pakietu platformy Azure Ruby, który zawiera zestaw bibliotek wygody, które komunikują się z usługami REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "gem install azure" w oknie poleceń, aby zainstalować klejnot i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj ulubionego edytora tekstu, dodaj następujące elementy do górnej części pliku Ruby, w którym zamierzasz korzystać z magazynu:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage
Moduł platformy Azure odczyta zmienne środowiskowe **KONTA USŁUGI AZURE\_STORAGE\_** i usługi AZURE **\_STORAGE\_ACCESS_KEY** informacji wymaganych do połączenia się z kontem usługi Azure storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie przed użyciem **usługi Azure::QueueService** z następującym kodem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Aby uzyskać te wartości z klasycznego konta magazynu lub konta magazynu menedżera zasobów w witrynie Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku Ustawienia po prawej stronie kliknij pozycję **Klucze dostępu**.
4. W wyświetlonym bloku Klucze dostępu widoczny będzie klucz dostępu 1 i klucz dostępu 2. Możesz użyć jednego z nich. 
5. Kliknij ikonę kopiowania, aby skopiować klucz do schowka. 

## <a name="how-to-create-a-queue"></a>Jak: Tworzenie kolejki
Poniższy kod tworzy **obiekt Azure::QueueService,** który umożliwia pracę z kolejkami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Użyj metody **create_queue(),** aby utworzyć kolejkę o określonej nazwie.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak: Wstawianie wiadomości do kolejki
Aby wstawić wiadomość do kolejki, użyj metody **create_message(),** aby utworzyć nową wiadomość i dodać ją do kolejki.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Zajrzeć do następnej wiadomości
Możesz zajrzeć do wiadomości z przodu kolejki bez usuwania go z kolejki, wywołując metodę **peek\_messages().** Domyślnie **zagląda\_wiadomość()** zagląda do pojedynczej wiadomości. Można również określić, ile wiadomości chcesz zajrzeć.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Jak: Dequeue następnej wiadomości
Wiadomość z kolejki można usunąć w dwóch krokach.

1. Podczas wywoływania **list\_messages()** domyślnie otrzymujesz następną wiadomość w kolejce. Można również określić, ile wiadomości chcesz uzyskać. Wiadomości zwracane z **listy\_messages()** stają się niewidoczne dla innych wiadomości odczytu kodu z tej kolejki. Przemiń w przeliczeń widoczności w sekundach jako parametr.
2. Aby zakończyć usuwanie wiadomości z kolejki, należy również wywołać **delete_message()**.

Ten dwuetapowy proces usuwania wiadomości zapewnia, że gdy kod nie może przetworzyć wiadomości z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Wywołania kodu **delete\_message()** zaraz po przetworzyniu wiadomości.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Zmienianie zawartości wiadomości w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Poniższy kod używa **metody update_message()** do aktualizacji wiadomości. Metoda zwróci krotki, która zawiera pop odbioru wiadomości kolejki i wartość daty utc, który reprezentuje, gdy wiadomość będzie widoczna w kolejce.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Jak: Dodatkowe opcje odkuwania wiadomości
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

1. Można uzyskać partię wiadomości.
2. Można ustawić dłuższy lub krótszy limit czasu niewidzialności, dzięki czemu kod mniej lub bardziej czas, aby w pełni przetworzyć każdą wiadomość.

Poniższy przykład kodu używa metody **list\_messages(),** aby uzyskać 15 wiadomości w jednym wywołaniu. Następnie drukuje i usuwa każdą wiadomość. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Jak: Uzyskać długość kolejki
Można uzyskać oszacowanie liczby wiadomości w kolejce. Metoda **\_get\_kolejki metadanych()** prosi usługę kolejki o zwrócenie przybliżonej liczby wiadomości i metadanych dotyczących kolejki.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Jak: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej wiadomości, wywołanie metody **delete\_queue()** w obiekcie kolejki.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy magazynu kolejek, skorzystaj z tych łączy, aby dowiedzieć się więcej o bardziej złożonych zadaniach magazynowania.

* Odwiedź blog [zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* Odwiedź [zestaw Azure SDK dla](https://github.com/WindowsAzure/azure-sdk-for-ruby) repozytorium Ruby w usłudze GitHub

Aby uzyskać porównanie usługi kolejkowania platformy Azure omówionych w tym artykule i kolejek usługi Azure Service Bus omówionych w [artykule Jak używać kolejek magistrali usług,](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) zobacz [Kolejki platformy Azure i kolejki magistrali usług — porównywane i kontrastowane](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
