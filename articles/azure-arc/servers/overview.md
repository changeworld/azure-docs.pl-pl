---
title: Omówienie usługi Azure Arc dla serwerów (wersja zapoznawcza)
description: Dowiedz się, jak używać usługi Azure Arc dla serwerów do zarządzania komputerami, które są hostowane poza platformą Azure, tak jakby był to zasób platformy Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, zapasy, elementy runbook, python, graficzny, hybrydowy
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5ffe845178fffc252e95794bfb58801c9bff16a7
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411224"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co to jest usługa Azure Arc dla serwerów (wersja zapoznawcza)

Usługa Azure Arc for servers (preview) umożliwia zarządzanie komputerami z systemem Windows i Linux hostowanymi poza platformą Azure w sieci firmowej lub innym dostawcy chmury, podobnie jak na zarządzania natywnymi maszynami wirtualnymi platformy Azure. Gdy komputer hybrydowy jest połączony z platformą Azure, staje się połączonym komputerem i jest traktowany jako zasób na platformie Azure. Każdy połączony komputer ma identyfikator zasobu, jest zarządzany jako część grupy zasobów wewnątrz subskrypcji i korzysta ze standardowych konstrukcji platformy Azure, takich jak zasady platformy Azure i stosowania tagów.

Aby zapewnić to środowisko z komputerami hybrydowymi hostowanymi poza platformą Azure, agent połączonego komputera platformy Azure musi zostać zainstalowany na każdym komputerze, który planujesz połączyć się z platformą Azure. Ten agent nie dostarcza żadnych innych funkcji i nie zastępuje agenta usługi Azure [Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) Agent analizy dzienników dla systemów Windows i Linux jest wymagany, gdy chcesz aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim przy użyciu umnień umowniczych automatyzacji lub rozwiązań, takich jak zarządzanie aktualizacjami, lub korzystać z innych usług platformy Azure Security [Center.](../../security-center/security-center-intro.md)

>[!NOTE]
>Ta wersja zapoznawcza jest przeznaczona do celów oceny i zaleca się, aby nie zarządzać krytycznymi maszynami produkcyjnymi.
>

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Usługa Azure Arc dla serwerów (wersja zapoznawcza) obsługuje następujące scenariusze z podłączonymi komputerami:

