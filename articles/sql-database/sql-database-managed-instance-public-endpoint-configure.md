---
title: Konfigurowanie publicznego punktu końcowego — wystąpienie zarządzane
description: Dowiedz się, jak skonfigurować publiczny punkt końcowy dla wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256161"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym usługi Azure SQL Database

Publiczny punkt końcowy dla [wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umożliwia dostęp danych do wystąpienia zarządzanego spoza [sieci wirtualnej.](../virtual-network/virtual-networks-overview.md) Możesz uzyskać dostęp do wystąpienia zarządzanego z usług platformy Azure z wieloma dzierżawców, takich jak Usługa Power BI, Usługa Azure App Service lub sieci lokalnej. Korzystając z publicznego punktu końcowego w wystąpieniu zarządzanym, nie trzeba używać sieci VPN, co może pomóc uniknąć problemów z przepływnością sieci VPN.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> - Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego w witrynie Azure portal
> - Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego przy użyciu programu PowerShell
> - Konfigurowanie sieciowej grupy zabezpieczeń wystąpienia zarządzanego w celu zezwalania na ruch do publicznego punktu końcowego wystąpienia zarządzanego
> - Uzyskiwanie ciągu połączenia publicznego punktu końcowego wystąpienia zarządzanego

## <a name="permissions"></a>Uprawnienia

Ze względu na wrażliwość danych, która znajduje się w wystąpieniu zarządzanym, konfiguracja, aby włączyć publicznego punktu końcowego wystąpienia zarządzanego wymaga procesu dwuetapowego. Ten środek bezpieczeństwa jest zgodny z podziałem obowiązków (SoD):

- Włączenie publicznego punktu końcowego w wystąpieniu zarządzanym musi być wykonane przez administratora wystąpienia zarządzanego. Administrator wystąpienia zarządzanego można znaleźć na stronie **Przegląd** zasobu wystąpienia zarządzanego SQL.
- Zezwalanie na ruch przy użyciu sieciowej grupy zabezpieczeń, która musi być wykonywana przez administratora sieci. Aby uzyskać więcej informacji, zobacz [uprawnienia sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego w witrynie Azure portal

1. Uruchom portal Platformy Azure pod adresem<https://portal.azure.com/.>
1. Otwórz grupę zasobów z wystąpieniem zarządzanym i wybierz **wystąpienie zarządzane SQL,** na które chcesz skonfigurować publiczny punkt końcowy.
1. W ustawieniach **zabezpieczeń** wybierz kartę **Sieć wirtualna.**
1. Na stronie Konfiguracja sieci wirtualnej wybierz pozycję **Włącz,** a następnie ikonę **Zapisz,** aby zaktualizować konfigurację.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego przy użyciu programu PowerShell

### <a name="enable-public-endpoint"></a>Włączanie publicznego punktu końcowego

Uruchom następujące polecenia programu PowerShell. Zamień **identyfikator subskrypcji** na identyfikator subskrypcji. Zastąp również **nazwę rg** grupą zasobów dla wystąpienia zarządzanego i zastąp **nazwę mi** nazwą wystąpienia zarządzanego.

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

### <a name="disable-public-endpoint"></a>Wyłączanie publicznego punktu końcowego

Aby wyłączyć publiczny punkt końcowy przy użyciu programu PowerShell, należy wykonać następujące polecenie (a także nie zapomnij zamknąć sieciowej sieciowej sieciowej dla portu przychodzącego 3342, jeśli jest skonfigurowany):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Zezwalaj na publiczny ruch w sieciowej grupie zabezpieczeń

1. Jeśli nadal otwarta jest strona konfiguracji wystąpienia zarządzanego, przejdź do karty **Przegląd.** W przeciwnym razie wróć do zasobu **wystąpienia zarządzanego SQL.** Wybierz **łącze Sieć wirtualna/podsieć,** które spowoduje przełączenie do strony konfiguracji sieci wirtualnej.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Wybierz kartę **Podsieci** w lewym okienku konfiguracji sieci wirtualnej i zanotuj **grupę zabezpieczeń** dla wystąpienia zarządzanego.

    ![mi-vnet-podsieci.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Wróć do grupy zasobów zawierającej wystąpienie zarządzane. Powinna zostać wyświetlna nazwa **grupy zabezpieczeń sieciowej,** o której wspomniano powyżej. Wybierz nazwę, aby przejść do strony konfiguracji sieciowej grupy zabezpieczeń.

1. Wybierz kartę **Przychodzące reguły zabezpieczeń** i **Dodaj** regułę o wyższym priorytecie niż **reguła deny_all_inbound** z następującymi ustawieniami: </br> </br>

    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    |**Źródła**     |Dowolny adres IP lub tag usługi         |<ul><li>W przypadku usług platformy Azure, takich jak usługa Power BI, wybierz tag usługi w chmurze azure</li> <li>W przypadku komputera lub maszyny Wirtualnej platformy Azure użyj adresu IP translatora adresów sieciowych</li></ul> |
    |**Zakresy portów źródłowych**     |*         |Zostaw to na * (dowolny) jako porty źródłowe są zwykle dynamicznie przydzielane i jako takie, nieprzewidywalne |
    |**Docelowy**     |Dowolne         |Pozostawienie miejsca docelowego jako dowolnego, aby zezwolić na ruch do podsieci wystąpienia zarządzanego |
    |**Zakresy portów docelowych**     |3342         |Zakres portu docelowego do 3342, który jest publicznym punktem końcowym TDS wystąpienia zarządzanego |
    |**Protokół**     |TCP         |Wystąpienie zarządzane używa protokołu TCP dla usługi TDS |
    |**Akcja**     |Zezwalaj         |Zezwalaj na ruch przychodzący do wystąpienia zarządzanego za pośrednictwem publicznego punktu końcowego |
    |**Priorytet**     |1300         |Upewnij się, że ta reguła ma wyższy priorytet niż reguła **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 jest używany dla połączeń publicznych punktów końcowych do wystąpienia zarządzanego i nie można go zmienić w tym momencie.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Uzyskiwanie ciągu połączenia publicznego punktu końcowego wystąpienia zarządzanego

1. Przejdź do strony konfiguracji wystąpienia zarządzanego SQL, która została włączona dla publicznego punktu końcowego. Wybierz kartę **Parametry połączenia** w obszarze **Konfiguracja ustawień.**
1. Należy zauważyć, że nazwa hosta publicznego punktu końcowego jest w formacie <mi_name>. **publiczne**<dns_zone>.database.windows.net i że port używany do połączenia to 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [bezpiecznym używaniu wystąpienia zarządzanego usługi Azure SQL Database z publicznym punktem końcowym.](sql-database-managed-instance-public-endpoint-securely.md)