---
title: Omówienie integracji wiadomości w usłudze Azure Blockchain Workbench
description: Omówienie używania wiadomości w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 49b2bdd1780caa4ae04efbc979e2ea33e2c13c4c
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147233"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Aplikacji Azure Blockchain Workbench komunikatów integracji

Oprócz interfejsu API REST, Azure Blockchain Workbench także bazujące na komunikatach integracji. Środowisko robocze publikuje księgi skoncentrowane na zdarzenia za pośrednictwem usługi Azure Event Grid, umożliwiając użytkownikom podrzędnego pozyskiwania danych lub podjąć działania na podstawie tych zdarzeń. Dla tych klientów, które wymagają niezawodną obsługę komunikatów aplikacji Azure Blockchain Workbench dostarcza komunikaty do punktu końcowego usługi Azure Service Bus.

## <a name="input-apis"></a>Interfejsy API danych wejściowych

Jeśli chcesz zainicjować transakcje z systemami zewnętrznymi, aby utworzyć użytkowników, Tworzenie umów i aktualizowanie umów służy komunikatów wejściowych interfejsów API do wykonania transakcji na księgowe. Zobacz [przykłady integracji komunikatów](https://aka.ms/blockchain-workbench-integration-sample) przykład demonstrujący wejściowych interfejsów API.

Poniżej przedstawiono aktualnie dostępne interfejsy API danych wejściowych.

### <a name="create-user"></a>Tworzenie użytkownika

Tworzy nowego użytkownika.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Klient podany identyfikator GUID                                |
| firstName            | Imię użytkownika                              |
| lastName             | Nazwisko użytkownika                               |
| emailAddress         | Adres e-mail użytkownika                           |
| externalId           | Usługa Azure AD identyfikator obiektu użytkownika                      |
| connectionId         | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion | Wersja schematu komunikatów                            |
| messageName          | **CreateUserRequest**                               |

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

Blockchain Workbench zwraca odpowiedź z następujących pól:

| **Nazwa**              | **Opis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Klient podany identyfikator GUID |
| userId                | Identyfikator użytkownika, który został utworzony |
| userChainIdentifier   | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, znajduje się adres użytkownika **w łańcuchu** adresu. |
| connectionId          | Unikatowy identyfikator połączenia łańcucha bloków|
| messageSchemaVersion  | Wersja schematu komunikatów |
| messageName           | **CreateUserUpdate** |
| status                | Stan żądania utworzenia użytkownika.  Jeśli operacja się powiedzie, wartość jest **Powodzenie**. W przypadku awarii, wartość jest **błąd**.     |
| additionalInformation | Dodatkowe informacje podane na podstawie stanu |

Przykład pomyślnie **Utwórz użytkownika** odpowiedzi z aplikacji Blockchain Workbench:

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

Jeśli żądanie nie powiodło się, szczegółowe informacje o błędzie znajdują się w dodatkowych informacji.

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
| requestId            | Klient podany identyfikator GUID |
| userChainIdentifier  | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, ten adres jest użytkownika **łańcuchu** adresu. |
| applicationName      | Nazwa aplikacji |
| version              | Wersja aplikacji. Wymagane, jeśli masz wiele wersji aplikacji, włączone. W przeciwnym razie wersja jest opcjonalne. Aby uzyskać więcej informacji na temat wersji aplikacji, zobacz [przechowywanie wersji aplikacji Azure Blockchain Workbench](version-app.md). |
| workflowName         | Nazwa przepływu pracy |
| parameters           | Parametry wejściowe dla tworzenia kontraktu |
| connectionId         | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion | Wersja schematu komunikatów |
| messageName          | **CreateContractRequest** |

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

Blockchain Workbench zwraca odpowiedź z następujących pól:

| **Nazwa**                 | **Opis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Klient podany identyfikator GUID                                                             |
| contractId               | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| contractLedgerIdentifier | Adres kontraktu na rejestr                                            |
| connectionId             | Unikatowy identyfikator połączenia łańcucha bloków                               |
| messageSchemaVersion     | Wersja schematu komunikatów                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Stan żądania utworzenia kontraktu.  Możliwe wartości: **Przesłano**, **zatwierdzone**, **błąd**.  |
| additionalInformation    | Dodatkowe informacje podane na podstawie stanu                              |

Przykład przesłane **tworzenie kontraktu** odpowiedzi z aplikacji Blockchain Workbench:

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

Przykład zatwierdzone **tworzenie kontraktu** odpowiedzi z aplikacji Blockchain Workbench:

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

Jeśli żądanie nie powiodło się, szczegółowe informacje o błędzie znajdują się w dodatkowych informacji.

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

### <a name="create-contract-action"></a>Utwórz akcję kontraktu

Tworzy nową akcję kontraktu.

Żądanie wymaga następujących pól:

| **Nazwa**                 | **Opis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Klient podany identyfikator GUID |
| userChainIdentifier      | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, ten adres jest użytkownika **łańcuchu** adresu. |
| contractLedgerIdentifier | Adres kontraktu na rejestr |
| version                  | Wersja aplikacji. Wymagane, jeśli masz wiele wersji aplikacji, włączone. W przeciwnym razie wersja jest opcjonalne. Aby uzyskać więcej informacji na temat wersji aplikacji, zobacz [przechowywanie wersji aplikacji Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Nazwa funkcji przepływu pracy |
| parameters               | Parametry wejściowe dla tworzenia kontraktu |
| connectionId             | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion     | Wersja schematu komunikatów |
| messageName              | **CreateContractActionRequest** |

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

Blockchain Workbench zwraca odpowiedź z następujących pól:

| **Nazwa**              | **Opis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Klient podany identyfikator GUID|
| contractId            | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| connectionId          | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion  | Wersja schematu komunikatów |
| messageName           | **CreateContractActionUpdate** |
| status                | Stan żądania akcji kontraktu. Możliwe wartości: **Przesłano**, **zatwierdzone**, **błąd**.                         |
| AdditionalInformation | Dodatkowe informacje podane na podstawie stanu |

Przykład przesłane **Utwórz akcję kontraktu** odpowiedzi z aplikacji Blockchain Workbench:

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

Przykład zatwierdzone **Utwórz akcję kontraktu** odpowiedzi z aplikacji Blockchain Workbench:

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

Jeśli żądanie nie powiodło się, szczegółowe informacje o błędzie znajdują się w dodatkowych informacji.

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

### <a name="input-api-error-codes-and-messages"></a>Kody błędów usługi danych wejściowych interfejsu API i komunikaty

**Kod błędu: 4000: Błąd złe żądanie**
- Nieprawidłowy identyfikator połączenia
- CreateUserRequest Deserializacja nie powiodła się
- CreateContractRequest Deserializacja nie powiodła się
- CreateContractActionRequest deserialization failed
- Aplikacja {identyfikowane przez nazwę aplikacji} nie istnieje
- Aplikacja {identyfikowane przez nazwę aplikacji} nie ma przepływu pracy
- UserChainIdentifier nie istnieje.
- Kontrakt {identyfikowane przez identyfikator księgi} nie istnieje.
- Kontrakt {identyfikowane przez identyfikator księgi} nie ma funkcji {Nazwa przepływu pracy funkcji}
- UserChainIdentifier nie istnieje.

**Kod błędu 4090: Błąd konfliktu**
- Użytkownik już istnieje.
- Kontrakt już istnieje.
- Akcja kontraktu już istnieje

**Kod błędu: 5000: Wewnętrzny błąd serwera**
- Komunikaty o wyjątkach

## <a name="event-notifications"></a>Powiadomienia o zdarzeniach

Powiadomienia o zdarzeniach może służyć do powiadamiania użytkowników i systemy klienckie zdarzeń, które odbywa się w aplikacji Blockchain Workbench i sieć łańcucha bloków, który jest połączony. Powiadomienia o zdarzeniach można wykorzystać bezpośrednio w kodzie lub używane z narzędzi, takich jak Logic Apps i Flow, aby wyzwolić przepływ danych w systemach transmisji do klientów.

Zobacz [odwołania komunikatu powiadomienia](#notification-message-reference) szczegóły różne komunikaty, które mogą być odbierane.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Korzystanie z usługi Event Grid zdarzeń za pomocą usługi Azure Functions

Jeśli użytkownik chce, aby otrzymywać powiadomienia o zdarzenia mające miejsce w aplikacji Blockchain Workbench przy użyciu usługi Event Grid, mogą wykorzystywać zdarzenia z usługi Event Grid przy użyciu usługi Azure Functions.

1. Tworzenie **aplikacja funkcji platformy Azure** w witrynie Azure portal.
2. Tworzenie nowej funkcji.
3. Zlokalizuj szablon dla usługi Event Grid. Odczytanie komunikatu o kod podstawowy szablon jest wyświetlany. Zmodyfikuj kod, zgodnie z potrzebami.
4. Zapisz tę funkcję. 
5. Wybierz usługi Event Grid z aplikacji Blockchain Workbench grupy zasobów.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Wykorzystywanie zdarzeń usługi Event Grid z usługą Logic Apps

1. Utwórz nową **aplikacji logiki platformy Azure** w witrynie Azure portal.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu, wyświetli się monit o wybierz wyzwalacz. Wybierz **usługi Azure Event Grid — gdy wystąpi zdarzenie zasobu**.
3. Po wyświetleniu projektanta przepływów pracy, pojawi się zalogować.
4. Wybierz subskrypcję. Zasób jako **Microsoft.EventGrid.Topics**. Wybierz **Nazwa zasobu** z nazwą zasobu z grupy zasobów aplikacji Azure Blockchain Workbench.
5. Wybierz usługi Event Grid z aplikacji Blockchain Workbench grupy zasobów.

## <a name="using-service-bus-topics-for-notifications"></a>Używanie tematów usługi Service Bus dla powiadomień

Tematy usługi Service Bus może służyć do powiadamiania użytkowników o zdarzeniach, które występują w aplikacji Blockchain Workbench. 

1. Przejdź do usługi Service Bus w grupie zasobów w aplikacji Workbench.
2. Wybierz **tematy**.
3. Wybierz **tematu ruch wychodzący**.
4. Utwórz nową subskrypcję do tego tematu. Uzyskaj klucz dla niego.
5. Napisz program, które subskrybuje do zdarzeń z tej subskrypcji.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Korzystanie z wiadomości usługi Service Bus z usługą Logic Apps

1. Utwórz nową **aplikacji logiki platformy Azure** w witrynie Azure portal.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu, wyświetli się monit o wybierz wyzwalacz. Typ **usługi Service Bus** w polu wyszukiwania i wybierz odpowiednie dla typu interakcji wyzwalacza ma znajdować się z usługą Service Bus. Na przykład **usługi Service Bus — gdy wiadomość zostaje odebrana w subskrypcji tematu (Automatyczne zakończenie)** .
3. Po wyświetleniu projektanta przepływów pracy, należy określić informacje o połączeniu usługi Service Bus.
4. Wybierz subskrypcję, a następnie określ temat **zewnętrzne workbench**.
5. Twórz logiki aplikacji korzystającej z typu komunikatów z tego wyzwalacza.

## <a name="notification-message-reference"></a>Dokumentacja komunikatów powiadomień

W zależności od **messageName**, komunikaty powiadomień mieć jedną z następujących typów komunikatów.

### <a name="block-message"></a>Komunikat dotyczący blokowania

Zawiera informacje o poszczególnych bloków. *BlockMessage* zawiera sekcja o informacje o poziomie bloku i sekcję przy użyciu informacji o transakcji.

| Name (Nazwa) | Opis |
|------|-------------|
| Blok | Zawiera [block informacji](#block-information) |
| transakcji | Zawiera kolekcję [informacji o transakcji](#transaction-information) bloku |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu komunikatów |
| messageName | **BlockMessage** |
| AdditionalInformation | Dodatkowe informacje podane |

#### <a name="block-information"></a>Blok informacji

| Name (Nazwa)              | Opis |
|-------------------|-------------|
| BlockId           | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench |
| BlockNumber       | Unikatowy identyfikator bloku na rejestr |
| BlockHash         | Wartość skrótu bloku |
| previousBlockHash | Skrót poprzedniego bloku |
| blockTimestamp    | Sygnatura czasowa bloku |

#### <a name="transaction-information"></a>Informacje o transakcji

| Name (Nazwa)               | Opis |
|--------------------|-------------|
| transactionId      | Unikatowy identyfikator transakcji w aplikacji Azure Blockchain Workbench |
| transactionHash    | Skrót transakcji w księdze |
| from               | Unikatowy identyfikator w księdze dla początkowy rekord transakcji |
| na                 | Unikatowy identyfikator w księdze dla docelowego transakcji |
| ProvisioningStatus | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla transakcji. Możliwe wartości: </br>0 – transakcja została utworzona przez interfejs API w bazie danych</br>1 — transakcja została wysłana do rejestru</br>2 transakcja została pomyślnie zatwierdzona w księdze</br>3 lub 4 - transakcji nie można przesłać do rejestru</br>5 — transakcja została pomyślnie zatwierdzona w księdze |

Przykład *BlockMessage* z aplikacji Blockchain Workbench:

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

### <a name="contract-message"></a>Kontrakt komunikatu

Zawiera informacje o kontraktu. Komunikat zawiera sekcja właściwości kontraktu i sekcję przy użyciu informacji o transakcji. Wszystkie transakcje, które zmodyfikowały kontrakt dla określonego bloku znajdują się w sekcji transakcji.

| Name (Nazwa) | Opis |
|------|-------------|
| BlockId | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench |
| BlockHash | Wartość skrótu bloku |
| modifyingTransactions | [Transakcje, które zmodyfikowane](#modifying-transaction-information) umowy |
| contractId | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| contractLedgerIdentifier | Unikatowy identyfikator dla kontraktu na rejestr |
| contractProperties | [Właściwości kontraktu](#contract-properties) |
| isNewContract | Wskazuje, czy ten kontrakt była nowo utworzona. Możliwe wartości to: true: ten kontrakt został nowego kontraktu utworzone. wartość false: Umowa jest aktualizacja kontraktu. |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu komunikatów |
| messageName | **ContractMessage** |
| AdditionalInformation | Dodatkowe informacje podane |

#### <a name="modifying-transaction-information"></a>Modyfikowanie informacji o transakcji

| Name (Nazwa)               | Opis |
|--------------------|-------------|
| transactionId | Unikatowy identyfikator transakcji w aplikacji Azure Blockchain Workbench |
| transactionHash | Skrót transakcji w księdze |
| from | Unikatowy identyfikator w księdze dla początkowy rekord transakcji |
| na | Unikatowy identyfikator w księdze dla docelowego transakcji |

#### <a name="contract-properties"></a>Właściwości kontraktu

| Name (Nazwa)               | Opis |
|--------------------|-------------|
| workflowPropertyId | Unikatowy identyfikator właściwości przepływu pracy w aplikacji Azure Blockchain Workbench |
| name | Nazwa właściwości przepływu pracy |
| value | Wartość właściwości przepływu pracy |

Przykład *ContractMessage* z aplikacji Blockchain Workbench:

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

### <a name="event-message-contract-function-invocation"></a>Komunikat zdarzenia: Kontrakt wywołania funkcji

Zawiera informacje, po wywołaniu funkcji kontraktu, takich jak nazwy funkcji, parametrów danych wejściowych i element wywołujący funkcji.

| Name (Nazwa) | Opis |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| Obiekt wywołujący                      | [Informacje o wywołującym](#caller-information) |
| contractId                  | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| contractLedgerIdentifier    | Unikatowy identyfikator dla kontraktu na rejestr |
| functionName                | Nazwa funkcji |
| parameters                  | [Informacje o parametrach](#parameter-information) |
| Transakcji                 | Informacje o transakcji |
| inTransactionSequenceNumber | Numer sekwencyjny transakcji w bloku |
| connectionId                | Unikatowy identyfikator połączenia |
| messageSchemaVersion        | Wersja schematu komunikatów |
| messageName                 | **EventMessage** |
| AdditionalInformation       | Dodatkowe informacje podane |

#### <a name="caller-information"></a>Informacje o wywołującym

| Name (Nazwa) | Opis |
|------|-------------|
| — typ | Typ obiektu wywołującego, takich jak kontraktu lub przez użytkownika |
| id | Unikatowy identyfikator obiektu wywołującego w aplikacji Azure Blockchain Workbench |
| ledgerIdentifier | Unikatowy identyfikator wywołującego w księdze |

#### <a name="parameter-information"></a>Informacje o parametrach

| Name (Nazwa) | Opis |
|------|-------------|
| name | Nazwa parametru |
| value | Wartość parametru |

#### <a name="event-message-transaction-information"></a>Informacje o transakcji komunikat zdarzenia

| Name (Nazwa)               | Opis |
|--------------------|-------------|
| transactionId      | Unikatowy identyfikator transakcji w aplikacji Azure Blockchain Workbench |
| transactionHash    | Skrót transakcji w księdze |
| from               | Unikatowy identyfikator w księdze dla początkowy rekord transakcji |
| na                 | Unikatowy identyfikator w księdze dla docelowego transakcji |

Przykład *EventMessage ContractFunctionInvocation* z aplikacji Blockchain Workbench:

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

### <a name="event-message-application-ingestion"></a>Komunikat zdarzenia: Wprowadzanie aplikacji

Zawiera informacje, gdy aplikacja zostanie przekazany do aplikacji Workbench, takie jak nazwa i wersja aplikacji przekazany.

| Name (Nazwa) | Opis |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Unikatowy identyfikator aplikacji w aplikacji Azure Blockchain Workbench |
| applicationName | Nazwa aplikacji |
| applicationDisplayName | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| applicationDefinitionLocation | Adres URL, w którym znajduje się w pliku konfiguracji aplikacji |
| contractCodes | Kolekcja [kontraktu kody](#contract-code-information) dla aplikacji |
| applicationRoles | Kolekcja [ról aplikacji](#application-role-information) dla aplikacji |
| applicationWorkflows | Kolekcja [przepływów pracy aplikacji](#application-workflow-information) dla aplikacji |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu komunikatów |
| messageName | **EventMessage** |
| AdditionalInformation | Podane tu informacje dodatkowe zawiera Stany przepływu pracy aplikacji i informacje o przejście. |

#### <a name="contract-code-information"></a>Informacje o kodu kontraktu

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator pliku kodu kontraktu w aplikacji Azure Blockchain Workbench |
| ledgerId | Unikatowy identyfikator rejestru w aplikacji Azure Blockchain Workbench |
| location | Adres URL, w którym znajduje się plik kodu kontraktu |

#### <a name="application-role-information"></a>Informacje o rolach aplikacji

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator dla roli aplikacji w aplikacji Azure Blockchain Workbench |
| name | Nazwa roli aplikacji |

#### <a name="application-workflow-information"></a>Informacje o przepływie pracy aplikacji

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator dla przepływu pracy aplikacji w aplikacji Azure Blockchain Workbench |
| name | Nazwa przepływu pracy aplikacji |
| displayName | Nazwa wyświetlana przepływu pracy aplikacji |
| — funkcje | Kolekcja [funkcji przepływ pracy aplikacji](#workflow-function-information)|
| Stany | Kolekcja [stanów przepływu pracy aplikacji](#workflow-state-information) |
| properties | Aplikacja [informacje o właściwościach przepływu pracy](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informacje o przepływie pracy — funkcja

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator dla funkcji przepływu pracy aplikacji w aplikacji Azure Blockchain Workbench |
| name | Nazwa funkcji |
| parameters | Parametry funkcji |

##### <a name="workflow-state-information"></a>Informacje o stanie przepływu pracy

| Name (Nazwa) | Opis |
|------|-------------|
| name | Nazwa województwa |
| displayName | Nazwa wyświetlana stanu |
| style | Styl stanu (powodzenie lub niepowodzenie) |

##### <a name="workflow-property-information"></a>Informacje o właściwości przepływu pracy

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator właściwości przepływu pracy aplikacji w aplikacji Azure Blockchain Workbench |
| name | Nazwa właściwości |
| type | Typ właściwości |

Przykład *EventMessage ApplicationIngestion* z aplikacji Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
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

### <a name="event-message-role-assignment"></a>Komunikat zdarzenia: Przypisanie roli

Zawiera informacje, gdy użytkownik ma przypisaną rolę w aplikacji Workbench, takie jak kto przeprowadził przypisania roli i nazwę roli oraz odpowiednia aplikacja.

| Name (Nazwa) | Opis |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Unikatowy identyfikator aplikacji w aplikacji Azure Blockchain Workbench |
| applicationName | Nazwa aplikacji |
| applicationDisplayName | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| applicationRole        | Informacje o [ról aplikacji](#roleassignment-application-role) |
| przypisujący               | Informacje o [użytkownika przypisującego](#roleassignment-assigner) |
| osoby przypisanej               | Informacje o [osoby przypisanej](#roleassignment-assignee) |
| connectionId           | Unikatowy identyfikator połączenia |
| messageSchemaVersion   | Wersja schematu komunikatów |
| messageName            | **EventMessage** |
| AdditionalInformation  | Dodatkowe informacje podane |

#### <a name="roleassignment-application-role"></a>Rola aplikacji RoleAssignment

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator dla roli aplikacji w aplikacji Azure Blockchain Workbench |
| name | Nazwa roli aplikacji |

#### <a name="roleassignment-assigner"></a>Przypisujący RoleAssignment

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator użytkownika w aplikacji Azure Blockchain Workbench |
| type | Typ użytkownika przypisującego |
| ChainIdentifier | Unikatowy identyfikator użytkownika na rejestr |

#### <a name="roleassignment-assignee"></a>RoleAssignment osoby przypisanej

| Name (Nazwa) | Opis |
|------|-------------|
| id | Unikatowy identyfikator użytkownika w aplikacji Azure Blockchain Workbench |
| — typ | Typ osoby przypisanej |
| ChainIdentifier | Unikatowy identyfikator użytkownika na rejestr |

Przykład *EventMessage RoleAssignment* z aplikacji Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
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

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce integracji inteligentne kontraktu](integration-patterns.md)
