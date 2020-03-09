---
title: Tworzenie pojedynczej bazy danych
description: Utwórz i zbadaj pojedynczą bazę danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 02/14/2020
ms.openlocfilehash: 2dacdfaa5443707ab82ae53922ac439319375276
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359814"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure

Tworzenie [pojedynczej bazy danych](sql-database-single-database.md) jest najszybszą i najprostszą opcją wdrażania służącą do tworzenia bazy danych w Azure SQL Database. W tym przewodniku szybki start przedstawiono sposób tworzenia i wykonywania zapytań dotyczących pojedynczej bazy danych przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/). 

W przypadku wszystkich kroków tego przewodnika Szybki Start Zaloguj się do [Azure Portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedynczą bazę danych można utworzyć w warstwie obliczeniowej zainicjowanej lub bezserwerowej.

- Pojedyncza baza danych w warstwie obliczeniowej zainicjowanej ma wstępnie przydzieloną stałą ilość zasobów obliczeniowych, w tym użycie procesora i pamięci przy użyciu jednego z dwóch [modeli zakupu](sql-database-purchase-models.md).
- Pojedyncza baza danych w warstwie obliczeń bezserwerowej ma zakres zasobów obliczeniowych, w tym procesor i pamięć, które są automatycznie skalowane i są dostępne tylko w [modelach zakupów opartych na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

Podczas tworzenia pojedynczej bazy danych należy również zdefiniować [serwer SQL Database](sql-database-servers.md) , aby nim zarządzać, i umieścić go w [grupie zasobów platformy Azure](../azure-resource-manager/management/overview.md) w określonym regionie.

> [!NOTE]
> Ten przewodnik Szybki Start korzysta z [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md), ale jest również dostępny [model zakupu oparty](sql-database-service-tiers-DTU.md) na jednostkach DTU.

Aby utworzyć pojedynczą bazę danych zawierającą dane przykładowe AdventureWorksLT:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Tworzenie zapytań względem bazy danych

Po utworzeniu bazy danych Użyj wbudowanego narzędzia do wykonywania zapytań w Azure Portal, aby nawiązać połączenie z bazą danych i wykonać zapytanie dotyczące danych.

1. Na stronie **SQL Database** bazy danych wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie.

   ![Zaloguj się do edytora zapytań](./media/sql-database-get-started-portal/query-editor-login.png)

2. Wprowadź informacje logowania, a następnie wybierz **przycisk OK**.
3. Wprowadź następujące zapytanie w okienku **edytora zapytań** .

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Wybierz pozycję **Uruchom**, a następnie przejrzyj wyniki zapytania w okienku **wyników** .

   ![Wyniki edytora zapytań](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zamknij stronę **Edytor zapytań** , a następnie wybierz **przycisk OK** po wyświetleniu monitu, aby odrzucić niezapisane zmiany.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do [następnych kroków](#next-steps), Zachowaj tę grupę zasobów, serwer bazy danych i pojedynczą bazę danych. W następnych krokach pokazano, jak nawiązać połączenie z bazą danych i wysyłać do niej zapytania przy użyciu różnych metod.

Po zakończeniu korzystania z tych zasobów można je usunąć w następujący sposób:

1. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **grupy zasobów**, a następnie wybierz pozycję Moja **resourceName**.
2. Na stronie Grupa zasobów wybierz pozycję **Usuń grupę zasobów**.
3. W polu Wprowadź nazwę *zasobu* , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Utwórz regułę zapory na poziomie serwera, aby nawiązać połączenie z pojedynczą bazą danych z poziomu narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).
- Po utworzeniu reguły zapory na poziomie serwera [Nawiąż połączenie z bazą danych i zbadaj](sql-database-connect-query.md) ją przy użyciu kilku różnych narzędzi i języków.
  - [Łączenie i wykonywanie zapytań przy użyciu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Łączenie i wykonywanie zapytań przy użyciu Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeniowej zainicjowanej przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeniowej zainicjowanej przy użyciu Azure PowerShell, zobacz [Azure PowerShell Samples](sql-database-powershell-samples.md).
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeń bezserwerowej przy użyciu programu Azure PowerShell, zobacz [Tworzenie bezserwerowej bazy danych](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
