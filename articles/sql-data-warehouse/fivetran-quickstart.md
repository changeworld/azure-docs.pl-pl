---
title: Fivetran — Szybki Start
description: Szybko Rozpocznij pracę z Fivetranami i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c9b4a15cd6cbae80d80407ba929bfbfa1402eeb5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689234"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Szybko Rozpocznij pracę z Fivetranami i SQL Data Warehouse

W tym przewodniku szybki start opisano sposób konfigurowania nowego użytkownika Fivetran do pracy z Azure SQL Data Warehouse. W tym artykule przyjęto założenie, że masz istniejące wystąpienie SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Skonfiguruj połączenie

1. Znajdź w pełni kwalifikowaną nazwę serwera i nazwę bazy danych, która jest używana do nawiązywania połączenia z SQL Data Warehouse.
    
    Jeśli potrzebujesz pomocy w znalezieniu tych informacji, zobacz [nawiązywanie połączenia z Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. W Kreatorze instalacji wybierz, czy chcesz połączyć bazę danych bezpośrednio, czy przy użyciu tunelu SSH.

   W przypadku wybrania opcji bezpośredniego połączenia z bazą danych należy utworzyć regułę zapory, aby zezwolić na dostęp. Ta metoda jest najprostszą i najbardziej bezpieczną metodą.

   W przypadku wybrania opcji nawiązywania połączenia przy użyciu tunelu SSH Fivetran nawiązuje połączenie z oddzielnym serwerem w sieci. Serwer udostępnia tunel SSH do bazy danych. Tej metody należy użyć, jeśli baza danych znajduje się w podsieci niedostępnej w sieci wirtualnej.

3. Dodaj adres IP **52.0.2.4** do zapory na poziomie serwera, aby zezwolić na połączenia przychodzące do wystąpienia SQL Data Warehouse z Fivetran.

   Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Skonfiguruj poświadczenia użytkownika

1. Połącz się z Azure SQL Data Warehouse przy użyciu SQL Server Management Studio lub preferowanego narzędzia. Zaloguj się jako administrator serwera. Następnie uruchom następujące polecenia SQL, aby utworzyć użytkownika dla Fivetran:
    - W bazie danych Master: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - W SQL Data Warehouse Database:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Przyznaj użytkownikowi Fivetran następujące uprawnienia do hurtowni:

    ```
    GRANT CONTROL to fivetran;
    ```

    Uprawnienie Kontrola jest wymagane do utworzenia poświadczeń z zakresem bazy danych, które są używane podczas ładowania plików z usługi Azure Blob Storage za pomocą programu Base.

3. Dodaj odpowiednią klasę zasobów do użytkownika Fivetran. Używana Klasa zasobów zależy od pamięci wymaganej do utworzenia indeksu magazynu kolumn. Na przykład integracje z produktami takimi jak Marketo i Salesforce wymagają wyższego poziomu klasy zasobów ze względu na dużą liczbę kolumn i większą ilość danych używanych przez produkty. Wyższa Klasa zasobów wymaga więcej pamięci do tworzenia indeksów magazynu kolumn.

    Zalecamy korzystanie z klas zasobów statycznych. Możesz rozpocząć od klasy zasobów `staticrc20`. Klasa zasobów `staticrc20` przydziela każdemu użytkownikowi 200 MB, niezależnie od używanego poziomu wydajności. Jeśli indeksowanie magazynu kolumn kończy się niepowodzeniem na poziomie początkowej klasy zasobów, zwiększ klasę zasobów.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Aby uzyskać więcej informacji, zapoznaj się z tematem [pamięć i limity współbieżności](memory-concurrency-limits.md) oraz [klasy zasobów](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Zaloguj się do Fivetran

Aby zalogować się do Fivetran, wprowadź poświadczenia, których używasz do uzyskiwania dostępu do SQL Data Warehouse: 

* Host (nazwa serwera).
* Przewożąc.
* Baza danych.
* Użytkownik (nazwa użytkownika powinna być **fivetran\@_server_name_**  , gdzie *server_name* jest częścią identyfikatora URI hosta platformy Azure:  **_Server\_Name_. Database.Windows.NET**).
* Hasło.
