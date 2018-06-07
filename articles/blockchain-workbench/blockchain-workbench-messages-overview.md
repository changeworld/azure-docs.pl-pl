---
title: Omówienie integracji komunikatów w usłudze Azure Blockchain Workbench
description: Omówienie za pomocą wiadomości w Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f45396c3af285026e16ce641bd37bf0eadcee56d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607604"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench wiadomości integracji

Oprócz zapewnienia interfejsu API REST, Azure Blockchain Workbench udostępnia integracji na podstawie wiadomości. Workbench publikuje skoncentrowane księgi zdarzenia za pośrednictwem Azure zdarzeń siatki, umożliwiając użytkownikom podrzędne pozyskiwania danych lub podjąć działania na podstawie tych zdarzeń. Dla tych klientów, które wymagają niezawodna obsługa komunikatów Azure Blockchain Workbench dostarcza komunikaty do punktu końcowego usługi Azure Service Bus.

Deweloperzy również wyrazili zainteresowanie możliwość komunikacji inicjować transakcji do tworzenia użytkowników, tworzyć umowy i zaktualizować kontraktów na księgowe systemami zewnętrznymi. Gdy ta funkcja nie jest aktualnie widoczne w publicznej wersji zapoznawczej, próbkę dostarczającej taką możliwość można znaleźć w folderze [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).

## <a name="event-notifications"></a>Powiadomienia o zdarzeniach

Powiadomienia o zdarzeniach może służyć do powiadamiania użytkowników i systemy klienckie zdarzeń, które pojawiają się w Blockchain Workbench i blockchain sieci, z którą jest połączona. Powiadomienia o zdarzeniach może być używane bezpośrednio w kodzie lub używana z narzędzi, takich jak aplikacje logiki i przepływu do wyzwalania przepływu danych, aby systemy klienckie.

