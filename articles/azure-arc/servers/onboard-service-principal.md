---
title: Łączenie maszyn hybrydowych z platformą Azure na dużą skalę
description: W tym artykule dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu usługi Azure Arc dla serwerów (w wersji zapoznawczej) przy użyciu jednostki usługi.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192274"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Łączenie maszyn hybrydowych z platformą Azure na dużą skalę

Można włączyć usługę Azure Arc dla serwerów (wersja zapoznawcza) dla wielu komputerów z systemem Windows lub Linux w twoim środowisku, z kilkoma elastycznymi opcjami w zależności od wymagań. Za pomocą skryptu szablonu, który udostępniamy, można zautomatyzować każdy krok instalacji, w tym ustanawianie połączenia z programem Azure Arc. Jednak są wymagane do interaktywnego wykonania tego skryptu z konta, które ma podwyższone uprawnienia na komputerze docelowym i na platformie Azure. Aby połączyć maszyny z usługą Azure Arc dla serwerów, można użyć [jednostki usługi](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory zamiast używać uprzywilejowanej tożsamości do [interaktywnego łączenia komputera.](onboard-portal.md) Podmiot usługi jest specjalną ograniczoną tożsamością zarządzania, która jest przyznawana tylko `azcmagent` minimalne uprawnienia niezbędne do łączenia maszyn do platformy Azure za pomocą polecenia. Jest to bezpieczniejsze niż przy użyciu konta o wyższych uprawnieniach, takich jak administrator dzierżawy, i jest zgodne z naszymi najlepszymi rozwiązaniami w zakresie zabezpieczeń kontroli dostępu. Podmiot usługi jest używany tylko podczas dołączania, nie jest używany do żadnych innych celów.  

Metody instalacji do zainstalowania i skonfigurowania agenta Połączonego komputera wymaga, aby używana metoda automatyczna ma uprawnienia administratora na komputerach. W systemie Linux, przy użyciu konta głównego i w systemie Windows, jako członek grupy Administratorzy lokalni.

Przed rozpoczęciem należy zapoznać się z [wymaganiami wstępnymi](overview.md#prerequisites) i sprawdzić, czy subskrypcja i zasoby spełniają wymagania.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Po zakończeniu tego procesu pomyślnie połączono maszyny hybrydowe z usługą Azure Arc dla serwerów.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Tworzenie jednostki usługi dla dołączania na dużą skalę

Za pomocą [programu Azure PowerShell](/powershell/azure/install-az-ps) można utworzyć jednostkę usługi za pomocą polecenia cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Możesz też wykonać kroki wymienione w obszarze [Tworzenie jednostki usługi przy użyciu witryny Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md) w celu wykonania tego zadania.

> [!NOTE]
> Podczas tworzenia jednostki usługi, konto musi być administratorem dostępu właściciela lub użytkownika w subskrypcji, która ma być używana do dołączania. Jeśli nie masz wystarczających uprawnień do tworzenia przypisań ról, może zostać utworzony podmiot usługi, ale nie będzie można na komputerach wbudowanych.
>

Aby utworzyć jednostkę usługi przy użyciu programu PowerShell, wykonaj następujące czynności.

1. Uruchom następujące polecenie. Dane wyjściowe [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) polecenia cmdlet należy przechowywać w zmiennej lub nie będzie można pobrać hasła wymaganego w późniejszym kroku.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Aby pobrać hasło przechowywane `$sp` w zmiennej, uruchom następujące polecenie:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. W danych wyjściowych znajdź wartość hasła pod **hasłem** pola i skopiuj je. Również znaleźć wartość w polu **ApplicationId** i skopiować go również. Zapisz je na później w bezpiecznym miejscu. Jeśli zapomnisz lub utracisz hasło głównej usługi, możesz je zresetować za pomocą polecenia [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

Wartości z następujących właściwości są używane z `azcmagent`parametrami przekazanymi do :

* Wartość z **ApplicationId** właściwość jest `--service-principal-id` używana dla wartości parametru
* Wartość z właściwości **password** jest `--service-principal-secret` używana dla parametru używanego do łączenia agenta.

> [!NOTE]
> Upewnij się, że należy użyć właściwości **applicationid** jednostki usługi, a nie **Id** właściwości.
>

Rola **dołączania połączonego komputera platformy Azure** zawiera tylko uprawnienia wymagane do połączonego komputera. Można przypisać uprawnienie jednostki usługi, aby zezwolić na jej zakres do grupy zasobów lub subskrypcji. Aby dodać przypisanie roli, zobacz [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md) lub dodawanie lub usuwanie [przydziałów ról przy użyciu usługi Azure RBAC i interfejsu wiersza polecenia platformy Azure.](../../role-based-access-control/role-assignments-cli.md)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i łączenie się z platformą Azure

Poniższe kroki zainstalować i skonfigurować agenta connected machine na komputerach hybrydowych przy użyciu szablonu skryptu, który wykonuje podobne kroki opisane w [Connect maszyn hybrydowych do platformy Azure z](onboard-portal.md) artykułu portalu Azure. Różnica jest w ostatnim kroku, w którym można `azcmagent` ustanowić połączenie z usługą Azure Arc przy użyciu polecenia przy użyciu jednostki usługi. 

Poniżej przedstawiono ustawienia, które `azcmagent` można skonfigurować polecenie do użycia dla jednostki usługi.

* `tenant-id`: Unikatowy identyfikator (GUID), który reprezentuje dedykowane wystąpienie usługi Azure AD.
* `subscription-id`: Identyfikator subskrypcji (GUID) subskrypcji platformy Azure, w której mają być na komputerze.
* `resource-group`: Nazwa grupy zasobów, do której mają należeć podłączone komputery.
* `location`: Zobacz [obsługiwane regiony platformy Azure](overview.md#supported-regions). Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.
* `resource-name`: (*Opcjonalnie)* Używane do reprezentacji zasobów platformy Azure komputera lokalnego. Jeśli ta wartość nie zostanie określona, używana jest nazwa hosta komputera.

Więcej informacji na `azcmagent` temat narzędzia wiersza polecenia można dowiedzieć się, przeglądając program [Azcmagent Reference](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Skrypt instalacyjny systemu Windows

Poniżej przedstawiono przykład połączonego agenta komputera dla skryptu instalacyjnego systemu Windows, który został zmodyfikowany w celu użycia jednostki usługi do obsługi w pełni zautomatyzowanej, nieinterakcyjnej instalacji agenta.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Skrypt instalacyjny systemu Linux

Poniżej przedstawiono przykład połączonego agenta komputera dla skryptu instalacyjnego systemu Linux, który został zmodyfikowany w celu użycia jednostki usługi do obsługi w pełni zautomatyzowanej, nieinterakcyjnej instalacji agenta.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Po zainstalowaniu agenta i skonfigurowaniu go do łączenia się z programem Azure Arc dla serwerów (wersja zapoznawcza) przejdź do witryny Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetlanie maszyn w [witrynie Azure portal](https://aka.ms/hybridmachineportal).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zarządzać komputerem przy użyciu [usługi Azure Policy](../../governance/policy/overview.md), dla takich rzeczy, jak [konfiguracja gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzanie, że maszyna zgłasza się do oczekiwanego obszaru roboczego usługi Log Analytics, włącz monitorowanie za pomocą [usługi Azure Monitor za pomocą maszyn wirtualnych](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent analizy dzienników dla systemów Windows i Linux jest wymagany, gdy chcesz aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim przy użyciu umnień umowniczych automatyzacji lub rozwiązań, takich jak zarządzanie aktualizacjami, lub korzystać z innych usług platformy Azure Security [Center.](../../security-center/security-center-intro.md)
