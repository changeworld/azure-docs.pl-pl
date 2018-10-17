---
title: Szybki start Fivetran za pomocą usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Szybko Rozpocznij pracę z Fivetran i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355475"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Szybko Rozpocznij pracę dzięki Fivetran i SQL Data Warehouse

Ten przewodnik Szybki Start założono, że masz już istniejące wystąpienie usługi SQL Data Warehouse.

## <a name="setup-connection"></a>Konfigurowanie połączenia

1. Znajdowanie w pełni kwalifikowaną nazwę serwera i nazwę bazy danych do łączenia z usługą Azure SQL Data Warehouse.

   [Jak znaleźć nazwę serwera i nazwę bazy danych z poziomu portalu?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. W Kreatorze instalacji należy zdecydować, jeśli chcesz się łączyć z bazy danych, bezpośrednio lub za pośrednictwem tunelu SSH.

   Jeśli zdecydujesz połączyć się bezpośrednio z bazą danych, należy utworzyć regułę zapory, aby zezwolić na dostęp. Ta metoda jest metodą najprostszy i najbezpieczniejszy.

   Jeśli zdecydujesz się nawiązać połączenie za pośrednictwem tunelu SSH, Fivetran połączy się na oddzielnym serwerze w sieci, która zapewnia tunelu SSH z bazą danych. Ta metoda jest niezbędna, jeśli baza danych znajduje się w niedostępnym podsieci w sieci wirtualnej.

3. Dodaj "52.0.2.4" adres IP zapory poziomu serwera z Fivetran zezwalać na połączenia przychodzące do usługi Azure SQL Data Warehouse.

   [Jak dodać zapory poziomu serwera?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Konfigurowanie poświadczeń użytkownika

Nawiązywanie połączenia usługi Azure SQL Data Warehouse przy użyciu programu SQL Server Management Studio lub w wybranym narzędziu jako administrator serwera i wykonaj następujące polecenia SQL, aby utworzyć użytkownika dla Fivetran:

W głównej bazie danych: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

W bazie danych SQL Data Warehouse:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Po utworzeniu użytkownika fivetran przyznania następujące uprawnienia do magazynu:

```
GRANT CONTROL to fivetran;
```

Dodaj klasę odpowiedni zasób na konto utworzonego użytkownika, w zależności od wymagania dotyczące pamięci dla tworzenia indeksu magazynu kolumn. Na przykład integracji, takich jak Marketo i Salesforce muszą wyższą klasą zasobu z powodu dużej liczby kolumn / większej ilości danych, który wymaga większej ilości pamięci do tworzenie indeksów magazynu kolumn.

Zaleca się użycie statycznych klas zasobów. Można zacząć od klasy zasobów `staticrc20`, który przydziela dla użytkownika, niezależnie od poziomu wydajności, możesz użyć 200 MB. Za pomocą bieżącej klasy zasobów magazynu kolumn nie powiedzie się, mamy zwiększyć klasy zasobów.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentów dla [limity pamięci i współbieżności](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) i [klasy zasobów](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)

Aby utworzyć poświadczenia bazy danych, które będą używane podczas ładowania plików z magazynu obiektów Blob przy użyciu technologii PolyBase, wymagane jest uprawnienie kontroli.

Wprowadź poświadczenia, które mają dostęp do usługi Azure SQL Data Warehouse

1. Hosta (nazwa serwera)
2. Port
3. Database (Baza danych)
4. Użytkownik (nazwa użytkownika powinna być `fivetran@<server_name>` gdzie `<server_name>` jest częścią identyfikatora uri hosta platformy azure: `<server_name>.database.windows.net`)
5. Hasło