---
title: 'Szybki Start: Fivetran i magazyn danych'
description: Rozpocznij pracę z usługą Fivetran i magazynem danych usługi Azure Synapse Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b068b2436aaa1df22e3c83a54fb384f925149cc2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194618"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Szybki Start: Fivetran z magazynem danych 

W tym przewodniku szybki start opisano sposób konfigurowania nowego użytkownika Fivetran do pracy z magazynem danych usługi Azure Synapse Analytics, który został zainicjowany przy użyciu puli SQL. W tym artykule przyjęto założenie, że masz istniejący magazyn danych.

## <a name="set-up-a-connection"></a>Skonfiguruj połączenie

1. Znajdź w pełni kwalifikowaną nazwę serwera i nazwę bazy danych, która jest używana do nawiązywania połączenia z magazynem danych.
    
    Jeśli potrzebujesz pomocy w znalezieniu tych informacji, zobacz [nawiązywanie połączenia z magazynem danych](sql-data-warehouse-connect-overview.md).

2. W Kreatorze instalacji wybierz, czy chcesz połączyć bazę danych bezpośrednio, czy przy użyciu tunelu SSH.

   W przypadku wybrania opcji bezpośredniego połączenia z bazą danych należy utworzyć regułę zapory, aby zezwolić na dostęp. Ta metoda jest najprostszą i najbardziej bezpieczną metodą.

   W przypadku wybrania opcji nawiązywania połączenia przy użyciu tunelu SSH Fivetran łączy się z osobnym serwerem w sieci. Serwer udostępnia tunel SSH do bazy danych. Tej metody należy użyć, jeśli baza danych znajduje się w podsieci niedostępnej w sieci wirtualnej.

3. Dodaj adres IP **52.0.2.4** do zapory na poziomie serwera, aby zezwolić na połączenia przychodzące do wystąpienia magazynu danych z Fivetran.

   Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Skonfiguruj poświadczenia użytkownika

1. Połącz się z magazynem danych, korzystając z SQL Server Management Studio (SSMS) lub narzędzia, którego wolisz. Zaloguj się jako administrator serwera. Następnie uruchom następujące polecenia SQL, aby utworzyć użytkownika dla Fivetran:

    - W bazie danych Master: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - W bazie danych magazynu danych:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Przyznaj użytkownikowi Fivetran następujące uprawnienia do magazynu danych:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Uprawnienie Kontrola jest wymagane do utworzenia poświadczeń z zakresem bazy danych, które są używane podczas ładowania plików z usługi Azure Blob Storage za pomocą programu Base.

3. Dodaj odpowiednią klasę zasobów do użytkownika Fivetran. Używana Klasa zasobów zależy od pamięci wymaganej do utworzenia indeksu magazynu kolumn. Na przykład integracje z produktami takimi jak Marketo i Salesforce wymagają wyższego poziomu klasy zasobów ze względu na dużą liczbę kolumn i większą ilość danych używanych przez produkty. Wyższa Klasa zasobów wymaga więcej pamięci do tworzenia indeksów magazynu kolumn.

    Zalecamy korzystanie z klas zasobów statycznych. Możesz rozpocząć od klasy zasobów `staticrc20`. Klasa zasobów `staticrc20` przydziela każdemu użytkownikowi 200 MB, niezależnie od używanego poziomu wydajności. Jeśli indeksowanie magazynu kolumn kończy się niepowodzeniem na poziomie początkowej klasy zasobów, zwiększ klasę zasobów.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Aby uzyskać więcej informacji, zapoznaj się z tematem [pamięć i limity współbieżności](memory-concurrency-limits.md) oraz [klasy zasobów](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Zaloguj się do Fivetran

Aby zalogować się do Fivetran, wprowadź poświadczenia, które są używane w celu uzyskania dostępu do magazynu danych: 

* Host (nazwa serwera).
* Przewożąc.
* Database.
* Użytkownik (nazwa użytkownika powinna być **fivetran\@_server_name_**  , gdzie *server_name* jest częścią identyfikatora URI hosta platformy Azure:  **_Server\_Name_. Database.Windows.NET**).
* Hasło.
