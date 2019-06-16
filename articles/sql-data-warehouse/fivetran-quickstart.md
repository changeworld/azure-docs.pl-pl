---
title: Fivetran Przewodnik Szybki start dotyczący usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Szybko Rozpocznij pracę z Fivetran i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d829ee67d516892283fa31d9180336d768170ac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65857016"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Szybko Rozpocznij pracę dzięki Fivetran i SQL Data Warehouse

Ten przewodnik Szybki Start opisuje sposób konfigurowania nowego użytkownika Fivetran do pracy z usługi Azure SQL Data Warehouse. Tego artykułu przyjęto założenie, że masz istniejące wystąpienie programu SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Konfigurowanie połączenia

1. Dowiedz się, w pełni kwalifikowaną nazwę serwera i nazwę bazy danych, którego używasz do łączenia z usługą SQL Data Warehouse.
    
    Jeśli potrzebujesz pomocy w znalezieniu tych informacji, zobacz [nawiązywanie połączenia z usługi Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. W Kreatorze instalacji wybierz opcję Połącz swoją bazę danych, bezpośrednio lub przy użyciu tunelu SSH.

   Jeśli użytkownik chce nawiązać bezpośrednie połączenie bazy danych, należy utworzyć regułę zapory, aby zezwolić na dostęp. Ta metoda jest metodą najprostszy i najbezpieczniejszy.

   Jeśli zdecydujesz się połączyć przy użyciu tunelu SSH, Fivetran łączy się z oddzielnych serwera w sieci. Serwer udostępnia tunelu SSH z bazą danych. Tej metody należy użyć, jeśli baza danych znajduje się w niedostępnym podsieci w sieci wirtualnej.

3. Dodaj adres IP **52.0.2.4** do zapory poziomu serwera od Fivetran zezwalać na połączenia przychodzące do wystąpienia usługi SQL Data Warehouse.

   Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Skonfiguruj poświadczenia użytkownika

1. Podłącz do usługi Azure SQL Data Warehouse przy użyciu programu SQL Server Management Studio lub narzędzie, którego chcesz używać. Zaloguj się jako użytkownik Administrator serwera. Następnie uruchom następujące polecenia SQL, aby utworzyć użytkownika dla Fivetran:
    - W głównej bazie danych: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - W bazie danych SQL Data Warehouse:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Przyznaj użytkownikowi Fivetran następujące uprawnienia do magazynu:

    ```
    GRANT CONTROL to fivetran;
    ```

    Aby utworzyć poświadczeń o zakresie bazy danych, które są używane, gdy użytkownik wczytuje pliki z usługi Azure Blob storage przy użyciu programu PolyBase, wymagane jest uprawnienie kontroli.

3. Dodaj klasę odpowiedni zasób użytkownikowi Fivetran. Klasa zasobów, których używasz, zależy od pamięci, które są wymagane do tworzenia indeksu magazynu kolumn. Na przykład integracji z produktami, takie jak Marketo i Salesforce wymagają wyższej klasie zasobów z powodu dużej liczby kolumn oraz większe ilości danych produktów użyć. Wyższe klasy zasobów wymaga większej ilości pamięci do tworzenie indeksów magazynu kolumn.

    Firma Microsoft zaleca użycie statycznych klas zasobów. Można zacząć od `staticrc20` klasy zasobów. `staticrc20` Klasy zasobów przydziela 200 MB dla każdego użytkownika, niezależnie od poziomu wydajności możesz użyć. W przypadku magazynu kolumn nie powiedzie się na poziomie klasy początkowej zasobów, należy zwiększyć klasy zasobów.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Aby uzyskać więcej informacji, przeczytaj o [limity pamięci i współbieżności](memory-and-concurrency-limits.md) i [klasy zasobów](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Zaloguj się do Fivetran

Aby zalogować się do Fivetran, wprowadź poświadczenia, które umożliwiają dostęp SQL Data Warehouse: 

* Host (nazwa serwera).
* Port.
* Baza danych.
* Użytkownik (nazwa użytkownika powinna być **fivetran\@_nazwa_serwera_**  gdzie *nazwa_serwera* jest częścią platformy Azure hosta identyfikatora URI: ***nazwa_serwera *. Database.Windows.NET**).
* Hasło.
