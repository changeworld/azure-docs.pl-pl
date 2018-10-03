---
title: Tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench
description: Sposób tworzenia aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a7ca3f42874bc844bc0036e37a790ffebdc5f8d8
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243135"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench

Azure Blockchain Workbench umożliwia tworzenie aplikacji łańcucha bloków, które reprezentują wieloosobowa przepływy zdefiniowane przez konfigurację i kod inteligentne kontraktu.

Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie aplikacji łańcucha bloków
> * Utwórz plik kodu kontraktu inteligentne
> * Dodawanie aplikacji łańcucha bloków na Blockchain Workbench
> * Dodaj członków do aplikacji łańcucha bloków

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrażanie aplikacji Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji Azure Blockchain Workbench](deploy.md) szczegółowe informacje na temat wdrażania.
* Usługa Azure Active Directory użytkownicy dzierżawca skojarzony z aplikacji Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [Dodaj użytkowników usługi Azure AD w aplikacji Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Konto administratora aplikacji Blockchain Workbench. Aby uzyskać więcej informacji, zobacz Dodawanie [administratorów aplikacji Blockchain Workbench w aplikacji Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Witaj, łańcuch bloków!

Utworzymy podstawową aplikację, w których obiekt żądający wysyła żądanie i obiekt odpowiadający czy wysłać odpowiedź na żądanie. Na przykład może być żądanie, "Witaj, jak się masz?" może odpowiedzi, być, "Jestem doskonały!". Żądania i odpowiedzi są rejestrowane na podstawowej łańcucha bloków. 

Postępuj zgodnie z instrukcjami, aby utworzyć pliki aplikacji możesz też [pobrać przykład z witryny GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Plik konfiguracji

Metadane konfiguracji definiuje wysokiego poziomu przepływów pracy i model interakcji aplikacji łańcucha bloków. Metadane konfiguracji reprezentuje etapów przepływu pracy i interakcji z modelem aplikacji łańcucha bloków.

1. W ulubionym edytorze, Utwórz plik o nazwie `HelloBlockchain.json`.
2. Dodaj następujący kod JSON, aby zdefiniować konfigurację aplikacji łańcucha bloków.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Zapisz plik `HelloBlockchain.json`.

Plik konfiguracji zawiera kilka sekcji. Szczegółowe informacje na temat każdej sekcji są następujące:

### <a name="application-metadata"></a>Metadanych aplikacji

Na początku pliku konfiguracji zawiera informacje o aplikacji, w tym nazwa i opis aplikacji.

### <a name="application-roles"></a>Role aplikacji

W sekcji role aplikacji zdefiniowano role użytkowników, którzy mogą działać lub wziąć udział w ramach aplikacji łańcucha bloków. Należy zdefiniować zestaw ról różne na podstawie funkcjonalności. W tym scenariuszu odpowiedź na żądanie brak rozróżnienia między funkcje obiekt żądający jako jednostka, która wywołuje żądania oraz obiekt odpowiadający jako jednostki, która generuje odpowiedzi.

### <a name="workflows"></a>Przepływy

Przepływy pracy zdefiniować co najmniej jeden etapów, jak i akcje kontraktu. W tym scenariuszu odpowiedź na żądanie pierwszego etapu (stan) przepływu pracy jest obiekt żądający (roli), wykonują akcję (przejście) można wysłać żądania (funkcja). Kolejnego etapu (stan) jest obiekt odpowiadający (roli), wykonują akcję (przejście) do wysłania odpowiedzi (funkcja). Przepływ pracy aplikacji może obejmować właściwości, funkcje, i Stany wymagane opisują przepływ kontraktu. 

Aby uzyskać więcej informacji o zawartość plików konfiguracyjnych, zobacz [informacje o konfiguracji Azure Blockchain Workflow](configuration.md).

## <a name="smart-contract-code-file"></a>Plik kodu kontraktu inteligentne

Kontrakty inteligentne reprezentują logiki biznesowej aplikacji łańcucha bloków. Obecnie Blockchain Workbench obsługuje Ethereum księgi łańcucha bloków. Używa Ethereum [Solidity](https://solidity.readthedocs.io) jako jego język programowania przeznaczony do zapisywania własnym wymuszania logikę biznesową dla kontraktów inteligentnych.

Kontrakty inteligentne w trwałość są podobne do klas w językach obiektowych. Każdej umowy zawiera stan i funkcji do zaimplementowania etapy i akcje inteligentne kontraktu.

W ulubionym edytorze, Utwórz plik o nazwie `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Wersja pragma

Najlepszym rozwiązaniem jest wskazywać wersję trwałość przeznaczonych do pracy. Określanie wersji pomaga uniknąć niezgodności z przyszłych wersji trwałość. 

Dodaj następujące dyrektywy w wersji w górnej części `HelloBlockchain.sol` plik kodu kontraktu inteligentne.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Klasa bazowa

**WorkbenchBase** klasy bazowej umożliwia aplikacji Blockchain Workbench do tworzenia i aktualizowania umowy. Klasa bazowa jest wymagana dla kodu określonych kontraktu inteligentnych aplikacji Blockchain Workbench. Twoja Umowa musi dziedziczyć **WorkbenchBase** klasy bazowej.

W `HelloBlockchain.sol` inteligentne plik kodu kontraktu, należy dodać **WorkbenchBase** klasy na początku pliku. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
Klasa bazowa zawiera dwie ważne funkcje:

|Klasa bazowa — funkcja  | Przeznaczenie  | Kiedy wywołać metodę  |
|---------|---------|---------|
| ContractCreated() | Powiadamia Blockchain Workbench kontrakt została utworzona. | Przed opuszczeniem Konstruktor kontraktu |
| ContractUpdated() | Powiadamia Blockchain Workbench został zaktualizowany stan zamówienia | Przed opuszczeniem funkcji kontraktu |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfiguracja i relacji kodu kontraktu inteligentne

Blockchain Workbench używa pliku konfiguracji i pliku kodu kontraktu inteligentnych do tworzenia aplikacji łańcucha bloków. Ma relacji między co to jest zdefiniowany w konfiguracji i kod w umowie dotyczącej inteligentnych. Szczegóły umowy, funkcje, parametry i typy są wymagane do dopasowania do tworzenia aplikacji. Blockchain Workbench sprawdzi pliki znajdujące się przed tworzenia aplikacji. 

### <a name="contract"></a>Kontrakt

Blockchain Workbench kontraktów na potrzeby dziedziczyć **WorkbenchBase** klasy bazowej. Podczas deklarowania kontrakt, musisz przekazać nazwę aplikacji i nazwę przepływu pracy jako argumenty.

Dodaj **kontraktu** nagłówek, aby Twoje `HelloBlockchain.sol` plik kodu kontraktu inteligentne. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Twoja Umowa musi dziedziczyć **WorkbenchBase** klasy bazowej i przekazywać w parametrach **ApplicationName** i przepływ pracy **nazwa** zgodnie z definicją w konfiguracji plik. W takim przypadku nazwa aplikacji i nazwa przepływu pracy są takie same.

### <a name="state-variables"></a>Zmienne stanu

Zmienne stanu przechowywania wartości stanu dla każdego wystąpienia kontraktu. Zmienne stanu w kontrakcie usługi musi być zgodna właściwości przepływu pracy zdefiniowane w pliku konfiguracyjnym.

Dodaj zmienne stanu do Twojej umowy w swojej `HelloBlockchain.sol` plik kodu kontraktu inteligentne. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

Konstruktor definiuje parametry wejściowe dla nowego kontraktu inteligentne wystąpienia przepływu pracy. Konstruktor jest zadeklarowany jako funkcję o nazwie identycznej z nazwą kontraktu. Wymagane parametry dla konstruktora są definiowane jako parametry konstruktora w pliku konfiguracji. Liczbę, kolejność i typ parametrów musi być zgodny w obu plikach.

W funkcji konstruktora zapisu do wykonania przed utworzeniem kontrakt wszelka logika biznesowa. Na przykład zainicjować zmienne stanu z uruchomieniem wartości.

Przed opuszczeniem funkcji konstruktora, należy wywołać `ContractCreated()` funkcji. Ta funkcja powiadomi Blockchain Workbench kontrakt została utworzona.

Dodawanie funkcji konstruktora do Twojej umowy w swojej `HelloBlockchain.sol` plik kodu kontraktu inteligentne. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Funkcje

Funkcje są wykonywalnego logiki biznesowej, w ramach umowy. Wymagane parametry funkcji są definiowane jako parametry funkcji w pliku konfiguracji. Liczbę, kolejność i typ parametrów musi być zgodny w obu plikach. Funkcje są skojarzone z przejścia w przepływie pracy aplikacji Blockchain Workbench w pliku konfiguracji. Przejście jest akcję wykonywaną, aby przejść do kolejnego etapu przepływ pracy aplikacji, zgodnie z ustaleniami umowy.

Zapisu wszelka logika biznesowa ma zostać wykonana w funkcji. Na przykład modyfikując wartość zmiennej stanu.

Przed opuszczeniem funkcji, należy wywołać `ContractUpdated()` funkcji. Funkcja powiadamia Blockchain Workbench został zaktualizowany stan zamówienia. Jeśli chcesz cofnąć zmiany stanu w funkcji, należy wywołać revert(). Przywróć odrzucenia stanu zmiany wprowadzone od czasu ostatniego wywołania do ContractUpdated().

1. Dodaj następujące funkcje do Twojej umowy w swojej `HelloBlockchain.sol` plik kodu kontraktu inteligentne. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Zapisz swoje `HelloBlockchain.sol` plik kodu kontraktu inteligentne.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Dodawanie aplikacji łańcucha bloków na Blockchain Workbench

Aby dodać aplikację Blockchain Workbench łańcucha bloków, możesz przekazać konfiguracji i plików inteligentne umów do definiowania aplikacji.

1. W przeglądarce internetowej przejdź do adresu internetowego aplikacji Blockchain Workbench. Na przykład `https://{workbench URL}.azurewebsites.net/` aplikacji sieci web jest tworzona podczas wdrażania aplikacji Blockchain Workbench. Aby uzyskać informacje na temat znajdowania adres sieci web z aplikacji Blockchain Workbench, zobacz [Blockchain Workbench — adres URL sieci Web](deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administratora aplikacji Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz **aplikacje** > **nowe**. **Nową aplikację** zostanie wyświetlone okienko.
4. Wybierz **Przekaż konfigurację kontraktu** > **Przeglądaj** zlokalizować **HelloBlockchain.json** utworzony plik konfiguracji. Plik konfiguracji jest automatycznie weryfikowana. Wybierz **Pokaż** łącze, aby wyświetlić błędy sprawdzania poprawności. Usuń błędy sprawdzania poprawności, przed wdrożeniem aplikacji.
5. Wybierz **przekazywanie kodu kontraktu** > **Przeglądaj** zlokalizować **HelloBlockchain.sol** plik kodu kontraktu inteligentne. Plik kodu jest automatycznie weryfikowana. Wybierz **Pokaż** łącze, aby wyświetlić błędy sprawdzania poprawności. Usuń błędy sprawdzania poprawności, przed wdrożeniem aplikacji.
6. Wybierz **Wdróż** do tworzenia aplikacji łańcucha bloków na podstawie konfiguracji i pliki inteligentne kontraktu.

Wdrażanie aplikacji łańcucha bloków zajmuje kilka minut. Po zakończeniu wdrażania nowej aplikacji jest wyświetlany w **aplikacji**. 

> [!NOTE]
> Możesz również tworzyć aplikacje łańcucha bloków przy użyciu [API REST usługi Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Dodaj członków aplikacji łańcucha bloków

Dodaj członków aplikacji do aplikacji do inicjowania i podejmować działania dotyczące zamówień. Aby dodać członków do aplikacji, musisz być [administratora aplikacji Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Wybierz **aplikacje** > **Witaj, łańcuch bloków!**.
2. Liczba członków skojarzonych z aplikacją jest wyświetlana w prawym górnym rogu strony. W przypadku nowych aplikacji liczba elementów członkowskich będą równe zeru.
3. Wybierz **członków** linku w prawym górnym rogu strony. Zostanie wyświetlona bieżąca lista elementów członkowskich dla aplikacji.
4. Na liście członkostwa, wybierz **dodawać członków**.
5. Wybierz lub wprowadź nazwę elementu członkowskiego, który chcesz dodać. Tylko użytkownicy usługi Azure AD, które istnieją w dzierżawie Blockchain Workbench są wyświetlane. Jeśli użytkownik nie zostanie znaleziony, musisz [Dodaj użytkowników usługi Azure AD](manage-users.md#add-azure-ad-users).
6. Wybierz **roli** dla elementu członkowskiego. Dla pierwszego elementu członkowskiego, wybierz **obiektu żądającego** jako rolę.
7. Wybierz **Dodaj** można dodać elementu członkowskiego z roli skojarzone z aplikacją.
8. Dodaj inny członek do aplikacji przy użyciu **obiektu odpowiadającego w trybie** roli.

Aby uzyskać więcej informacji na temat zarządzania użytkownikami w aplikacji Blockchain Workbench zobacz [Zarządzanie użytkownikami w aplikacji Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zostanie utworzona Podstawowa aplikacja żądań i odpowiedzi. Informacje na temat korzystania z aplikacji, przejdź do następnego artykułu porad.

> [!div class="nextstepaction"]
> [Za pomocą aplikacji łańcucha bloków](use.md)
