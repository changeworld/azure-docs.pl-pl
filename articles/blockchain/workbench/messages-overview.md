---
title: Omówienie integracji wiadomości w usłudze Azure Blockchain Workbench
description: Omówienie używania wiadomości w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dcf31bd6b128115962e30dd6653364c4cd65047d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242881"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Aplikacji Azure Blockchain Workbench komunikatów integracji

Oprócz interfejsu API REST, Azure Blockchain Workbench także bazujące na komunikatach integracji. Środowisko robocze publikuje księgi skoncentrowane na zdarzenia za pośrednictwem usługi Azure Event Grid, umożliwiając użytkownikom podrzędnego pozyskiwania danych lub podjąć działania na podstawie tych zdarzeń. Dla tych klientów, które wymagają niezawodną obsługę komunikatów aplikacji Azure Blockchain Workbench dostarcza komunikaty do punktu końcowego usługi Azure Service Bus.

Deweloperzy również wyrazili swoje zainteresowania możliwość systemów zewnętrznych, które komunikują się inicjować transakcji do tworzenia użytkowników, Tworzenie umów i aktualizowanie umów na rejestr. Gdy ta funkcja nie jest obecnie dostępny w publicznej wersji zapoznawczej, przykład zapewnia taką możliwość znajduje się w temacie [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).

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
| ChainID | Unikatowy identyfikator łańcucha skojarzony z tym żądaniem.|
| BlockId | Unikatowy identyfikator bloku na księgowe.|
| ContractId | Unikatowy identyfikator dla kontraktu.|
| ContractAddress |       Adres kontraktu w księdze.|
| TransactionHash  |     Skrót transakcji w księdze.|
| OriginatingAddress |   Adres serwera początkowego transakcji.|
| Nazwa akcji       |     Nazwa akcji.|
| IsUpdate        |      Określa, czy jest to aktualizacja.|
| Parametry       |     Lista obiektów, które identyfikują typ nazwę, wartość i dane wysyłane do akcji, parametrów.|
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
| ContractActionId         | Unikatowy identyfikator dla tej akcji kontraktu                                                                                                                                |
| ChainIdentifier          | Unikatowy identyfikator łańcucha                                                                                                                                           |
| Identyfikator połączenia             | Unikatowy identyfikator połączenia                                                                                                                                      |
| UserChainIdentifier      | Adres użytkownika, który został utworzony na sieć łańcucha bloków. W Ethereum będzie to adres "w łańcuchu" dla użytkownika.                                                     |
| ContractLedgerIdentifier | Adres kontraktu w księdze.                                                                                                                                        |
| WorkflowFunctionName     | Nazwa funkcji przepływu pracy.                                                                                                                                                |
| WorkflowName             | Nazwa przepływu pracy.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Adres url zamówienia w magazynie obiektów blob.                                                                                                                                      |
| ContractActionParameters | Parametry akcji kontraktu.                                                                                                                                           |
| TransactionHash          | Skrót transakcji w księdze.                                                                                                                                    |
| Stan obsługi administracyjnej      | Bieżący stan inicjowania obsługi administracyjnej akcji.</br>0 — utworzone</br>1 — w toku</br>2 – ukończenia</br> Pełne wskazuje potwierdzenia z księgi, tym został pomyślnie dodany.                                               |
|                          |                                                                                                                                                                               |

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
| Adres | Adres użytkownika, który został funded. |
| Saldo | Saldo saldo użytkownika.         |
| ChainID | Unikatowy identyfikator łańcucha.     |


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
| ChainId        | Unikatowy identyfikator łańcucha, do której dodano bloku.             |
| BlockId        | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench. |
| BlockHash      | Wartość skrótu bloku.                                                 |
| BlockTimeStamp | Sygnatura czasowa bloku.                                            |

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
| ChainId         | Unikatowy identyfikator łańcucha, do której dodano bloku.             |
| BlockId         | Unikatowy identyfikator bloku sterującego aplikacji Azure Blockchain Workbench. |
| TransactionHash | Skrót transakcji.                                           |
| Z            | Adres serwera początkowego transakcji.                      |
| Do              | Adres zamierzonym odbiorcą transakcji.              |
| Wartość           | Wartość przedmiot danej transakcji.                                 |
| IsAppBuilderTx  | Określa, czy jest to transakcja Blockchain Workbench.                         |

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
| ContractId      | Jest to unikatowy identyfikator dla kontraktu w aplikacji Azure Blockchain Workbench. |
| ChainIdentifier | Jest to identyfikator zamówienia w łańcuchu.                             |

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
| OperationName | Nazwa operacji.           |
| Identyfikator żądania     | Unikatowy identyfikator dla żądania. |

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
| Name (Nazwa)  | Nazwa parametru.  |
| Wartość | Wartość parametru. |
| Typ  | Typ parametru.  |

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
| Identyfikator    | Identyfikator właściwości.    |
| Name (Nazwa)  | Nazwa właściwości.  |
| Wartość | Wartość właściwości. |
| Typ  | Typ właściwości.  |

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