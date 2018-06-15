---
title: Zewnętrznego powiązania tabeli dla usługi Azure Functions (eksperymentalne)
description: Przy użyciu powiązań zewnętrznych tabeli w funkcji Azure
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2018
ms.locfileid: "27613285"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Zewnętrznego powiązania tabeli dla usługi Azure Functions (eksperymentalne)

W tym artykule opisano sposób pracy z tabelarycznego na dostawców w modelu SaaS, takich jak Sharepoint i Dynamics w usługi Azure Functions. Azure Functions obsługuje wejściowa i wyjściowa powiązania dla tabel zewnętrznych.

> [!IMPORTANT]
> Powiązania tabeli zewnętrznej jest eksperymentalna i nigdy nie może osiągnąć stanu ogólnie dostępna (GA). Znajduje się tylko na platformie Azure funkcje 1.x i nie ma żadnych planów, aby dodać go do usługi Azure Functions 2.x. W przypadku scenariuszy, które wymagają dostępu do danych w modelu SaaS dostawców, rozważ zastosowanie [logikę aplikacji, które wywołują funkcje](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Połączenia interfejsu API

Powiązania tabeli korzystać z połączeń zewnętrznych interfejsu API do uwierzytelniania za pomocą dostawcy SaaS. 

Podczas przypisywania powiązanie możesz utworzyć nowe połączenie interfejsu API lub użyć istniejącego połączenia interfejsu API w tej samej grupie zasobów.

### <a name="available-api-connections-tables"></a>Dostępne połączenia interfejsu API (tabele)

|Łącznik|Wyzwalacz|Dane wejściowe|Dane wyjściowe|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 dla operacji](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Arkusze Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 dla Finanse](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Baza danych Oracle](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Wspólne usługi danych](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Połączenia zewnętrzne tabeli można również w [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Tworzenie połączenia interfejsu API: krok po kroku

1. Na stronie portalu platformy Azure dla aplikacji funkcji, kliknij znak plus (**+**) można utworzyć funkcji.

1. W **scenariusza** wybierz opcję **eksperymentalne**.

1. Wybierz **tabeli zewnętrznej**.

1. Wybierz język.

2. W obszarze **połączenia tabeli zewnętrznej**, wybrać istniejące połączenie, lub wybierz **nowe**.

1. Nowe połączenie, skonfiguruj ustawienia, a następnie wybierz **autoryzacji**.

1. Wybierz **Utwórz** można utworzyć funkcji.

1. Wybierz **integracji > tabeli zewnętrznej**.

1. Skonfiguruj połączenie z tabeli docelowej. Te ustawienia będą się różnić od dostawców w modelu SaaS. Przykłady znajdują się w poniższej sekcji.

## <a name="example"></a>Przykład

W tym przykładzie łączy do tabeli z kolumnami Identyfikator, nazwisko i imię o nazwie "Skontaktuj się z". Kod wyświetla jednostkami kontaktowymi w tabeli i dzienniki imiona i nazwiska.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Oto kod skryptu C#:

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Źródło danych programu SQL Server

Aby utworzyć tabelę w programie SQL Server do użycia z tym przykładem, w tym miejscu jest skrypt. `dataSetName`to "domyślny".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Źródło danych Google arkuszy

Aby utworzyć tabelę do użycia z tym przykładem w Google Docs, należy utworzyć arkusz kalkulacyjny w arkuszu o nazwie `Contact`. Łącznik nie można użyć Nazwa wyświetlana arkusza kalkulacyjnego. Potrzeb wewnętrzna nazwa (pogrubione) ma być używany jako dataSetName, na przykład: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  dodać nazwy kolumn `Id`, `LastName`, `FirstName` do pierwszego wiersza, następnie wypełnij dane na kolejne wiersze.

### <a name="salesforce"></a>SalesForce

Aby użyć tego przykładu z usług Salesforce, `dataSetName` jest "domyślny".

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|----------------------|
|**Typ** | należy wybrać opcję `apiHubTable`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**Kierunek** | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzeń w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia interfejsu API. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia interfejsu API w integracji interfejsu użytkownika.|
|**dataSetName**|Nazwa zestawu danych, który zawiera tabelę do odczytu.|
|**tableName**|Nazwa tabeli|
|**Identyfikator jednostki**|Może być puste dla powiązania tabeli.

Łącznik tabelarycznych zawiera zestawy danych, a każdy zestaw danych zawiera tabele. Nazwa domyślnego zestawu danych to "domyślny". Poniżej wymieniono tytułów dla zestawu danych i tabeli w różnych dostawców SaaS:

|Łącznik|Zestaw danych|Tabela|
|:-----|:---|:---| 
|**SharePoint**|Witryna|Listy programu SharePoint
|**SQL**|Database (Baza danych)|Tabela 
|**Arkusz Google**|Arkusz kalkulacyjny|Arkusz 
|**Excel**|Plik programu Excel|Arkusz 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
