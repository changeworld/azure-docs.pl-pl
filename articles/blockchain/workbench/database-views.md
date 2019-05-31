---
title: Widoki bazy danych w aplikacji Azure Blockchain Workbench
description: Omówienie bazy danych SQL Azure Blockchain Workbench widoki bazy danych.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9071cf524a0f3d319d108cb5c961fa886cf8747f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399904"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Widoki bazy danych w aplikacji Azure Blockchain Workbench

Azure Blockchain Workbench dostarcza dane z rejestrów rozproszonych do *poza łańcuchem* bazy danych SQL database. Bazy danych poza łańcuchem sprawia, że można używać języków SQL i istniejących narzędzi, takich jak [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), interakcję z danymi łańcucha bloków.

Azure Blockchain Workbench zawiera zestaw widoków bazy danych, które zapewniają dostęp do danych, które będą pomocne podczas wykonywania zapytania. Widoki te są silnie nieznormalizowany, aby ułatwić szybkie rozpoczęcie tworzyć raporty, analizy i w przeciwnym razie korzystanie z rejestrowanych danych łańcucha bloków z istniejącymi narzędziami i bez konieczności Ponowne szkolenie pracowników bazy danych.

Ta sekcja zawiera omówienie widoki bazy danych i danych, które zawierają.

> [!NOTE]
> Bezpośrednie użycie tabel bazy danych w bazie danych poza te widoki, ile jest to możliwe, nie jest obsługiwane.
>

## <a name="vwapplication"></a>vwApplication

Ten widok zawiera szczegółowe informacje dotyczące **aplikacje** zostały przekazane do aplikacji Azure Blockchain Workbench.

| Name (Nazwa)                             | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                  | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDescription           | nvarchar(255) | Tak         | Opis aplikacji |
| ApplicationDisplayName           | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled               | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br /> **Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych. |
| UploadedDtTm                     | datetime2(7)  | Nie          | Data i godzina, który został przekazany kontraktu |
| UploadedByUserId                 | int           | Nie          | Identyfikator użytkownika, który przekazywane aplikacji |
| UploadedByUserExternalId         | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który przekazywane aplikacji. Domyślnie ten identyfikator jest użytkownika z usługi Azure Active Directory przez konsorcjum.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nie          | Określa bieżący stan aprowizacji procesu dla użytkownika. Możliwe wartości to: <br />0 – użytkownika została utworzona przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zaaprowizowanym                         |
| UploadedByUserFirstName          | nvarchar(50)  | Tak         | Imię użytkownika, który kontrakt przekazany |
| UploadedByUserLastName           | nvarchar(50)  | Tak         | Nazwisko użytkownika, który kontrakt przekazany |
| UploadedByUserEmailAddress       | nvarchar(255) | Tak         | Adres e-mail użytkownika, który kontrakt przekazany |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ten widok zawiera szczegółowe informacje dotyczące ról, które zostały zdefiniowane w aplikacji Azure Blockchain Workbench.

W *transferu zawartości* aplikacji, na przykład ról, takich jak *kupujący* i *sprzedawcy* ról może być zdefiniowana.

| Name (Nazwa)                   | Type             | Może mieć wartości Null | Opis                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nie          | Unikatowy identyfikator aplikacji           |
| ApplicationName        | nvarchar(50)     | Nie          | Nazwa aplikacji                       |
| ApplicationDescription | nvarchar(255)    | Yes         | Opis aplikacji                  |
| ApplicationDisplayName | nvarchar(255)    | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika      |
| RoleId                 | int              | Nie          | Unikatowy identyfikator dla roli w aplikacji |
| RoleName               | nvarchar50)      | Nie          | Nazwa roli                              |
| RoleDescription        | description(255) | Tak         | Opis roli                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ten widok zawiera szczegółowe informacje dotyczące ról, które zostały zdefiniowane w aplikacji Azure Blockchain Workbench i użytkowników skojarzonych z nimi.

