---
title: Omówienie integracji wiadomości w usłudze Azure Blockchain Workbench
description: Omówienie używania wiadomości w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614365"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Aplikacji Azure Blockchain Workbench komunikatów integracji

Oprócz interfejsu API REST, Azure Blockchain Workbench także bazujące na komunikatach integracji. Środowisko robocze publikuje księgi skoncentrowane na zdarzenia za pośrednictwem usługi Azure Event Grid, umożliwiając użytkownikom podrzędnego pozyskiwania danych lub podjąć działania na podstawie tych zdarzeń. Dla tych klientów, które wymagają niezawodną obsługę komunikatów aplikacji Azure Blockchain Workbench dostarcza komunikaty do punktu końcowego usługi Azure Service Bus.

## <a name="input-apis"></a>Interfejsy API danych wejściowych

Jeśli chcesz zainicjować transakcje z systemami zewnętrznymi, aby utworzyć użytkowników, Tworzenie umów i aktualizowanie umów służy komunikatów wejściowych interfejsów API do wykonania transakcji na księgowe. Zobacz [przykłady integracji komunikatów](https://aka.ms/blockchain-workbench-integration-sample) przykład demonstrujący wejściowych interfejsów API.

Poniżej przedstawiono aktualnie dostępne interfejsy API danych wejściowych.

### <a name="create-user"></a>Utwórz użytkownika

Tworzy nowego użytkownika.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Klient podany identyfikator GUID                                |
| Imię            | Imię użytkownika                              |
| Nazwisko             | Nazwisko użytkownika                               |
| EmailAddress         | Adres e-mail użytkownika                           |
| externalId           | Usługa Azure AD identyfikator obiektu użytkownika                      |
| Identyfikator połączenia         | Unikatowy identyfikator połączenia łańcucha bloków |
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
| UserChainIdentifier   | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, znajduje się adres użytkownika **w łańcuchu** adresu. |
| Identyfikator połączenia          | Unikatowy identyfikator połączenia łańcucha bloków|
| messageSchemaVersion  | Wersja schematu komunikatów |
| messageName           | **CreateUserUpdate** |
| status                | Stan żądania utworzenia użytkownika.  Jeśli operacja się powiedzie, wartość jest **Powodzenie**. W przypadku awarii, wartość jest **błąd**.     |
| AdditionalInformation | Dodatkowe informacje podane na podstawie stanu |

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

Jeśli żądanie nie powiodło się, szczegółowe informacje o awarii są objęte dodatkowe informacje.

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
| UserChainIdentifier  | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, ten adres jest użytkownika **łańcuchu** adresu. |
| ApplicationName      | Nazwa aplikacji |
| WorkflowName         | Nazwa przepływu pracy |
| parameters           | Parametry wejściowe dla tworzenia kontraktu |
| Identyfikator połączenia         | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion | Wersja schematu komunikatów |
| messageName          | **CreateContractRequest** |

Przykład:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
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
| ContractId               | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| ContractLedgerIdentifier | Adres kontraktu na rejestr                                            |
| Identyfikator połączenia             | Unikatowy identyfikator połączenia łańcucha bloków                               |
| messageSchemaVersion     | Wersja schematu komunikatów                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Stan żądania utworzenia kontraktu.  Możliwe wartości: **Submitted**, **przydzielony**, **błąd**.  |
| AdditionalInformation    | Dodatkowe informacje podane na podstawie stanu                              |

Przykład przesłane **tworzenie kontraktu** odpowiedzi z aplikacji Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
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

Jeśli żądanie nie powiodło się, szczegółowe informacje o awarii są objęte dodatkowe informacje.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
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
| UserChainIdentifier      | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, jest to użytkownika **łańcuchu** adresu. |
| ContractLedgerIdentifier | Adres kontraktu na rejestr |
| WorkflowFunctionName     | Nazwa funkcji przepływu pracy |
| parameters               | Parametry wejściowe dla tworzenia kontraktu |
| Identyfikator połączenia             | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion     | Wersja schematu komunikatów |
| messageName              | **CreateContractActionRequest** |

Przykład:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
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
| ContractId            | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| Identyfikator połączenia          | Unikatowy identyfikator połączenia łańcucha bloków |
| messageSchemaVersion  | Wersja schematu komunikatów |
| messageName           | **CreateContractActionUpdate** |
| status                | Stan żądania akcji kontraktu. Możliwe wartości: **Submitted**, **przydzielony**, **błąd**.                         |
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
    "status": "Committed"
    "additionalInformation": { }
}
```

Jeśli żądanie nie powiodło się, szczegółowe informacje o awarii są objęte dodatkowe informacje.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Kody błędów usługi danych wejściowych interfejsu API i komunikaty

**Kod błędu: 4000: błąd złe żądanie**
- Nieprawidłowy identyfikator połączenia
- CreateUserRequest Deserializacja nie powiodła się
- CreateContractRequest Deserializacja nie powiodła się
- CreateContractActionRequest Deserializacja nie powiodła się
- Aplikacja {identyfikowane przez nazwę aplikacji} nie istnieje
- Aplikacja {identyfikowane przez nazwę aplikacji} nie ma przepływu pracy
- UserChainIdentifier nie istnieje.
- Kontrakt {identyfikowane przez identyfikator księgi} nie istnieje.
- Kontrakt {identyfikowane przez identyfikator księgi} nie ma funkcji {Nazwa przepływu pracy funkcji}
- UserChainIdentifier nie istnieje.

**4090. kod błędu: błąd w konflikcie**
- Użytkownik już istnieje.
- Kontrakt już istnieje.
- Akcja kontraktu już istnieje

**Kod błędu: 5000: wewnętrzny błąd serwera**
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

1.  Utwórz nową **aplikacji logiki platformy Azure** w witrynie Azure portal.
2.  Podczas otwierania aplikacji logiki platformy Azure w portalu, wyświetli się monit o wybierz wyzwalacz. Wybierz **usługi Azure Event Grid — gdy wystąpi zdarzenie zasobu**.
3. Po wyświetleniu projektanta przepływów pracy, pojawi się zalogować.
4. Wybierz subskrypcję. Zasób jako **Microsoft.EventGrid.Topics**. Wybierz **Nazwa zasobu** z nazwą zasobu z grupy zasobów aplikacji Azure Blockchain Workbench.
5. Wybierz usługi Event Grid z aplikacji Blockchain Workbench grupy zasobów.

## <a name="using-service-bus-topics-for-notifications"></a>Używanie tematów usługi Service Bus dla powiadomień

Tematy usługi Service Bus może służyć do powiadamiania użytkowników o zdarzeniach, które występują w aplikacji Blockchain Workbench. 

1.  Przejdź do usługi Service Bus w grupie zasobów w aplikacji Workbench.
2.  Wybierz **tematy**.
3.  Wybierz **zewnętrzne workbench**.
4.  Utwórz nową subskrypcję do tego tematu. Uzyskaj klucz dla niego.
5.  Napisz program, które subskrybuje do zdarzeń z tej subskrypcji.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Korzystanie z wiadomości usługi Service Bus z usługą Logic Apps

1. Utwórz nową **aplikacji logiki platformy Azure** w witrynie Azure portal.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu, wyświetli się monit o wybierz wyzwalacz. Typ **usługi Service Bus** w polu wyszukiwania i wybierz odpowiednie dla typu interakcji wyzwalacza ma znajdować się z usługą Service Bus. Na przykład **usługi Service Bus — gdy wiadomość zostaje odebrana w subskrypcji tematu (Automatyczne zakończenie)**.
3. Po wyświetleniu projektanta przepływów pracy, należy określić informacje o połączeniu usługi Service Bus.
4. Wybierz subskrypcję, a następnie określ temat **zewnętrzne workbench**.
5. Twórz logiki aplikacji korzystającej z typu komunikatów z tego wyzwalacza.

## <a name="notification-message-reference"></a>Dokumentacja komunikatów powiadomień

W zależności od **OperationName**, komunikaty powiadomień mieć jedną z następujących typów komunikatów.

### <a name="accountcreated"></a>AccountCreated

Wskazuje, że ma zostać dodany do określonego łańcucha zażądano nowego konta.

| Name (Nazwa)    | Opis  |
|----------|--------------|
| UserId  | Identyfikator użytkownika, który został utworzony. |
| ChainIdentifier | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, będzie to użytkownika **w łańcuchu** adresu. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Wskazuje, czy przeprowadzono żądania, aby wstawić lub zaktualizować kontraktu na rejestru rozproszonego.

| Name (Nazwa) | Opis |
|-----|--------------|
| ChainID | Unikatowy identyfikator łańcucha skojarzony z żądaniem |
| BlockId | Unikatowy identyfikator bloku na rejestr |
| ContractId | Unikatowy identyfikator zamówienia |
| ContractAddress |       Adres kontraktu na rejestr |
| TransactionHash  |     Skrót transakcji w księdze |
| OriginatingAddress |   Adres serwera początkowego transakcji |
| Nazwa akcji       |     Nazwa akcji |
| IsUpdate        |      Określa, czy jest to aktualizacja |
| Parametry       |     Listę obiektów, które identyfikują typ nazwę, wartość i dane wysyłane do akcji, parametrów |
| TopLevelInputParams |  W scenariuszach, w których kontrakt jest podłączony do jednej lub kilku umów są to parametry z kontraktu najwyższego poziomu. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Wskazuje, że zażądano do wykonywania akcji na konkretne zamówienie na rejestru rozproszonego.

| Name (Nazwa)                     | Opis                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Unikatowy identyfikator dla tej akcji kontraktu |
| ChainIdentifier          | Unikatowy identyfikator łańcucha |
| Identyfikator połączenia             | Unikatowy identyfikator połączenia |
| UserChainIdentifier      | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum, ten adres jest użytkownika **łańcuchu** adresu. |
| ContractLedgerIdentifier | Adres kontraktu na rejestr |
| WorkflowFunctionName     | Nazwa funkcji przepływu pracy |
| WorkflowName             | Nazwa przepływu pracy |
| WorkflowBlobStorageURL   | Adres url zamówienia w magazynie obiektów blob |
| ContractActionParameters | Parametry akcji kontraktu |
| TransactionHash          | Skrót transakcji w księdze |
| Stan obsługi administracyjnej      | Bieżący stan inicjowania obsługi administracyjnej akcji.</br>0 — utworzone</br>1 — w toku</br>2 – ukończenia</br> Pełne wskazuje potwierdzenia z księgi, tym został pomyślnie dodany |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Wskazuje, czy przeprowadzono żądania można zaktualizować użytkownika saldu określonego rejestru rozproszonego.

> [!NOTE]
> Ten komunikat jest generowany tylko w przypadku tych ksiąg, które wymagają finansowania kont.
> 

| Name (Nazwa)    | Opis                              |
|---------|------------------------------------------|
| Adres | Adres użytkownika, który został funded |
| Saldo | Saldo saldo użytkownika         |
| ChainID | Unikatowy identyfikator łańcucha     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Komunikat wskazuje, czy przeprowadzono żądania, aby dodać bloku na rejestru rozproszonego.

| Name (Nazwa)           | Opis                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Unikatowy identyfikator łańcucha, do której dodano bloku             |
| BlockId        | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench |
| BlockHash      | Wartość skrótu bloku                                                 |
| BlockTimeStamp | Sygnatura czasowa bloku                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Komunikat zawiera szczegółowe informacje dotyczące żądania, aby dodać transakcji na rejestru rozproszonego.

| Name (Nazwa)            | Opis                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Unikatowy identyfikator łańcucha, do której dodano bloku             |
| BlockId         | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench |
| TransactionHash | Skrót transakcji                                           |
| Z            | Adres serwera początkowego transakcji                      |
| Do              | Adres zamierzonym odbiorcą transakcji              |
| Wartość           | Wartość przedmiot danej transakcji                                 |
| IsAppBuilderTx  | Określa, czy jest to transakcja Blockchain Workbench                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Zawiera szczegółowe informacje dotyczące przypisywania identyfikator łańcucha dla kontraktu. Na przykład w łańcuch bloków Ethereum adres kontraktu w księdze.

| Name (Nazwa)            | Opis                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench |
| ChainIdentifier | Identyfikator zamówienia w łańcuchu                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Klasy używane przez typy komunikatów

### <a name="messagemodelbase"></a>MessageModelBase

Podstawowy model dla wszystkich wiadomości.

| Name (Nazwa)          | Opis                          |
|---------------|--------------------------------------|
| OperationName | Nazwa operacji           |
| Identyfikator żądania     | Unikatowy identyfikator żądania |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Zawiera nazwę, wartość i typ parametru.

| Name (Nazwa)  | Opis                 |
|-------|-----------------------------|
| Name (Nazwa)  | Nazwa parametru  |
| Wartość | Wartość parametru |
| Typ  | Typ parametru  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Zawiera identyfikator, nazwę, wartość i typ właściwości.

| Name (Nazwa)  | Opis                |
|-------|----------------------------|
| Identyfikator    | Identyfikator właściwości    |
| Name (Nazwa)  | Nazwa właściwości  |
| Wartość | Wartość właściwości |
| Typ  | Typ właściwości  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wzorce integracji inteligentne kontraktu](integration-patterns.md)