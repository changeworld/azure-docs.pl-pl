---
title: Tworzenie aplikacji blockchain w Azure Blockchain Workbench
description: Jak utworzyć aplikację blockchain w Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a4b704f433f02afcff7b94f98c19a478caaa02b2
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808064"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Tworzenie aplikacji blockchain w Azure Blockchain Workbench

Azure Blockchain Workbench umożliwia tworzenie aplikacji blockchain, które reprezentują wieloosobowa przepływów pracy zdefiniowanych przez konfigurację i kodu kontraktu inteligentne.

Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie aplikacji blockchain
> * Utwórz plik kodu kontraktu inteligentne
> * Dodawanie aplikacji blockchain do Blockchain Workbench
> * Dodawanie członków do aplikacji blockchain

## <a name="prerequisites"></a>Wymagania wstępne

* Deployment Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [deployment Azure Blockchain Workbench](blockchain-workbench-deploy.md) szczegółowe informacje na temat wdrażania.
* Użytkownicy usługi Azure Active Directory dzierżawca skojarzony z Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [Dodaj użytkowników usługi Azure AD w Azure Blockchain Workbench](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Konto administratora Blockchain Workbench. Aby uzyskać więcej informacji, zobacz temat Dodawanie [administratorów Blockchain Workbench w Azure Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Witaj, Blockchain!

Umożliwia tworzenia podstawowej aplikacji, w którym obiekt żądający wysyła żądanie i obiekt odpowiadający wysyłania odpowiedzi na żądanie. Można na przykład żądania, "Witaj, jak jesteś?" i umożliwia odpowiedzi można "Jestem doskonałe!". Zarówno żądania i odpowiedzi są rejestrowane na blockchain podstawowej. 

Wykonaj kroki, aby utworzyć pliki aplikacji lub możesz [Pobierz przykład z usługi GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Plik konfiguracji

Metadane konfiguracji definiują wysokiego poziomu przepływów pracy i modelu interakcji blockchain aplikacji. Metadane konfiguracji reprezentuje etapach przepływu pracy i modelu interakcji aplikacji blockchain.

1. W edytorze Ulubione, Utwórz plik o nazwie `HelloBlockchain.json`.
2. Dodaj następujące JSON, aby zdefiniować konfigurację aplikacji blockchain.

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

Plik konfiguracji ma kilka sekcji. Szczegółowe informacje na temat każdej sekcji są następujące:

### <a name="application-metadata"></a>Metadane aplikacji

Na początku pliku konfiguracji zawiera informacje o aplikacji, w tym nazwa i opis aplikacji.

### <a name="application-roles"></a>Role aplikacji

W sekcji role aplikacji definiuje role użytkowników, którzy mogą pełnić lub uczestniczyć w aplikacji blockchain. Możesz zdefiniować zestaw różne role oparte na funkcjonalność. W scenariuszu żądanie odpowiedź jest różnica między funkcjami podmiotu żądającego jako jednostki, która tworzy żądania oraz obiekt odpowiadający jako jednostki, która tworzy odpowiedzi.

### <a name="workflows"></a>Przepływy

Przepływy pracy określają etapów i akcje kontraktu. W scenariuszu żądań i odpowiedzi pierwszego etap (stan) przepływu pracy jest obiekt żądający (rola) podejmuje działania (przejście), Wyślij żądanie (funkcja). Obiekt odpowiadający (rola) podejmuje działania (przejście) wysyłania odpowiedzi (funkcja) jest kolejnego etapu (stan). Przepływ pracy aplikacji może obejmować właściwości, funkcje, i stanów wymagane opisano przepływ kontrakt. 

Aby uzyskać więcej informacji o zawartość plików konfiguracyjnych, zobacz [odwołania konfiguracji przepływu pracy Blockchain Azure](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>Plik kodu kontraktu inteligentne

Kontrakty inteligentne reprezentują logiki biznesowej aplikacji blockchain. Obecnie Blockchain Workbench obsługuje Ethereum księgi blockchain. Używa Ethereum [trwałość](https://solidity.readthedocs.io) jako jego język programowania własnym wymuszenie logikę biznesową dla kontraktów inteligentne.

Kontrakty inteligentne w trwałość są podobne do klasy w językach zorientowane obiektowo. Każdej umowy zawiera stan i funkcji do zaimplementowania etapów i akcje inteligentne kontraktu.

W edytorze Ulubione, Utwórz plik o nazwie `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Wersja pragma

Najlepszym rozwiązaniem wskazywać wersję przeznaczonych trwałość. Określanie wersji pomaga uniknąć niezgodności w przyszłych wersjach trwałość. 

Dodaj następujące pragma wersji w górnej części `HelloBlockchain.sol` inteligentne kontraktu pliku kodu.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Klasa podstawowa

**WorkbenchBase** Blockchain Workbench do tworzenia i aktualizacji kontrakt umożliwia klasy podstawowej. Klasa podstawowa jest wymagana dla Blockchain Workbench określonym kontraktem inteligentne kodu. Umowa musi dziedziczyć **WorkbenchBase** klasy podstawowej.

W `HelloBlockchain.sol` inteligentne pliku kodu kontraktu, Dodaj **WorkbenchBase** klasy na początku pliku. 

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
Klasa podstawowa zawiera dwie ważne funkcje:

|Klasa podstawowa funkcja  | Przeznaczenie  | Kiedy wywołać metodę  |
|---------|---------|---------|
| ContractCreated() | Powiadamia Blockchain Workbench kontrakt został utworzony. | Przed zamknięciem konstruktora kontraktu |
| ContractUpdated() | Powiadamia Blockchain Workbench został zaktualizowany stan kontraktu | Przed opuszczeniem funkcji kontraktu |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfiguracja i relacji kodu kontraktu inteligentne

Blockchain Workbench korzysta z pliku konfiguracji i pliku kodu kontraktu inteligentne do tworzenia aplikacji blockchain. Brak relacji między co to jest zdefiniowany w konfiguracji i kodu w umowie dotyczącej inteligentnych. Szczegóły umowy, funkcje, parametry i typy są wymagane do dopasowania do tworzenia aplikacji. Blockchain Workbench sprawdzi pliki przed tworzenia aplikacji. 

### <a name="contract"></a>Kontrakt

Aby Blockchain Workbench kontrakty muszą dziedziczyć **WorkbenchBase** klasy podstawowej. Przy deklarowaniu kontraktu, musisz przekazać nazwę aplikacji, a nazwa przepływu pracy jako argumenty.

Dodaj **kontraktu** nagłówka do Twojej `HelloBlockchain.sol` inteligentne kontraktu pliku kodu. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Umowa musi dziedziczyć **WorkbenchBase** klasa podstawowa i Podaj parametry **ApplicationName** i przepływ pracy **nazwa** zgodnie z definicją w konfiguracji plik. W takim przypadku nazwa aplikacji, a nazwa przepływu pracy są takie same.

### <a name="state-variables"></a>Zmienne stanu

Zmienne stanu przechowywania wartości stanu dla każdego wystąpienia kontraktu. Zmienne stanu Umowa musi odpowiadać właściwości przepływu pracy zdefiniowane w pliku konfiguracyjnym.

Dodaj zmienne stanu do Umowy w Twojej `HelloBlockchain.sol` inteligentne kontraktu pliku kodu. 

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

Konstruktor definiuje parametry wejściowe dla kontraktu inteligentne nowe wystąpienie przepływu pracy. Konstruktor jest zadeklarowany jako funkcję z taką samą nazwę jak kontraktu. Wymagane parametry dla konstruktora są definiowane jako parametry konstruktora w pliku konfiguracji. Liczba, kolejność i typ parametrów muszą być zgodne w obu plikach.

W funkcji konstruktora zapisu należy wykonać przed utworzeniem kontrakt wszelka logika biznesowa. Na przykład zainicjować zmienne stanu z uruchomieniem wartości.

Przed opuszczeniem funkcji konstruktora, wywołaj `ContractCreated()` funkcji. Ta funkcja powiadamia Blockchain Workbench kontrakt został utworzony.

Dodaj Konstruktor do Umowy w Twojej `HelloBlockchain.sol` inteligentne kontraktu pliku kodu. 

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

Funkcje są wykonywalnego logiki biznesowej w ramach umowy. Wymaganych parametrów funkcji są definiowane jako parametry funkcji w pliku konfiguracji. Liczba, kolejność i typ parametrów muszą być zgodne w obu plikach. Funkcje są skojarzone z przejścia w przepływie pracy Blockchain Workbench w pliku konfiguracji. Przejście jest akcję wykonywaną, aby przejść do kolejnego etapu przepływ pracy aplikacji, zgodnie z ustaleniami umowy.

Zapisu wszelka logika biznesowa ma zostać wykonana w funkcji. Na przykład modyfikując wartość zmiennej stanu.

Przed opuszczeniem funkcji, należy wywołać `ContractUpdated()` funkcji. Funkcja powiadamia Blockchain Workbench został zaktualizowany stan kontraktu. Jeśli chcesz cofnąć zmiany stanu w funkcji, należy wywołać revert(). Przywróć stan odrzucenia: zmiany wprowadzone od czasu ostatniego wywołania ContractUpdated().

1. Dodaj następujące funkcje do Umowy w Twojej `HelloBlockchain.sol` inteligentne kontraktu pliku kodu. 

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

2. Zapisz z `HelloBlockchain.sol` inteligentne kontraktu pliku kodu.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Dodaj aplikację blockchain Blockchain Workbench

Aby dodać aplikację blockchain do Blockchain Workbench, możesz przekazać konfiguracji i kontraktu inteligentne plików do definiowania aplikacji.

1. W przeglądarce sieci web przejdź do adresu internetowego Blockchain Workbench. Na przykład `https://{workbench URL}.azurewebsites.net/` aplikacji sieci web jest tworzona podczas wdrażania Blockchain Workbench. Aby uzyskać informacje na temat można znaleźć sieci Blockchain Workbench adres sieci web, zobacz [Blockchain Workbench — adres URL sieci Web](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administratora Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz **aplikacji** > **nowe**. **Nowej aplikacji** jest wyświetlany w okienku.
4. Wybierz **przekazać konfiguracji kontraktu** > **Przeglądaj** zlokalizować **HelloBlockchain.json** utworzony plik konfiguracji. Automatycznie jest weryfikowana w pliku konfiguracji. Wybierz **Pokaż** łącze, aby wyświetlić błędy sprawdzania poprawności. Usuń błędy sprawdzania poprawności, przed wdrożeniem aplikacji.
5. Wybierz **przekazywanie kodu kontraktu** > **Przeglądaj** zlokalizować **HelloBlockchain.sol** inteligentne kontraktu pliku kodu. Automatycznie jest weryfikowana w pliku kodu. Wybierz **Pokaż** łącze, aby wyświetlić błędy sprawdzania poprawności. Usuń błędy sprawdzania poprawności, przed wdrożeniem aplikacji.
6. Wybierz **Wdróż** do utworzenia aplikacji blockchain na podstawie konfiguracji i pliki inteligentne kontraktu.

Wdrożenie aplikacji blockchain zajmuje kilka minut. Po zakończeniu wdrażania Nowa aplikacja jest wyświetlana w **aplikacji**. 

> [!NOTE]
> Można również utworzyć aplikacje blockchain przy użyciu [interfejsu API REST Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Dodawanie członków aplikacji blockchain

Dodawanie członków aplikacji do aplikacji do inicjowania i podjąć działania w umowach. Aby dodać członków aplikacji, musisz być [administratora Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Wybierz **aplikacji** > **Hello, Blockchain!**.
2. Liczba elementów członkowskich skojarzonych aplikacji jest wyświetlany w prawym górnym rogu strony. Dla nowej aplikacji do liczby elementów członkowskich będą miały wartość zero.
3. Wybierz **członków** łącze w prawym górnym rogu strony. Zostanie wyświetlona lista bieżącego elementów członkowskich dla aplikacji.
4. Na liście członkostwa, wybierz **dodawać członków**.
5. Wybierz lub wprowadź nazwę elementu członkowskiego, który chcesz dodać. Tylko Azure użytkowników usługi AD, które istnieją w dzierżawie Blockchain Workbench są wyświetlane. Jeśli użytkownik nie zostanie znaleziony, musisz [Dodaj użytkowników usługi Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Wybierz **roli** dla elementu członkowskiego. Dla pierwszego elementu członkowskiego, wybierz **obiektu żądającego** rolę.
7. Wybierz **Dodaj** można dodać elementu członkowskiego z rolą skojarzone z aplikacją.
8. Dodawanie do aplikacji z innego elementu członkowskiego **obiektu odpowiadającego w trybie** roli.

Aby uzyskać więcej informacji o zarządzaniu użytkownikami w Blockchain Workbench, zobacz [Zarządzanie użytkownikami w Azure Blockchain Workbench](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zostanie utworzona podstawowej aplikacji żądań i odpowiedzi. Aby dowiedzieć się, jak korzystać z aplikacji, nadal dalej artykule.

> [!div class="nextstepaction"]
> [Przy użyciu aplikacji blockchain](blockchain-workbench-use.md)
