---
title: Korzystanie z magazynu kolejki platformy Azure z pliku Node.js — Usługa Azure Storage
description: Dowiedz się, jak używać usługi Kolejka platformy Azure do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania wiadomości. Przykłady napisane w pliku Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060932"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Tworzenie i usuwanie kolejek z pliku Node.js za pomocą usługi kolejek platformy Azure
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Microsoft Azure Queue. Przykłady są zapisywane przy użyciu interfejsu API node.js. Scenariusze obejmują **wstawianie,** **podglądanie,** **uzyskiwanie**i **usuwanie** wiadomości kolejki, a także **tworzenie i usuwanie kolejek.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie aplikacji sieci Web Node.js w usłudze Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), Tworzenie i [wdrażanie aplikacji Node.js w usłudze Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) przy użyciu programu Windows PowerShell lub Visual Studio [Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurowanie aplikacji do uzyskiwania dostępu do magazynu
Aby korzystać z usługi Azure Storage, potrzebujesz zestawu SDK usługi Azure Storage dla node.js, który zawiera zestaw bibliotek wygody, które komunikują się z usługami REST magazynu.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Użyj Menedżera pakietów węzłów (NPM), aby uzyskać pakiet
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (Windows,) **Terminal** (Mac) lub **Bash** (Unix), przejdź do folderu, w którym utworzono przykładową aplikację.
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

3. Można ręcznie uruchomić polecenie **ls,** aby sprawdzić, czy folder **\_modułów węzłów** został utworzony. Wewnątrz tego folderu znajduje się pakiet **azure-storage** zawierający biblioteki wymagane do uzyskiwania dostępu do magazynu.

### <a name="import-the-package"></a>Importowanie pakietu
Za pomocą Notatnika lub innego edytora tekstu dodaj na górze plik **server.js** aplikacji, w którym zamierzasz używać magazynu:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurowanie połączenia usługi Azure Storage
Moduł platformy Azure odczyta zmienne\_środowiskowe\_\_KONTO\_USŁUGI AZURE\_\_\_STORAGE\_i KLUCZ DOSTĘPU USŁUGI AZURE STORAGE LUB CIĄG POŁĄCZENIA USŁUGI AZURE STORAGE, aby uzyskać informacje wymagane do połączenia się z kontem usługi Azure storage. Jeśli te zmienne środowiskowe nie są ustawione, należy określić informacje o koncie podczas wywoływania **createQueueService**.

## <a name="how-to-create-a-queue"></a>Jak: Tworzenie kolejki
Poniższy kod tworzy **Obiekt Usługi kolejki,** który umożliwia pracę z kolejkami.

```javascript
var queueSvc = azure.createQueueService();
```

Użyj **createQueueIfNotExists** metoda, która zwraca określoną kolejkę, jeśli już istnieje lub tworzy nową kolejkę o określonej nazwie, jeśli jeszcze nie istnieje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Jeśli kolejka jest `result.created` tworzona, jest true. Jeśli kolejka istnieje, `result.created` jest false.

### <a name="filters"></a>Filtry
Opcjonalne operacje filtrowania mogą być stosowane do operacji wykonywanych przy użyciu **usługi QueueService**. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itp. Filtry są obiektami, które implementują metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu jego wstępnego przetwarzania na opcje żądania, metoda musi wywołać "next" przekazywanie wywołania zwrotnego z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołaniu zwrotnym i po przetworzeniu returnObject (odpowiedź z żądania do serwera), wywołanie zwrotne musi albo wywołać dalej, jeśli istnieje, aby kontynuować przetwarzanie innych filtrów lub po prostu wywołać finalCallback w przeciwnym razie, aby zakończyć usługę Wywołania.

Dwa filtry, które implementują logikę ponawiania prób, wchodzą w skład zestawu Azure SDK dla platformy Node.js: **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Poniżej tworzy **obiekt QueueService,** który używa **filtra WykładnikPolicyPolicy:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak: Wstawianie wiadomości do kolejki
Aby wstawić wiadomość do kolejki, użyj metody **createMessage,** aby utworzyć nową wiadomość i dodać ją do kolejki.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Zajrzeć do następnej wiadomości
Można zajrzeć do wiadomości z przodu kolejki bez usuwania go z kolejki, wywołując **metodę peekMessages.** Domyślnie **zagląda do** pojedynczej wiadomości.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Zawiera `result` komunikat.

> [!NOTE]
> Za pomocą **peekMessages,** gdy nie ma żadnych komunikatów w kolejce nie zwróci błąd, jednak żadne komunikaty zostaną zwrócone.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Jak: Dequeue następnej wiadomości
Przetwarzanie wiadomości jest procesem dwuetapowym:

1. Odsuń wiadomość w kolejce.
2. Usuń wiadomość.

Aby odsunąć wiadomość w kolejce, użyj **programu getMessages**. To sprawia, że wiadomości niewidoczne w kolejce, więc żaden inny klient może je przetworzyć. Po przetworzyniu wiadomości przez aplikację należy wywołać **deleteMessage,** aby usunąć ją z kolejki. Poniższy przykład pobiera wiadomość, a następnie usuwa go:

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
> Domyślnie wiadomość jest ukryta tylko przez 30 sekund, po czym jest widoczna dla innych klientów. Można określić inną wartość `options.visibilityTimeout` za pomocą **getMessages**.
> 
> [!NOTE]
> Korzystanie **getMessages,** gdy nie ma żadnych komunikatów w kolejce nie zwróci błąd, jednak żadne komunikaty zostaną zwrócone.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Zmienianie zawartości wiadomości w kolejce
Zawartość wiadomości w miejscu w kolejce można zmienić za pomocą **updateMessage**. Poniższy przykład aktualizuje tekst wiadomości:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Jak: Dodatkowe opcje odkuwania wiadomości
Istnieją dwa sposoby dostosowywania pobierania wiadomości z kolejki:

* `options.numOfMessages`- Pobierz partię wiadomości (do 32.)
* `options.visibilityTimeout`- Ustaw dłuższy lub krótszy limit czasu niewidzialności.

W poniższym przykładzie użyto **getMessages** metody, aby uzyskać 15 wiadomości w jednym wywołaniu. Następnie przetwarza każdą wiadomość za pomocą for pętli. Ustawia również limit czasu niewidzialności do pięciu minut dla wszystkich wiadomości zwracanych przez tę metodę.

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

## <a name="how-to-get-the-queue-length"></a>Jak: Uzyskać długość kolejki
**GetQueueMetadata** zwraca metadane dotyczące kolejki, w tym przybliżoną liczbę wiadomości oczekujących w kolejce.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Jak: Lista kolejek
Aby pobrać listę kolejek, użyj **listyQueuesSegmented**. Aby pobrać listę filtrowane przez określony prefiks, użyj **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Jeśli nie można zwrócić `result.continuationToken` wszystkich kolejek, może służyć jako pierwszy parametr **listQueuesSegmented** lub drugi parametr **listQueuesSegmentedWithPrefix,** aby pobrać więcej wyników.

## <a name="how-to-delete-a-queue"></a>Jak: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie wiadomości w niej zawarte, wywołaj metodę **deleteQueue** w obiekcie kolejki.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Aby wyczyścić wszystkie wiadomości z kolejki bez usuwania jej, użyj **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Jak: Praca z podpisami dostępu współdzielonego
Sygnatury dostępu współdzielonego (SAS) to bezpieczny sposób zapewnienia szczegółowego dostępu do kolejek bez podawania nazwy konta magazynu lub kluczy. Sygnatury dostępu Współdzielonego są często używane do zapewnienia ograniczonego dostępu do kolejek, takich jak zezwolenie aplikacji mobilnej na przesyłanie wiadomości.

Zaufana aplikacja, taka jak usługa oparta na chmurze, generuje sygnaturę dostępu Współdzielonego przy użyciu **generateSharedAccessSignature** of the **QueueService**i udostępnia ją niezaufanej lub częściowo zaufanej aplikacji. Na przykład aplikacja mobilna. Sygnatura dostępu współdzielonego jest generowana przy użyciu zasad opisujących daty rozpoczęcia i zakończenia okresu, w którym ta sygnatura obowiązuje, a także poziom dostępu przyznany właścicielowi sygnatury dostępu współdzielonego.

Poniższy przykład generuje nową zasadę dostępu współdzielonego, która pozwoli posiadaczowi sygnatury dostępu Współdzielonego na dodawanie wiadomości do kolejki i wygaśnie 100 minut po jego utworzeniu.

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

Należy zauważyć, że informacje o hoście muszą być również dostarczone, ponieważ jest to wymagane, gdy posiadacz sygnatury dostępu współdzielonego próbuje uzyskać dostęp do kolejki.

Następnie aplikacja kliencka używa sygnatury dostępu Współdzielonego z **usługą KolejkaSAS** do wykonywania operacji względem kolejki. Poniższy przykład łączy się z kolejką i tworzy komunikat.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Ponieważ sygnatury dostępu współdzielonego został wygenerowany z dostępem do dodawania, jeśli podjęto próbę odczytu, aktualizacji lub usunięcia wiadomości, zostanie zwrócony błąd.

### <a name="access-control-lists"></a>Listy kontroli dostępu
Do ustawienia zasad dostępu powiązanych z sygnaturą dostępu współdzielonego można również użyć listy kontroli dostępu (ACL, Access Control List). Jest to przydatne, jeśli chcesz zezwolić wielu klientom na dostęp do kolejki, ale zapewniają różne zasady dostępu dla każdego klienta.

Lista ACL jest implementowana przy użyciu tablicy zasad dostępu z identyfikatorem skojarzonym z poszczególnymi zasadami. Poniższy przykład definiuje dwie zasady; jeden dla "user1" i jeden dla "user2":

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

W poniższym przykładzie pobiera bieżącą listy ACL dla **myqueue**, a następnie dodaje nowe zasady za pomocą **setQueueAcl**. W przypadku takiego podejścia:

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

Po ustawieniu listy ACL można następnie utworzyć sygnatury dostępu Współdzielonego na podstawie identyfikatora zasad. W poniższym przykładzie jest tworzona nowa sygnatura dostępu współdzielonego dla użytkownika „user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy magazynu kolejek, skorzystaj z tych łączy, aby dowiedzieć się więcej o bardziej złożonych zadaniach magazynowania.

* Odwiedź [Blog zespołu odpowiedzialnego za usługę Azure Storage][Azure Storage Team Blog].
* Odwiedź [zestaw SDK usługi Azure Storage dla repozytorium węzłów][Azure Storage SDK for Node] w usłudze GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Tworzenie aplikacji internetowej Node.js w usłudze Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Tworzenie i wdrażanie aplikacji Node.js do usługi w chmurze Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
