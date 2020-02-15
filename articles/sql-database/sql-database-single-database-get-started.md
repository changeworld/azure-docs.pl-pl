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
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252155"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure

Tworzenie [pojedynczej bazy danych](sql-database-single-database.md) to najszybsza i najprostsza opcja wdrażania w przypadku tworzenia bazy danych w usłudze Azure SQL Database. W tym przewodniku Szybki start przedstawiono, jak utworzyć pojedynczą bazę danych i wykonywać względem jej zapytania za pomocą witryny Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

W przypadku wszystkich kroków z tego przewodnika Szybki start musisz zalogować się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedynczą bazę danych można utworzyć w warstwie obliczeniowej zainicjowanej lub bezserwerowej.

- Pojedyncza baza danych w warstwie obliczeniowej zainicjowanej ma wstępnie przydzieloną stałą ilość zasobów obliczeniowych, w tym użycie procesora i pamięci przy użyciu jednego z dwóch [modeli zakupu](sql-database-purchase-models.md).
- Pojedyncza baza danych w warstwie obliczeń bezserwerowej ma zakres zasobów obliczeniowych, w tym procesor i pamięć, które są automatycznie skalowane i są dostępne tylko w [modelach zakupów opartych na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

Podczas tworzenia pojedynczej bazy danych definiowany jest również [serwer usługi SQL Database](sql-database-servers.md) służący do zarządzania tą bazą danych i umieszczania jej w ramach [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w określonym regionie.

> [!NOTE]
> Ten przewodnik Szybki Start korzysta z [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md), ale jest również dostępny [model zakupu oparty](sql-database-service-tiers-DTU.md) na jednostkach DTU.

Aby utworzyć bazę danych zawierającą przykładowe dane firmy Adventure Works LT:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Teraz, po utworzeniu bazy danych, użyjemy wbudowanego narzędzia do obsługi zapytań w witrynie Azure Portal, aby nawiązać połączenie z bazą danych i wykonać zapytanie dotyczące danych.

1. Na stronie **SQL Database** dla używanej bazy danych wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie.

   ![Logowanie w Edytorze zapytań](./media/sql-database-get-started-portal/query-editor-login.png)

2. Wprowadź informacje dotyczące logowania, a następnie wybierz przycisk **OK**.
3. Wprowadź następujące zapytanie w okienku **Edytora zapytań**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Wybierz opcję **Uruchom**, a następnie przejrzyj wyniki zapytania w okienku **Wyniki**.

   ![Wyniki Edytora zapytań](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zamknij stronę **Edytor zapytań**, a następnie kliknij przycisk **OK** po wyświetleniu monitu o odrzucenie niezapisanych zmian.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zachowaj tę grupę zasobów, serwer bazy danych i pojedynczą bazę danych, jeśli chcesz przejść do [następnych kroków](#next-steps). W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Po zakończeniu korzystania z tych zasobów możesz je usunąć w następujący sposób:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź w polu ciąg *myResourceGroup*, a następnie wybierz opcję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](sql-database-connect-query.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeniowej zainicjowanej przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeniowej zainicjowanej przy użyciu Azure PowerShell, zobacz [Azure PowerShell Samples](sql-database-powershell-samples.md).
- Aby utworzyć pojedynczą bazę danych w warstwie obliczeń bezserwerowej przy użyciu programu Azure PowerShell, zobacz [Tworzenie bezserwerowej bazy danych](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
