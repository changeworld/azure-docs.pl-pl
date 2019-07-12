---
title: Konfigurowanie reguł zapory usługi Azure usług aplikacji przy użyciu nazw FQDN programu SQL
description: W tym artykule dowiesz się, jak skonfigurować SQL nazwy FQDN w regułach aplikacji zapory usługi Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786584"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurowanie reguł zapory usługi Azure usług aplikacji przy użyciu nazw FQDN programu SQL

> [!IMPORTANT]
> Reguły aplikacji przy użyciu nazw FQDN programu SQL dla zapory usługi Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teraz można skonfigurować reguły aplikacji zapory platformy Azure przy użyciu nazw FQDN programu SQL. Dzięki temu można ograniczyć dostęp z sieci wirtualnej tylko określonego wystąpienia programu SQL server.

Za pomocą nazwy FQDN programu SQL można filtrować ruch:

- Z sieci wirtualnych na bazę danych Azure SQL lub usługi Azure SQL Data Warehouse. Na przykład: Zezwalaj na dostęp do tylko *sql server1.database.windows.net*.
- Ze środowiska lokalnego do wystąpienia zarządzanego SQL Azure lub rozwiązanie SQL IaaS w Twoich sieciach wirtualnych.
- Ze szprychy do szprychy do wystąpienia zarządzanego SQL Azure lub rozwiązanie SQL IaaS w Twoich sieciach wirtualnych.

W publicznej wersji zapoznawczej, filtrowanie nazwa FQDN programu SQL jest obsługiwana w [tryb serwera proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) tylko (port 1433). Jeśli używasz programu SQL w domyślnym trybie przekierowania, możesz filtrować dostępu przy użyciu tagu usługi SQL w ramach [reguł sieciowych](overview.md#network-traffic-filtering-rules).
Jeśli używasz innych niż domyślne porty dla ruchu SQL IaaS w regułach zapory aplikacji można skonfigurować te porty.

> [!NOTE]
> Zasady aplikacji przy użyciu nazw FQDN programu SQL jest obecnie dostępna we wszystkich regionach za pomocą wiersza polecenia platformy Azure, REST i szablony. Interfejs użytkownika portalu jest dodawany do regionów stopniowo i będą dostępne we wszystkich regionach, po zakończeniu wdrożenia.

## <a name="configure-using-azure-cli"></a>Konfigurowanie przy użyciu wiersza polecenia platformy Azure

1. Wdrażanie [zapory platformy Azure przy użyciu wiersza polecenia platformy Azure](deploy-cli.md).
2. Jeśli możesz filtrować ruch do usługi Azure SQL Database, SQL Data Warehouse lub wystąpienia zarządzanego usługi SQL, upewnij się, tryb łączności SQL jest ustawiony na **Proxy**. Aby dowiedzieć się, jak przełączać tryb łączności SQL, zobacz [architektura łączności SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* tryb może spowodować opóźnienia więcej w porównaniu do *przekierowania*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, co jest ustawieniem domyślnym dla klientów łączących się w obrębie platformy Azure, można filtrować dostępu przy użyciu języka SQL [tag usługi](service-tags.md) w zaporze [reguł sieciowych](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Skonfiguruj reguły aplikacji za pomocą nazwy FQDN programu SQL, aby zezwolić na dostęp do programu SQL server:

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurowanie przy użyciu witryny Azure portal
1. Wdrażanie [zapory platformy Azure przy użyciu wiersza polecenia platformy Azure](deploy-cli.md).
2. Jeśli możesz filtrować ruch do usługi Azure SQL Database, SQL Data Warehouse lub wystąpienia zarządzanego usługi SQL, upewnij się, tryb łączności SQL jest ustawiony na **Proxy**. Aby dowiedzieć się, jak przełączać tryb łączności SQL, zobacz [architektura łączności SQL Azure](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* tryb może spowodować opóźnienia więcej w porównaniu do *przekierowania*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, co jest ustawieniem domyślnym dla klientów łączących się w obrębie platformy Azure, można filtrować dostępu przy użyciu języka SQL [tag usługi](service-tags.md) w zaporze [reguł sieciowych](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Dodawanie reguły aplikacji za pomocą odpowiedni protokół, port i nazwa FQDN programu SQL, a następnie wybierz pozycję **Zapisz**.
   ![Reguła aplikacji z nazwa FQDN programu SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Dostęp do programu SQL z maszyny wirtualnej w sieci wirtualnej, który filtruje ruch przez zaporę. 
5. Sprawdzić, czy [dzienniki zapory usługi Azure](log-analytics-samples.md) Pokaż ruch jest dozwolony.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o serwerze proxy SQL i przekierować trybów, zobacz [architektura łączności bazy danych Azure SQL](../sql-database/sql-database-connectivity-architecture.md).