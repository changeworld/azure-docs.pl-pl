---
title: 'Witryna Azure Portal: tworzenie bazy danych SQL | Microsoft Docs'
description: Utwórz serwer logiczny, regułę zapory na poziomie serwera i bazę danych usługi SQL Database w witrynie Azure Portal, a następnie wykonuj w niej zapytania.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165011"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Tworzenia bazy danych SQL platformy Azure w witrynie Azure Portal

Ten przewodnik Szybki start zawiera szczegółowe instrukcje dotyczące tworzenia bazy danych SQL Database na platformie Azure przy użyciu [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md). Usługa Azure SQL Database to oferta typu „baza danych jako usługa”, która pozwala na uruchamianie i skalowanie baz danych SQL Server o wysokiej dostępności w chmurze. Ten przewodnik Szybki start pokazuje, jak rozpocząć pracę od utworzenia bazy danych SQL, a następnie wykonywać zapytania za pomocą witryny Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

  >[!NOTE]
  >W tym samouczku jest używany model zakupu w oparciu o jednostki DTU, ale dostępny jest także [model zakupu w oparciu o rdzeń wirtualny](sql-database-service-tiers-vcore.md).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

Baza danych Azure SQL jest tworzona ze zdefiniowanym zestawem [zasobów obliczeniowych i przechowywania](sql-database-service-tiers-dtu.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) oraz na [serwerze logicznym bazy danych Azure SQL Database](sql-database-features.md).

Wykonaj te kroki, aby utworzyć bazę danych SQL zawierającą przykładowe dane firmy Adventure Works LT.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Nowy** w obszarze **SQL Database** wybierz pozycję **Utwórz**.

   ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

3. Wypełnij formularz Baza danych SQL w sposób pokazany na wcześniejszej ilustracji, używając następujących informacji:   

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa bazy danych** | mySampleDatabase | Prawidłowe nazwy baz danych opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). |
   | **Subskrypcja** | Twoja subskrypcja  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów**  | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Wybierz źródło** | Próbka (AdventureWorksLT) | Ładuje schemat AdventureWorksLT i dane do nowej bazy danych |

   > [!IMPORTANT]
   > Musisz wybrać przykładową bazę danych w tym formularzu, ponieważ jest ona używana w pozostałej części tego przewodnika Szybki start.
   >

4. W pozycji **Serwer** kliknij opcję **Skonfiguruj wymagane ustawienia** i wypełnij formularz serwera SQL (serwera logicznego) w sposób pokazany na wcześniejszej ilustracji, używając następujących informacji:   

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy identyfikatorów logowania opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). |
   | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej 8 znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
   | **Subskrypcja** | Twoja subskrypcja | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony platformy Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.
   >  

   ![tworzenie serwera bazy danych](./media/sql-database-get-started-portal/create-database-server.png)

5. Po wypełnieniu formularza kliknij pozycję **Wybierz**.

6. Kliknij pozycję **Warstwa cenowa**, aby określić warstwę usługi, liczbę jednostek DTU i ilość miejsca do magazynowania. Przejrzyj opcje liczby jednostek DTU i miejsca do magazynowania dostępne dla poszczególnych warstw usługi.

   > [!IMPORTANT]
   > Więcej niż 1 TB pamięci w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem poniższych: Północne Zjednoczone Królestwo, Zachodnio-środkowe stany USA, Południowe Zjednoczone Królestwo 2, Chiny Wschodnie, USDoDCentral, Niemcy Środkowe, USDoDEast, Południowo-Zachodnie Stany USA US Gov, Południowo-środkowe stany USA US Gov, Niemcy Północno-Wschodnie, Chiny Północne, Wschodnie stany USA US Gov. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Na potrzeby tego przewodnika Szybki start wybierz warstwę usługi **Standardowa**, a następnie wybierz za pomocą suwaka **10 jednostek DTU (S0)** i **1** GB miejsca do magazynowania.

   ![tworzenie bazy danych s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Zaakceptuj warunki wersji zapoznawczej, aby użyć opcji **dodatkowego magazynu**.

   > [!IMPORTANT]
   > Więcej niż 1 TB pamięci w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem poniższych: Zachodnio-środkowe stany USA, Chiny Wschodnie, USDoDCentral, US Gov Iowa, Niemcy Środkowe, USDoDEast, US Gov Southwest, Niemcy Północno-Wschodnie, Chiny Północne. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Po wybraniu warstwy serwera, liczby jednostek DTU i ilości miejsca do magazynowania kliknij przycisk **Zastosuj**.  

10. Teraz, po uzupełnieniu formularza usługi SQL Database, kliknij przycisk **Utwórz**, aby aprowizować bazę danych. Aprowizacja zajmuje kilka minut.

11. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.

     ![powiadomienie](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Wykonywanie zapytań względem bazy danych SQL

Teraz, po utworzeniu przykładowej bazy danych na platformie Azure, użyjemy wbudowanego narzędzia do obsługi zapytań w witrynie Azure Portal, aby potwierdzić, że możesz nawiązać połączenie z bazą danych i wysłać zapytanie dotyczące danych.

1. Na stronie SQL Database dla konkretnej bazy danych kliknij pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie, a następnie kliknij pozycję **Zaloguj się**.

   ![logowanie](./media/sql-database-get-started-portal/query-editor-login.png)

2. Wybierz opcję uwierzytelniania programu SQL Server, podaj informacje wymagane do zalogowania, a następnie kliknij przycisk **OK**, aby się zalogować.

3. Po uwierzytelnieniu się jako **administrator systemu** w okienku edytora zapytań wpisz poniższe zapytanie.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Kliknij pozycję **Uruchom**, a następnie przejrzyj wyniki zapytania w okienku **Wyniki**.

   ![wyniki edytora zapytań](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zamknij stronę **Edytor zapytań**, a następnie kliknij przycisk **OK**, aby odrzucić niezapisane zmiany.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zapisz te zasoby, jeśli chcesz przejść do sekcji [Następne kroki](#next-steps) i dowiedzieć się, jak połączyć bazę danych i wykonywać w niej zapytania przy użyciu różnych metod. Jeśli jednak chcesz usunąć zasoby utworzone w tym przewodniku Szybki start, wykonaj poniższe czynności.


1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy baza danych już istnieje, należy utworzyć regułę zapory na poziomie serwera, aby połączyć się z nią przy użyciu narzędzi w środowisku lokalnym. Zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-get-started-portal-firewall.md)
- W przypadku utworzenia reguły zapory na poziomie serwera, można [nawiązać połączenie i wykonywać zapytania](sql-database-connect-query.md) przy użyciu jednego z ulubionych narzędzi lub języków, w tym
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby tworzyć bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md)
- Aby tworzyć bazy danych przy użyciu środowiska Azure PowerShell, zobacz [Przykłady programu Azure PowerShell](sql-database-powershell-samples.md)