Zobacz [dokumentacja komunikatów powiadomień](#notification-message-reference) informacji o różnych komunikatów, które mogą być odbierane.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Wykorzystywanie zdarzenia siatki zdarzeń za pomocą usługi Azure Functions

Jeśli użytkownik chce Użyj siatki zdarzeń, aby otrzymywać powiadomienia o zdarzeniach, które pojawiają się w Blockchain Workbench, możesz korzystać ze zdarzeń siatki zdarzeń za pomocą usługi Azure Functions.

1. Utwórz **aplikacji funkcji Azure** w portalu Azure.
2. Utwórz nową funkcję.
3. Znajdź szablon do zdarzenia siatki. Pokazano kod podstawowy szablon do odczytywania wiadomości. Zmodyfikuj kod w razie potrzeby.
4. Zapisz funkcji. 
5. Wybierz siatki zdarzeń z Blockchain Workbench grupy zasobów.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Korzystanie z siatki zdarzeń zdarzenia z usługą Logic Apps

1.  Utwórz nową **aplikacji logiki Azure** w portalu Azure.
2.  Podczas otwierania aplikacji logiki platformy Azure w portalu, pojawi się monit wybierz wyzwalacz. Wybierz **Azure siatka zdarzenia — po wystąpieniu zdarzenia zasobów**.
3. Gdy zostanie wyświetlona projektanta przepływów pracy, pojawi się monit do logowania.
4. Wybierz subskrypcję. Zasobu jako **Microsoft.EventGrid.Topics**. Wybierz **Nazwa zasobu** z nazwą zasobu z grupy zasobów Azure Blockchain Workbench.
5. Wybierz siatki zdarzeń z Blockchain Workbench grupy zasobów.

## <a name="using-service-bus-topics-for-notifications"></a>Za pomocą tematów usługi Service Bus dla powiadomień

Tematów usługi Service Bus może służyć do powiadamiania użytkowników o zdarzeniach, które pojawiają się w Blockchain Workbench. 

1.  Przejdź do usługi Service Bus w grupie zasobów Workbench.
2.  Wybierz **tematy**.
3.  Wybierz **zewnętrzne workbench**.
4.  Utwórz nową subskrypcję do tego tematu. Uzyskaj klucz dla niego.
5.  Tworzenie programów, które subskrybuje zdarzenia z tej subskrypcji.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Korzystanie z komunikatów magistrali usługi z usługą Logic Apps

1. Utwórz nową **aplikacji logiki Azure** w portalu Azure.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu, pojawi się monit wybierz wyzwalacz. Typ **usługi Service Bus** w polu wyszukiwania i wybierz odpowiednią dla typu interakcji wyzwalacza mają z magistralą usług. Na przykład **usługi Service Bus — po odebraniu komunikatu w subskrypcji tematu (automatycznie uzupełniać)**.
3. Gdy zostanie wyświetlona projektanta przepływów pracy, należy określić informacje o połączeniu dla usługi Service Bus.
4. Wybierz subskrypcję i określ temat **zewnętrzne workbench**.
5. Opracowywanie logikę aplikacji, która korzysta z wiadomości z tego wyzwalacza.

## <a name="notification-message-reference"></a>Dokumentacja komunikatów powiadomień

W zależności od **OperationName**, komunikaty powiadomień mieć jeden z następujących typów wiadomości.

### <a name="accountcreated"></a>AccountCreated

Wskazuje, że mają zostać dodane do określonej łańcucha zażądano nowego konta.

| Name (Nazwa)    | Opis  |
|----------|--------------|
| UserId  | Identyfikator użytkownika, który został utworzony. |
| ChainIdentifier | Adres użytkownika, który został utworzony w sieci blockchain. W Ethereum, to użytkownika **w łańcuchu** adres. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Wskazuje, wprowadzono żądanie można wstawić ani zaktualizować kontraktu w księdze rozproszonych.

| Name (Nazwa) | Opis |
|-----|--------------|
| ChainID | Unikatowy identyfikator skojarzony z żądaniem łańcucha.|
| BlockId | Unikatowy identyfikator bloku na księgowe.|
| ContractId | Unikatowy identyfikator dla umowy.|
| ContractAddress |       Adres kontraktu w księdze.|
| TransactionHash  |     Skrót transakcji w księdze.|
| OriginatingAddress |   Adres inicjatorem transakcji.|
| Nazwa akcji       |     Nazwa akcji.|
| IsUpdate        |      Określa, czy jest to aktualizacja.|
| Parametry       |     Lista obiektów, które identyfikują typu nazwę, wartość i dane wysyłane do akcji, parametrów.|
| TopLevelInputParams |  W scenariuszach, w których kontrakt jest podłączony do jednego lub więcej umów są parametry z umową najwyższego poziomu. |

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

Wskazuje, że zażądano do wykonywania akcji na określonych kontraktu w księdze rozproszonej.

| Name (Nazwa)                     | Opis                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Unikatowy identyfikator dla tej akcji kontraktu                                                                                                                                |
| ChainIdentifier          | Unikatowy identyfikator dla łańcucha                                                                                                                                           |
| Identyfikator połączenia             | Unikatowy identyfikator dla połączenia                                                                                                                                      |
| UserChainIdentifier      | Adres użytkownika, który został utworzony w sieci blockchain. W Ethereum to adres "w łańcuchu" dla użytkownika.                                                     |
| ContractLedgerIdentifier | Adres kontraktu w księdze.                                                                                                                                        |
| WorkflowFunctionName     | Nazwa funkcji przepływu pracy.                                                                                                                                                |
| WorkflowName             | Nazwa przepływu pracy.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Adres url kontraktu w magazynie obiektów blob.                                                                                                                                      |
| ContractActionParameters | Parametry akcji kontraktu.                                                                                                                                           |
| TransactionHash          | Skrót transakcji w księdze.                                                                                                                                    |
| Stan obsługi administracyjnej      | Bieżący stan inicjowania obsługi administracyjnej akcji.</br>0 — utworzone</br>1 — w procesie</br>2 — zakończenie</br> Wskazuje pełną potwierdzenia z księgi który tym został pomyślnie dodany.                                               |
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

Wskazuje, wprowadzono żądanie aktualizacji saldo użytkownika określonego księgi rozproszonych.

> [!NOTE]
> Ten komunikat jest wyświetlany tylko dla tych księgi, które wymagają fundusze kont.
> 

| Name (Nazwa)    | Opis                              |
|---------|------------------------------------------|
| Adres | Adres użytkownika, który został finansowanych. |
| Saldo | Saldo saldo użytkownika.         |
| ChainID | Unikatowy identyfikator dla łańcucha.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Komunikat wskazuje, wprowadzono żądanie można dodawać blok w księdze rozproszonych.

| Name (Nazwa)           | Opis                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Unikatowy identyfikator łańcucha, do której dodano bloku.             |
| BlockId        | Unikatowy identyfikator dla bloku wewnątrz Azure Blockchain Workbench. |
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

Komunikat zawiera szczegółowe informacje na żądanie, aby dodać transakcji rozproszonej księgi.

| Name (Nazwa)            | Opis                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Unikatowy identyfikator łańcucha, do której dodano bloku.             |
| BlockId         | Unikatowy identyfikator dla bloku wewnątrz Azure Blockchain Workbench. |
| TransactionHash | Skrót transakcji.                                           |
| Z            | Adres inicjatorem transakcji.                      |
| Do              | Adres adresata transakcji.              |
| Wartość           | Wartość uwzględniona w transakcji.                                 |
| IsAppBuilderTx  | Określa, czy to jest Blockchain Workbench transakcji.                         |

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

Zawiera szczegółowe informacje na przypisanie identyfikator łańcucha dla kontraktu. Na przykład w blockchain Ethereum, adres kontraktu w księdze.

| Name (Nazwa)            | Opis                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Jest to unikatowy identyfikator dla kontraktu wewnątrz Azure Blockchain Workbench. |
| ChainIdentifier | Jest to identyfikator dla kontraktu w łańcuchu.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Klasy używane przez typy komunikatu

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

Zawiera nazwę, wartość oraz typ parametru.

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
> [Wzorce integracji inteligentne kontraktu](blockchain-workbench-integration-patterns.md)