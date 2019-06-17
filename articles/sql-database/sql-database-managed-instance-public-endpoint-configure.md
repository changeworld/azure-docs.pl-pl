---
title: Konfigurowanie publicznego punktu końcowego — w usłudze Azure SQL Database wystąpienia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować publiczny punkt końcowy dla wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465162"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurowanie publicznego punktu końcowego w usłudze Azure SQL Database, wystąpienia zarządzanego

Publiczny punkt końcowy dla [wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umożliwia dostęp do danych do wystąpienia zarządzanego z poza [sieci wirtualnej](../virtual-network/virtual-networks-overview.md). Jesteś w stanie uzyskać dostęp do Twojego wystąpienia zarządzanego z wielodostępnych usług platformy Azure, takich jak Power BI, usługa Azure App Service lub siecią lokalną. Korzystając z publicznym punktem końcowym w wystąpieniu zarządzanym, nie trzeba używać sieci VPN, co może pomóc uniknąć problemów przepływność sieci VPN.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> - Włączanie publicznego punktu końcowego dla Twojego wystąpienia zarządzanego w witrynie Azure portal
> - Włączanie publicznego punktu końcowego dla Twojego wystąpienia zarządzanego przy użyciu programu PowerShell
> - Konfigurowanie wystąpienia zarządzanego sieciowej grupy zabezpieczeń zezwalającą na ruch do publicznego punktu końcowego wystąpienia zarządzanego
> - Uzyskaj parametry połączenia publicznego punktu końcowego wystąpienia zarządzanego

## <a name="permissions"></a>Uprawnienia

Z powodu poufności danych w wystąpieniu zarządzanym konfigurację, aby włączyć publiczny punkt końcowy wystąpienie zarządzane wymaga procesem dwuetapowym. To zabezpieczenie działa zgodnie z rozdzielenia obowiązków (darni):

- Włączanie publicznego punktu końcowego na wystąpienie zarządzane musi odbywać się przez administratora wystąpienia zarządzanego. Administrator wystąpienia zarządzanego można znaleźć na **Przegląd** strony SQL zasobu wystąpienia zarządzanego.
- Zezwala na ruch za pomocą sieciowej grupy zabezpieczeń, który ma odbywać się przez administratora sieci. Aby uzyskać więcej informacji, zobacz [uprawnienia grupy zabezpieczeń sieci](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego w witrynie Azure portal

1. Uruchamianie witryny Azure portal pod <https://portal.azure.com/.>
1. Otwórz grupę zasobów za pomocą wystąpienia zarządzanego, a następnie wybierz pozycję **wystąpienie zarządzane SQL** chcesz skonfigurować publiczny punkt końcowy na.
1. Na **zabezpieczeń** ustawienia, wybierz opcję **sieć wirtualna** kartę.
1. Na stronie konfiguracji sieci wirtualnej wybierz **Włącz** i następnie **Zapisz** ikonę, aby zaktualizować konfigurację.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego przy użyciu programu PowerShell

### <a name="enable-public-endpoint"></a>Włączanie publicznego punktu końcowego

Uruchom następujące polecenia programu PowerShell. Zastąp **identyfikator subskrypcji** przy użyciu identyfikatora subskrypcji. Zastąp również **rg-name** z grupą zasobów dla wystąpienia zarządzanego i Zastąp **mi-name** nazwą wystąpienia zarządzanego.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Wyłącz publicznego punktu końcowego

Aby wyłączyć publicznego punktu końcowego przy użyciu programu PowerShell, można będzie wykonaj następujące polecenie (i również Pamiętaj zamknąć sieciowej grupy zabezpieczeń dla portu wejściowego 3342, jeśli została ona skonfigurowana):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Zezwalaj na ruch publiczny punkt końcowy na sieciową grupę zabezpieczeń

1. Jeśli na stronie konfiguracji wystąpienia zarządzanego, która jest wciąż otwarty, przejdź do **Przegląd** kartę. W przeciwnym razie przejdź wstecz do Twojej **wystąpienie zarządzane SQL** zasobów. Wybierz **podsieci sieci wirtualnej** łącza, co spowoduje przejście do strony konfiguracji sieci wirtualnej.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Wybierz **podsieci** w okienku po lewej stronie konfiguracji sieci wirtualnej, a następnie zanotuj **grupy zabezpieczeń** wystąpienia zarządzanego.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Wróć do swojej grupy zasobów, który zawiera Twoje wystąpienie zarządzane. Powinien zostać wyświetlony **sieciowej grupy zabezpieczeń** nazwa przedstawionych powyżej. Wybierz nazwę aby przejść do strony konfiguracji grupy zabezpieczeń sieci.

1. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** kartę, a **Dodaj** regułę, która ma wyższy priorytet niż **deny_all_inbound** reguły z następującymi ustawieniami: </br> </br>

    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    |**Element źródłowy**     |Adresy IP lub tag usługi         |<ul><li>Dla usług platformy Azure, takich jak Power BI wybierz Tag usługi w chmurze Azure</li> <li>Na komputerze lub maszynie Wirtualnej platformy Azure Użyj adresu IP translatora adresów Sieciowych</li></ul> |
    |**Zakresów portów źródłowych**     |*         |Ten element, aby pozostawić * (wszystkie) zgodnie z portów źródłowych są zwykle nieprzewidywalna dynamicznie przydzielonego i jako takie, |
    |**miejsce docelowe**     |Dowolne         |Pozostawienie docelowy jako zezwalająca na ruch do podsieci wystąpienia zarządzanego |
    |**Docelowe zakresy portów**     |3342         |Zakres docelowy port 3342, który jest wystąpienie zarządzane publicznego punktu końcowego strumienia TDS |
    |**Protokół**     |TCP         |Zarządzany protokół TCP używa wystąpienia TDS |
    |**Akcja**     |Zezwalaj         |Zezwalaj na ruch przychodzący do wystąpienia zarządzanego za pośrednictwem publicznego punktu końcowego |
    |**Priorytet**     |1300         |Upewnij się, że ta reguła ma wyższy priorytet niż **deny_all_inbound** reguły |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 jest używany dla publicznego punktu końcowego połączenia do wystąpienia zarządzanego i nie można zmienić w tym momencie.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Uzyskiwanie parametrów połączenia publicznego punktu końcowego wystąpienia zarządzanego

1. Przejdź do strony konfiguracji wystąpienia zarządzanego SQL, który został włączony dla publicznego punktu końcowego. Wybierz **parametry połączenia** karcie **ustawienia** konfiguracji.
1. Należy pamiętać, że nazwa hosta punktu końcowego publicznego jest dostępna w formacie < mi_name >. **publicznych**. < dns_zone >. database.windows.net i że port używany dla połączenia jest 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [za pomocą usługi Azure SQL Database managed wystąpienie bezpiecznie z publicznym punktem końcowym](sql-database-managed-instance-public-endpoint-securely.md).