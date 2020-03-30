---
title: Jak korzystać z magazynu kolejek (C++) — usługa Azure Storage
description: Dowiedz się, jak korzystać z usługi magazynu kolejek na platformie Azure. Przykłady są zapisywane w języku C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 4fe543010df9514cb2b22c56482a4b592574e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941779"
---
# <a name="how-to-use-queue-storage-from-c"></a>Jak używać usługi Queue Storage z poziomu języka C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Azure Queue storage. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenariusze obejmują **wstawianie,** **podglądanie,** **uzyskiwanie**i **usuwanie** wiadomości kolejki, a także **tworzenie i usuwanie kolejek.**

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Zalecana wersja biblioteki klienta usługi Storage to wersja 2.2.0, dostępna za pośrednictwem narzędzia [NuGet](https://www.nuget.org/packages/wastorage) lub witryny [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++
W tym przewodniku użyjesz funkcji magazynu, które można uruchomić w aplikacji Języka C++.

W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.

Możesz zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, korzystając z następujących metod:

* **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla języka C++ README: Wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie Linux.
* **Windows:** W systemie Windows użyj [vcpkg](https://github.com/microsoft/vcpkg) jako menedżera zależności. Postępuj zgodnie z [szybkim startem,](https://github.com/microsoft/vcpkg#quick-start) aby zainicjować vcpkg. Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Można znaleźć przewodnik, jak skompilować kod źródłowy i eksportować do NuGet w pliku [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu kolejek
Dodaj następujące instrukcje include w górnej części pliku C++, w którym chcesz użyć interfejsów API magazynu platformy Azure, aby uzyskać dostęp do kolejek:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie ciągu połączenia magazynu platformy Azure
W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującyformat, przy użyciu nazwy konta magazynu i klucz dostępu do magazynu dla konta magazynu wymienionych w [witrynie Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje na temat kont magazynu i kluczy dostępu, zobacz [Informacje o kontach usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację na lokalnym komputerze z systemem Windows, można użyć [emulatora magazynu](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) platformy Microsoft Azure zainstalowanego przy użyciu [narzędzia Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu to narzędzie, które symuluje usługi obiektów Blob, Queue i Table dostępne na platformie Azure na komputerze deweloperskim lokalnym. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulator magazynu platformy Azure, wybierz przycisk **Start** lub naciśnij klawisz **Windows.** Zacznij wpisywać **emulator usługi Azure Storage**i wybierz **emulator usługi Microsoft Azure Storage** z listy aplikacji.

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.

## <a name="retrieve-your-connection-string"></a>Pobieranie parametrów połączenia
Możesz użyć klasy **cloud_storage_account** do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Jak: Tworzenie kolejki
Obiekt **cloud_queue_client** umożliwia uzyskanie obiektów referencyjnych dla kolejek. Poniższy kod tworzy **obiekt cloud_queue_client.**

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Użyj **obiektu cloud_queue_client,** aby uzyskać odwołanie do kolejki, której chcesz użyć. Można utworzyć kolejkę, jeśli nie istnieje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak: Wstawianie wiadomości do kolejki
Aby wstawić wiadomość do istniejącej kolejki, należy najpierw utworzyć nową **cloud_queue_message**. Następnie należy wywołać **metodę add_message.** **Cloud_queue_message** można utworzyć z tablicy ciągu lub **bajtów.** Oto kod, który tworzy kolejkę (jeśli kolejka nie istnieje) i wstawia komunikat „Hello, World”:

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Zajrzeć do następnej wiadomości
Można zajrzeć do wiadomości z przodu kolejki bez usuwania go z kolejki, wywołując **peek_message** metody.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Zmienianie zawartości wiadomości w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż n razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Jak: Odsuń kolejkę następnej wiadomości
Twój kod usuwa komunikat z kolejki w dwóch etapach. Gdy dzwonisz **get_message**, otrzymasz następną wiadomość w kolejce. Wiadomość zwrócona z **get_message** staje się niewidoczna dla innych wiadomości odczytu kodu z tej kolejki. Aby zakończyć usuwanie wiadomości z kolejki, należy również wywołać **delete_message**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Wywołania kodu **delete_message** zaraz po przetworzyniu wiadomości.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Jak: Wykorzystanie dodatkowych opcji usuwania kolejek
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu używa **metody get_messages,** aby uzyskać 20 wiadomości w jednym wywołaniu. Następnie przetwarza każdą wiadomość za pomocą **for** pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy pamiętać, że 5 minut rozpoczyna się dla wszystkich wiadomości w tym samym czasie, więc po 5 minut minęło od połączenia **get_messages,** wszystkie wiadomości, które nie zostały usunięte staną się widoczne ponownie.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Jak: Uzyskać długość kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **download_attributes** prosi usługę Kolejka o pobranie atrybutów kolejki, w tym liczby wiadomości. Metoda **approximate_message_count** pobiera przybliżoną liczbę wiadomości w kolejce.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Jak: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej wiadomości, należy wywołać metodę **delete_queue_if_exists** w obiekcie kolejki.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz podstawy magazynu kolejek, skorzystaj z tych łączy, aby dowiedzieć się więcej o usłudze Azure Storage.

* [Jak korzystać z magazynu obiektów Blob z języka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak korzystać z magazynu tabel z języka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Wyświetlanie listy zasobów usługi Azure Storage w języku C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Biblioteka klienta magazynu dla odwołania języka C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
