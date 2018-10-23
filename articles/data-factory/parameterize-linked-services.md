---
title: Parametryzacja połączone usługi w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdefiniować parametry połączone usługi w usłudze Azure Data Factory, a następnie przekazać wartości dynamicznych w czasie wykonywania.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: douglasl
ms.openlocfilehash: 99efd29165f381b9038758c3384774a65da91501
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649418"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametryzacja połączone usługi w usłudze Azure Data Factory

Możesz teraz parametryzacja połączonej usługi i przekazać wartości dynamicznych w czasie wykonywania. Na przykład jeśli chcesz nawiązać połączenie z różnych baz danych na tym samym serwerze Azure SQL Database teraz parametryzuj Nazwa bazy danych w definicji połączonej usługi. Ten sposób można uniknąć konieczności tworzenia połączonej usługi dla każdej bazy danych na serwerze bazy danych Azure SQL. Możecie się inne właściwości w definicji usługi połączonej także — na przykład *nazwy użytkownika.*

Interfejs użytkownika usługi Data Factory w witrynie Azure Portal lub interfejsu programowania można użyć próby parametryzacji połączonych usług.

> [!TIP]
> Nie zaleca się parametryzacja hasła lub kluczy tajnych. Zamiast tego Store wszystkie parametry połączenia w usłudze Azure Key Vault i sparametryzuj *nazw klucz tajny*.

7 minutowym wprowadzenie i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Magazyny danych obsługiwanych

W tej chwili parametryzacji połączonej usługi jest obsługiwana w interfejs użytkownika usługi Data Factory w witrynie Azure portal, w którym są następujące magazyny danych. Dla wszystkich innych magazynów danych można parametryzacja połączonej usługi, wybierając **kodu** ikony na kartach potoku i za pomocą edytora JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Usługa Azure Database for MySQL

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

![Dodaj zawartość dynamiczną do definicji usługi połączonej](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Tworzenie nowego parametru](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
