---
title: Usługa Azure ARC dla serwerów — Omówienie
description: Dowiedz się, jak za pomocą usługi Azure Arc zautomatyzować cykl życia infrastruktury i aplikacji.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: d091b89342570b73ccde5fe496a3432102617918
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951432"
---
# <a name="what-is-azure-arc-for-servers"></a>Co to jest usługa Azure ARC dla serwerów

Usługa Azure ARC dla serwerów umożliwia zarządzanie maszynami spoza platformy Azure.
Gdy maszyna spoza platformy Azure jest połączona z platformą Azure, zostanie ona **podłączona** i jest traktowana jako zasób na platformie Azure. Każda **podłączona maszyna** ma identyfikator zasobu, który jest zarządzany jako część grupy zasobów w ramach subskrypcji, i korzyści ze standardowych konstrukcji platformy Azure, takich jak Azure Policy i tagowanie.

Pakiet agenta musi być zainstalowany na każdej maszynie, aby można było połączyć go z platformą Azure. Pozostała część tego dokumentu wyjaśnia proces bardziej szczegółowo.

Maszyny będą miały stan **połączone** lub **rozłączone** w zależności od tego, jak ostatnio zaewidencjonowano Agent. Każda ewidencjonowanie jest nazywana pulsem. Jeśli maszyna nie została zaewidencjonowana w ciągu ostatnich 5 minut, zostanie wyświetlona w trybie offline do momentu przywrócenia łączności.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Połączone serwery](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Klienci

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W publicznej wersji zapoznawczej firma Microsoft wspiera:

- System Windows Server 2012 R2 lub nowszy
- Ubuntu 16,04 i 18,04

Publiczna wersja zapoznawcza jest przeznaczona do celów ewaluacyjnych i nie należy jej używać do zarządzania krytycznymi zasobami produkcyjnymi.

## <a name="azure-subscription-and-service-limits"></a>Limity subskrypcji i usług platformy Azure

Upewnij się, że zostały odczytane limity Azure Resource Manager i Zaplanuj liczbę maszyn, które mają być połączone zgodnie z wytycznymi wymienionymi dla [subskrypcji](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), a dla [grup zasobów](../../azure-subscription-service-limits.md#resource-group-limits). W szczególności domyślnie istnieje limit 800 serwerów na grupę zasobów.

## <a name="networking-configuration"></a>Konfiguracja sieci

Podczas instalacji i środowiska uruchomieniowego Agent wymaga łączności z **punktami końcowymi usługi Azure Arc**. Jeśli łączność wychodząca jest blokowana przez zapory, upewnij się, że następujące adresy URL nie są domyślnie blokowane. Wszystkie połączenia są wychodzące z agenta do platformy Azure i są zabezpieczone przy użyciu **protokołu SSL**. Cały ruch można skierować za pośrednictwem serwera proxy **https** . W przypadku zezwolenia na zakresy adresów IP lub nazwy domen, z którymi serwery mogą się łączyć, należy zezwolić na dostęp do portu 443 do następujących tagów usługi i nazw DNS.

Tagi usługi:

* AzureActiveDirectory
* AzureTrafficManager

Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). Firma Microsoft publikuje cotygodniowe aktualizacje zawierające poszczególne usługi platformy Azure i zakresy adresów IP, z których korzystają. Aby uzyskać więcej informacji, zobacz [Tagi usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Te nazwy DNS są dostarczane oprócz informacji o zakresie adresów IP znacznika usługi, ponieważ większość usług nie ma obecnie rejestracji tagu usługi i w związku z tym adresy IP mogą ulec zmianie. Jeśli dla konfiguracji zapory wymagane są zakresy adresów IP, należy użyć znacznika usługi **AzureCloud** , aby zezwolić na dostęp do wszystkich usług platformy Azure. Nie należy wyłączać monitorowania zabezpieczeń ani inspekcji tych adresów URL, ale Zezwalaj na tak jak w przypadku innego ruchu internetowego.

| Środowisko domeny | Wymagane punkty końcowe usług platformy Azure |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Usługa Active Directory systemu Azure|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfiguracja gościa|
|*-agentservice-prod-1.azure-automation.net|Konfiguracja gościa|
|*. his.hybridcompute.azure-automation.net|Hybrydowa usługa tożsamości|

### <a name="installation-network-requirements"></a>Wymagania dotyczące sieci instalacji

Pobierz [pakiet agenta maszyny połączonej z platformą Azure](https://aka.ms/AzureConnectedMachineAgent) z oficjalnych serwerów dystrybucji. poniższe Lokacje muszą być dostępne ze środowiska. Możesz pobrać pakiet do udziału plików i zainstalować na nim agenta. W takim przypadku może być konieczne zmodyfikowanie skryptu dołączania wygenerowanego na podstawie Azure Portal.

W systemie Windows:

* `aka.ms`
* `download.microsoft.com`

W systemie Linux:

* `aka.ms`
* `packages.microsoft.com`

Zapoznaj się z sekcją [Konfiguracja serwera proxy](quickstart-onboard-powershell.md#proxy-server-configuration), aby uzyskać informacje na temat konfigurowania agenta do korzystania z serwera proxy.

## <a name="register-the-required-resource-providers"></a>Rejestrowanie wymaganych dostawców zasobów

Aby można było korzystać z usługi Azure ARC dla serwerów, należy zarejestrować wymaganych dostawców zasobów.

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Dostawców zasobów można zarejestrować za pomocą następujących poleceń:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfejs wiersza polecenia platformy Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Dostawców zasobów można rejestrować również przy użyciu portalu, wykonując czynności opisane w sekcji [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="machine-changes-after-installing-the-agent"></a>Zmiany maszyny po zainstalowaniu agenta

Jeśli w Twoim środowisku wdrożono rozwiązanie do śledzenia zmian, możesz użyć poniższej listy, aby śledzić, identyfikować i zezwalać na zmiany wprowadzone przez pakiet instalacyjny **agenta połączonej maszyny (AzCMAgent) platformy Azure** .

Po zainstalowaniu agenta zobaczysz następujące zmiany wprowadzone na serwerach.

### <a name="windows"></a>Windows

Zainstalowane usługi:

* `Himds` — usługa **agenta połączonego z platformą Azure** .
* `Dscservice` lub `gcd` — usługa **konfiguracji gościa** .

Pliki dodane do serwera:

* `%ProgramFiles%\AzureConnectedMachineAgent\*.*` lokalizacji plików **agenta połączonego z platformą Azure** .
* `%ProgramData%\GuestConfig\*.*` - dzienników **konfiguracji gościa** .

Lokalizacje kluczy rejestru:

* `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Connected Machine Agent`-klucze rejestru dla **agenta połączonego z platformą Azure**.

### <a name="linux"></a>Linux

Zainstalowane usługi:

* `Himdsd` — usługa **agenta połączonego z platformą Azure** .
* `dscd` lub `gcd` — usługa **konfiguracji gościa** .

Pliki dodane do serwera:

* `/var/opt/azcmagent/**` lokalizacji plików **agenta połączonego z platformą Azure** .
* `/var/lib/GuestConfig/**` - dzienników **konfiguracji gościa** .

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

Po zarejestrowaniu węzła można rozpocząć zarządzanie węzłami przy użyciu innych usług platformy Azure.

W publicznej wersji zapoznawczej są obsługiwane następujące scenariusze dla **połączonych maszyn**.

## <a name="guest-configuration"></a>Konfiguracja gościa

Po nawiązaniu połączenia między maszyną a platformą Azure można przypisać zasady platformy Azure do **połączonych maszyn** przy użyciu tego samego środowiska, co w przypadku przypisywania zasad do maszyn wirtualnych platformy Azure.

Aby uzyskać więcej informacji, zobacz [Opis konfiguracji gościa Azure Policy](../../governance/policy/concepts/guest-configuration.md).

Dzienniki agenta konfiguracji gościa dla **połączonej maszyny** znajdują się w następujących lokalizacjach:

* Windows — `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

Dane dziennika zbierane przez [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) i przechowywane w obszarze roboczym log Analytics będą zawierać właściwości specyficzne dla maszyny, takie jak **ResourceID**, które mogą być używane do dostępu do dziennika skoncentrowanego na zasobach.

- Na maszynach, na których zainstalowano już agenta MMA, będzie dostępna funkcja **usługi Azure Arc** z włączonymi zaktualizowanymi pakietami administracyjnymi.
- W przypadku usługi Azure Arc na potrzeby integracji serwerów jest wymagany [Agent MMA w wersji 10.20.18011 lub nowszej](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) .
- Podczas wykonywania zapytania dotyczącego danych dziennika w [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), zwracany schemat danych będzie zawierać hybrydowy **ResourceId** w formularzu `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Następne kroki

Istnieją dwie metody łączenia maszyn przy użyciu usługi Azure ARC dla serwerów.

* **Interaktywnie** — postępuj zgodnie z [przewodnikiem Szybki Start portalu](quickstart-onboard-portal.md) w celu wygenerowania skryptu z portalu i wykonania go na maszynie. Jest to najlepsza opcja w przypadku łączenia jednej maszyny w danym momencie.
* **W obszarze skalowanie** postępuj zgodnie z [przewodnikiem Szybki Start dla programu PowerShell](quickstart-onboard-powershell.md) , aby utworzyć jednostkę usługi do łączenia maszyn w sposób nieinteraktywny.