- Przypisywanie [konfiguracji gościa usługi Azure Policy](../../governance/policy/concepts/guest-configuration.md) przy użyciu tego samego środowiska co przypisanie zasad dla maszyn wirtualnych platformy Azure.
- Rejestrowanie danych zebranych przez agenta usługi Log Analytics i przechowywanych w obszarze roboczym usługi Log Analytics, w którym maszyna jest zarejestrowana, zawiera teraz właściwości specyficzne dla komputera, takie jak identyfikator zasobu, który może służyć do obsługi dostępu do dziennika [kontekstu zasobów.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Obsługiwane regiony

W przypadku usługi Azure Arc dla serwerów (wersja zapoznawcza) obsługiwane są tylko niektóre regiony:

- Okręg wyborczy WestUS2
- WestEurope
- WestAsia (WestAsia)

W większości przypadków lokalizacja wybrana podczas tworzenia skryptu instalacyjnego powinna być geograficznie najbliższa lokalizacji komputera w regionie platformy Azure. Dane w stanie spoczynku będą przechowywane w obszarze geograficznym platformy Azure zawierającego określony region, co może również mieć wpływ na wybór regionu, jeśli masz wymagania dotyczące rezydencji danych. Jeśli region platformy Azure, z którego jest połączony komputer, jest zagrożony awarią, nie ma to wpływu na podłączoną maszynę, ale operacje zarządzania przy użyciu platformy Azure mogą nie być w stanie ukończyć. W przypadku odporności w przypadku awarii regionalnej, jeśli masz wiele lokalizacji, które zapewniają usługę nadmiarową geograficznie, najlepiej jest połączyć maszyny w każdej lokalizacji z innym regionem platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Następujące wersje systemu operacyjnego Windows i Linux są oficjalnie obsługiwane dla agenta połączonego komputera platformy Azure: 

- Windows Server 2012 R2 lub wyższy (w tym Windows Server Core)
- Ubuntu 16.04 i 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>Ta wersja zapoznawcza agenta Connected Machine dla systemu Windows obsługuje tylko system Windows Server skonfigurowany do używania języka angielskiego.
>

### <a name="required-permissions"></a>Wymagane uprawnienia

- Do komputerów wbudowanych, jesteś członkiem roli **dołączania połączonego komputera platformy Azure.**

- Aby odczytać, zmodyfikować, ponownie na pokładzie i usunąć komputer, jesteś członkiem roli **administratora zasobów połączonych komputera platformy Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Limity subskrypcji i usług platformy Azure

Przed skonfigurowaniem maszyn za pomocą usługi Azure Arc dla serwerów (wersja zapoznawcza) należy przejrzeć [limity subskrypcji usługi](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager i [limity grupy zasobów,](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) aby zaplanować liczbę komputerów, które mają być połączone.

## <a name="tls-12-protocol"></a>Protokół TLS 1.2

Aby zapewnić bezpieczeństwo danych przesyłanych na platformę Azure, zdecydowanie zachęcamy do skonfigurowania komputera do korzystania z usługi Transport Layer Security (TLS) 1.2. Starsze wersje TLS/Secure Sockets Layer (SSL) okazały się podatne na ataki i chociaż nadal działają, aby umożliwić zgodność z powrotem, nie są **zalecane.** 

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje Linuksa zwykle polegają na [OpenSSL](https://www.openssl.org) dla obsługi TLS 1.2. | Sprawdź [OpenSSL Changelog,](https://www.openssl.org/news/changelog.html) aby potwierdzić, że twoja wersja OpenSSL jest obsługiwana.|
| Windows Server 2012 R2 lub nowsze | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfiguracja sieci

Agent Connected Machine dla systemów Linux i Windows komunikuje się bezpiecznie wychodzące do usługi Azure Arc za pomocą portu TCP 443. Jeśli komputer łączy się za pośrednictwem zapory lub serwera proxy w celu komunikowania się przez Internet, zapoznaj się z poniższymi wymaganiami, aby zrozumieć wymagania dotyczące konfiguracji sieci.

Jeśli łączność wychodząca jest ograniczona przez zaporę lub serwer proxy, upewnij się, że adresy URL wymienione poniżej nie są zablokowane. Jeśli zezwalasz tylko na zakresy adresów IP lub nazwy domen wymagane dla agenta do komunikowania się z usługą, należy również zezwolić na dostęp do następujących tagów usług i adresów URL.

Tagi usług:

- Usługa AzureActiveDirectory
- AzureTrafficManager

Adresy url:

| Zasób agenta | Opis |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Usługa Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfiguracja gościa|
|*-agentservice-prod-1.azure-automation.net|Konfiguracja gościa|
|*.his.hybridcompute.azure-automation.net|Usługa tożsamości hybrydowej|

Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). Firma Microsoft publikuje cotygodniowe aktualizacje zawierające każdą usługę platformy Azure i zakresy adresów IP, których używa. Aby uzyskać więcej informacji, zapoznaj [się z tagami usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Adresy URL w poprzedniej tabeli są wymagane oprócz informacji o zakresie adresów IP tagu usługi, ponieważ większość usług nie ma obecnie rejestracji tagu usługi. W związku z tym adresy IP mogą ulec zmianie. Jeśli zakresy adresów IP są wymagane dla konfiguracji zapory, tag usługi **AzureCloud** powinien być używany do zezwalania na dostęp do wszystkich usług platformy Azure. Nie wyłączaj monitorowania zabezpieczeń ani nie przeprowadzaj inspekcji tych adresów URL, zezwalaj na nie tak jak w przypadku innego ruchu internetowego.

### <a name="register-azure-resource-providers"></a>Rejestrowanie dostawców zasobów platformy Azure

Usługa Azure Arc dla serwerów (wersja zapoznawcza) zależy od następujących dostawców zasobów platformy Azure w ramach subskrypcji w celu korzystania z tej usługi:

- **Microsoft.HybridCompute**
- **Konfiguracja witryny Microsoft.GuestConfiguration**

Jeśli nie są zarejestrowane, można je zarejestrować za pomocą następujących poleceń:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Można również zarejestrować dostawców zasobów w witrynie Azure portal, wykonując kroki w witrynie [Azure portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agent podłączonej maszyny

Pakiet agenta połączonego komputera platformy Azure dla systemów Windows i Linux można pobrać z lokalizacji wymienionych poniżej.

- [Pakiet Instalatora Windows agenta systemu Windows](https://aka.ms/AzureConnectedMachineAgent) z Centrum pobierania Microsoft.
- Pakiet agenta systemu Linux jest dystrybuowany z [repozytorium pakietów](https://packages.microsoft.com/) firmy Microsoft przy użyciu preferowanego formatu pakietu dla dystrybucji (. Rpm lub . DEB).

>[!NOTE]
>Podczas tej wersji zapoznawczej został wydany tylko jeden pakiet, który jest odpowiedni dla Ubuntu 16.04 lub 18.04.

Agent połączonego komputera platformy Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od wymagań. Aby uzyskać więcej informacji, zobacz [tutaj](manage-agent.md)

### <a name="agent-status"></a>Stan agenta

Agent podłączonego komputera wysyła zwykły komunikat pulsu do usługi co 5 minut. Jeśli usługa przestaje odbierać te komunikaty pulsu z komputera, to urządzenie jest uważane za w trybie offline i stan zostanie automatycznie zmieniony na **Rozłączony** w portalu w ciągu 15 do 30 minut. Po otrzymaniu kolejnego komunikatu pulsu od agenta Podłączonego komputera jego stan zostanie automatycznie zmieniony na **Połączony**.

## <a name="install-and-configure-agent"></a>Instalowanie i konfigurowanie agenta

Łączenie maszyn w środowisku hybrydowym bezpośrednio z platformą Azure można wykonać przy użyciu różnych metod w zależności od wymagań. W poniższej tabeli wyróżniono każdą metodę, aby określić, która z nich jest najlepsza dla twojej organizacji.

| Metoda | Opis |
|--------|-------------|
| Interaktywnie | Ręcznie zainstaluj agenta na jednej lub niewielkiej liczbie maszyn, wykonując kroki opisane w [programie Connect machines z witryny Azure portal](onboard-portal.md).<br> Z witryny Azure portal można wygenerować skrypt i wykonać go na komputerze, aby zautomatyzować kroki instalacji i konfiguracji agenta.|
| Na dużą skalę | Zainstaluj i skonfiguruj agenta dla wielu komputerów następujących po [maszynach Connect przy użyciu jednostki usługi](onboard-service-principal.md).<br> Ta metoda tworzy jednostkę usługi do łączenia maszyn nieinterakcyjnie.|
| Na dużą skalę | Zainstaluj i skonfiguruj agenta dla wielu komputerów zgodnie z metodą [Korzystanie z programu Windows PowerShell DSC](onboard-dsc.md).<br> Ta metoda używa jednostki usługi do łączenia maszyn nieinterakcyjnie z programem PowerShell DSC. |

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć ocenę usługi Azure Arc dla serwerów (wersja zapoznawcza), postępuj zgodnie [z artykułem Łączenie maszyn hybrydowych z platformą Azure z witryny Azure portal](onboard-portal.md). 
