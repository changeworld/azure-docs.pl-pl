---
title: Skonfiguruj zaporę bazy danych SQL Workbench Blockchain Azure
description: Dowiedz się, jak skonfigurować zaporę bazy danych SQL Workbench Blockchain platformy Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dc22f212c014ab1d6622eff3491d669b21ca6f47
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Skonfiguruj zaporę bazy danych Azure Blockchain Workbench

W tym artykule przedstawiono sposób konfigurowania reguły zapory przy użyciu portalu Azure. Reguły zapory zezwala klientom na zewnętrznych lub aplikacje połączenia z bazą danych Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Połączenie z bazą danych Blockchain Workbench

Aby podłączyć się do bazy danych, w której chcesz skonfigurować regułę:

1. Zaloguj się do portalu Azure za pomocą konta mającego **właściciela** uprawnienia do zasobów Azure Blockchain Workbench.
2. W okienku nawigacji po lewej stronie wybierz **grup zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia Blockchain Workbench.
4. Wybierz **typu** sortowania listy zasobów, a następnie wybierz pozycję z **programu SQL server**.
5. Przykład listy zasobów w następujących zrzucie ekranu przedstawiono dwie bazy danych: *wzorca* i *lsgn sdk*. Konfigurowanie reguł zapory na *lsgn sdk*.

![Lista zasobów Blockchain Workbench](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Tworzenie reguły zapory bazy danych

Aby utworzyć regułę zapory:

1. Wybierz link do bazy danych "lsgn zestawu sdk".
2. Na pasku menu wybierz **ustawić Zapora serwera**.

   ![Ustaw zaporę serwera](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Aby utworzyć regułę dla Twojej organizacji:

   * Wprowadź **Nazwa reguły**
   * Wprowadź adres IP dla **START IP** zakresu adresów
   * Wprowadź adres IP dla **KOŃCOWEMU adresowi IP** zakresu adresów

   ![Tworzenie reguły zapory](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Jeśli chcesz dodać adres IP komputera, wybierz **+ Dodaj adres IP klienta**.
        
1. Aby zapisać konfigurację zapory, wybierz **zapisać**.
2. Test skonfigurowany dla bazy danych, nawiązując połączenie z aplikacji lub zakres adresów IP. Na przykład SQL Server Management Studio.

## <a name="next-steps"></a>Kolejne kroki

* [Widoki bazy danych w Azure Blockchain Workbench](blockchain-workbench-database-views.md)