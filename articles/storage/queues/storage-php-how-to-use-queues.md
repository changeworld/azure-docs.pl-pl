---
title: Jak korzystać z magazynu kolejek z PHP — Usługa Azure Storage
description: Dowiedz się, jak używać usługi Azure Queue storage do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania wiadomości. Próbki są zapisywane w PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302969"
---
# <a name="how-to-use-queue-storage-from-php"></a>Jak używać Magazynu kolejek w języku PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Azure Queue storage. Przykłady są zapisywane za pośrednictwem klas z [biblioteki klienta usługi Azure Storage dla PHP][download]. Omówione scenariusze obejmują wstawianie, wgląd, uzyskiwanie i usuwanie wiadomości kolejki, a także tworzenie i usuwanie kolejek.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP

Jedynym wymaganiem tworzenia aplikacji PHP, która uzyskuje dostęp do magazynu kolejki platformy Azure, jest odwoływanie się do klas w [bibliotece klienta usługi Azure Storage dla PHP][download] z poziomu kodu. Do utworzenia aplikacji możesz użyć dowolnych narzędzi programistycznych, w tym programu Notatnik.

W tym przewodniku należy użyć funkcji usługi magazynu kolejek, które mogą być wywoływane w aplikacji PHP lokalnie lub w kodzie uruchomionym w aplikacji sieci web na platformie Azure.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klientów platformy Azure

### <a name="install-via-composer"></a>Zainstaluj za pośrednictwem composer

1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu i dodaj do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Pobierz **[composer.phar][composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i wykonaj następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

Alternatywnie przejdź do [biblioteki klienta PHP usługi Azure Storage][download] w usłudze GitHub, aby sklonować kod źródłowy.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu kolejek

Aby korzystać z interfejsów API dla magazynu kolejki platformy Azure, należy:

1. Odwołanie się do pliku autoloader przy użyciu [require_once] instrukcji.
2. Odwoływać się do wszystkich klas, które można użyć.

W poniższym przykładzie pokazano, jak dołączyć plik autoloader i odwołać się do **queuerestproxy** klasy.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

W poniższych przykładach `require_once` instrukcja jest zawsze wyświetlana, ale odwołuje się tylko do klas, które są niezbędne dla przykładu do wykonania.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurowanie połączenia magazynu platformy Azure

Aby utworzyć wystąpienie klienta magazynu kolejki platformy Azure, musisz najpierw mieć prawidłowy parametry połączenia. Format ciągu połączenia usługi kolejki jest następujący.

Aby uzyskać dostęp do usługi na żywo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Aby uzyskać dostęp do magazynu emulatora:

```php
UseDevelopmentStorage=true
```

Aby utworzyć klienta usługi Azure Queue, należy użyć **QueueRestProxy** klasy. Można użyć jednej z następujących technik:

* Przekaż ciąg połączenia bezpośrednio do niego.
* Użyj zmiennych środowiskowych w aplikacji sieci Web do przechowywania ciągu połączenia. Zobacz dokument [ustawień konfiguracji aplikacji sieci Web platformy Azure](../../app-service/configure-common.md) do konfigurowania ciągów połączeń.
W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Obiekt **QueueRestProxy** umożliwia utworzenie kolejki przy użyciu metody **createQueue.** Podczas tworzenia kolejki można ustawić opcje w kolejce, ale nie jest to wymagane. (Poniższy przykład pokazuje, jak ustawić metadane w kolejce).

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
> Nie należy polegać na wielkość liter dla kluczy metadanych. Wszystkie klawisze są odczytywane z usługi w małych literach.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Dodawanie wiadomości do kolejki

Aby dodać wiadomość do kolejki, użyj **funkcji QueueRestProxy->createMessage**. Metoda przyjmuje nazwę kolejki, tekst wiadomości i opcje wiadomości (które są opcjonalne).

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

Możesz zajrzeć do wiadomości (lub wiadomości) z przodu kolejki bez usuwania go z kolejki, wywołując **QueueRestProxy->zagląda.** Domyślnie metoda **peekMessage** zwraca pojedynczą wiadomość, ale można zmienić tę wartość za pomocą **metody PeekMessagesOptions->setNumberOfMessages.**

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

Kod usuwa wiadomość z kolejki w dwóch krokach. Najpierw wywołać **QueueRestProxy->listMessages**, co sprawia, że wiadomość niewidoczna dla innego kodu, który jest odczytywanie z kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. (Jeśli wiadomość nie zostanie usunięta w tym okresie, staje się ponownie widoczna w kolejce). Aby zakończyć usuwanie wiadomości z kolejki, należy wywołać **QueueRestProxy->deleteMessage**. Ten dwuetapowy proces usuwania wiadomości zapewnia, że gdy kod nie może przetworzyć wiadomości z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Wywołania kodu **deleteMessage** zaraz po przetworzyniu wiadomości.

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

Zawartość wiadomości w miejscu w kolejce można zmienić, wywołując **system QueueRestProxy->updateMessage**. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia limit czasu widoczności, aby przedłużyć kolejne 60 sekund. Pozwala to zaoszczędzić stan pracy, który jest skojarzony z wiadomością i daje klientowi kolejną minutę, aby kontynuować pracę nad wiadomością. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

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

## <a name="additional-options-for-de-queuing-messages"></a>Dodatkowe opcje usuwania kolejkowania wiadomości

Istnieją dwa sposoby dostosowywania pobierania wiadomości z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu widoczności, dzięki czemu kod mniej lub bardziej czas, aby w pełni przetworzyć każdą wiadomość. Poniższy przykład kodu używa **getMessages** metody, aby uzyskać 16 wiadomości w jednym wywołaniu. Następnie przetwarza każdą wiadomość za pomocą **for** pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

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

## <a name="get-queue-length"></a>Uzyskaj długość kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **Metoda >getQueueMetadata** w kolejce prosi usługę kolejki o zwrócenie metadanych dotyczących kolejki. Wywołanie **getApproximateMessageCount** metody na zwrócony obiekt zapewnia liczbę, ile wiadomości są w kolejce. Liczba jest przybliżona, ponieważ wiadomości można dodać lub usunąć po usługa kolejki odpowiada na żądanie.

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

Aby usunąć kolejkę i wszystkie wiadomości w niej, wywołaj **metodę QueueRestProxy->deleteQueue.**

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

## <a name="next-steps"></a>Następne kroki

Teraz, gdy poznasz podstawy usługi Azure Queue storage, skorzystaj z tych łączy, aby dowiedzieć się więcej o bardziej złożonych zadaniach magazynu:

* Odwiedź dokumentację [interfejsu API dla biblioteki klienta PHP usługi Azure Storage](https://azure.github.io/azure-storage-php/)
* Zobacz [przykład kolejki zaawansowanej](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Aby uzyskać więcej informacji, zobacz także [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
