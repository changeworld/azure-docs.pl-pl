---
title: Jak używać magazynu kolejek w języku PHP | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z usługi magazyn kolejek platformy Azure do tworzenia i usuwania kolejki, wstawianie, Pobierz i usunąć wiadomości. Przykłady są zapisywane w kodzie PHP.
documentationcenter: php
services: storage
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.openlocfilehash: ec0c6e22919e84bbaed284e16211ef52e526991d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-use-queue-storage-from-php"></a>Jak używać Magazynu kolejek w języku PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi magazynu kolejek Azure. Przykłady są napisane przy użyciu klasy z [biblioteki klienta magazynu Azure dla programu PHP][download]. Objęte usługą scenariusze obejmują, wstawianie, wgląd, pobieranie i usuwanie komunikatów kolejek, a także tworzenie i usuwanie kolejek.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji PHP
Jedynym wymaganiem w przypadku tworzenia aplikacji PHP, który uzyskuje dostęp do magazynu kolejek Azure jest odwoływanie się do klasy w [biblioteki klienta magazynu Azure dla programu PHP] [ download] od w kodzie. Narzędzia do programowania służy do tworzenia aplikacji, łącznie z Notatnika.

W tym przewodniku korzystanie z funkcji usługi kolejki magazynu, które można wywołać w ramach aplikacji PHP lokalnie lub w kodzie działających w roli sieci web platformy Azure, roli procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz bibliotek klienta platformy Azure
### <a name="install-via-composer"></a>Zainstaluj za pośrednictwem Composer
1. Utwórz plik o nazwie **composer.json** w folderze głównym projektu i Dodaj do niej następujący kod:
   
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

Można również przejdź do [biblioteki klienta usługi Azure Storage PHP] [ download] w serwisie GitHub klonowanie kodu źródłowego.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurowanie aplikacji dostęp do kolejki magazynu
Aby korzystanie z interfejsów API magazynu kolejek Azure, musisz:

1. Odwołanie do pliku automatycznej ładowarki przy użyciu [require_once] instrukcji.
2. Odwoływać się do wszystkich klas, których można użyć.

Poniższy przykład pokazuje, jak dołączyć plik automatycznej ładowarki i odwołanie **QueueRestProxy** klasy.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

W poniższych przykładach `require_once` instrukcji jest zawsze wyświetlany, ale odwołuje się tylko klasy, które są niezbędne, na przykład do wykonania.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurowanie połączenia z magazynem Azure
Można utworzyć wystąpienia klienta magazynu kolejek Azure, najpierw musi mieć prawidłowe parametry połączenia. Format dla parametrów połączenia usługi kolejki ma następującą składnię.

Aby uzyskać dostęp do usługi na żywo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Aby uzyskać dostęp do emulatora magazynu:

```php
UseDevelopmentStorage=true
```

Aby utworzyć klienta usługi kolejek platformy Azure, musisz użyć **QueueRestProxy** klasy. Można użyć jednej z następujących metod:

* Parametry połączenia należy przekazać bezpośrednio do niego.
* W aplikacji sieci Web można używać zmiennych środowiskowych, aby zapisać parametry połączenia. Zobacz [ustawienia konfiguracji aplikacji sieci web platformy Azure](../../app-service/web-sites-configure.md) dokumentu do konfigurowania parametrów połączenia.
Przykłady przedstawione w tym miejscu ciąg połączenia jest przekazywany bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
A **QueueRestProxy** obiekt umożliwia tworzenie kolejki przy użyciu **createQueue** metody. Podczas tworzenia kolejki, można ustawić opcje w kolejce, ale spowoduje to nie jest wymagane. (W poniższym przykładzie pokazano, jak ustawić metadanych w kolejce).

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Nie należy polegać na uwzględnianie wielkości liter w metadanych kluczy. Wszystkie klucze są odczytywane z usługi pisane małymi literami.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Dodaj komunikat do kolejki
Aby dodać wiadomości do kolejki, użyj **QueueRestProxy -> polecenie createMessage**. Metoda ma nazwę kolejki, tekst komunikatu i opcje wiadomości, (które są opcjonalne).

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
    $builder = new ServicesBuilder();
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu
Możesz uzyskać wgląd w komunikat (lub wiadomości) z przodu kolejki bez jego usuwania z kolejki, wywołując **QueueRestProxy -> peekMessages**. Domyślnie **peekMessage** metoda zwraca pojedynczą wiadomość, ale można zmienić tę wartość przy użyciu **PeekMessagesOptions -> setNumberOfMessages** metody.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
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
Twój kod usuwa komunikat z kolejki w dwóch etapach. Najpierw należy wywołać **QueueRestProxy -> listMessages**, która sprawia, że komunikat niewidoczne dla innego kodu, który jest czytania z kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. (Jeśli wiadomość nie jest usuwany w tym okresie, staje się widoczny w kolejce ponownie.) Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać **QueueRestProxy -> deleteMessage**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że gdy kodu nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie. Twój kod wywołuje **deleteMessage** natychmiast po przetworzeniu komunikatu.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce
Można zmienić zawartość komunikatu w miejscu w kolejce przez wywołanie metody **QueueRestProxy -> updateMessage**. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i Ustawia rozszerzenie limitu czasu widoczności kolejne 60 sekund. Zapisuje stan pracy, który został skojarzony z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad wiadomości. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Dodatkowe opcje usuwania komunikatów z kolejek
Istnieją dwa sposoby, który można dostosować pobieranie wiadomości z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić widoczności dłuższy lub krótszy limit czasu, dzięki czemu kod będzie bardziej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje **getMessages** metodę, aby pobrać 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu **dla** pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **QueueRestProxy -> getQueueMetadata** metody prosi usługę kolejki do zwracania metadanych dotyczących kolejki. Wywoływanie **getApproximateMessageCount** metody na zwracanym obiekcie zawiera liczbę liczbę wiadomości w kolejce. Wartość licznika jest tylko przybliżoną, ponieważ komunikaty mogą dodane lub usunięte po usługa kolejki odpowiada na żądania.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie znajdujące się w niej wiadomości, wywołaj **QueueRestProxy -> deleteQueue** metody.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy magazynu kolejek Azure, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu:

* Odwiedź stronę [dokumentacja interfejsu API dla biblioteki klienta magazynu Azure PHP](http://azure.github.io/azure-storage-php/)
* Zobacz [przykład zaawansowane kolejki](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: http://getcomposer.org/composer.phar

