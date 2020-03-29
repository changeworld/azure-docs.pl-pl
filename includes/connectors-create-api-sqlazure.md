---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789204"
---
* Jeśli używasz usługi Azure SQL Database, wykonaj kroki opisane w obszarze [Łączenie się z bazą danych SQL platformy Azure](#connect-azure-sql-db).

* Jeśli używasz programu SQL Server, wykonaj kroki opisane w obszarze [Łączenie się z programem SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Łączenie się z bazą danych SQL platformy Azure

Gdy wyzwalacz SQL lub akcja monituje o informacje o połączeniu, wykonaj następujące kroki, które działają zarówno dla wyzwalaczy, jak i akcji.

1. W przypadku **nazwy połączenia**należy utworzyć nazwę połączenia.

1. W obszarze **Nazwa programu SQL Server**wybierz serwer SQL platformy Azure. Po wyświetleniu listy **Nazwa bazy danych SQL** wybierz bazę danych. Podaj nazwę użytkownika i hasło dla serwera SQL platformy Azure.

   Te informacje można również znaleźć w witrynie Azure portal w obszarze właściwości bazy danych SQL lub w ciągu połączenia:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Tworzenie połączenia z bazą danych SQL platformy Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Po utworzeniu połączenia kontynuuj [dodawanie wyzwalacza SQL](#add-sql-trigger) lub dodawanie akcji [SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

Gdy wyzwalacz SQL lub akcja monituje o informacje o połączeniu, wykonaj następujące kroki, które działają zarówno dla wyzwalaczy, jak i akcji. W scenariuszach, które wymagają [zainstalowania lokalnej bramy danych na](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) komputerze lokalnym i [utworzenia zasobu bramy danych platformy Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)upewnij się, że najpierw wykonasz te wymagania. W przeciwnym razie zasób bramy nie pojawi się na liście bram podczas tworzenia połączenia.

Ponadto, aby użyć uwierzytelniania systemu Windows z łącznikiem programu SQL Server w [środowisku usługi integracji (ISE),](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)należy użyć wersji programu łącznika innej niż ISE i lokalnej bramy danych. Wersja z etykietą ISE nie obsługuje uwierzytelniania systemu Windows.

1. W przypadku **nazwy połączenia**należy utworzyć nazwę połączenia.

1. W wyzwalaczu lub akcji wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych,** aby były wyświetlane opcje serwera SQL.

1. W przypadku **nazwy serwera SQL** i nazwy bazy danych **SQL**podaj adres serwera SQL i nazwę bazy danych. W przypadku **nazwy użytkownika** i **hasła**podaj nazwę użytkownika i hasło serwera.

   Informacje te można również znaleźć w ciągu połączenia:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Tworzenie połączenia z programem SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Jeśli serwer SQL korzysta z systemu Windows lub uwierzytelniania podstawowego, wybierz **opcję Typ uwierzytelniania**.

1. W obszarze **Bramy**wybierz subskrypcję platformy Azure skojarzoną z wcześniej utworzoną lokalną bramą danych i wybierz nazwę lokalnej bramy danych.

   Jeśli brama nie jest wyświetlana na liście, sprawdź, czy brama została poprawnie [skonfigurowana](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Ukończono tworzenie połączenia programu SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Po utworzeniu połączenia kontynuuj dodawanie [wyzwalacza SQL](#add-sql-trigger) lub [dodawanie akcji SQL](#add-sql-action).
