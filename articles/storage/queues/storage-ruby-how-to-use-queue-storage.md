---
title: Jak używać magazynu kolejek w języku Ruby - usługi Azure Storage
description: Dowiedz się, jak używać usługi kolejek platformy Azure do tworzenia i usuwania kolejki oraz wstawiania, pobieranie i usuwanie wiadomości. Przykłady napisane w języku Ruby.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 30a090aeb2d66c732e70a9acce67d5f3374c32fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153166"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Jak używać Magazynu kolejek w języku Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą usługi Microsoft Azure Queue Storage. Przykłady są napisane przy użyciu interfejsu API Azure Ruby.
Omówione scenariusze obejmują **wstawianie**, **wgląd do**, **wprowadzenie**, i **usuwanie** kolejki komunikatów, a także  **Tworzenie i usuwanie kolejek**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Tworzenie aplikacji w języku Ruby
Jak utworzyć aplikację języka Ruby. Aby uzyskać instrukcje, zobacz [tworzenie aplikacji Ruby w usłudze App Service w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Umożliwia skonfigurowanie aplikacji dostęp do magazynu
Aby użyć usługi Azure storage, należy do pobrania i użycia języka Ruby pakiet platformy azure, który zawiera zbiór bibliotek wygody, które komunikują się z usług REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "azure gem instalacji" w oknie wiersza polecenia, aby zainstalować rozwiązanie gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj ulubionego edytora tekstu, Dodaj następujący kod do górnej części pliku języka Ruby, których zamierzasz używać magazynu:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Skonfigurować połączenie usługi Azure Storage
Moduł azure odczyta zmienne środowiskowe **AZURE\_MAGAZYNU\_konta** i **AZURE\_MAGAZYNU\_ACCESS_KEY** informacji wymagane do połączenia z kontem usługi Azure storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie, przed rozpoczęciem korzystania z **Azure::QueueService** następującym kodem:

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

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki
Poniższy kod tworzy **Azure::QueueService** obiektu, który umożliwia pracę z kolejkami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Użyj **create_queue()** metodę, aby utworzyć kolejkę o określonej nazwie.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, należy użyć **create_message()** metodę, aby utworzyć nową wiadomość i dodać go do kolejki.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: Podgląd kolejnego komunikatu
Użytkownik może wglądu do wiadomości uzyskać kolejki bez jego usuwania z kolejki, wywołując **podglądu\_messages()** metody. Domyślnie **podglądu\_messages()** wglądu w pojedynczym komunikacie. Można również określić, ile komunikatów, które chcesz wglądu.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: Usuń następny komunikat z kolejki
Możesz usunąć komunikatu z kolejki w dwóch etapach.

1. Gdy wywołujesz **listy\_messages()** , uzyskasz następny komunikat w kolejce domyślnie. Można również określić, ile komunikatów, które chcesz pobrać. Komunikaty zwracane z **listy\_messages()** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Limit czasu widoczności przekazanej w ciągu kilku sekund, jako parametr.
2. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **delete_message()** .

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **Usuń\_message()** natychmiast po przetworzeniu komunikatu.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: Zmień zawartość komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Kod poniżej został użyty **update_message()** metodę, aby zaktualizować wiadomości. Metoda zwraca spójną kolekcję zawierającą pop otrzymywania komunikatu w kolejce i czasem UTC wartość daty i godziny reprezentujące komunikat będzie widoczny w kolejce.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instrukcje: Dodatkowe opcje usuwania z kolejki komunikatów
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

1. Możesz uzyskać partię komunikatów.
2. Możesz ustawić limit czasu niewidoczności dłuższy lub krótszy, dzięki czemu kod więcej lub mniej czasu na pełne przetworzenie każdego komunikatu.

Poniższy przykład kodu wykorzystuje **listy\_messages()** metodę, aby uzyskać 15 komunikatów w jednym wywołaniu. Następnie wyświetla i usuwa każdego komunikatu. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: Pobieranie długości kolejki
Możesz uzyskać oszacowanie liczby wiadomości w kolejce. **Uzyskać\_kolejki\_metadata()** metoda prosi usługę kolejki do zwrócenia liczba komunikatów przybliżony oraz metadane dotyczące kolejki.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **Usuń\_queue()** metody na obiekcie kolejki.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi queue storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Odwiedź stronę [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* Odwiedź stronę [zestawu Azure SDK dla języka Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repozytorium w witrynie GitHub

W celu porównania omówionych w tym artykule usługi kolejek platformy Azure i kolejek usługi Azure Service Bus omówione w [jak używać kolejek usługi Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) artykuł, zobacz [kolejek platformy Azure i kolejek usługi Service Bus — porównanie i Porównanie](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
