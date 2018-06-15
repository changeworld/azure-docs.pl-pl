---
title: Jak używać magazynu kolejek w języku Ruby | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi kolejek platformy Azure do tworzenia i usuwania kolejki, Wstaw, Pobierz i usunąć wiadomości. Przykłady napisany w języku Ruby.
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 0d7624d47a6924a5c8dec66b47ac0887ff493879
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2018
ms.locfileid: "27993640"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Jak używać Magazynu kolejek w języku Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi Microsoft Azure Queue Storage. Przykłady są napisane przy użyciu interfejsu API Azure Ruby.
Omówione scenariusze obejmują **wstawianie**, **wybierania**, **pobierania**, i **usuwanie** kolejki komunikatów, a także **tworzenie i usuwanie kolejek**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Tworzenie aplikacji Ruby
Utwórz aplikację dopisków fonetycznych. Aby uzyskać instrukcje, zobacz [tworzenie Ruby aplikacji w usłudze App Service w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji na dostęp do magazynu
Aby korzystać z usługi Azure storage, konieczne pobranie i użycie dopisków fonetycznych pakiet azure zawiera zestaw wygody bibliotek, które komunikują się z magazynu usługi REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Umożliwia uzyskanie pakietu RubyGems
1. Użyj interfejsu wiersza polecenia, takich jak **PowerShell** (system Windows), **terminali** (Mac), lub **Bash** (Unix).
2. Wpisz "azure gem instalacji" w oknie wiersza polecenia, aby zainstalować gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj edytora tekstu, Dodaj następujący element do góry pliku dopisków fonetycznych, których zamierzasz używać magazynu:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Ustawienia połączenia z magazynem Azure
Moduł azure odczyta zmiennych środowiskowych **AZURE\_MAGAZYNU\_konta** i **AZURE\_MAGAZYNU\_ACCESS_KEY** informacji wymagane do łączenia się z kontem magazynu platformy Azure. Jeśli te zmienne środowiskowe nie są skonfigurowane, należy określić informacje o koncie przed użyciem **Azure::QueueService** następującym kodem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Aby uzyskać te wartości z klasyczny lub Menedżera zasobów konta magazynu w portalu Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku ustawienia po prawej stronie, kliknij przycisk **klucze dostępu**.
4. W bloku klucze dostępu pojawia się zostanie wyświetlony klucz dostępu 1 i klucz dostępu 2. Można użyć jednego z tych. 
5. Kliknij ikonę kopiowania, aby skopiować klucz do Schowka. 

## <a name="how-to-create-a-queue"></a>Porady: Tworzenie kolejki
Poniższy kod tworzy **Azure::QueueService** obiektu, który umożliwia pracę z kolejki.

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

## <a name="how-to-insert-a-message-into-a-queue"></a>Porady: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, użyj **create_message()** metodę, aby utworzyć nową wiadomość i dodaj go do kolejki.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Porady: Podgląd kolejnego komunikatu
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując **peek\_messages()** metody. Domyślnie **peek\_messages()** wglądu w pojedynczym komunikacie. Można również określić, ile komunikatów, aby Podgląd.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Porady: Następny komunikat usuwania z kolejki
Można usunąć wiadomości z kolejki w dwóch etapach.

1. Podczas wywoływania **listy\_messages()**, Pobierz następnej wiadomości w kolejce domyślnie. Można również określić, ile komunikatów, które chcesz pobrać. Komunikaty zwracane z **listy\_messages()** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Przekaż widoczność limit czasu w sekundach jako parametr.
2. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **delete_message()**.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **usunąć\_message()** natychmiast po przetworzeniu komunikatu.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Porady: Zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Kod poniżej używa **update_message()** metodę aktualizowania wiadomości. Metoda zwraca spójną kolekcję zawierającą pop odbieranie komunikatu w kolejce i UTC wartość daty i godziny reprezentujące komunikat będzie widoczny w kolejce.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Porady: Dodatkowych opcji usuwania komunikatów
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

1. Możesz uzyskać partii komunikatu.
2. Można ustawić limitu czasu niewidoczności dłuższy lub krótszy, dzięki czemu kod będzie bardziej lub mniej czasu na pełne przetworzenie każdego komunikatu.

Poniższy przykład kodu wykorzystuje **listy\_messages()** metodę, aby pobrać 15 komunikatów w jednym wywołaniu. Następnie wyświetla i usuwa każdego komunikatu. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Porady: Pobieranie długości kolejki
Możesz uzyskać oszacowanie liczbę wiadomości w kolejce. **Uzyskać\_kolejki\_metadata()** metody prosi usługę kolejki, aby przekazać przybliżone wiadomości i metadane dotyczące kolejki.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Porady: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **usunąć\_queue()** metody dla obiekt kolejki.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy magazynu kolejek, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Odwiedź stronę [Blog zespołu usługi Magazyn Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Odwiedź stronę [zestawu Azure SDK dla środowiska Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repozytorium w witrynie GitHub

Porównanie między omówione w tym artykule usługi kolejek platformy Azure i Azure kolejek usługi Service Bus omówione w [jak używać kolejek usługi Service Bus](/develop/ruby/how-to-guides/service-bus-queues/) artykułu, zobacz [kolejek Azure i kolejek usługi Service Bus - porównaniu i Odróżniające](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)