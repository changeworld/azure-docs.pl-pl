---
title: Zewnętrzne powiązanie tabeli dla usługi Azure Functions (wersja eksperymentalna)
description: Za pomocą powiązania tabeli zewnętrznej w usłudze Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 24728414747d8ad8a8d7ee0d8a21be2177a15ddd
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093816"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Zewnętrzne powiązanie tabeli dla usługi Azure Functions (wersja eksperymentalna)

W tym artykule opisano sposób pracy z danymi tabelarycznymi na dostawców SaaS, takich jak Sharepoint i Dynamics w usłudze Azure Functions. Usługi Azure Functions obsługuje danych wejściowych i wyjściowych powiązania dla tabel zewnętrznych.

> [!IMPORTANT]
> Powiązania tabeli zewnętrznej jest eksperymentalny i nigdy nie może osiągnąć stan jest ogólnie dostępna (GA). Znajduje się tylko na platformie Azure funkcji 1.x i nie ma żadnych planów, aby dodać go do usługi Azure Functions 2.x. W przypadku scenariuszy, które wymagają dostępu do danych w modelu SaaS dostawców należy wziąć pod uwagę przy użyciu [aplikacje logiki, które wywołują funkcje](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Połączenia interfejsu API

Powiązania tabeli wykorzystać zewnętrzne połączenia interfejsu API do uwierzytelniania przy użyciu dostawców SaaS innych firm. 

Podczas przypisywania powiązanie możesz utworzyć nowe połączenie interfejsu API lub użyć istniejącego połączenia interfejsu API w ramach tej samej grupie zasobów.

### <a name="available-api-connections-tables"></a>Dostępne połączenia interfejsu API (tabele)

|Łącznik|Wyzwalacz|Dane wejściowe|Dane wyjściowe|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Arkusze Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Baza danych Oracle](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Połączenia zewnętrzne tabeli można również w [usługi Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Tworzenie połączenia interfejsu API: krok po kroku

1. W witrynie Azure portal strona dla aplikacji funkcji, wybierz znak plus (**+**) aby utworzyć funkcję.

1. W **scenariusza** wybierz opcję **eksperymentalne**.

1. Wybierz **tabeli zewnętrznej**.

1. Wybierz język.

2. W obszarze **połączenie zewnętrzne z tabelą**, wybierz istniejące połączenie lub **nowe**.

1. Nowe połączenie, skonfiguruj ustawienia, a następnie wybierz pozycję **Autoryzuj**.

1. Wybierz **Utwórz** Aby utworzyć funkcję.

1. Wybierz **integracja > tabeli zewnętrznej**.

1. Skonfiguruj połączenie do użycia w tabeli docelowej. Te ustawienia będą się różnić między dostawcami SaaS. Przykłady znajdują się w poniższej sekcji.

## <a name="example"></a>Przykład

W tym przykładzie łączy do tabeli o nazwie "Contact" z kolumnami Identyfikator, nazwisko i imię. Kod zawiera listę jednostek kontaktu w tabeli i rejestruje imiona i nazwiska.

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

Poniżej przedstawiono kod skryptu języka C#:

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

Aby utworzyć tabelę w programie SQL Server do korzystania z tego przykładu, w tym polu to skrypt. `dataSetName` to "default".

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

### <a name="google-sheets-data-source"></a>Źródło danych w arkuszach Google

Aby utworzyć tabelę do użycia z tym przykładem w witrynie Docs Google, należy utworzyć arkusz kalkulacyjny z arkusza o nazwie `Contact`. Łącznik nie można użyć nazwy wyświetlanej w arkuszu kalkulacyjnym. Potrzeb nazwa wewnętrzna (pogrubiony), który ma być używany jako dataSetName, na przykład: `docs.google.com/spreadsheets/d/` **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** dodać nazwy kolumn `Id`, `LastName`, `FirstName` do pierwszego wiersza, a następnie wypełnimy danych na kolejnych wierszy.

### <a name="salesforce"></a>SalesForce

W tym przykładzie za pomocą usługi Salesforce, `dataSetName` jest "default".

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|----------------------|
|**type** | Musi być równa `apiHubTable`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. | 
|**połączenia**| Określa ustawienia aplikacji, która przechowuje parametry połączenia interfejsu API. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia interfejsu API w integracja interfejsu użytkownika.|
|**dataSetName**|Nazwa zestawu danych, który zawiera tabelę do odczytu.|
|**Właściwość TableName**|Nazwa tabeli|
|**Identyfikator jednostki**|Musi być pusta dla powiązania tabeli.

Tabelaryczne łącznik udostępnia zestawy danych, a każdy zestaw danych zawiera tabele. Nazwa domyślny zestaw danych to "default". Poniżej wymieniono tytułów do zestawu danych i tabelę w różnych dostawców SaaS:

|Łącznik|Zestaw danych|Tabela|
|:-----|:---|:---| 
|**SharePoint**|Witryna|Lista programu SharePoint
|**SQL**|Database (Baza danych)|Tabela 
|**Arkusz Google**|Arkusz kalkulacyjny|Arkusz 
|**Excel**|Plik programu Excel|Arkusz 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
