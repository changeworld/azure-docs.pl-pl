---
title: Konfigurowanie zapory bazy danych SQL DB usługi Azure Blockchain Workbench
description: Dowiedz się, jak skonfigurować zaporę bazy danych SQL usługi Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074953"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurowanie zapory bazy danych usługi Azure Blockchain Workbench

W tym artykule przedstawiono sposób konfigurowania reguły zapory przy użyciu witryny Azure Portal. Reguły zapory pozwalają klientom lub aplikacjom zewnętrznym na łączenie się z bazą danych usługi Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych, w której chcesz skonfigurować regułę:

1. Zaloguj się do witryny Azure Portal za pomocą konta z uprawnieniami **Właściciel** do zasobów usługi Azure Blockchain Workbench.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia usługi Blockchain Workbench.
4. Wybierz **Typ**, aby posortować listę zasobów, a następnie wybierz **program SQL Server**.
5. Przykładowa lista zasobów na poniższym zrzucie ekranu przedstawia dwie bazy danych: *master* i *lsgn-sdk*. Skonfigurujesz regułę zapory dla bazy danych *lsgn-sdk*.

![Lista zasobów usługi Blockchain Workbench](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Tworzenie reguły zapory bazy danych

Aby utworzyć regułę zapory:

1. Wybierz link do bazy danych „lsgn-sdk”.
2. Na pasku menu wybierz pozycję **Ustaw zaporę serwera**.

   ![Ustaw zaporę serwera](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Aby utworzyć regułę dla swojej organizacji:

   * Wprowadź nazwę w polu **NAZWA REGUŁY**
   * Wprowadź adres IP w polu **POCZĄTKOWY ADRES IP** zakresu adresów
   * Wprowadź adres IP w polu **KOŃCOWY ADRES IP** zakresu adresów

   ![Tworzenie reguły zapory](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Jeśli chcesz tylko dodać adres IP swojego komputera, wybierz pozycję **+ Dodaj adres IP klienta**.
        
1. Aby zapisać konfigurację zapory, wybierz pozycję **Zapisz**.
2. Przetestuj zakres adresów IP skonfigurowany dla bazy danych, nawiązując połączenie z poziomu aplikacji lub narzędzia, na przykład programu SQL Server Management Studio.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](blockchain-workbench-database-views.md)