W *transferu zawartości* aplikacji, na przykład *Jan Kowalski* mogą być skojarzone z *kupujący* roli.

| Name (Nazwa)                       | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nie          | Unikatowy identyfikator aplikacji                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Nie          | Nazwa aplikacji                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Yes         | Opis aplikacji                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nie          | Unikatowy identyfikator dla roli w aplikacji                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nie          | Nazwa roli                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Tak         | Opis roli                                                                                                                                                                                                             |
| UserId                     | int           | Nie          | Identyfikator użytkownika skojarzony z rolą |
| UserExternalId             | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który jest skojarzony z rolą. Domyślnie ten identyfikator jest użytkownika z usługi Azure Active Directory przez konsorcjum.                                                                     |
| UserProvisioningStatus     | int           | Nie          | Określa bieżący stan aprowizacji procesu dla użytkownika. Możliwe wartości to: <br />0 – użytkownika została utworzona przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zaaprowizowanym |
| UserFirstName              | nvarchar(50)  | Tak         | Imię użytkownika, który jest skojarzony z rolą |
| UserLastName               | nvarchar(255) | Yes         | Nazwisko użytkownika, który jest skojarzony z rolą |
| UserEmailAddress           | nvarchar(255) | Tak         | Adres e-mail użytkownika, który jest skojarzony z rolą |

## <a name="vwconnectionuser"></a>vwConnectionUser

Ten widok zawiera szczegółowe informacje dotyczące połączenia zdefiniowane w aplikacji Azure Blockchain Workbench i użytkowników skojarzonych z nimi. Dla każdego połączenia ten widok zawiera następujące dane:

-   Szczegóły skojarzone księgi
-   Informacje skojarzone z użytkownikami

| Name (Nazwa)                     | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nie          | Unikatowy identyfikator połączenia w aplikacji Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Nie          | Adres url punktu końcowego połączenia |
| ConnectionFundingAccount | nvarchar(255) | Tak         | Finansowania konto skojarzone z połączeniem, jeśli ma to zastosowanie |
| LedgerId                 | int           | Nie          | Unikatowy identyfikator rejestr |
| LedgerName               | nvarchar(50)  | Nie          | Nazwa rejestru |
| LedgerDisplayName        | nvarchar(255) | Nie          | Nazwa rejestru do wyświetlenia w interfejsie użytkownika |
| UserId                   | int           | Nie          | Identyfikator użytkownika skojarzony z tym połączeniem |
| UserExternalId           | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który jest skojarzony z tym połączeniem. Domyślnie ten identyfikator jest użytkownika z usługi Azure Active Directory przez konsorcjum. |
| UserProvisioningStatus   | int           | Nie          |Określa bieżący stan aprowizacji procesu dla użytkownika. Możliwe wartości to: <br />0 – użytkownika została utworzona przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zaaprowizowanym |
| UserFirstName            | nvarchar(50)  | Yes         | Imię użytkownika, który jest skojarzony z tym połączeniem |
| UserLastName             | nvarchar(255) | Tak         | Nazwisko użytkownika, który jest skojarzony z tym połączeniem |
| UserEmailAddress         | nvarchar(255) | Tak         | Adres e-mail użytkownika, który jest skojarzony z tym połączeniem |

## <a name="vwcontract"></a>vwContract

Ten widok udostępnia szczegółowe informacje o wdrożonych umów. Dla każdej umowy ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Skojarzone księgi implementacji dla tej funkcji
-   Szczegóły dotyczące użytkownika, który zainicjował akcję
-   Szczegółowe informacje związane z bloku łańcucha bloków i transakcji

