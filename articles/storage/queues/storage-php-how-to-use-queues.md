---
title: Jak używać magazynu kolejek w języku PHP — Azure Storage
description: Dowiedz się, jak używać usługi Azure Queue storage do tworzenia i usuwania kolejki oraz wstawiania, pobieranie i usuwanie wiadomości. Przykłady są napisane w języku PHP.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: php
ms.topic: article
ms.date: 01/11/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 4ff54f9ca20c101de55bec2c7acf914c17bd7709
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951201"
---
# <a name="how-to-use-queue-storage-from-php"></a>Jak używać Magazynu kolejek w języku PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Przegląd
Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą usługi Azure Queue storage. Przykłady są napisane przy użyciu klas z [biblioteki klienta usługi Azure Storage dla języka PHP][download]. Pokryte scenariusze obejmują wstawiania, wgląd, pobierania i usuwaniem komunikatów kolejek, a także tworzenia i usuwania kolejek.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem do tworzenia aplikacji w języku PHP, który uzyskuje dostęp do usługi Azure Queue storage jest odwołanie do klasy w [biblioteki klienta usługi Azure Storage dla języka PHP] [ download] z w obrębie kodu. Do utworzenia aplikacji możesz użyć dowolnych narzędzi programistycznych, w tym programu Notatnik.

W tym przewodniku możesz użyć funkcji usługi kolejki magazynu, które mogą być wywoływane w ramach aplikacji PHP lokalnie lub w kodzie, w ramach roli sieci web platformy Azure, rola procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienckie systemu Azure
### <a name="install-via-composer"></a>Instalowanie za pomocą Composer (kompozytor)
1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu i dodać do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Pobierz **[composer.phar] [ composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i uruchom następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

Ewentualnie przejdź do [biblioteki klienta usługi Azure Storage PHP] [ download] w serwisie GitHub, klonowanie kodu źródłowego.

## <a name="configure-your-application-to-access-queue-storage"></a>Umożliwia skonfigurowanie aplikacji dostęp do usługi Queue storage
Aby użyć interfejsów API dla usługi Azure Queue storage, należy:

1. Odwołanie do pliku automatycznej ładowarki przy użyciu [require_once] instrukcji.
2. Odwoływać się do wszystkich klas, których można korzystać.

Poniższy przykład pokazuje, jak to plik automatycznej ładowarki i odwołania **QueueRestProxy** klasy.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

W poniższych przykładach `require_once` instrukcji jest wyświetlane zawsze, ale są wywoływane tylko klasy, które są niezbędne, na przykład do wykonania.

## <a name="set-up-an-azure-storage-connection"></a>Skonfigurować połączenie usługi Azure storage
Aby utworzyć wystąpienia klienta usługi Azure Queue storage, najpierw musisz mieć prawidłowe parametry połączenia. Format parametrów połączenia usługi kolejki jest w następujący sposób.

Aby uzyskać dostęp do usługi na żywo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Aby uzyskać dostęp do magazynu emulatora:

```php
UseDevelopmentStorage=true
```

Aby utworzyć klienta usługi Azure Queue, należy użyć **QueueRestProxy** klasy. Można użyć jednej z następujących technik:

* Przekaż parametry połączenia do niego bezpośrednio.
* Używać zmiennych środowiskowych w aplikacji sieci Web, aby przechowywać parametry połączenia. Zobacz [ustawień konfiguracji aplikacji sieci web platformy Azure](../../app-service/configure-common.md) dokumentu do konfigurowania parametrów połączenia.
W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
A **QueueRestProxy** obiekt umożliwia tworzenie kolejki za pomocą **createQueue** metody. Podczas tworzenia kolejki, można ustawić opcje w kolejce, ale spowoduje tak nie jest wymagana. (Poniższy przykład pokazuje, jak ustawić metadanych w kolejce).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Nie należy polegać na rozróżnianie wielkości liter dla kluczy metadanych. Wszystkie klucze są odczytywane z usługi małymi literami.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki
Aby dodać komunikat do kolejki, użyj **QueueRestProxy -> createMessage**. Ta metoda przyjmuje nazwę kolejki, tekst komunikatu i opcje wiadomości, (które są opcjonalne).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu
Możesz wgląd w komunikat (lub wiadomości) z przodu kolejki bez jego usuwania z kolejki, wywołując **QueueRestProxy -> peekMessages**. Domyślnie **peekMessage** metoda zwraca pojedynczą wiadomość, ale można zmienić tę wartość za pomocą **PeekMessagesOptions -> setNumberOfMessages** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Usunięcie następnego komunikatu z kolejki
Twój kod usuwa komunikat z kolejki w dwóch etapach. Po pierwsze wywołanie **QueueRestProxy -> listMessages**, co sprawia, że komunikat niewidoczny dla innego kodu, która odczytuje z kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. (Jeśli komunikat nie zostanie usunięty w tym okresie, staje się widoczny w kolejce ponownie.) Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać **QueueRestProxy -> deleteMessage**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **deleteMessage** natychmiast po przetworzeniu komunikatu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w miejscu w kolejce przez wywołanie metody **QueueRestProxy -> updateMessage**. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i Ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Zapisuje stan pracy, który jest skojarzony z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy w komunikacie. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Dodatkowe opcje usuwania komunikatów z kolejek
Istnieją dwa sposoby, które można dostosować odebrania komunikatu z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić widoczności dłuższy lub krótszy limit czasu, dzięki czemu kod więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje **getMessages** metodę, aby uzyskać 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu **dla** pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **QueueRestProxy -> getQueueMetadata** metoda prosi usługę kolejki do zwracania metadanych dotyczących kolejki. Wywoływanie **getApproximateMessageCount** metody zwrócony obiekt zawiera liczbę liczbę komunikatów w kolejce. Wartość licznika jest przybliżona tylko w przypadku, ponieważ komunikaty mogą dodany lub usunięty, gdy usługa kolejki odpowiada na żądania.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie komunikaty w nim, wywołaj **QueueRestProxy -> deleteQueue** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi Azure Queue storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu:

* Odwiedź stronę [wykaz interfejsów API dla biblioteki klienta PHP w usłudze Azure Storage](https://azure.github.io/azure-storage-php/)
* Zobacz [przykład zaawansowane kolejki](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar

