---
title: Jak używać magazynu kolejek (C++) | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z usługi magazyn kolejek na platformie Azure. Przykłady są napisane w języku C++.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: jahogg
editor: tysonn
ms.assetid: c8a36365-29f6-404d-8fd1-858a7f33b50a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.openlocfilehash: 5e81d5e0af9871099b7f921f355cf94249e4d30c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874052"
---
# <a name="how-to-use-queue-storage-from-c"></a>Jak używać magazynu kolejek w języku C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku opisano sposób wykonywania typowych scenariuszy przy użyciu usługi magazynu kolejek Azure. Przykłady są napisane w C++ i użyj [biblioteki klienta usługi Azure Storage dla języka C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Omówione scenariusze obejmują **wstawianie**, **wybierania**, **pobierania**, i **usuwanie** kolejki komunikatów, a także **tworzenie i usuwanie kolejek**.

> [!NOTE]
> Ten przewodnik jest przeznaczony dla biblioteki klienta magazynu Azure dla języka C++ w wersji 1.0.0 i powyżej. Zalecana wersja jest biblioteka klienta usługi Storage 2.2.0, który jest dostępny za pośrednictwem [NuGet](http://www.nuget.org/packages/wastorage) lub [GitHub](http://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji C++
W tym przewodniku użyje funkcji magazynu, które mogą być uruchamiane w ramach aplikacji C++.

Aby to zrobić, należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i Utwórz konto magazynu Azure w ramach subskrypcji platformy Azure.

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, można użyć następujących metod:

* **Linux:** postępuj zgodnie z instrukcjami [biblioteki klienta usługi Azure Storage dla języka C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) strony.
* **System Windows:** w programie Visual Studio, kliknij przycisk **Narzędzia > Menedżera pakietów NuGet > konsoli Menedżera pakietów**. Wpisz następujące polecenie w [Konsola Menedżera pakietów NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i naciśnij klawisz **ENTER**.

```  
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurowanie aplikacji dostęp do kolejki magazynu
Dodaj następujące instrukcje na początku pliku C++, których chcesz użyć interfejsów API magazynu Azure można uzyskać dostępu do kolejek obejmują:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage
Klienta usługi Azure storage używa parametrów połączenia magazynu do przechowywania punktów końcowych i poświadczeń do uzyskiwania dostępu do danych usługi zarządzania. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującym formacie, przy użyciu nazwy konta magazynu i klucz dostępu do magazynu dla konta magazynu na liście [Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje dotyczące kont magazynu i klucze dostępu, zobacz [o kontach magazynu Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania parametrów połączenia:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację w lokalnym komputerze z systemem Windows, można użyć Microsoft Azure [emulatora magazynu](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) zainstalowane z [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu jest narzędziem, która symuluje dostępnej na platformie Azure na komputerze deweloperskim lokalnych usług obiektów Blob, kolejki i tabeli. W poniższym przykładzie pokazano, jak można zadeklarować pole statyczne, aby mógł pomieścić parametry połączenia z lokalnym emulatorze magazynu:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulatora magazynu Azure, wybierz **Start** przycisk lub naciśnij przycisk **Windows** klucza. Rozpocznij wpisywanie **emulatora magazynu Azure**i wybierz **emulatora magazynu Azure Microsoft** z listy aplikacji.

Poniższe przykłady założono użycie jednej z tych dwóch metod można pobrać parametry połączenia magazynu.

## <a name="retrieve-your-connection-string"></a>Pobranie parametrów połączenia
Można użyć **cloud_storage_account** klasy do reprezentowania informacje o koncie magazynu. Aby uzyskać informacje o koncie magazynu z parametrów połączenia magazynu, można użyć **przeanalizować** metody.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Porady: Tworzenie kolejki
A **cloud_queue_client** obiektu pozwala pobierać obiekty odwołań do kolejki. Poniższy kod tworzy **cloud_queue_client** obiektu.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Użyj **cloud_queue_client** obiekt, aby pobrać odwołanie do kolejki, którego chcesz użyć. Można utworzyć kolejkę, jeśli nie istnieje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Porady: wstawianie komunikatu do kolejki
Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nową **cloud_queue_message**. Następnie wywołaj **add_message** metody. A **cloud_queue_message** można tworzyć przy użyciu dowolnego ciągu lub **bajtów** tablicy. Oto kod, który tworzy kolejkę (jeśli kolejka nie istnieje) i wstawia komunikat „Hello, World”:

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

## <a name="how-to-peek-at-the-next-message"></a>Porady: Podgląd kolejnego komunikatu
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując **peek_message** metody.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Porady: zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj zachowa również liczbę ponownych prób, a jeśli komunikat zostanie ponowiony więcej niż n razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

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

## <a name="how-to-de-queue-the-next-message"></a>Porady: usuwanie następnego komunikatu z kolejki
Twój kod usuwa komunikat z kolejki w dwóch etapach. Podczas wywoływania **get_message**, Pobierz następnej wiadomości w kolejce. Komunikat zwrócony z **get_message** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **delete_message**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Twój kod wywołuje **delete_message** natychmiast po przetworzeniu komunikatu.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Porady: wykorzystanie dodatkowych opcji do usuwania komunikatów z kolejek
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje **get_messages** metodę, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu **dla** pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy pamiętać, że 5 minut rozpoczyna się dla wszystkich wiadomości w tym samym czasie, więc po 5 minut przekazany od czasu wywołania **get_messages**, wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

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

## <a name="how-to-get-the-queue-length"></a>Porady: pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **Download_attributes** metody prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. **Approximate_message_count** metoda pobiera przybliżoną liczbę wiadomości w kolejce.

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

## <a name="how-to-delete-a-queue"></a>Porady: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **delete_queue_if_exists** metody dla obiekt kolejki.

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
Teraz, kiedy znasz już podstawy magazynu kolejek, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage.

* [Jak używać magazynu obiektów Blob w języku C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista zasobów magazynu Azure w języku C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Biblioteka klienta usługi Storage for C++ — dokumentacja](http://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)