| Name (Nazwa)                                     | Type           | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nie          | Unikatowy identyfikator połączenia w aplikacji Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Nie          | Adres url punktu końcowego połączenia |
| ConnectionFundingAccount                 | nvarchar(255)  | Tak         | Finansowania konto skojarzone z połączeniem, jeśli ma to zastosowanie |
| LedgerId                                 | int            | Nie          | Unikatowy identyfikator rejestr |
| LedgerName                               | nvarchar(50)   | Nie          | Nazwa rejestru |
| LedgerDisplayName                        | nvarchar(255)  | Nie          | Nazwa rejestru do wyświetlenia w interfejsie użytkownika |
| ApplicationId                            | int            | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName                   | nvarchar (255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                       | bit            | Nie          | Określa, czy aplikacja jest obecnie włączona.<br /> **Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych.  |
| WorkflowId                               | int            | Nie          | Unikatowy identyfikator dla przepływu pracy skojarzonego z umową |
| WorkflowName                             | nvarchar(50)   | Nie          | Nazwa przepływu pracy skojarzonego z umową |
| WorkflowDisplayName                      | nvarchar(255)  | Nie          | Nazwa przepływu pracy skojarzonego z kontraktem wyświetlane w interfejsie użytkownika |
| WorkflowDescription                      | nvarchar(255)  | Yes         | Opis przepływu pracy skojarzonego z umową |
| ContractCodeId                           | int            | Nie          | Unikatowy identyfikator dla kodu kontraktu skojarzonej z kontraktem |
| ContractFileName                         | int            | Nie          | Nazwa pliku zawierającego kod inteligentne umowy, dla tego przepływu pracy. |
| ContractUploadedDtTm                     | int            | Nie          | Data i godzina, który został przekazany kodu kontraktu |
| ContractId                               | int            | Nie          | Unikatowy identyfikator zamówienia |
| ContractProvisioningStatus               | int            | Nie          | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla kontraktu. Możliwe wartości to: <br />0 – kontrakt utworzonych przy użyciu interfejsu API w bazie danych<br />1 — Umowa została wysłana do rejestru<br />2 — Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - kontrakt nie udało się wdrożyć w księdze<br />5 — Umowy został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. Dla wstecznej zgodności w bieżącej wersji widoku **vwContractV0** jest dostępna, że obsługuje tylko wartości 0 do 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | Adres e-mail użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserId                 | int            | Nie          | Zewnętrzny identyfikator użytkownika, który kontrakt wdrożone. Domyślnie ten identyfikator jest identyfikator guid reprezentujący identyfikator Azure Active Directory dla użytkownika.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nie          | Zewnętrzny identyfikator użytkownika, który kontrakt wdrożone. Domyślnie ten identyfikator jest identyfikator guid reprezentujący identyfikator Azure Active Directory dla użytkownika.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nie          | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości to: <br />0 – użytkownika została utworzona przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych <br />2 — użytkownik jest w pełni zaaprowizowanym                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Tak         | Imię użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserLastName           | nvarchar(255)  | Tak         | Nazwisko użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Yes         | Adres e-mail użytkownika, który jest wdrożony umowy |

## <a name="vwcontractaction"></a>vwContractAction

Ten widok zaspokajają większość informacji dotyczących działania podjęte w kontraktach i zaprojektowano w celu ułatwienia łatwo typowych scenariuszy raportowania. Dla każdej akcji podjętej ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Skojarzone kontraktu inteligentne definicji funkcji i parametrów
-   Skojarzone księgi implementacji dla tej funkcji
-   Konkretne wystąpienie wartości dla parametrów
-   Szczegóły dotyczące użytkownika, który zainicjował akcję
-   Szczegółowe informacje związane z bloku łańcucha bloków i transakcji

| Name (Nazwa)                                     | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName                   | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                       | bit           | Nie          | To pole wskazuje, czy aplikacja jest obecnie włączona. Pamiętaj — mimo że aplikacja może pojawiają się jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych.                                                  |
| WorkflowId                               | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                             | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                      | nvarchar(255) | Nie          | Nazwa przepływu pracy do wyświetlenia w interfejsie użytkownika |
| WorkflowDescription                      | nvarchar(255) | Tak         | Opis przepływu pracy |
| ContractId                               | int           | Nie          | Unikatowy identyfikator zamówienia |
| ContractProvisioningStatus               | int           | Nie          | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla kontraktu. Możliwe wartości to: <br />0 – kontrakt utworzonych przy użyciu interfejsu API w bazie danych<br />1 — Umowa została wysłana do rejestru<br />2 — Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - kontrakt nie udało się wdrożyć w księdze<br />5 — Umowy został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. Dla wstecznej zgodności w bieżącej wersji widoku **vwContractActionV0** jest dostępna, że obsługuje tylko wartości 0 do 2. |
| ContractCodeId                           | int           | Nie          | Unikatowy identyfikator dla implementacji kodu kontraktu |
| ContractLedgerIdentifier                 | nvarchar(255) | Yes         | Unikatowy identyfikator skojarzony z wdrożoną wersję inteligentne kontraktu dla określonego rejestru rozproszonego. Na przykład, Ethereum. |
| ContractDeployedByUserId                 | int           | Nie          | Unikatowy identyfikator użytkownika, który kontrakt wdrożone |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Tak         | Imię użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserLastName           | nvarchar(255) | Tak         | Nazwisko użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który kontrakt wdrożone. Domyślnie ten identyfikator jest identyfikator guid, który reprezentuje swojej tożsamości w konsorcjum usługi Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Tak         | Adres e-mail użytkownika, który jest wdrożony umowy |
| WorkflowFunctionId                       | int           | Nie          | Unikatowy identyfikator dla funkcji przepływu pracy |
| WorkflowFunctionName                     | nvarchar(50)  | Nie          | Nazwa funkcji |
| WorkflowFunctionDisplayName              | nvarchar(255) | Nie          | Nazwa funkcji ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionDescription              | nvarchar(255) | Nie          | Opis funkcji |
| ContractActionId                         | int           | Nie          | Unikatowy identyfikator dla akcji kontraktu |
| ContractActionProvisioningStatus         | int           | Nie          | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla akcji kontraktu. Możliwe wartości to: <br />0 – akcji kontraktu utworzonych przy użyciu interfejsu API w bazie danych<br />1 — Akcja umowy zostało wysłane do rejestru<br />2 — Akcja kontraktu został pomyślnie wdrożony w księdze<br />3 lub 4 - kontrakt nie udało się wdrożyć w księdze<br />5 — Umowy został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. Dla wstecznej zgodności w bieżącej wersji widoku **vwContractActionV0** jest dostępna, że obsługuje tylko wartości 0 do 2. |
| ContractActionTimestamp                  | DATETIME(2,7) | Nie          | Sygnatura czasowa akcji kontraktu |
| ContractActionExecutedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, która wykonana akcja kontraktu |
| ContractActionExecutedByUserFirstName    | int           | Yes         | Imię użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Yes         | Nazwisko użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Tak         | Zewnętrzny identyfikator użytkownika, który wykonał akcję kontraktu. Domyślnie ten identyfikator jest identyfikator guid, który reprezentuje swojej tożsamości w konsorcjum usługi Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Tak         | Adres e-mail użytkownika, który wykonał akcję kontraktu |
| WorkflowFunctionParameterId              | int           | Nie          | Unikatowy identyfikator dla parametru funkcji |
| WorkflowFunctionParameterName            | nvarchar(50)  | Nie          | Nazwa parametru funkcji |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Nie          | Nazwa parametru funkcji ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionParameterDataTypeId      | int           | Nie          | Unikatowy identyfikator dla typu danych skojarzonych z parametrem funkcji przepływu pracy |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Nie          | Nazwa typu danych skojarzonych z parametrem funkcji przepływu pracy |
| ContractActionParameterValue             | nvarchar(255) | Nie          | Wartość parametru przechowywane w kontrakcie inteligentne |
| BlockHash                                | nvarchar(255) | Yes         | Wartość skrótu bloku |
| BlockNumber                              | int           | Tak         | Liczba bloku na rejestr |
| BlockTimestamp                           | DATETIME(2,7) | Tak         | Sygnatura czasowa bloku |
| TransactionId                            | int           | Nie          | Unikatowy identyfikator dla transakcji |
| TransactionFrom                          | nvarchar(255) | Yes         | Strona, która pochodzi transakcji |
| TransactionTo                            | nvarchar(255) | Yes         | Strona, która została dokonana transakcja z |
| TransactionHash                          | nvarchar(255) | Tak         | Skrót transakcji |
| TransactionIsWorkbenchTransaction        | bit           | Tak         | Bit, który określa, czy transakcja jest transakcja aplikacji Azure Blockchain Workbench |
| TransactionProvisioningStatus            | int           | Tak         | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla transakcji. Możliwe wartości to: <br />0 – transakcja została utworzona przez interfejs API w bazie danych<br />1 — transakcja została wysłana do rejestru<br />2 — transakcja została pomyślnie wdrożona w księdze                 |
| TransactionValue                         | decimal(32,2) | Tak         | Wartość transakcji |

## <a name="vwcontractproperty"></a>vwContractProperty

Ten widok zaspokajają większość informacje dotyczące właściwości skojarzone z kontraktu i zaprojektowano w celu ułatwienia łatwo typowych scenariuszy raportowania. Dla każdej właściwości jest wykonywane ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Szczegóły dotyczące użytkownika, który jest wdrożony przepływ pracy
-   Definicja właściwości skojarzone kontraktu inteligentne
-   Konkretne wystąpienie wartości właściwości
-   Szczegóły dotyczące właściwości stanu zamówienia

| Name (Nazwa)                               | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName             | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                 | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych.                      |
| WorkflowId                         | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                       | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                | nvarchar(255) | Nie          | Nazwa przepływu pracy, wyświetlana w interfejsie użytkownika |
| WorkflowDescription                | nvarchar(255) | Yes         | Opis przepływu pracy |
| ContractId                         | int           | Nie          | Unikatowy identyfikator zamówienia |
| ContractProvisioningStatus         | int           | Nie          | Określa bieżący stan procesu inicjowania obsługi administracyjnej dla kontraktu. Możliwe wartości to: <br />0 – kontrakt utworzonych przy użyciu interfejsu API w bazie danych<br />1 — Umowa została wysłana do rejestru<br />2 — Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - kontrakt nie udało się wdrożyć w księdze<br />5 — Umowy został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. Dla wstecznej zgodności w bieżącej wersji widoku **vwContractPropertyV0** jest dostępna, że obsługuje tylko wartości 0 do 2. |
| ContractCodeId                     | int           | Nie          | Unikatowy identyfikator dla implementacji kodu kontraktu |
| ContractLedgerIdentifier           | nvarchar(255) | Tak         | Unikatowy identyfikator skojarzony z wdrożoną wersję inteligentne kontraktu dla określonego rejestru rozproszonego. Na przykład, Ethereum. |
| ContractDeployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który kontrakt wdrożone |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Yes         | Imię użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserLastName     | nvarchar(255) | Tak         | Nazwisko użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który kontrakt wdrożone. Domyślnie ten identyfikator jest identyfikator guid, który reprezentuje swojej tożsamości w konsorcjum usługi Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Tak         | Adres e-mail użytkownika, który jest wdrożony umowy |
| WorkflowPropertyId                 | int           |             | Unikatowy identyfikator dla właściwości przepływu pracy |
| WorkflowPropertyDataTypeId         | int           | Nie          | Identyfikator typu danych właściwości |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nie          | Nazwa typu danych właściwości |
| WorkflowPropertyName               | nvarchar(50)  | Nie          | Nazwa właściwości przepływu pracy |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nie          | Nazwa wyświetlana właściwości przepływu pracy |
| WorkflowPropertyDescription        | nvarchar(255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar(255) | Nie          | Wartość właściwości umowy |
| StateName                          | nvarchar(50)  | Tak         | Jeśli ta właściwość zawiera stan zamówienia, jest nazwa wyświetlana stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |
| StateDisplayName                   | nvarchar(255) | Nie          | Jeśli ta właściwość zawiera stan, jest nazwa wyświetlana stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |
| StateValue                         | nvarchar(255) | Tak         | Jeśli ta właściwość zawiera stan, to wartość stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |

## <a name="vwcontractstate"></a>vwContractState

Ten widok zaspokajają większość informacji dotyczących stanu określonych zamówień i zaprojektowano w celu ułatwienia łatwo typowych scenariuszy raportowania. Każdy rekord w tym widoku zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Szczegóły dotyczące użytkownika, który jest wdrożony przepływ pracy
-   Definicja właściwości skojarzone kontraktu inteligentne
-   Szczegóły dotyczące właściwości stanu zamówienia

| Name (Nazwa)                               | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName             | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                 | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych. |
| WorkflowId                         | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                       | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| WorkflowDescription                | nvarchar(255) | Tak         | Opis przepływu pracy |
| ContractLedgerImplementationId     | nvarchar(255) | Tak         | Unikatowy identyfikator skojarzony z wdrożoną wersję inteligentne kontraktu dla określonego rejestru rozproszonego. Na przykład, Ethereum. |
| ContractId                         | int           | Nie          | Unikatowy identyfikator zamówienia |
| ContractProvisioningStatus         | int           | Nie          |Określa bieżący stan procesu inicjowania obsługi administracyjnej dla kontraktu. Możliwe wartości to: <br />0 – kontrakt utworzonych przy użyciu interfejsu API w bazie danych<br />1 — Umowa została wysłana do rejestru<br />2 — Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - kontrakt nie udało się wdrożyć w księdze<br />5 — Umowy został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. Dla wstecznej zgodności w bieżącej wersji widoku **vwContractStateV0** jest dostępna, że obsługuje tylko wartości 0 do 2. |
| ConnectionId                       | int           | Nie          | Unikatowy identyfikator wystąpienia łańcucha bloków, które jest wdrażana przepływu pracy |
| ContractCodeId                     | int           | Nie          | Unikatowy identyfikator dla implementacji kodu kontraktu |
| ContractDeployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożony umowy |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który kontrakt wdrożone. Domyślnie ten identyfikator jest identyfikator guid, który reprezentuje swojej tożsamości w konsorcjum usługi Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Tak         | Imię użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserLastName     | nvarchar(255) | Tak         | Nazwisko użytkownika, który jest wdrożony umowy |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Yes         | Adres e-mail użytkownika, który jest wdrożony umowy |
| WorkflowPropertyId                 | int           | Nie          | Unikatowy identyfikator właściwości przepływu pracy |
| WorkflowPropertyDataTypeId         | int           | Nie          | Identyfikator typu danych właściwości przepływu pracy |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nie          | Nazwa typu danych właściwości przepływu pracy |
| WorkflowPropertyName               | nvarchar(50)  | Nie          | Nazwa właściwości przepływu pracy |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nie          | Nazwa wyświetlana właściwości do wyświetlenia w interfejsie użytkownika |
| WorkflowPropertyDescription        | nvarchar(255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar(255) | Nie          | Wartość dla właściwości przechowywanych w kontrakcie |
| StateName                          | nvarchar(50)  | Tak         | Jeśli ta właściwość zawiera stan, jego nazwa wyświetlana stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |
| StateDisplayName                   | nvarchar(255) | Nie          | Jeśli ta właściwość zawiera stan, jest nazwa wyświetlana stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |
| StateValue                         | nvarchar(255) | Tak         | Jeśli ta właściwość zawiera stan, to wartość stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie równa null. |

## <a name="vwuser"></a>vwUser

Ten widok udostępnia szczegółowe informacje na elementach członkowskich konsorcjum, które jest przygotowany do korzystania z aplikacji Azure Blockchain Workbench. Domyślnie dane są wprowadzane przy użyciu początkowego udostępnienia użytkownika.

| Name (Nazwa)               | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Nie          | Unikatowy identyfikator dla użytkownika |
| externalID         | nvarchar(255) | Nie          | Zewnętrznego identyfikatora użytkownika. Domyślnie ten identyfikator jest identyfikator guid reprezentujący identyfikator Azure Active Directory dla użytkownika. |
| ProvisioningStatus | int           | Nie          |Określa bieżący stan aprowizacji procesu dla użytkownika. Możliwe wartości to: <br />0 – użytkownika została utworzona przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zaaprowizowanym |
| FirstName          | nvarchar(50)  | Tak         | Imię użytkownika |
| LastName           | nvarchar(50)  | Tak         | Nazwisko użytkownika |
| EmailAddress       | nvarchar(255) | Yes         | Adres e-mail użytkownika |

## <a name="vwworkflow"></a>vwWorkflow

Ten widok przedstawia metadane przepływu pracy podstawowe szczegóły, a także funkcje i parametry przepływu pracy. Zaprojektowana pod kątem raportowania, zawiera on również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel w celu ułatwienia tworzenia raportów w przepływach pracy. Dla każdego przepływu pracy ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Skojarzone informacje o stanie uruchamiania przepływu pracy

| Name (Nazwa)                              | Type          | Może mieć wartości Null | Opis                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                   | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName            | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                | bit           | Nie          | Określa, czy aplikacja jest włączona |
| WorkflowId                        | int           | Tak         | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                      | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName               | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| WorkflowDescription               | nvarchar(255) | Tak         | Opis przepływu pracy. |
| WorkflowConstructorFunctionId     | int           | Nie          | Identyfikator funkcji przepływu pracy, który służy jako konstruktor dla przepływu pracy |
| WorkflowStartStateId              | int           | Nie          | Unikatowy identyfikator stanu |
| WorkflowStartStateName            | nvarchar(50)  | Nie          | Nazwa stanu |
| WorkflowStartStateDisplayName     | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika dla stanu |
| WorkflowStartStateDescription     | nvarchar(255) | Yes         | Opis stanu przepływu pracy |
| WorkflowStartStateStyle           | nvarchar(50)  | Tak         | Ta wartość Określa procent wykonania, że przepływ pracy jest w tym stanie |
| WorkflowStartStateValue           | int           | Nie          | Wartość stanu |
| WorkflowStartStatePercentComplete | int           | Nie          | Opis tekstowy, który stanowi wskazówkę dla klientów w sposób renderowania ten stan w interfejsie użytkownika. Obsługiwane stany to *Powodzenie* i *awarii* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Ten widok przedstawia metadane przepływu pracy podstawowe szczegóły, a także funkcje i parametry przepływu pracy. Zaprojektowana pod kątem raportowania, zawiera on również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel w celu ułatwienia tworzenia raportów w przepływach pracy. Dla każdej funkcji przepływu pracy ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Szczegóły funkcji przepływu pracy

| Name (Nazwa)                                 | Type          | Może mieć wartości Null | Opis                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                      | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName               | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                   | bit           | Nie          | Określa, czy aplikacja jest włączona |
| WorkflowId                           | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                         | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                  | nvarchar(255) | Nie          | Nazwa przepływu pracy, wyświetlana w interfejsie użytkownika |
| WorkflowDescription                  | nvarchar(255) | Yes         | Opis przepływu pracy |
| WorkflowFunctionId                   | int           | Nie          | Unikatowy identyfikator dla funkcji |
| WorkflowFunctionName                 | nvarchar(50)  | Tak         | Nazwa funkcji |
| WorkflowFunctionDisplayName          | nvarchar(255) | Nie          | Nazwa funkcji ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionDescription          | nvarchar(255) | Tak         | Opis funkcji przepływu pracy |
| WorkflowFunctionIsConstructor        | bit           | Nie          | Określa, czy funkcja przepływ pracy jest konstruktora dla przepływu pracy |
| WorkflowFunctionParameterId          | int           | Nie          | Unikatowy identyfikator dla parametru funkcji |
| WorkflowFunctionParameterName        | nvarchar(50)  | Nie          | Nazwa parametru funkcji |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Nie          | Nazwa parametru funkcji ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionParameterDataTypeId  | int           | Nie          | Unikatowy identyfikator dla typu danych skojarzonych z parametrem funkcji przepływu pracy |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Nie          | Nazwa typu danych skojarzonych z parametrem funkcji przepływu pracy |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Ten widok przedstawia właściwości zdefiniowane dla przepływu pracy. Dla każdej właściwości w tym widoku zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Szczegóły właściwości przepływu pracy

| Name (Nazwa)                         | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName       | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| ApplicationEnabled           | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych. |
| WorkflowId                   | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                 | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName          | nvarchar(255) | Nie          | Nazwa wyświetlana przepływu pracy za pomocą interfejsu użytkownika |
| WorkflowDescription          | nvarchar(255) | Yes         | Opis przepływu pracy |
| WorkflowPropertyID           | int           | Nie          | Unikatowy identyfikator dla właściwości przepływu pracy |
| WorkflowPropertyName         | nvarchar(50)  | Nie          | Nazwa właściwości |
| WorkflowPropertyDescription  | nvarchar(255) | Yes         | Opis właściwości |
| WorkflowPropertyDisplayName  | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika |
| WorkflowPropertyWorkflowId   | int           | Nie          | Identyfikator przepływu pracy, z którym jest skojarzony tej właściwości |
| WorkflowPropertyDataTypeId   | int           | Nie          | Identyfikator z typem danych zdefiniowanym dla właściwości |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Nie          | Nazwa typu danych zdefiniowanej dla właściwości |
| WorkflowPropertyIsState      | bit           | Nie          | To pole identyfikuje, jeśli ta właściwość przepływu pracy zawiera stan przepływu pracy |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ten widok przedstawia właściwości skojarzone z przepływem pracy. Dla każdej umowy ten widok zawiera następujące dane:

-   Definicja skojarzonej aplikacji
-   Definicja przepływu pracy skojarzonego
-   Informacje o stanie przepływu pracy

| Name (Nazwa)                         | Type          | Może mieć wartości Null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar(50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName       | nvarchar(255) | Nie          | Opis aplikacji |
| ApplicationEnabled           | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może uwzględnione jako wyłączone w bazie danych, skojarzonych umów nadal korzystać z łańcucha bloków i dane dotyczące tych umów pozostają w bazie danych. |
| WorkflowId                   | int           | Nie          | Unikatowy identyfikator dla przepływu pracy |
| WorkflowName                 | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName          | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika dla przepływu pracy |
| WorkflowDescription          | nvarchar(255) | Yes         | Opis przepływu pracy |
| WorkflowStateID              | int           | Nie          | Unikatowy identyfikator stanu |
| WorkflowStateName            | nvarchar(50)  | Nie          | Nazwa stanu |
| WorkflowStateDisplayName     | nvarchar(255) | Nie          | Nazwa będzie wyświetlana w interfejsie użytkownika dla stanu |
| WorkflowStateDescription     | nvarchar(255) | Tak         | Opis stanu przepływu pracy |
| WorkflowStatePercentComplete | int           | Nie          | Ta wartość Określa procent wykonania, że przepływ pracy jest w tym stanie |
| WorkflowStateValue           | nvarchar(50)  | Nie          | Wartość stanu |
| WorkflowStateStyle           | nvarchar(50)  | Nie          | Opis tekstowy, który stanowi wskazówkę dla klientów w sposób renderowania ten stan w interfejsie użytkownika. Obsługiwane stany to *Powodzenie* i *awarii* |
