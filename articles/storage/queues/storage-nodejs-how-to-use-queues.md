---
title: Jak używać magazynu kolejek w języku Node.js — Azure Storage
description: Dowiedz się, jak używać usługi kolejek platformy Azure do tworzenia i usuwania kolejki oraz wstawiania, pobieranie i usuwanie wiadomości. Przykłady napisany w języku Node.js.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 01afe1ab7b9028f3f77d52f7d6f8ced27f6a79c7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142708"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Jak używać Magazynu kolejek w oprogramowaniu Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Omówienie
Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą usługi Microsoft Azure Queue. Przykłady są napisane przy użyciu interfejsu API środowiska Node.js. Omówione scenariusze obejmują **wstawianie**, **wgląd do**, **wprowadzenie**, i **usuwanie** kolejki komunikatów, a także  **Tworzenie i usuwanie kolejek**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js
Tworzenie pustej aplikacji Node.js. Aby uzyskać instrukcje tworzenia aplikacji w technologii Node.js, zobacz [tworzenie aplikacji sieci web Node.js w usłudze Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [kompilowanie i wdrażanie aplikacji Node.js w usłudze w chmurze Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) przy użyciu programu Windows PowerShell lub [ Program Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Umożliwia skonfigurowanie aplikacji dostęp do magazynu
Aby użyć usługi Azure storage, konieczne jest zestaw SDK usługi Azure Storage dla środowiska Node.js, która zawiera zbiór bibliotek wygody, które komunikują się z usług REST magazynu.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Uzyskiwanie pakietu przy użyciu Node Package Manager (NPM)
1. Korzystanie z interfejsu wiersza polecenia takich jak **PowerShell** (Windows), **terminalu** (Mac), lub **Bash** (Unix), przejdź do folderu, w której utworzono aplikację przykładową.
2. Wpisz ciąg **npm install azure-storage** w oknie polecenia. Dane wyjściowe polecenia są podobne do poniższego przykładu.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Można ręcznie uruchomić **ls** polecenie, aby sprawdzić, czy **węzła\_modułów** folder został utworzony. Wewnątrz tego folderu znajduje się pakiet **azure-storage** zawierający biblioteki wymagane do uzyskiwania dostępu do magazynu.

### <a name="import-the-package"></a>Importowanie pakietu
Za pomocą Notatnika lub innego edytora tekstu, Dodaj następujący kod do górnej **server.js** pliku aplikacji, których zamierzasz używać magazynu:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Skonfigurować połączenie usługi Azure Storage
Moduł azure odczyta zmiennych środowiskowych platformy AZURE\_MAGAZYNU\_konta i AZURE\_MAGAZYNU\_dostępu\_klucza lub AZURE\_MAGAZYNU\_połączenia\_ CIĄG, aby uzyskać informacje wymagane do połączenia z kontem usługi Azure storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie podczas wywoływania **createQueueService**.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki
Poniższy kod tworzy **QueueService** obiektu, który umożliwia pracę z kolejkami.

```javascript
var queueSvc = azure.createQueueService();
```

Użyj **createQueueIfNotExists** metody, która zwraca określoną kolejkę, jeśli już istnieje, lub tworzy nową kolejkę o określonej nazwie, jeśli jeszcze nie istnieje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Jeśli kolejka została utworzona, `result.created` ma wartość true. Jeśli istnieje kolejka, `result.created` ma wartość false.

### <a name="filters"></a>Filtry
Opcjonalne filtrowania operacje mogą być stosowane do operacji wykonywanych przy użyciu **QueueService**. Filtrowanie operacji mogą obejmować rejestrowanie automatyczne ponawianie, itp. Filtry to obiekty, które implementują metodę o następującej sygnaturze:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu tej przetwarzania wstępnego na temat opcji żądania, metoda musi wywołać "dalej" przekazywanie wywołanie zwrotne z następującą sygnaturą:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołaniu zwrotnym i po przetworzeniu returnObject (odpowiedź z żądania do serwera) wywołanie zwrotne musi wywołać następne istnienia kontynuować przetwarzanie inne filtry lub po prostu wywołać finalCallback w przeciwnym razie skończyć z usługi wywołania.

Dwa filtry, które implementują logikę ponawiania prób, wchodzą w skład zestawu Azure SDK dla platformy Node.js: **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Tworzy następujące **QueueService** obiektu, który używa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki
Aby wstawić komunikat do kolejki, należy użyć **createMessage** metodę, aby utworzyć nową wiadomość i dodać go do kolejki.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: Podgląd kolejnego komunikatu
Użytkownik może wglądu do wiadomości uzyskać kolejki bez jego usuwania z kolejki, wywołując **peekMessages** metody. Domyślnie **peekMessages** wglądu w pojedynczym komunikacie.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` Zawiera komunikat.

> [!NOTE]
> Za pomocą **peekMessages** gdy nie ma żadnych komunikatów w kolejce nie zwróci błąd, jednak zostaną zwrócone żadne komunikaty.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: Usuń następny komunikat z kolejki
Przetwarza komunikat jest procesem dwuetapowym:

1. Usuń komunikat z kolejki.
2. Usuń komunikat.

Komunikatu z kolejki, użyj **getMessages**. To sprawia, że komunikaty niewidoczne w kolejce, dzięki czemu można je przetworzyć żadnych innych klientów. Po przetworzeniu komunikatu aplikacji wywołać **deleteMessage** usunąć go z kolejki. Poniższy przykład pobiera komunikat, a następnie usuwa je:

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Domyślnie wiadomość została ukryta 30 sekund, po których jest widoczny dla innych klientów. Można określić inną wartość przy użyciu `options.visibilityTimeout` z **getMessages**.
> 
> [!NOTE]
> Za pomocą **getMessages** gdy nie ma żadnych komunikatów w kolejce nie zwróci błąd, jednak zostaną zwrócone żadne komunikaty.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: Zmień zawartość komunikatu w kolejce
Możesz zmienić zawartość komunikatu w miejscu w kolejce przy użyciu **updateMessage**. Poniższy przykład aktualizuje tekst wiadomości:

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instrukcje: Dodatkowe opcje usuwania z kolejki komunikatów
Istnieją dwa sposoby, które można dostosować odebrania komunikatu z kolejki:

* `options.numOfMessages` -Pobrać partię komunikatów (maksymalnie 32).
* `options.visibilityTimeout` -Ustawienia limitu czasu niewidoczności dłuższy lub krótszy.

W poniższym przykładzie użyto **getMessages** metodę, aby uzyskać 15 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limitu czasu niewidoczności na pięć minut dla wszystkich komunikatów zwracanego przez tę metodę.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: Pobieranie długości kolejki
**GetQueueMetadata** zwraca metadane dotyczące kolejki, łącznie z przybliżona liczba komunikatów oczekujących w kolejce.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Instrukcje: Wyświetl listę kolejek
Aby pobrać listę kolejek, użyj **listQueuesSegmented**. Aby pobrać listy filtrowane według określonego prefiksu, użyj **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Jeśli nie można zwrócić wszystkich kolejkach, `result.continuationToken` mogą być używane jako pierwszy parametr **listQueuesSegmented** lub drugi parametr **listQueuesSegmentedWithPrefix** można pobrać więcej wyników.

## <a name="how-to-delete-a-queue"></a>Instrukcje: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **deleteQueue** metody na obiekcie kolejki.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Aby wyczyścić wszystkie komunikaty z kolejki bez jego usuwania, należy użyć **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Instrukcje: Praca z sygnaturami dostępu współdzielonego
Udostępnione sygnatur dostępu (SAS) to bezpieczny sposób zapewnienia szczegółowej dostępu do kolejek bez podawania swojej nazwy konta magazynu lub klucze. Sygnatury dostępu Współdzielonego są często używane do udzielany ograniczony dostęp do kolejek, na przykład zezwalając aplikacji mobilnej w celu przesyłania wiadomości.

Generuje zaufanych aplikacji, takich jak usługi oparte na chmurze, przy użyciu sygnatury dostępu Współdzielonego **generateSharedAccessSignature** z **QueueService**i przekazuje go do niezaufanych lub na wpół zaufanych aplikacji. Na przykład, aplikacja mobilna. Sygnatura dostępu współdzielonego jest generowana przy użyciu zasad opisujących daty rozpoczęcia i zakończenia okresu, w którym ta sygnatura obowiązuje, a także poziom dostępu przyznany właścicielowi sygnatury dostępu współdzielonego.

Poniższy przykład generuje nowej zasady dostępu współdzielonego, która umożliwi posiadacza sygnatury dostępu Współdzielonego do dodawania komunikatów do kolejki i wygasa 100 minut od czasu, w którym zostały utworzone.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Należy pamiętać, że informacji o hoście należy podać również, jak jest to wymagane, gdy właściciel sygnatury dostępu Współdzielonego podejmuje próbę dostępu do kolejki.

Następnie aplikacja kliencka korzysta z sygnatury dostępu Współdzielonego za pomocą **QueueServiceWithSAS** wykonywania operacji dotyczących kolejki. Poniższy przykład łączy do kolejki i tworzy komunikat.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Ponieważ sygnatury dostępu Współdzielonego zostały wygenerowane z Dodawanie dostępu, jeśli zostanie podjęta próba zostały wprowadzone do odczytywania, aktualizowania lub usuwania wiadomości, czy zwracany błąd.

### <a name="access-control-lists"></a>Listy kontroli dostępu
Do ustawienia zasad dostępu powiązanych z sygnaturą dostępu współdzielonego można również użyć listy kontroli dostępu (ACL, Access Control List). Jest to przydatne, jeśli chcesz umożliwić wielu klientom dostęp do kolejki, ale zapewnia różne zasady dostępu dla każdego klienta.

Lista ACL jest implementowana przy użyciu tablicy zasad dostępu z identyfikatorem skojarzonym z poszczególnymi zasadami. W poniższym przykładzie zdefiniowano dwie zasady; jeden dla "Użytkownik1" i jeden dla "Użytkownik2":

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Poniższy przykład pobiera bieżące listy ACL dla **myqueue**, następnie dodaje nowe zasady przy użyciu **setQueueAcl**. W przypadku takiego podejścia:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Po ustawieniu listy ACL następnie można utworzyć na podstawie Identyfikatora zasad sygnatury dostępu Współdzielonego. W poniższym przykładzie jest tworzona nowa sygnatura dostępu współdzielonego dla użytkownika „user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi queue storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Odwiedź stronę [Blog zespołu usługi Azure Storage][Azure Storage Team Blog].
* Odwiedź stronę [zestawu SDK usługi Azure Storage dla węzła] [ Azure Storage SDK for Node] repozytorium w witrynie GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Tworzenie aplikacji sieci web Node.js w usłudze Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Tworzenie i wdrażanie aplikacji Node.js w usłudze w chmurze platformy Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
