---
title: Konfigurowanie reguł aplikacji Zapory platformy Azure przy za pomocą sieci AQDN SQL
description: W tym artykule dowiesz się, jak skonfigurować nazwy FQDN SQL w regułach aplikacji Zapory platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501502"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurowanie reguł aplikacji Zapory platformy Azure przy za pomocą sieci AQDN SQL

> [!IMPORTANT]
> Reguły aplikacji Zapory platformy Azure z sieciami FQDN SQL są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teraz można skonfigurować reguły aplikacji Zapory platformy Azure za pomocą sieci AQDN SQL. Dzięki temu można ograniczyć dostęp z sieci wirtualnych tylko do określonych wystąpień serwera SQL.

Dzięki sieciom FQDN SQL można filtrować ruch:

- Od sieci wirtualnych do bazy danych SQL azure lub usługi Azure SQL Data Warehouse. Na przykład: Zezwalaj tylko na dostęp do *sql-server1.database.windows.net*.
- Od lokalnych do wystąpień zarządzanych sql sql platformy Azure lub SQL IaaS uruchomionych w sieciach wirtualnych.
- Od szprychy do wystąpienia zarządzanego sql sql lub SQL IaaS uruchomiony w sieciach wirtualnych.

Podczas publicznej wersji zapoznawczej filtrowanie FQDN SQL jest obsługiwane tylko w [trybie proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Jeśli używasz SQL w domyślnym trybie przekierowania, możesz filtrować dostęp przy użyciu tagu usługi SQL jako części [reguł sieciowych](overview.md#network-traffic-filtering-rules).
Jeśli używasz portów innych niż domyślne dla ruchu IaaS SQL, można skonfigurować te porty w regułach aplikacji zapory.

Reguły aplikacji z nazwami FQDN SQL są obecnie dostępne we wszystkich regionach za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure, REST i szablonów.

## <a name="configure-using-azure-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

1. Wdrażanie [zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
2. W przypadku filtrowania ruchu do bazy danych SQL, magazynu danych SQL lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **Serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Tryb *serwera proxy* SQL może spowodować większe opóźnienie w porównaniu do *przekierowania*. Jeśli chcesz nadal korzystać z trybu przekierowania, który jest domyślny dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu [tagu usługi](service-tags.md) SQL w [regułach sieci](tutorial-firewall-deploy-portal.md#configure-a-network-rule)zapory .

3. Skonfiguruj regułę aplikacji przy pomocą sieci FQDN SQL, aby zezwolić na dostęp do serwera SQL:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurowanie przy użyciu witryny Azure Portal
1. Wdrażanie [zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
2. W przypadku filtrowania ruchu do bazy danych SQL, magazynu danych SQL lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **Serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Tryb *serwera proxy* SQL może spowodować większe opóźnienie w porównaniu do *przekierowania*. Jeśli chcesz nadal korzystać z trybu przekierowania, który jest domyślny dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu [tagu usługi](service-tags.md) SQL w [regułach sieci](tutorial-firewall-deploy-portal.md#configure-a-network-rule)zapory .
3. Dodaj regułę aplikacji z odpowiednim protokołem, portem i numerem FQDN SQL, a następnie wybierz pozycję **Zapisz**.
   ![reguła aplikacji z omdnem SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Dostęp sql z maszyny wirtualnej w sieci wirtualnej, która filtruje ruch za pośrednictwem zapory. 
5. Sprawdź, czy [dzienniki zapory platformy Azure](log-analytics-samples.md) pokazują, że ruch jest dozwolony.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o trybach serwera proxy i przekierowania SQL, zobacz [architektura łączności bazy danych SQL platformy Azure](../sql-database/sql-database-connectivity-architecture.md).