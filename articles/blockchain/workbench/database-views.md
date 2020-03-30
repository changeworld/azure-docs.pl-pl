---
title: Widoki bazy danych usługi Azure Blockchain Workbench
description: Omówienie dostępnych widoków bazy danych bazy danych usługi Azure Blockchain Workbench Preview.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325991"
---
# <a name="azure-blockchain-workbench-database-views"></a>Widoki bazy danych usługi Azure Blockchain Workbench

Usługa Azure Blockchain Workbench Preview dostarcza dane z rozproszonych książek do *niełasce* bazy danych bazy danych SQL DB. Poza łańcuchowa baza danych umożliwia korzystanie z języka SQL i istniejących narzędzi, takich jak [SQL Server Management Studio,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)do interakcji z danymi łańcucha bloków.

Azure Blockchain Workbench udostępnia zestaw widoków bazy danych, które zapewniają dostęp do danych, które będą przydatne podczas wykonywania zapytań. Widoki te są silnie zdenormalizowane, aby ułatwić szybkie rozpoczęcie tworzenia raportów, analiz i w inny sposób zużywać dane blockchain za pomocą istniejących narzędzi i bez konieczności ponownego szkolenia personelu bazy danych.

Ta sekcja zawiera przegląd widoków bazy danych i danych, które zawierają.

> [!NOTE]
> Wszelkie bezpośrednie użycie tabel bazy danych znalezionych w bazie danych poza tymi widokami, podczas gdy jest to możliwe, nie jest obsługiwane.
>

## <a name="vwapplication"></a>vwApplication (wniosek o odwołanie)

Ten widok zawiera szczegółowe informacje na temat **aplikacji,** które zostały przekazane do workbench platformy Azure Blockchain.

| Nazwa                             | Typ          | Może być null | Opis                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                  | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Opis aplikacji           | nvarchar(255) | Tak         | Opis wniosku |
| Nazwa funkcji ApplicationDisplay           | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)               | bit           | Nie          | Określa, czy aplikacja jest aktualnie włączona<br /> **Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych. |
| PrzesłaneDtTm                     | datetime2(7)  | Nie          | Data i godzina przesłania umowy |
| Identyfikator przesłany przez identyfikatoruserida                 | int           | Nie          | Identyfikator użytkownika, który przesłał aplikację |
| Identyfikator przesłany przez identyfikatorExternalId         | nvarchar(255) | Nie          | Identyfikator zewnętrzny użytkownika, który przekazał aplikację. Domyślnie ten identyfikator jest użytkownikiem z usługi Azure Active Directory dla konsorcjum.                                                                                                |
| Przekazany przezużytnikudowy stan | int           | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości: <br />0 – Użytkownik został utworzony przez API<br />1 – Klucz został skojarzony z użytkownikiem w bazie danych<br />2 – Użytkownik jest w pełni aprowizować                         |
| UploadedByUserFirstName          | nvarchar(50)  | Tak         | Imię użytkownika, który przesłał umowę |
| Nazwa przesłana przezuserLastname           | nvarchar(50)  | Tak         | Nazwisko użytkownika, który przesłał umowę |
| UploadedByUserEmailAddress       | nvarchar(255) | Tak         | Adres e-mail użytkownika, który przesłał umowę |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ten widok zawiera szczegółowe informacje na temat ról, które zostały zdefiniowane w aplikacjach Azure Blockchain Workbench.

W aplikacji *Przeniesienia zasobów* można na przykład zdefiniować role, takie jak rola *kupującego* i *sprzedającego.*

| Nazwa                   | Typ             | Może być null | Opis                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nie          | Unikatowy identyfikator aplikacji           |
| ApplicationName        | nvarchar(50)     | Nie          | Nazwa aplikacji                       |
| Opis aplikacji | nvarchar(255)    | Tak         | Opis wniosku                  |
| Nazwa funkcji ApplicationDisplay | nvarchar(255)    | Nie          | Nazwa wyświetlana w interfejsie użytkownika      |
| Ład roli                 | int              | Nie          | Unikatowy identyfikator roli w aplikacji |
| RoleName               | nvarchar50)      | Nie          | Nazwa roli                              |
| Opis roli        | opis(255) | Tak         | Opis roli                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ten widok zawiera szczegółowe informacje na temat ról, które zostały zdefiniowane w aplikacjach Azure Blockchain Workbench i użytkowników skojarzonych z nimi.

