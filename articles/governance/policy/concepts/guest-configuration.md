---
title: Dowiedz się, jak przeprowadzać inspekcje zawartości maszyn wirtualnych
description: Dowiedz się, jak usługa Azure Policy używa agenta konfiguracji gościa do inspekcji ustawień wewnątrz maszyn wirtualnych.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 9e8486af2a9b7ab9e18b8c16f08e51759d1123d7
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998855"
---
# <a name="understand-azure-policys-guest-configuration"></a>Opis konfiguracji gościa zasad platformy Azure

Oprócz inspekcji i [korygowania](../how-to/remediate-resources.md) zasobów platformy Azure usługa Azure Policy może przeprowadzać inspekcję ustawień wewnątrz komputera. Taka weryfikacja jest wykonywana przez klienta i rozszerzenie konfiguracji gościa. Rozszerzenie to, obsługiwane za pośrednictwem klienta, umożliwia weryfikację następujących ustawień:

- Konfiguracja systemu operacyjnego
- Konfiguracja lub obecność aplikacji
- Ustawienia środowiska

W tej chwili większość zasad konfiguracji gościa zasad usługi Azure Policy tylko inspekcji ustawień wewnątrz komputera. Nie stosują konfiguracji. Wyjątkiem jest jedna wbudowana [zasada, do którą odwołuje się poniżej](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Rozszerzenie i klient

Aby przeprowadzić inspekcję ustawień wewnątrz maszyny, jest włączone [rozszerzenie maszyny wirtualnej.](../../../virtual-machines/extensions/overview.md) Rozszerzenie pobiera odpowiednie przypisanie zasad i odpowiednią definicję konfiguracji.

### <a name="limits-set-on-the-extension"></a>Limity ustawione na rozszerzeniu

Aby ograniczyć rozszerzenie od wpływu aplikacji działających wewnątrz komputera, konfiguracja gościa nie może przekraczać więcej niż 5% wykorzystania procesora CPU. To ograniczenie istnieje zarówno dla wbudowanych i niestandardowych definicji.

## <a name="register-guest-configuration-resource-provider"></a>Zarejestruj dostawcę zasobów konfiguracji gościa

Aby można było używać konfiguracji gościa, należy zarejestrować dostawcę zasobów. Można zarejestrować za pośrednictwem portalu lub za pośrednictwem programu PowerShell. Dostawca zasobów jest rejestrowany automatycznie, jeśli przypisanie zasad konfiguracji gościa odbywa się za pośrednictwem portalu.

### <a name="registration---portal"></a>Rejestracja - Portal

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem witryny Azure portal, wykonaj następujące kroki:

1. Uruchom portal Platformy Azure i kliknij **wszystkie usługi**. Wyszukaj i wybierz **subskrypcje**.

1. Znajdź i kliknij subskrypcję, dla której chcesz włączyć konfigurację gościa.

1. W menu po lewej stronie **Subskrypcja** kliknij pozycję **Dostawcy zasobów**.

1. Filtruj lub przewiń, aż zlokalizujesz **witrynę Microsoft.GuestConfiguration**, a następnie kliknij pozycję **Zarejestruj się** w tym samym wierszu.

### <a name="registration---powershell"></a>Rejestracja - PowerShell

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Narzędzia sprawdzania poprawności

Wewnątrz komputera klient konfiguracji gościa używa narzędzi lokalnych do uruchomienia inspekcji.

W poniższej tabeli przedstawiono listę narzędzi lokalnych używanych w każdym obsługiwanym systemie operacyjnym:

|System operacyjny|Narzędzie sprawdzania poprawności|Uwagi|
|-|-|-|
|Windows|[Konfiguracja żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview) w wersji 2| |
|Linux|[Szef kuchni InSpec](https://www.chef.io/inspec/)| Jeśli Ruby i Python nie są na komputerze, są one instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="validation-frequency"></a>Częstotliwość sprawdzania poprawności

Klient konfiguracji gościa sprawdza dostępność nowej zawartości co 5 minut. Po odebraniu przypisania gościa ustawienia tej konfiguracji są ponownie sprawdzane w odstępie 15 minut.
Wyniki są wysyłane do dostawcy zasobów konfiguracji gościa po zakończeniu inspekcji. Po wystąpieniu [wyzwalacza oceny](../how-to/get-compliance-data.md#evaluation-triggers) zasad stan komputera jest zapisywany do dostawcy zasobów konfiguracji gościa. Ta aktualizacja powoduje, że zasady platformy Azure do oceny właściwości usługi Azure Resource Manager. Ocena zasad platformy Azure na żądanie pobiera najnowszą wartość od dostawcy zasobów konfiguracji gościa. Jednak nie wyzwala nowy audyt konfiguracji w komputerze.

## <a name="supported-client-types"></a>Obsługiwane typy klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych na obrazach platformy Azure:

|Wydawca|Nazwa|Wersje|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ ( Credativ )|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient systemu Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Nieobsługiwały typy klientów

System Windows Server Nano Server nie jest obsługiwany w żadnej wersji.

## <a name="guest-configuration-extension-network-requirements"></a>Wymagania sieciowe rozszerzenia konfiguracji gościa

Aby komunikować się z dostawcą zasobów konfiguracji gościa na platformie Azure, maszyny wymagają dostępu wychodzącego do centrów danych platformy Azure na porcie **443**. Jeśli używasz prywatnej sieci wirtualnej na platformie Azure, która nie zezwala na ruch wychodzący, skonfiguruj wyjątki za pomocą reguł [sieciowej grupy zabezpieczeń.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
[Tag usługi](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" może służyć do odwoływania się do usługi konfiguracji gościa.

## <a name="azure-managed-identity-requirements"></a>Wymagania dotyczące tożsamości zarządzanej platformy Azure

**DeployIfNotExists** zasady, które dodają rozszerzenie do maszyn wirtualnych również włączyć system przypisany tożsamości zarządzanej, jeśli nie istnieje.

> [!WARNING]
> Należy unikać włączania tożsamości zarządzanej przypisanej przez użytkownika do maszyn wirtualnych w zakresie zasad, które umożliwiają tożsamości zarządzanej przypisanej przez system. Przypisana przez użytkownika tożsamość zostanie zastąpiona i może przestać odpowiadać.

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każdy inspekcji prowadzony przez konfigurację gościa wymaga dwóch definicji zasad, **DeployIfNotExists** definicji i **AuditIfNotExists** definicji. Definicja **DeployIfNotExists** służy do przygotowania komputera za pomocą agenta konfiguracji gościa i innych składników do obsługi [narzędzi sprawdzania poprawności.](#validation-tools)

Definicja zasad **DeployIfNotExists** sprawdza poprawność i poprawia następujące elementy:

- Sprawdź poprawność maszyna została przypisana konfiguracja do oceny. Jeśli obecnie nie ma przypisania, pobierz przypisanie i przygotuj urządzenie przez:
  - Uwierzytelnianie na komputerze przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji rozszerzenia **Microsoft.GuestConfiguration**
  - Instalowanie narzędzi i zależności [sprawdzania poprawności,](#validation-tools) jeśli to konieczne

Jeśli **deployIfNotExists** przypisanie jest niezgodne, można użyć [zadania korygowania.](../how-to/remediate-resources.md#create-a-remediation-task)

Gdy przypisanie **DeployIfNotExists** jest zgodne, przypisanie zasad **AuditIfNotExists** używa lokalnych narzędzi sprawdzania poprawności, aby określić, czy przypisanie konfiguracji jest zgodne lub niezgodne. Narzędzie sprawdzania poprawności udostępnia wyniki klientowi konfiguracji gościa. Klient przekazuje wyniki do rozszerzenia gościa, który udostępnia je za pośrednictwem dostawcy zasobów konfiguracji gościa.

Usługa Azure Policy używa właściwości **compliance statusus** dostawców zasobów konfiguracji gościa do zgłaszania zgodności w węźle **Zgodność.** Aby uzyskać więcej informacji, zobacz [uzyskiwanie danych dotyczących zgodności](../how-to/get-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** zasady jest wymagane dla **AuditIfNotExists** zasady do zwracania wyników. Bez **DeployIfNotExists,** **AuditIfNotExists** zasady pokazuje "0 z 0" zasobów jako stan.

Wszystkie wbudowane zasady konfiguracji gościa są uwzględnione w inicjatywie grupowania definicji do użycia w przydziałach. Wbudowana inicjatywa o nazwie _ \[\]Preview : Audit Password security settings inside Linux and Windows machines_ contains 18 policies. Istnieje sześć **DeployIfNotExists** i **AuditIfNotExists** pary dla systemu Windows i trzy pary dla systemu Linux. Logika [definicji zasad](definition-structure.md#policy-rule) sprawdza, czy tylko docelowy system operacyjny jest oceniany.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Inspekcja ustawień systemu operacyjnego zgodnie z branżowymi punktami bazowymi

Jedna z inicjatyw dostępnych w usłudze Azure Policy umożliwia inspekcję ustawień systemu operacyjnego wewnątrz maszyn wirtualnych po "linii bazowej" firmy Microsoft. _ \[Definicja,\]Wersja zapoznawcza: Inspekcja maszyn wirtualnych systemu Windows, które nie są zgodne z ustawieniami planu bazowego zabezpieczeń platformy Azure,_ zawiera pełny zestaw reguł inspekcji opartych na ustawieniach z zasad grupy usługi Active Directory.

Większość ustawień jest dostępna jako parametry. Ta funkcja umożliwia dostosowanie inspekcji zasad w celu dostosowania zasad do wymagań organizacji lub mapowanie zasad na informacje innych firm, takie jak branżowe standardy regulacyjne.

Niektóre parametry obsługują zakres wartości całkowitej. Na przykład parametr Maksymalny wiek hasła można ustawić za pomocą operatora zakresu, aby zapewnić elastyczność właścicielom komputerów. Można przeprowadzić inspekcję, czy skuteczne ustawienie zasad grupy wymagające od użytkowników zmiany haseł powinno trwać nie więcej niż 70 dni, ale nie powinno być mniejsze niż jeden dzień. Jak opisano w dymku informacyjnym dla parametru, aby ta polityka biznesowa była skuteczną wartością inspekcji, należy ustawić wartość na "1,70".

Jeśli zasady zostaną przypisane przy użyciu szablonu wdrożenia usługi Azure Resource Manager, można użyć pliku parametrów do zarządzania tymi ustawieniami za pomocą kontroli źródła. Za pomocą narzędzia, takiego jak Git do zarządzania zmianami w zasadach inspekcji z komentarzami na każdym dokumentach ewidencjonowania dowodzą, dlaczego przypisanie powinno być wyjątkiem od oczekiwanej wartości.

#### <a name="applying-configurations-using-guest-configuration"></a>Stosowanie konfiguracji przy użyciu konfiguracji gościa

Najnowsza funkcja usługi Azure Policy konfiguruje ustawienia wewnątrz komputerów. Definicja _Konfiguruj strefę czasową na komputerach z systemem Windows_ wprowadza zmiany na komputerze, konfigurując strefę czasową.

Podczas przypisywania definicji rozpoczynających się od _opcji Konfiguruj_należy również przypisać wymagania wstępne definicji _Wdrażanie, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych systemu Windows_. W przypadku wybrania tej opcji można połączyć te definicje w inicjatywie.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Przypisywanie zasad do komputerów spoza platformy Azure

Zasady inspekcji dostępne dla konfiguracji gościa obejmują typ zasobu **Microsoft.HybridCompute/machines.** Wszystkie maszyny dołączane do [usługi Azure Arc dla serwerów,](../../../azure-arc/servers/overview.md) które znajdują się w zakresie przypisania zasad, są automatycznie uwzględniane.

### <a name="multiple-assignments"></a>Wiele przypisań

Zasady konfiguracji gościa obsługują obecnie tylko przypisywanie tego samego przypisania gościa raz na komputer, nawet jeśli przypisanie zasad używa różnych parametrów.

## <a name="client-log-files"></a>Pliki dziennika klienta

Rozszerzenie Konfiguracja gościa zapisuje pliki dziennika w następujących lokalizacjach:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Gdzie `<version>` odnosi się do bieżącego numeru wersji.

### <a name="collecting-logs-remotely"></a>Zdalne zbieranie dzienników

Pierwszym krokiem w rozwiązywaniu problemów z konfiguracją gościa konfiguracja lub moduły powinno być użycie `Test-GuestConfigurationPackage` polecenia cmdlet po instrukcje, jak utworzyć [niestandardowe zasady inspekcji konfiguracji gościa dla systemu Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Jeśli to się nie powiedzie, zbieranie dzienników klientów może pomóc w diagnozowaniu problemów.

#### <a name="windows"></a>Windows

Aby użyć funkcji polecenia uruchamiania maszyny Wirtualnej platformy Azure do przechwytywania informacji z plików dziennika na komputerach z systemem Windows, przydatny może być poniższy przykładowy skrypt programu PowerShell. Aby uzyskać więcej informacji, zobacz [Uruchamianie skryptów programu PowerShell w maszynie Wirtualnej systemu Windows za pomocą polecenia Uruchom](../../../virtual-machines/windows/run-command.md)polecenie .

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Aby użyć funkcji polecenia uruchamiania maszyny Wirtualnej platformy Azure do przechwytywania informacji z plików dziennika na komputerach z systemem Linux, przydatny może być poniższy przykładowy skrypt Bash. Aby uzyskać więcej informacji, zobacz [Uruchamianie skryptów powłoki na maszynie Wirtualnej systemu Linux za pomocą polecenia Uruchom](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Przykłady konfiguracji gościa

Źródło wbudowanych inicjatyw Konfiguracja gościa zasad są dostępne w następujących lokalizacjach:

- [Wbudowane definicje zasad — konfiguracja gościa](../samples/built-in-policies.md#guest-configuration)
- [Wbudowane inicjatywy — konfiguracja gościa](../samples/built-in-initiatives.md#guest-configuration)
- [Przykłady usługi Azure Policy repo usługi GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
