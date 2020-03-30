---
title: Korzystanie z interfejsów API REST programu Azure Blockchain Workbench
description: Scenariusze dotyczące korzystania z interfejsu API REST systemu Azure Blockchain Workbench Preview
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672750"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Korzystanie z interfejsu API REST systemu Azure Blockchain Workbench Preview

Interfejs API REST usługi Azure Blockchain Workbench Preview zapewnia deweloperom i pracownikom informacji sposób tworzenia rozbudowanych integracji z aplikacjami blockchain. W tym artykule przedstawiono kilka scenariuszy, jak używać interfejsu API REST workbench. Załóżmy na przykład, że chcesz utworzyć niestandardowy klient łańcucha bloków, który umożliwia zalogowanym użytkownikom wyświetlanie przypisanych im aplikacji blockchain i interakcję z nimi. Klient może używać interfejsu API blockchain workbench do wyświetlania wystąpień umowy i podejmowania działań na inteligentnych kontraktach.

## <a name="blockchain-workbench-api-endpoint"></a>Punkt końcowy interfejsu API systemu roboczego blockchain

Interfejsy API systemu Blockchain Workbench są dostępne za pośrednictwem punktu końcowego dla wdrożenia. Aby uzyskać adres URL punktu końcowego interfejsu API dla wdrożenia:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
1. Wybierz nazwę grupy zasobów wdrożonego systemu Blockchain Workbench.
1. Wybierz nagłówek kolumny **TYP,** aby posortować listę alfabetycznie według typu.
1. Istnieją dwa zasoby z **typem App Service**. Wybierz zasób typu **App Service** *z* sufiksem "-api".
1. W **przeglądzie**usługi aplikacji skopiuj wartość **adresu URL,** która reprezentuje adres URL punktu końcowego interfejsu API do wdrożonego workbencha blockchain.

    ![Adres URL punktu końcowego interfejsu API usługi aplikacji](media/use-api/app-service-api.png)

## <a name="authentication"></a>Uwierzytelnianie

Żądania do interfejsu API REST systemu Blockchain Workbench są chronione za pomocą usługi Azure Active Directory (Azure AD).

