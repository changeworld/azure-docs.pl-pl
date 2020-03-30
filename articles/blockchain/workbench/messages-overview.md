---
title: Integracja wiadomości z platformą Azure Blockchain Workbench
description: Omówienie używania wiadomości do integracji usługi Azure Blockchain Workbench Preview z innymi systemami.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324506"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integracja wiadomości z programem Azure Blockchain Workbench

Oprócz zapewnienia interfejsu API REST, Azure Blockchain Workbench zapewnia również integrację opartą na wiadomościach. Workbench publikuje zdarzenia zorientowane na księgę za pośrednictwem usługi Azure Event Grid, umożliwiając konsumentom podrzędnym publikowanie danych lub podejmowanie działań na podstawie tych zdarzeń. Dla tych klientów, którzy wymagają niezawodnej obsługi wiadomości, Azure Blockchain Workbench dostarcza wiadomości do punktu końcowego usługi Azure Service Bus, jak również.

## <a name="input-apis"></a>Wejściowe interfejsy API

Jeśli chcesz inicjować transakcje z systemów zewnętrznych w celu tworzenia użytkowników, tworzenia kontraktów i aktualizowania kontraktów, można użyć interfejsów API wprowadzania obsługi wiadomości do wykonywania transakcji w księdze. Zobacz [przykłady integracji obsługi wiadomości](https://aka.ms/blockchain-workbench-integration-sample) dla przykładu, który demonstruje wejściowe interfejsy API.

Poniżej przedstawiono aktualnie dostępne wejściowe interfejsy API.

### <a name="create-user"></a>Tworzenie użytkownika

Tworzy nowego użytkownika.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                      |
|----------------------|------------------------------------------------------|
| Requestid            | Identyfikator GUID dostarczony przez klienta                                |
| firstName            | Imię użytkownika                              |
| lastName             | Nazwisko użytkownika                               |
| Emailaddress         | Adres e-mail użytkownika                           |
| identyfikator zewnętrzny           | Identyfikator obiektu usługi Azure AD użytkownika                      |
| Connectionid         | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion | Wersja schematu obsługi wiadomości                            |
| nazwa wiadomości          | **Prośba o utworzenie**                               |

Przykład:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**              | **Opis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Requestid             | Identyfikator GUID dostarczony przez klienta |
| userId                | Identyfikator użytkownika, który został utworzony |
| userChainIdentifier   | Adres użytkownika, który został utworzony w sieci blockchain. W Ethereum adres jest adresem użytkownika **w łańcuchu.** |
| Connectionid          | Unikatowy identyfikator połączenia łańcucha bloków|
| messageSchemaVersion  | Wersja schematu obsługi wiadomości |
| nazwa wiadomości           | **Utwórujużykupdy** |
| status                | Stan żądania utworzenia użytkownika.  Jeśli się powiedzie, wartość to **Sukces**. W przypadku awarii wartością jest **błąd**.     |
| dodatkoweInformaacja | Dodatkowe informacje dostarczone w oparciu o status |

Przykład udanego tworzenia odpowiedzi **użytkownika** z Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu są zawarte w dodatkowych informacjach.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Tworzenie kontraktu

Tworzy nową umowę.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Requestid            | Identyfikator GUID dostarczony przez klienta |
| userChainIdentifier  | Adres użytkownika, który został utworzony w sieci blockchain. W Ethereum ten adres jest adresem użytkownika na adres **łańcucha.** |
| applicationName      | Nazwa aplikacji |
| version              | Wersja aplikacji. Wymagane, jeśli masz włączone wiele wersji aplikacji. W przeciwnym razie wersja jest opcjonalna. Aby uzyskać więcej informacji na temat przechowywania wersji aplikacji, zobacz [Azure Blockchain Workbench versioning aplikacji](version-app.md). |
| nazwa przepływu pracy         | Nazwa przepływu pracy |
| parameters           | Dane wejściowe parametrów do tworzenia kontraktów |
| Connectionid         | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion | Wersja schematu obsługi wiadomości |
| nazwa wiadomości          | **Utwórz umowyrequest** |

Przykład:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**                 | **Opis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Requestid                | Identyfikator GUID dostarczony przez klienta                                                             |
| contractId (ida)               | Unikatowy identyfikator umowy wewnątrz workbench azure blockchain |
| contractLedgerIdentifier | Adres umowy w księdze                                            |
| Connectionid             | Unikatowy identyfikator połączenia łańcucha bloków                               |
| messageSchemaVersion     | Wersja schematu obsługi wiadomości                                                         |
| nazwa wiadomości              | **Utwórz kontraktUpdate**                                                      |
| status                   | Stan żądania utworzenia umowy.  Możliwe wartości: **Przesłane**, **Zatwierdzone**, **Niepowodzenie**.  |
| dodatkoweInformaacja    | Dodatkowe informacje dostarczone w oparciu o status                              |

Przykład przesłanej odpowiedzi **na umowę tworzenia** z blockchain workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Przykład zatwierdzonej odpowiedzi **na kontrakt z** blockchain workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu są zawarte w dodatkowych informacjach.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Tworzenie akcji kontraktu

Tworzy nową akcję kontraktu.

Żądanie wymaga następujących pól:

| **Nazwa**                 | **Opis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Requestid                | Identyfikator GUID dostarczony przez klienta |
| userChainIdentifier      | Adres użytkownika, który został utworzony w sieci blockchain. W Ethereum ten adres jest adresem użytkownika na adres **łańcucha.** |
| contractLedgerIdentifier | Adres umowy w księdze |
| version                  | Wersja aplikacji. Wymagane, jeśli masz włączone wiele wersji aplikacji. W przeciwnym razie wersja jest opcjonalna. Aby uzyskać więcej informacji na temat przechowywania wersji aplikacji, zobacz [Azure Blockchain Workbench versioning aplikacji](version-app.md). |
| nazwa funkcji pracy     | Nazwa funkcji przepływu pracy |
| parameters               | Dane wejściowe parametrów do tworzenia kontraktów |
| Connectionid             | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion     | Wersja schematu obsługi wiadomości |
| nazwa wiadomości              | **Utwórz UmowyszakcjaRequest** |

Przykład:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**              | **Opis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Requestid             | Identyfikator GUID dostarczony przez klienta|
| contractId (ida)            | Unikatowy identyfikator umowy wewnątrz workbench azure blockchain |
| Connectionid          | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion  | Wersja schematu obsługi wiadomości |
| nazwa wiadomości           | **Utwórz Umowyszczenie AkcjąUpdate** |
| status                | Stan żądania akcji umowy. Możliwe wartości: **Przesłane**, **Zatwierdzone**, **Niepowodzenie**.                         |
| dodatkoweInformaacja | Dodatkowe informacje dostarczone w oparciu o status |

Przykład przesłanej odpowiedzi na działanie związane z **utworzeniem umowy** z blockchain workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Przykład zatwierdzonej odpowiedzi na działanie związane z **utworzeniem umowy** z blockchain workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu są zawarte w dodatkowych informacjach.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Wejściowe kody błędów interfejsu API i komunikaty

**Kod błędu 4000: Nieprawidłowy błąd żądania**
- Nieprawidłowy identyfikator połączenia
- Nie powiodła się deserializacja createUserRequest
- Nie powiodła się deserializacja CreateContractRequest
- Nie powiodła się deserializacja CreateContractActionRequest
- Aplikacja {identyfikowana przez nazwę aplikacji} nie istnieje
- Aplikacja {identyfikowana przez nazwę aplikacji} nie ma przepływu pracy
- UserChainIdentifier nie istnieje
- Kontrakt {identyfikowany przez identyfikator księgi} nie istnieje
- Kontrakt {identyfikowany przez identyfikator księgi} nie ma funkcji {nazwa funkcji przepływu pracy}
- UserChainIdentifier nie istnieje

**Kod błędu 4090: Błąd powodujący konflikt**
- Użytkownik już istnieje
- Umowa już istnieje
- Działanie kontraktowe już istnieje

**Kod błędu 5000: Wewnętrzny błąd serwera**
- Komunikaty o wyjątkach

## <a name="event-notifications"></a>Powiadomienia o zdarzeniach

Powiadomienia o zdarzeniach mogą być używane do powiadamiania użytkowników i systemów niższego szczebla o zdarzeniach, które mają miejsce w Blockchain Workbench i sieci blockchain, z którą jest połączony. Powiadomienia o zdarzeniach mogą być używane bezpośrednio w kodzie lub używane z narzędziami, takimi jak logic apps i flow, aby wyzwolić przepływ danych do systemów podrzędnych.

Zobacz [odwołanie do wiadomości powiadomień, aby](#notification-message-reference) uzyskać szczegółowe informacje o różnych wiadomościach, które można odbierać.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Korzystanie ze zdarzeń w siatce zdarzeń za pomocą funkcji platformy Azure

Jeśli użytkownik chce użyć usługi Event Grid, aby otrzymywać powiadomienia o zdarzeniach, które mają miejsce w blockchain Workbench, można korzystać ze zdarzeń z usługi Event Grid przy użyciu usługi Azure Functions.

1. Utwórz **aplikację funkcji platformy Azure** w witrynie Azure portal.
2. Utwórz nową funkcję.
3. Znajdź szablon siatki zdarzeń. Wyświetlany jest podstawowy kod szablonu do odczytu wiadomości. W razie potrzeby zmodyfikuj kod.
4. Zapisz funkcję. 
5. Wybierz siatkę zdarzeń z grupy zasobów Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Korzystanie ze zdarzeń w siatce zdarzeń za pomocą aplikacji logiki

1. Utwórz nową **aplikację logiki platformy Azure** w witrynie Azure portal.
2. Podczas otwierania aplikacji logiki Azure w portalu zostanie wyświetlony monit o wybranie wyzwalacza. Wybierz **usługę Azure Event Grid — po wystąpieniu zdarzenia zasobu**.
3. Po wyświetleniu projektanta przepływu pracy zostanie wyświetlony monit o zalogowanie się.
4. Wybierz subskrypcję. jako **Microsoft.EventGrid.Topics**. Wybierz **nazwę zasobu** z nazwy zasobu z grupy zasobów Azure Blockchain Workbench.
5. Wybierz siatkę zdarzeń z grupy zasobów Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Używanie tematów usługi Service Bus do powiadomień

Tematy usługi Service Bus mogą służyć do powiadamiania użytkowników o zdarzeniach, które mają miejsce w blockchain workbench. 

1. Przejdź do usługi Service Bus w grupie zasobów workbench.
2. Wybierz **pozycję Tematy**.
3. Wybierz **temat ruchu wychodzącego**.
4. Utwórz nową subskrypcję tego tematu. Uzyskaj dla niego klucz.
5. Utwórz program, który subskrybuje zdarzenia z tej subskrypcji.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Korzystanie z komunikatów magistrali usług z aplikacjami logiki

1. Utwórz nową **aplikację logiki platformy Azure** w witrynie Azure portal.
2. Podczas otwierania aplikacji logiki Azure w portalu zostanie wyświetlony monit o wybranie wyzwalacza. Wpisz **usługę Service Bus** w polu wyszukiwania i wybierz wyzwalacz odpowiedni dla typu interakcji, które mają mieć z usługą Service Bus. Na przykład **usługa Service Bus — po odebraniu wiadomości w subskrypcji tematu (autouzupełnienie)**.
3. Gdy projektant przepływu pracy jest wyświetlany, określ informacje o połączeniu dla usługi Service Bus.
4. Wybierz subskrypcję i określ temat **warsztatu zewnętrznego**.
5. Opracowanie logiki dla aplikacji, która wykorzystuje komunikat z tego wyzwalacza.

## <a name="notification-message-reference"></a>Odwołanie do wiadomości powiadomienia

W zależności od **messageName**, wiadomości powiadomień mają jeden z następujących typów wiadomości.

### <a name="block-message"></a>Zablokuj komunikat

Zawiera informacje o poszczególnych blokach. *BlockMessage* zawiera sekcję z informacjami o poziomie bloku i sekcję z informacjami o transakcji.

| Nazwa | Opis |
|------|-------------|
| blokowanie | Zawiera [informacje o bloku](#block-information) |
| transakcje | Zawiera [informacje o transakcji](#transaction-information) zbierania dla bloku |
| Connectionid | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi wiadomości |
| nazwa wiadomości | **BlockMessage (BlokMessage)** |
| dodatkoweInformaacja | Dodatkowe informacje dostarczone |

#### <a name="block-information"></a>Informacje o blokach

| Nazwa              | Opis |
|-------------------|-------------|
| blockId (blokId)           | Unikatowy identyfikator bloku wewnątrz programu Azure Blockchain Workbench |
| blockNumber       | Unikatowy identyfikator bloku w księdze |
| blockHash (blokHash)         | Skrót bloku |
| poprzedniBlockHash | Skrót poprzedniego bloku |
| blockTimestamp    | Sygnatura czasowa bloku |

#### <a name="transaction-information"></a>Informacje o transakcji

| Nazwa               | Opis |
|--------------------|-------------|
| identyfikator transakcji      | Unikatowy identyfikator transakcji wewnątrz workbench azure blockchain |
| transactionHash (łań.)    | Skrót transakcji w księdze |
| Z               | Unikatowy identyfikator w księdze dla źródła transakcji |
| na                 | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |
| aprowizowanieStatus | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla transakcji. Możliwe wartości: </br>0 – Transakcja została utworzona przez INTERFEJS API w bazie danych</br>1 – Transakcja została wysłana do księgi</br>2 – Transakcja została pomyślnie zatwierdzona w księdze</br>3 lub 4 - Transakcja nie została zatwierdzona w księdze</br>5 - Transakcja została pomyślnie zatwierdzona w księdze |

Przykład *BlockMessage* z Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Komunikat o umowie

Zawiera informacje o umowie. Komunikat zawiera sekcję z właściwościami kontraktu i sekcję z informacjami o transakcji. Wszystkie transakcje, które zmodyfikowały kontrakt dla określonego bloku są uwzględnione w sekcji transakcji.

| Nazwa | Opis |
|------|-------------|
| blockId (blokId) | Unikatowy identyfikator bloku wewnątrz programu Azure Blockchain Workbench |
| blockHash (blokHash) | Skrót bloku |
| modyfikowanieTransakcje | [Transakcje, które zmodyfikowała](#modifying-transaction-information) umowę |
| contractId (ida) | Unikatowy identyfikator umowy wewnątrz workbench azure blockchain |
| contractLedgerIdentifier | Unikatowy identyfikator kontraktu w księdze |
| contractProperties | [Właściwości umowy](#contract-properties) |
| isNewContract | Wskazuje, czy ta umowa została nowo utworzona. Możliwe wartości to: prawda: ten kontrakt był nowym kontraktem stworzonym. false: ta umowa jest aktualizacją umowy. |
| Connectionid | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi wiadomości |
| nazwa wiadomości | **ContractMessage (KontraktMessage)** |
| dodatkoweInformaacja | Dodatkowe informacje dostarczone |

#### <a name="modifying-transaction-information"></a>Modyfikowanie informacji o transakcji

| Nazwa               | Opis |
|--------------------|-------------|
| identyfikator transakcji | Unikatowy identyfikator transakcji wewnątrz workbench azure blockchain |
| transactionHash (łań.) | Skrót transakcji w księdze |
| Z | Unikatowy identyfikator w księdze dla źródła transakcji |
| na | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |

#### <a name="contract-properties"></a>Właściwości kontraktu

| Nazwa               | Opis |
|--------------------|-------------|
| przepływ pracyPodwładnianie właściwości | Unikatowy identyfikator właściwości przepływu pracy w usłudze Azure Blockchain Workbench |
| name | Nazwa właściwości przepływu pracy |
| value | Wartość właściwości przepływu pracy |

Przykład *contractMessage* z Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Komunikat o zdarzeniu: wywołanie funkcji kontraktu

Zawiera informacje o wywoływanej funkcji kontraktu, takie jak nazwa funkcji, parametry wejściowe i wywołujący funkcję.

| Nazwa | Opis |
|------|-------------|
| Eventname                   | **ContractFunctionInvocation (Wywołanie kontraktu** |
| Obiekt wywołujący                      | [Informacje o wywołującym](#caller-information) |
| contractId (ida)                  | Unikatowy identyfikator umowy wewnątrz workbench azure blockchain |
| contractLedgerIdentifier    | Unikatowy identyfikator kontraktu w księdze |
| Functionname                | Nazwa funkcji |
| parameters                  | [Informacje o parametrach](#parameter-information) |
| Transakcji                 | Informacje o transakcji |
| inTransactionSequenceNumber | Numer sekwencyjny transakcji w bloku |
| Connectionid                | Unikatowy identyfikator połączenia |
| messageSchemaVersion        | Wersja schematu obsługi wiadomości |
| nazwa wiadomości                 | **EventMessage (EventMessage)** |
| dodatkoweInformaacja       | Dodatkowe informacje dostarczone |

#### <a name="caller-information"></a>Informacje o wywołującym

| Nazwa | Opis |
|------|-------------|
| type | Typ osoby dzwoniącej, takiej jak użytkownik lub umowa |
| id | Unikatowy identyfikator obiektu wywołującego w usłudze Azure Blockchain Workbench |
| Identyfikowalator księgi | Unikatowy identyfikator osoby dzwoniącej w księdze |

#### <a name="parameter-information"></a>Informacje o parametrach

| Nazwa | Opis |
|------|-------------|
| name | Nazwa parametru |
| value | Wartość parametru |

#### <a name="event-message-transaction-information"></a>Informacje o transakcji komunikatu zdarzenia

| Nazwa               | Opis |
|--------------------|-------------|
| identyfikator transakcji      | Unikatowy identyfikator transakcji wewnątrz workbench azure blockchain |
| transactionHash (łań.)    | Skrót transakcji w księdze |
| Z               | Unikatowy identyfikator w księdze dla źródła transakcji |
| na                 | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |

Przykład *wystąpienia funkcji umowy EventMessage z* pracy blockchain:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Komunikat o zdarzeniu: Połknienie aplikacji

Zawiera informacje o przekazaniu aplikacji do workbench, takie jak nazwa i wersja przekazanej aplikacji.

| Nazwa | Opis |
|------|-------------|
| Eventname | **Połknienie aplikacji** |
| applicationId | Unikatowy identyfikator aplikacji w usłudze Azure Blockchain Workbench |
| applicationName | Nazwa aplikacji |
| aplikacjaName funkcji | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| aplikacjaDefinitionLokacja | Adres URL, w którym znajduje się plik konfiguracji aplikacji |
| contractCodes | Zbieranie [kodów umów](#contract-code-information) dla wniosku |
| ZastosowanieRolki | Zbieranie [ról aplikacji](#application-role-information) dla aplikacji |
| applicationPracflows | Zbieranie [przepływów pracy aplikacji](#application-workflow-information) dla aplikacji |
| Connectionid | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi wiadomości |
| nazwa wiadomości | **EventMessage (EventMessage)** |
| dodatkoweInformaacja | Dodatkowe informacje podane w tym miejscu obejmują stany przepływu pracy aplikacji i informacje o przejściu. |

#### <a name="contract-code-information"></a>Informacje o kodzie umowy

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator pliku kodu kontraktu w usłudze Azure Blockchain Workbench |
| ledgerId (100- 1 | Unikatowy identyfikator księgi wewnątrz workbench azure blockchain |
| location | Adres URL, w którym znajduje się plik kodu kontraktu |

#### <a name="application-role-information"></a>Informacje o roli aplikacji

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator roli aplikacji wewnątrz workbench azure blockchain |
| name | Nazwa roli aplikacji |

#### <a name="application-workflow-information"></a>Informacje o przepływie pracy aplikacji

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator przepływu pracy aplikacji w usłudze Azure Blockchain Workbench |
| name | Nazwa przepływu pracy aplikacji |
| displayName | Nazwa wyświetlana przepływu pracy aplikacji |
|  — funkcje | Zbieranie [funkcji dla przepływu pracy aplikacji](#workflow-function-information)|
| Państw | Zbieranie [stanów dla przepływu pracy aplikacji](#workflow-state-information) |
| properties | [Informacje o właściwościach przepływu pracy](#workflow-property-information) aplikacji |

##### <a name="workflow-function-information"></a>Informacje o funkcji przepływu pracy

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator funkcji przepływu pracy aplikacji wewnątrz workbenchu Azure Blockchain |
| name | Nazwa funkcji |
| parameters | Parametry funkcji |

##### <a name="workflow-state-information"></a>Informacje o stanie przepływu pracy

| Nazwa | Opis |
|------|-------------|
| name | Nazwa państwa |
| displayName | Nazwa wyświetlana stanu |
|  — styl | Styl stanu (sukces lub porażka) |

##### <a name="workflow-property-information"></a>Informacje o właściwościach przepływu pracy

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator właściwości przepływu pracy aplikacji w usłudze Azure Blockchain Workbench |
| name | Nazwa właściwości |
| type | Typ właściwości |

Przykład *aplikacji EventMessageIngestion* z blockchain workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Komunikat o zdarzeniu: przypisanie roli

Zawiera informacje o przypisaniu roli użytkownika w workbench, takie jak kto wykonał przypisanie roli oraz nazwę roli i odpowiedniej aplikacji.

| Nazwa | Opis |
|------|-------------|
| Eventname | **Znak ról** |
| applicationId | Unikatowy identyfikator aplikacji w usłudze Azure Blockchain Workbench |
| applicationName | Nazwa aplikacji |
| aplikacjaName funkcji | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| Applicationrole        | Informacje o [roli aplikacji](#roleassignment-application-role) |
| zbywcy               | Informacje o [zbywcy](#roleassignment-assigner) |
| Cesjonariusza               | Informacje o [cesjonariuszu](#roleassignment-assignee) |
| Connectionid           | Unikatowy identyfikator połączenia |
| messageSchemaVersion   | Wersja schematu obsługi wiadomości |
| nazwa wiadomości            | **EventMessage (EventMessage)** |
| dodatkoweInformaacja  | Dodatkowe informacje dostarczone |

#### <a name="roleassignment-application-role"></a>Rola aplikacji RoleAssignment

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator roli aplikacji wewnątrz workbench azure blockchain |
| name | Nazwa roli aplikacji |

#### <a name="roleassignment-assigner"></a>Przypiszew przypisacza roli

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator użytkownika w usłudze Azure Blockchain Workbench |
| type | Typ zdydawcy |
| łańcuchIdentyfikator | Unikatowy identyfikator użytkownika w księdze |

#### <a name="roleassignment-assignee"></a>Cesjonariusz przypisania do przypisania roleAssignment

| Nazwa | Opis |
|------|-------------|
| id | Unikatowy identyfikator użytkownika w usłudze Azure Blockchain Workbench |
| type | Typ cesjonariusza |
| łańcuchIdentyfikator | Unikatowy identyfikator użytkownika w księdze |

Przykład *zdarzeniaMessage RoleAssignment* z Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Następne kroki

- [Wzorce integracji kontraktów inteligentnych](integration-patterns.md)