Na przykład w aplikacji *Przeniesienia środków* na przykład *Jan Kowalski* może być skojarzony z rolą *Kupującego.*

| Nazwa                       | Typ          | Może być null | Opis                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nie          | Unikatowy identyfikator aplikacji                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Nie          | Nazwa aplikacji                                                                                                                                                                                                           |
| Opis aplikacji     | nvarchar(255) | Tak         | Opis wniosku                                                                                                                                                                                                      |
| Nazwa funkcji ApplicationDisplay     | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika                                                                                                                                                                                          |
| Identyfikator aplikacji          | int           | Nie          | Unikatowy identyfikator roli w aplikacji                                                                                                                                                                                     |
| Nazwa aplikacji        | nvarchar50)   | Nie          | Nazwa roli                                                                                                                                                                                                                  |
| ApplicationRoleDescription (Opis aplikacji) | nvarchar(255) | Tak         | Opis roli                                                                                                                                                                                                             |
| UserId                     | int           | Nie          | Identyfikator użytkownika skojarzonego z rolą |
| Identyfikator użytkownika             | nvarchar(255) | Nie          | Identyfikator zewnętrzny użytkownika skojarzonego z rolą. Domyślnie ten identyfikator jest użytkownikiem z usługi Azure Active Directory dla konsorcjum.                                                                     |
| Stan obsługi użytkownika     | int           | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości: <br />0 – Użytkownik został utworzony przez API<br />1 – Klucz został skojarzony z użytkownikiem w bazie danych<br />2 – Użytkownik jest w pełni aprowizować |
| Nazwa użytkownikaFirstName              | nvarchar(50)  | Tak         | Imię użytkownika skojarzonego z rolą |
| Nazwa użytkownika               | nvarchar(255) | Tak         | Nazwisko użytkownika skojarzonego z rolą |
| Adres użytkownika Emailaddress           | nvarchar(255) | Tak         | Adres e-mail użytkownika skojarzonego z rolą |

## <a name="vwconnectionuser"></a>vwConnectionUżyciesz

Ten widok zawiera szczegółowe informacje na temat połączeń zdefiniowanych w usłudze Azure Blockchain Workbench i użytkowników skojarzonych z nimi. Dla każdego połączenia ten widok zawiera następujące dane:

-   Szczegóły skojarzonej księgi
-   Skojarzone informacje o użytkowniku