Aby wykonać uwierzytelnione żądanie do interfejsów API REST, kod klienta wymaga uwierzytelniania z prawidłowymi poświadczeniami przed wywołaniem interfejsu API. Uwierzytelnianie jest koordynowane między różnymi podmiotami przez usługę Azure AD i zapewnia klientowi [token dostępu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) jako dowód uwierzytelniania. Token jest następnie wysyłany w nagłówku autoryzacji HTTP żądań interfejsu API REST. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz [Usługa Azure Active Directory dla deweloperów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

Zobacz [przykłady interfejsu API REST,](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) aby uzyskać przykłady uwierzytelniania.

## <a name="using-postman"></a>Używanie narzędzia Postman

Jeśli chcesz przetestować lub eksperymentować z interfejsami API systemu Roboczego, możesz użyć [postmana](https://www.postman.com) do nawiązywać wywołania interfejsu API do wdrożenia. [Pobierz przykładową kolekcję postman żądań interfejsu API workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) z usługi GitHub. Zobacz plik README, aby uzyskać szczegółowe informacje na temat uwierzytelniania i używania przykładowych żądań interfejsu API.

## <a name="create-an-application"></a>Tworzenie aplikacji

Za pomocą dwóch wywołań interfejsu API, aby utworzyć aplikację Blockchain Workbench. Ta metoda może być wykonywana tylko przez użytkowników, którzy są administratorami workbench.

Użyj [interfejsu API Applications POST,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) aby przekazać plik JSON aplikacji i uzyskać identyfikator aplikacji.

### <a name="applications-post-request"></a>Wnioski POST wniosek

Użyj **appFile** parametr, aby wysłać plik konfiguracji jako część treści żądania.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Aplikacje POST odpowiedź

Identyfikator utworzonej aplikacji jest zwracany w odpowiedzi. Identyfikator aplikacji jest potrzebny do skojarzenia pliku konfiguracyjnego z plikiem kodu podczas wywoływania następnego interfejsu API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Kod umowy POST żądanie

Użyj [interfejsu API POST kodu kontraktu aplikacji,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) przekazując identyfikator aplikacji, aby przekazać plik kodu Solidity aplikacji. Ładunkiem może być pojedynczy plik Solidity lub spakowany plik zawierający pliki Solidity.

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| {identyfikator aplikacji} | Zwraca wartość z interfejsu API POST aplikacji. |
| {ledgerId} | Indeks księgi. Wartość jest zwykle 1. Można również sprawdzić [tabelę Księga](data-sql-management-studio.md) dla wartości. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Odpowiedź POST z kodem umowy

W przypadku powodzenia odpowiedź zawiera utworzony identyfikator kodu kontraktu z [tabeli Kod kontraktu](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Przypisywanie ról do użytkowników

Użyj [przypisania roli Aplikacje POST INTERFEJSU API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) przekazując identyfikator aplikacji, identyfikator użytkownika i identyfikator roli aplikacji, aby utworzyć mapowanie między użytkownikami w określonej aplikacji łańcucha bloków. Ta metoda może być wykonywana tylko przez użytkowników, którzy są administratorami workbench.

### <a name="role-assignments-post-request"></a>Przypisania ról ŻĄDANIE POST

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| {identyfikator aplikacji} | Zwraca wartość z interfejsu API Applications POST. |
| {identyfikator użytkownika} | Wartość identyfikatora użytkownika z [tabeli Użytkownik](data-sql-management-studio.md). |
| {applicationRoleId} | Wartość identyfikatora roli aplikacji skojarzona z identyfikatorem aplikacji z [tabeli ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Przypisania ról ODPOWIEDŹ POST

Jeśli odpowiedź się powiedzie, odpowiedź zawiera utworzony identyfikator przypisania roli z [tabeli RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Wyświetlanie listy aplikacji

Użyj [interfejsu API Applications GET,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) aby pobrać wszystkie aplikacje Blockchain Workbench dla użytkownika. W tym przykładzie zalogowany użytkownik ma dostęp do dwóch aplikacji:

- [Transfer zasobów](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Transport mrożonek](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Żądania GET aplikacji

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Odpowiedzi get aplikacji

Odpowiedź zawiera listę wszystkich aplikacji blockchain, do których użytkownik ma dostęp w Blockchain Workbench. Administratorzy Blockchain Workbench otrzymują każdą aplikację blockchain. Administratorzy systemu non-Workbench otrzymują wszystkie aplikacje łańcucha bloków, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub skojarzoną rolę wystąpienia inteligentnego kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Wyświetlanie listy przepływów pracy dla aplikacji

Użyj [interfejsu API get przepływów pracy aplikacji,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) aby wyświetlić listę wszystkich przepływów pracy określonej aplikacji blockchain, do której użytkownik ma dostęp w blockchain workbench. Każda aplikacja łańcucha bloków ma co najmniej jeden przepływ pracy, a każdy przepływ pracy ma zero lub więcej wystąpień kontraktu inteligentnego. W przypadku aplikacji klienckiej łańcucha bloków, która ma tylko jeden przepływ pracy, zalecamy pominięcie przepływu środowiska użytkownika, który umożliwia użytkownikom wybranie odpowiedniego przepływu pracy.

### <a name="application-workflows-request"></a>Żądanie przepływów pracy aplikacji

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Odpowiedź przepływów pracy aplikacji

Administratorzy Blockchain Workbench otrzymują każdy przepływ pracy blockchain. Administratorzy nieobjęcie roboczym otrzymują wszystkie przepływy pracy, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub są skojarzone z rolą wystąpienia inteligentnego kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Tworzenie wystąpienia kontraktu

Użyj [interfejsu API post kontrakty V2,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) aby utworzyć nowe wystąpienie inteligentnego kontraktu dla przepływu pracy. Użytkownicy mogą tworzyć nowe wystąpienie inteligentnego kontraktu tylko wtedy, gdy użytkownik jest skojarzony z rolą aplikacji, która może zainicjować wystąpienie inteligentnego kontraktu dla przepływu pracy.

> [!NOTE]
> W tym przykładzie jest używana wersja 2 interfejsu API. Interfejsy API umowy w wersji 2 zapewniają większą szczegółowość dla skojarzonych pól ProvisioningStatus.

### <a name="contracts-post-request"></a>Wnioski o umowy POST

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| {workflowId} | Wartość identyfikatora przepływu pracy jest identyfikatorem konstruktora kontraktu z [tabeli Przepływ pracy](data-sql-management-studio.md). |
| {contractCodeId} | Wartość identyfikatora kodu kontraktu z [tabeli Kod kontraktu](data-sql-management-studio.md). Skoreluj identyfikator aplikacji i identyfikator księgi dla wystąpienia kontraktu, które chcesz utworzyć. |
| {connectionId} | Wartość identyfikatora połączenia z [tabeli Połączenie](data-sql-management-studio.md). |

Dla treści żądania należy ustawić wartości przy użyciu następujących informacji:

| Parametr | Wartość |
|-----------|-------|
| workflowFunctionID | Identyfikator z [tabeli WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Para wartości nazwy parametrów przekazanych do konstruktora. Dla każdego parametru należy użyć wartości workflowFunctionParameterID z tabeli [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Odpowiedzi post umów

W przypadku powodzenia interfejs API przypisywania ról zwraca adres ContractActionID z [tabeli ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Wyświetlanie listy wystąpień kontraktu inteligentnego dla przepływu pracy

Użyj [interfejsu API kontrakty GET,](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) aby wyświetlić wszystkie wystąpienia inteligentnego kontraktu dla przepływu pracy. Możesz też zezwolić użytkownikom na głębokie zagłębienie się w dowolne z pokazanych wystąpień inteligentnego kontraktu.

### <a name="contracts-request"></a>Wniosek o umowy

W tym przykładzie zakładamy, ze użytkownik chce wejść w interakcję z jednym z wystąpień kontraktu inteligentnego, aby wykonać akcję.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Odpowiedź na umowy

Odpowiedź zawiera listę wszystkich wystąpień umowy inteligentnej określonego przepływu pracy. Administratorzy systemu roboczego otrzymują wszystkie wystąpienia inteligentnych umów. Administratorzy systemu non-Workbench otrzymują każde wystąpienie inteligentnego kontraktu, dla którego mają co najmniej jedną skojarzoną rolę aplikacji lub są skojarzone z rolą wystąpienia inteligentnego kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Wyświetlanie listy dostępnych akcji dla kontraktu

Użyj [interfejsu API GET akcji kontraktu,](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) aby wyświetlić dostępne akcje użytkownika, biorąc pod uwagę stan kontraktu. 

### <a name="contract-action-request"></a>Żądanie działania umowy

W tym przykładzie użytkownik przegląda wszystkie dostępne akcje dla nowego kontraktu inteligentnego, który stworzyli.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Odpowiedź na działanie kontraktu

Odpowiedź zawiera listę wszystkich akcji, które użytkownik może wykonać dla danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego.

* Zmień: umożliwia użytkownikowi zmodyfikowanie opisu i ceny zasobu.
* Wypowiedzenie: Umożliwia użytkownikowi rozwiązanie umowy z zasobem.

Użytkownicy uzyskują wszystkie odpowiednie akcje, jeśli użytkownik ma skojarzoną rolę aplikacji lub jest skojarzony z rolą wystąpienia kontraktu inteligentnego dla bieżącego stanu określonego wystąpienia kontraktu inteligentnego.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Wykonywanie akcji dla kontraktu

Użyj [interfejsu API POST akcji kontraktu,](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) aby podjąć działania dla określonego wystąpienia umowy inteligentnej.

### <a name="contract-action-post-request"></a>Żądanie POST działania umowy

W takim przypadku należy wziąć pod uwagę scenariusz, w którym użytkownik chciałby zmodyfikować opis i cenę środka trwałego.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Użytkownicy mogą wykonać akcję tylko w przypadku danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego i skojarzonej roli aplikacji lub roli wystąpienia kontraktu inteligentnego użytkownika.

### <a name="contract-action-post-response"></a>Odpowiedź post działania kontraktu

Jeśli tworzenie zakończy się pomyślnie, zwrócona zostanie odpowiedź HTTP 200 OK bez treści odpowiedzi.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje referencyjne dotyczące interfejsów API programu Blockchain Workbench, zobacz [odwołanie do interfejsu API REST systemu Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).