| Nazwa                     | Typ          | Może być null | Opis                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Connectionid             | int           | Nie          | Unikatowy identyfikator połączenia w usłudze Azure Blockchain Workbench |
| ConnectionEndpointUrl (Punkt połączenia)    | nvarchar(50)  | Nie          | Adres url punktu końcowego dla połączenia |
| Konto finansowania połączeń | nvarchar(255) | Tak         | Rachunek finansowania powiązany z połączeniem, w stosownych przypadkach |
| LedgerId (100- 1                 | int           | Nie          | Unikatowy identyfikator księgi |
| Nazwa księgi               | nvarchar(50)  | Nie          | Nazwa księgi |
| Nazwa funkcji Księgawywydajna        | nvarchar(255) | Nie          | Nazwa księgi do wyświetlenia w interfejsie użytkownika |
| UserId                   | int           | Nie          | Identyfikator użytkownika skojarzonego z połączeniem |
| Identyfikator użytkownika           | nvarchar(255) | Nie          | Identyfikator zewnętrzny użytkownika skojarzonego z połączeniem. Domyślnie ten identyfikator jest użytkownikiem z usługi Azure Active Directory dla konsorcjum. |
| Stan obsługi użytkownika   | int           | Nie          |Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości: <br />0 – Użytkownik został utworzony przez API<br />1 – Klucz został skojarzony z użytkownikiem w bazie danych<br />2 – Użytkownik jest w pełni aprowizować |
| Nazwa użytkownikaFirstName            | nvarchar(50)  | Tak         | Imię użytkownika skojarzonego z połączeniem |
| Nazwa użytkownika             | nvarchar(255) | Tak         | Nazwisko użytkownika skojarzonego z połączeniem |
| Adres użytkownika Emailaddress         | nvarchar(255) | Tak         | Adres e-mail użytkownika skojarzonego z połączeniem |

## <a name="vwcontract"></a>program vwContract

Ten widok zawiera szczegółowe informacje na temat wdrożonych kontraktów. Dla każdej umowy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Implementacja skojarzonej księgi dla funkcji
-   Szczegółowe informacje dotyczące użytkownika, który zainicjował akcję
-   Szczegóły związane z blokiem blockchain i transakcją

| Nazwa                                     | Typ           | Może być null | Opis                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Connectionid                             | int            | Nie          | Unikatowy identyfikator połączenia w usłudze Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl (Punkt połączenia)                    | nvarchar(50)   | Nie          | Adres url punktu końcowego dla połączenia |
| Konto finansowania połączeń                 | nvarchar(255)  | Tak         | Rachunek finansowania powiązany z połączeniem, w stosownych przypadkach |
| LedgerId (100- 1                                 | int            | Nie          | Unikatowy identyfikator księgi |
| Nazwa księgi                               | nvarchar(50)   | Nie          | Nazwa księgi |
| Nazwa funkcji Księgawywydajna                        | nvarchar(255)  | Nie          | Nazwa księgi do wyświetlenia w interfejsie użytkownika |
| ApplicationId                            | int            | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar (50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay                   | nvarchar (255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                       | bit            | Nie          | Określa, czy aplikacja jest aktualnie włączona.<br /> **Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych.  |
| Ład przepływu pracy                               | int            | Nie          | Unikatowy identyfikator przepływu pracy skojarzonego z umową |
| Nazwa przepływu pracy                             | nvarchar(50)   | Nie          | Nazwa przepływu pracy skojarzonego z umową |
| Nazwa przepływu pracyWyświetlajnawydań                      | nvarchar(255)  | Nie          | Nazwa przepływu pracy skojarzonego z umową wyświetlana w interfejsie użytkownika |
| Opis przepływu pracy                      | nvarchar(255)  | Tak         | Opis przepływu pracy skojarzonego z umową |
| KodcodeId kontraktu                           | int            | Nie          | Unikatowy identyfikator kodu kontraktu skojarzonego z umową |
| Nazwa pliku kontraktu                         | int            | Nie          | Nazwa pliku zawierającego inteligentny kod kontraktu dla tego przepływu pracy. |
| ContractUploadedDtTm                     | int            | Nie          | Data i godzina przesłania kodu umowy |
| ContractId (ida umowy)                               | int            | Nie          | Unikatowy identyfikator umowy |
| Status umowy               | int            | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla umowy. Możliwe wartości: <br />0 – Umowa została utworzona przez API w bazie danych<br />1 – Umowa została wysłana do księgi<br />2 – Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - Umowa nie została wdrożona w księdze<br />5 - Umowa została pomyślnie wdrożona w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. W przypadku zgodności z powrotem w bieżącej wersji dostępny jest widok **vwContractV0,** który obsługuje tylko wartości od 0 do 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | Adres e-mail użytkownika, który wdrożył umowę |
| Identyfikator umowyDployedByUserId                 | int            | Nie          | Zewnętrzny identyfikator dla użytkownika, który wdrożył umowę. Domyślnie ten identyfikator jest identyfikatorem guid reprezentującym identyfikator usługi Azure Active Directory dla użytkownika.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem guid reprezentującym identyfikator usługi Azure Active Directory dla użytkownika.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości: <br />0 – użytkownik został utworzony przez API<br />1 – Klucz został skojarzony z użytkownikiem w bazie danych <br />2 – Użytkownik jest w pełni aprowizować                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Tak         | Imię użytkownika, który wdrożył umowę |
| ContractDeployedByUserLastName           | nvarchar(255)  | Tak         | Nazwisko użytkownika, który wdrożył umowę |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Tak         | Adres e-mail użytkownika, który wdrożył umowę |

## <a name="vwcontractaction"></a>vwContractAction (VwContractAction)

Pogląd ten stanowi większość informacji związanych z działaniami podejmowanych w odniesieniu do umów i ma na celu ułatwienie typowych scenariuszy raportowania. Dla każdej podjętej akcji ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Skojarzona inteligentna funkcja kontraktu i definicja parametrów
-   Implementacja skojarzonej księgi dla funkcji
-   Określone wartości wystąpienia podane dla parametrów
-   Szczegółowe informacje dotyczące użytkownika, który zainicjował akcję
-   Szczegóły związane z blokiem blockchain i transakcją

| Nazwa                                     | Typ          | Może być null | Opis                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay                   | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                       | bit           | Nie          | To pole określa, czy aplikacja jest aktualnie włączona. Uwaga — mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych.                                                  |
| Ład przepływu pracy                               | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                             | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań                      | nvarchar(255) | Nie          | Nazwa przepływu pracy wyświetlanego w interfejsie użytkownika |
| Opis przepływu pracy                      | nvarchar(255) | Tak         | Opis przepływu pracy |
| ContractId (ida umowy)                               | int           | Nie          | Unikatowy identyfikator kontraktu |
| Status umowy               | int           | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla umowy. Możliwe wartości: <br />0 – Umowa została utworzona przez API w bazie danych<br />1 – Umowa została wysłana do księgi<br />2 – Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - Umowa nie została wdrożona w księdze<br />5 - Umowa została pomyślnie wdrożona w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. W przypadku zgodności z powrotem w bieżącej wersji dostępny jest widok **vwContractActionV0,** który obsługuje tylko wartości od 0 do 2. |
| KodcodeId kontraktu                           | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| ContractLedgerIdentifier                 | nvarchar(255) | Tak         | Unikatowy identyfikator skojarzony z wdrożoną wersją inteligentnego kontraktu dla określonej rozproszonej księgi. Na przykład Ethereum. |
| Identyfikator umowyDployedByUserId                 | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył umowę |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Tak         | Imię użytkownika, który wdrożył umowę |
| ContractDeployedByUserLastName           | nvarchar(255) | Tak         | Nazwisko użytkownika, który wdrożył umowę |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył umowę. Domyślnie ten identyfikator jest identyfikatorem guid, który reprezentuje ich tożsamość w konsorcjum usługi Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Tak         | Adres e-mail użytkownika, który wdrożył umowę |
| -Funkcjonuje pracy                       | int           | Nie          | Unikatowy identyfikator funkcji przepływu pracy |
| Nazwa funkcji przepływu pracy                     | nvarchar(50)  | Nie          | Nazwa funkcji |
| Nazwa funkcji funkcji przepływu pracy              | nvarchar(255) | Nie          | Nazwa funkcji, która ma być wyświetlana w interfejsie użytkownika |
| Opis funkcji przepływu pracy              | nvarchar(255) | Nie          | Opis funkcji |
| ContractActionId                         | int           | Nie          | Unikatowy identyfikator akcji kontraktu |
| ContractActionProvisioningStatus         | int           | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla akcji umowy. Możliwe wartości: <br />0 – Działanie kontraktowe zostało utworzone przez API w bazie danych<br />1 – Akcja kontraktowa została wysłana do księgi<br />2 – Akcja kontraktu została pomyślnie wdrożona w księdze<br />3 lub 4 - Umowa nie została wdrożona w księdze<br />5 - Umowa została pomyślnie wdrożona w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. W przypadku zgodności z powrotem w bieżącej wersji dostępny jest widok **vwContractActionV0,** który obsługuje tylko wartości od 0 do 2. |
| ContractActionStamp                  | datetime(2,7) | Nie          | Sygnatura czasowa działania umowy |
| ContractActionExecutedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserFirstName    | int           | Tak         | Imię użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Tak         | Nazwisko użytkownika, który wykonał akcję kontraktowej |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Tak         | Zewnętrzny identyfikator użytkownika, który wykonał akcję kontraktu. Domyślnie ten identyfikator jest identyfikatorem guid, który reprezentuje ich tożsamość w konsorcjum usługi Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Tak         | Adres e-mail użytkownika, który wykonał akcję kontraktowej |
| Element Parametru funkcji przepływu pracy              | int           | Nie          | Unikatowy identyfikator parametru funkcji |
| Funkcja pracyFunkctionParameterName            | nvarchar(50)  | Nie          | Nazwa parametru funkcji |
| Funkcja pracyFunkctionParameterDisplayName     | nvarchar(255) | Nie          | Nazwa parametru funkcji wyświetlanego w interfejsie użytkownika |
| Element Typuj parametru parametru parametru Przepływu pracy      | int           | Nie          | Unikatowy identyfikator typu danych skojarzonego z parametrem funkcji przepływu pracy |
| Nazwa rodzaju parametru parametru pracy            | nvarchar(50)  | Nie          | Nazwa typu danych skojarzonego z parametrem funkcji przepływu pracy |
| ContractActionParameterValue             | nvarchar(255) | Nie          | Wartość parametru przechowywanego w umowie inteligentnej |
| BlockHash (BlokHash)                                | nvarchar(255) | Tak         | Skrót bloku |
| Numer bloku                              | int           | Tak         | Numer bloku w księdze |
| Sygnatura czasowa bloku                           | datetime(2,7) | Tak         | Sygnatura czasowa bloku |
| Identyfikator transakcji                            | int           | Nie          | Unikatowy identyfikator transakcji |
| TransakcjaOd                          | nvarchar(255) | Tak         | Strona, która zainicjowała transakcję |
| TransactionTo                            | nvarchar(255) | Tak         | Strona, która została dokonywała transakcji z |
| TransakcjaHash                          | nvarchar(255) | Tak         | Skrót transakcji |
| TransactionIsWorkbenchTransakcja        | bit           | Tak         | Bit identyfikujące, czy transakcja jest transakcją azure blockchain workbench |
| Status obsługi transakcji            | int           | Tak         | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla transakcji. Możliwe wartości: <br />0 – Transakcja została utworzona przez INTERFEJS API w bazie danych<br />1 – Transakcja została wysłana do księgi<br />2 – Transakcja została pomyślnie wdrożona w księdze                 |
| Wartość transakcji                         | dziesiętne(32,2) | Tak         | Wartość transakcji |

## <a name="vwcontractproperty"></a>vwContractProperty

Ten widok reprezentuje większość informacji związanych z właściwości skojarzonych z umową i ma na celu łatwo ułatwić typowych scenariuszy raportowania. Dla każdej pobranej właściwości ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegółowe informacje dotyczące użytkownika, który wdrożył przepływ pracy
-   Skojarzona definicja właściwości inteligentnego kontraktu
-   Wartości określonego wystąpienia właściwości
-   Szczegóły dotyczące majątku państwowego umowy

| Nazwa                               | Typ          | Może być null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay             | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                 | bit           | Nie          | Określa, czy aplikacja jest aktualnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych.                      |
| Ład przepływu pracy                         | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                       | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań                | nvarchar(255) | Nie          | Nazwa przepływu pracy wyświetlana w interfejsie użytkownika |
| Opis przepływu pracy                | nvarchar(255) | Tak         | Opis przepływu pracy |
| ContractId (ida umowy)                         | int           | Nie          | Unikatowy identyfikator umowy |
| Status umowy         | int           | Nie          | Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla umowy. Możliwe wartości: <br />0 – Umowa została utworzona przez API w bazie danych<br />1 – Umowa została wysłana do księgi<br />2 – Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - Umowa nie została wdrożona w księdze<br />5 - Umowa została pomyślnie wdrożona w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. W przypadku zgodności z powrotem w bieżącej wersji dostępny jest widok **vwContractPropertyV0,** który obsługuje tylko wartości od 0 do 2. |
| KodcodeId kontraktu                     | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| ContractLedgerIdentifier           | nvarchar(255) | Tak         | Unikatowy identyfikator skojarzony z wdrożoną wersją inteligentnego kontraktu dla określonej rozproszonej księgi. Na przykład Ethereum. |
| Identyfikator umowyDployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył umowę |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Tak         | Imię użytkownika, który wdrożył umowę |
| ContractDeployedByUserLastName     | nvarchar(255) | Tak         | Nazwisko użytkownika, który wdrożył umowę |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył umowę. Domyślnie ten identyfikator jest identyfikatorem guid reprezentującym ich tożsamość w konsorcjum usługi Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Tak         | Adres e-mail użytkownika, który wdrożył umowę |
| Wydajność przepływu pracyPropertyId                 | int           |             | Unikatowy identyfikator właściwości przepływu pracy |
| Typotypejat przepływu pracy         | int           | Nie          | Identyfikator typu danych właściwości |
| Nazwa rodzaju danych o przepływie pracy       | nvarchar(50)  | Nie          | Nazwa typu danych właściwości |
| Nazwa właściwości przepływu pracy               | nvarchar(50)  | Nie          | Nazwa właściwości przepływu pracy |
| Nazwa obiektu przepływu pracyPropertyDisplayname        | nvarchar(255) | Nie          | Wyświetlana nazwa właściwości przepływu pracy |
| Opis właściwości przepływu pracy        | nvarchar(255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar(255) | Nie          | Wartość nieruchomości w umowie |
| Nazwa stanu                          | nvarchar(50)  | Tak         | Jeśli ta właściwość zawiera stan kontraktu, jest wyświetlaną nazwą stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |
| Nazwa stanu                   | nvarchar(255) | Nie          | Jeśli ta właściwość zawiera stan, jest nazwą wyświetlaną dla stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |
| Wartość stanu                         | nvarchar(255) | Tak         | Jeśli ta właściwość zawiera stan, jest to wartość stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |

## <a name="vwcontractstate"></a>vwContractState

Pogląd ten reprezentuje większość informacji związanych ze stanem konkretnej umowy i ma na celu łatwe ułatwienie typowych scenariuszy raportowania. Każdy rekord w tym widoku zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegółowe informacje dotyczące użytkownika, który wdrożył przepływ pracy
-   Skojarzona definicja właściwości inteligentnego kontraktu
-   Szczegóły dotyczące majątku państwowego umowy

| Nazwa                               | Typ          | Może być null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay             | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                 | bit           | Nie          | Określa, czy aplikacja jest aktualnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych. |
| Ład przepływu pracy                         | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                       | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań                | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| Opis przepływu pracy                | nvarchar(255) | Tak         | Opis przepływu pracy |
| ContractLedgerImplementationIdId     | nvarchar(255) | Tak         | Unikatowy identyfikator skojarzony z wdrożoną wersją inteligentnego kontraktu dla określonej rozproszonej księgi. Na przykład Ethereum. |
| ContractId (ida umowy)                         | int           | Nie          | Unikatowy identyfikator kontraktu |
| Status umowy         | int           | Nie          |Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla umowy. Możliwe wartości: <br />0 – Umowa została utworzona przez API w bazie danych<br />1 – Umowa została wysłana do księgi<br />2 – Umowa została pomyślnie wdrożona w księdze<br />3 lub 4 - Umowa nie została wdrożona w księdze<br />5 - Umowa została pomyślnie wdrożona w księdze <br /><br />Począwszy od wersji 1.5, wartości od 0 do 5 są obsługiwane. W przypadku zgodności z powrotem w bieżącej wersji dostępny jest widok **vwContractStateV0,** który obsługuje tylko wartości od 0 do 2. |
| Connectionid                       | int           | Nie          | Unikatowy identyfikator wystąpienia łańcucha bloków, do jakiego przepływ pracy jest wdrażany |
| KodcodeId kontraktu                     | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| Identyfikator umowyDployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył umowę |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył umowę. Domyślnie ten identyfikator jest identyfikatorem guid, który reprezentuje ich tożsamość w konsorcjum usługi Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Tak         | Imię użytkownika, który wdrożył umowę |
| ContractDeployedByUserLastName     | nvarchar(255) | Tak         | Nazwisko użytkownika, który wdrożył umowę |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Tak         | Adres e-mail użytkownika, który wdrożył umowę |
| Wydajność przepływu pracyPropertyId                 | int           | Nie          | Unikatowy identyfikator właściwości przepływu pracy |
| Typotypejat przepływu pracy         | int           | Nie          | Identyfikator typu danych właściwości przepływ pracy |
| Nazwa rodzaju danych o przepływie pracy       | nvarchar(50)  | Nie          | Nazwa typu danych właściwości przepływ pracy |
| Nazwa właściwości przepływu pracy               | nvarchar(50)  | Nie          | Nazwa właściwości przepływu pracy |
| Nazwa obiektu przepływu pracyPropertyDisplayname        | nvarchar(255) | Nie          | Wyświetlana nazwa właściwości do wyświetlenia w interfejsie użytkownika |
| Opis właściwości przepływu pracy        | nvarchar(255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar(255) | Nie          | Wartość właściwości przechowywanej w umowie |
| Nazwa stanu                          | nvarchar(50)  | Tak         | Jeśli ta właściwość zawiera stan, jest wyświetlana nazwa stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |
| Nazwa stanu                   | nvarchar(255) | Nie          | Jeśli ta właściwość zawiera stan, jest nazwą wyświetlaną dla stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |
| Wartość stanu                         | nvarchar(255) | Tak         | Jeśli ta właściwość zawiera stan, jest to wartość stanu. Jeśli nie jest skojarzony ze stanem, wartość będzie null. |

## <a name="vwuser"></a>firma vwUser

Ten widok zawiera szczegółowe informacje na temat członków konsorcjum, które są aprowied do korzystania z usługi Azure Blockchain Workbench. Domyślnie dane są wypełniane za pośrednictwem początkowej inicjowania obsługi administracyjnej użytkownika.

| Nazwa               | Typ          | Może być null | Opis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Nie          | Unikatowy identyfikator użytkownika |
| Identyfikator zewnętrzny         | nvarchar(255) | Nie          | Identyfikator zewnętrzny dla użytkownika. Domyślnie ten identyfikator jest identyfikatorem guid reprezentującym identyfikator usługi Azure Active Directory dla użytkownika. |
| AprowizowanieStatus | int           | Nie          |Identyfikuje bieżący stan procesu inicjowania obsługi administracyjnej dla użytkownika. Możliwe wartości: <br />0 – Użytkownik został utworzony przez API<br />1 – Klucz został skojarzony z użytkownikiem w bazie danych<br />2 – Użytkownik jest w pełni aprowizować |
| FirstName          | nvarchar(50)  | Tak         | Imię użytkownika |
| LastName           | nvarchar(50)  | Tak         | Nazwisko użytkownika |
| Emailaddress       | nvarchar(255) | Tak         | Adres e-mail użytkownika |

## <a name="vwworkflow"></a>vwWorkflow

Ten widok reprezentuje szczegóły podstawowych metadanych przepływu pracy, a także funkcje i parametry przepływu pracy. Przeznaczony do raportowania zawiera również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel bazowych, aby ułatwić raportowanie przepływów pracy. Dla każdego przepływu pracy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Informacje o stanie rozpoczęcia skojarzonego przepływu pracy

| Nazwa                              | Typ          | Może być null | Opis                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                   | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay            | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                | bit           | Nie          | Określa, czy aplikacja jest włączona |
| Ład przepływu pracy                        | int           | Tak         | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                      | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań               | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| Opis przepływu pracy               | nvarchar(255) | Tak         | Opis przepływu pracy. |
| (ida pracy przepływuKonstruktorfunkcjiId)     | int           | Nie          | Identyfikator funkcji przepływu pracy, która służy jako konstruktor przepływu pracy |
| Opoąd Stanu stanu 2019 przepływ pracy              | int           | Nie          | Unikatowy identyfikator stanu |
| Nazwa stanu uruchamiania przepływu pracy            | nvarchar(50)  | Nie          | Nazwa państwa |
| Nazwa uruchamiania przepływustartstatewyświetlanie     | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika dla stanu |
| Opis stanu przepływu pracy     | nvarchar(255) | Tak         | Opis stanu przepływu pracy |
| Styl uruchamiania przepływu pracyStateStateStyle           | nvarchar(50)  | Tak         | Ta wartość identyfikuje procent ukończenia przepływu pracy, gdy w tym stanie |
| Wartość obiegu pracyStartStateValue           | int           | Nie          | Wartość stanu |
| Przepływ pracyStartStatePercentComplete | int           | Nie          | Opis tekstu, który zawiera wskazówkę dla klientów, jak renderować ten stan w interfejsie użytkownika. Obsługiwane stany obejmują *sukces* i *niepowodzenie* |

## <a name="vwworkflowfunction"></a>funkcja vwWorkflow

Ten widok reprezentuje szczegóły podstawowych metadanych przepływu pracy, a także funkcje i parametry przepływu pracy. Przeznaczony do raportowania zawiera również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel bazowych, aby ułatwić raportowanie przepływów pracy. Dla każdej funkcji przepływu pracy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły funkcji przepływu pracy

| Nazwa                                 | Typ          | Może być null | Opis                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                      | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay               | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)                   | bit           | Nie          | Określa, czy aplikacja jest włączona |
| Ład przepływu pracy                           | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                         | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań                  | nvarchar(255) | Nie          | Nazwa przepływu pracy wyświetlana w interfejsie użytkownika |
| Opis przepływu pracy                  | nvarchar(255) | Tak         | Opis przepływu pracy |
| -Funkcjonuje pracy                   | int           | Nie          | Unikatowy identyfikator funkcji |
| Nazwa funkcji przepływu pracy                 | nvarchar(50)  | Tak         | Nazwa funkcji |
| Nazwa funkcji funkcji przepływu pracy          | nvarchar(255) | Nie          | Nazwa funkcji, która ma być wyświetlana w interfejsie użytkownika |
| Opis funkcji przepływu pracy          | nvarchar(255) | Tak         | Opis funkcji przepływu pracy |
| Funkcjonowanie pracyIskonstruktor        | bit           | Nie          | Określa, czy funkcja przepływu pracy jest konstruktorem przepływu pracy |
| Element Parametru funkcji przepływu pracy          | int           | Nie          | Unikatowy identyfikator parametru funkcji |
| Funkcja pracyFunkctionParameterName        | nvarchar(50)  | Nie          | Nazwa parametru funkcji |
| Funkcja pracyFunkctionParameterDisplayName | nvarchar(255) | Nie          | Nazwa parametru funkcji wyświetlanego w interfejsie użytkownika |
| Element Typuj parametru parametru parametru Przepływu pracy  | int           | Nie          | Unikatowy identyfikator typu danych skojarzonego z parametrem funkcji przepływu pracy |
| Nazwa rodzaju parametru parametru pracy        | nvarchar(50)  | Nie          | Nazwa typu danych skojarzonego z parametrem funkcji przepływu pracy |

## <a name="vwworkflowproperty"></a>vwWorkflowWłasnawłaściwość

Ten widok reprezentuje właściwości zdefiniowane dla przepływu pracy. Dla każdej właściwości ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły właściwości przepływu pracy

| Nazwa                         | Typ          | Może być null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay       | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| ApplicationEnabled (Nie można pojąć)           | bit           | Nie          | Określa, czy aplikacja jest aktualnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych. |
| Ład przepływu pracy                   | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                 | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań          | nvarchar(255) | Nie          | Nazwa wyświetlana dla przepływu pracy w interfejsie użytkownika |
| Opis przepływu pracy          | nvarchar(255) | Tak         | Opis przepływu pracy |
| Moduł właściwości przepływu pracy           | int           | Nie          | Unikatowy identyfikator właściwości przepływu pracy |
| Nazwa właściwości przepływu pracy         | nvarchar(50)  | Nie          | Nazwa właściwości. |
| Opis właściwości przepływu pracy  | nvarchar(255) | Tak         | Opis właściwości |
| Nazwa obiektu przepływu pracyPropertyDisplayname  | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| Przepływ pracyPropertyWorkflowId   | int           | Nie          | Identyfikator przepływu pracy, z którym jest skojarzona ta właściwość |
| Typotypejat przepływu pracy   | int           | Nie          | Identyfikator typu danych zdefiniowanego dla właściwości |
| Nazwa rodzaju danych o przepływie pracy | nvarchar(50)  | Nie          | Nazwa typu danych zdefiniowanego dla właściwości |
| Stan przepływu pracyPropertyIsState      | bit           | Nie          | To pole identyfikuje, czy ta właściwość przepływu pracy zawiera stan przepływu pracy |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ten widok reprezentuje właściwości skojarzone z przepływem pracy. Dla każdej umowy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Informacje o stanie przepływu pracy

| Nazwa                         | Typ          | Może być null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar(50)  | Nie          | Nazwa aplikacji |
| Nazwa funkcji ApplicationDisplay       | nvarchar(255) | Nie          | Opis wniosku |
| ApplicationEnabled (Nie można pojąć)           | bit           | Nie          | Określa, czy aplikacja jest aktualnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają w łańcuchu bloków, a dane dotyczące tych umów pozostają w bazie danych. |
| Ład przepływu pracy                   | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| Nazwa przepływu pracy                 | nvarchar(50)  | Nie          | Nazwa przepływu pracy |
| Nazwa przepływu pracyWyświetlajnawydań          | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika dla przepływu pracy |
| Opis przepływu pracy          | nvarchar(255) | Tak         | Opis przepływu pracy |
| Stan przepływu pracy              | int           | Nie          | Unikatowy identyfikator stanu |
| Nazwa stanu przepływu pracy            | nvarchar(50)  | Nie          | Nazwa państwa |
| Nazwa obiektu RoboczegoStateDisplayName     | nvarchar(255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika dla stanu |
| Opis stanu przepływu pracy     | nvarchar(255) | Tak         | Opis stanu przepływu pracy |
| Przepływ pracyStatePercentComplete | int           | Nie          | Ta wartość identyfikuje procent ukończenia przepływu pracy, gdy w tym stanie |
| Wartość obiegu roboczego           | nvarchar(50)  | Nie          | Wartość stanu |
| Styl pracyflowstateStyle           | nvarchar(50)  | Nie          | Opis tekstu, który zawiera wskazówkę dla klientów, jak renderować ten stan w interfejsie użytkownika. Obsługiwane stany obejmują *sukces* i *niepowodzenie* |
