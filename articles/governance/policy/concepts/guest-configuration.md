---
title: Dowiedz się, jak przeprowadzić inspekcję zawartości maszyn wirtualnych
description: Dowiedz się, w jaki sposób Azure Policy używa agenta konfiguracji gościa do inspekcji ustawień wewnątrz maszyn wirtualnych.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 73f986774fc13ac8c69cd800c977c909b591a74c
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369743"
---
# <a name="understand-azure-policys-guest-configuration"></a>Omówienie usługi Azure Policy gościa konfiguracji

Poza inspekcją i [korygowaniem](../how-to/remediate-resources.md) zasobów platformy Azure, Azure Policy może przeprowadzać inspekcję ustawień wewnątrz maszyny. Taka weryfikacja jest wykonywana przez klienta i rozszerzenie konfiguracji gościa. Rozszerzenie to, obsługiwane za pośrednictwem klienta, umożliwia weryfikację następujących ustawień:

- Konfiguracja systemu operacyjnego
- Konfiguracja lub obecność aplikacji
- Ustawienia środowiska

Aktualnie konfiguracja gościa usługi Azure Policy umożliwia przeprowadzanie tylko inspekcji ustawień wewnątrz maszyny. Stosowanie konfiguracji nie jest obsługiwane.

## <a name="extension-and-client"></a>Rozszerzenie i klienta

Aby przeprowadzić inspekcję ustawień wewnątrz maszyny, [rozszerzenie maszyny wirtualnej](../../../virtual-machines/extensions/overview.md) jest włączone. Rozszerzenie pobiera przypisania zasad mających zastosowanie i odpowiedniej definicji konfiguracji.

### <a name="limits-set-on-the-extension"></a>Limity ustawione dla rozszerzenia

Aby ograniczyć rozszerzenie z wpływu na aplikacje działające na komputerze, konfiguracja gościa nie może przekroczyć więcej niż 5% użycia procesora CPU. To ograniczenie istnieje dla wbudowanych i niestandardowych definicji.

## <a name="register-guest-configuration-resource-provider"></a>Procedura Rejestruj dostawcę zasobów konfiguracji gościa

Zanim użyjesz konfiguracji gościa, należy zarejestrować dostawcę zasobów. Możesz zarejestrować się za pośrednictwem portalu lub za pomocą programu PowerShell. Dostawca zasobów jest automatycznie rejestrowany w przypadku przypisywania zasad konfiguracji gościa za pomocą portalu.

### <a name="registration---portal"></a>Rejestracja — Portal

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem witryny Azure portal, wykonaj następujące kroki:

1. Uruchom Azure Portal a następnie kliknij pozycję **wszystkie usługi**. Wyszukaj i wybierz pozycję **subskrypcje**.

1. Znajdź, a następnie kliknij subskrypcję, dla której chcesz włączyć konfigurację gościa.

1. W menu po lewej stronie **subskrypcja** kliknij pozycję **dostawcy zasobów**.

1. Odfiltruj lub Przewijaj do momentu zlokalizowania **Microsoft. GuestConfiguration**, a następnie kliknij pozycję **zarejestruj** w tym samym wierszu.

### <a name="registration---powershell"></a>Rejestracja — PowerShell

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Narzędzia do sprawdzania poprawności

W ramach maszyny klient konfiguracji gościa używa lokalnych narzędzi do uruchomienia inspekcji.

W poniższej tabeli przedstawiono listę narzędzi lokalnego, używane we wszystkich obsługiwanych systemach operacyjnych:

|System operacyjny|Narzędzie sprawdzania poprawności|Uwagi|
|-|-|-|
|System Windows|[Konfiguracja żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview) w wersji 2| |
|Linux|[Chef — Specyfikacja](https://www.chef.io/inspec/)| Język Ruby i Python są instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="validation-frequency"></a>Częstotliwość walidacji

Klient konfiguracji gościa sprawdza nową zawartość co 5 minut. Po odebraniu przypisania gościa ustawienia są sprawdzane w przedziale 15 minut. Wyniki są wysyłane do dostawcy zasobów konfiguracji gościa zaraz po zakończeniu inspekcji. Po wystąpieniu [wyzwalacza oceny](../how-to/get-compliance-data.md#evaluation-triggers) zasad stan maszyny jest zapisywana w dostawcy zasobów konfiguracji gościa. Ta aktualizacja powoduje, że Azure Policy ocen Azure Resource Manager właściwości. Ocena Azure Policy na żądanie Pobiera najnowszą wartość z dostawcy zasobów konfiguracji gościa. Jednak nie wyzwala on nowej inspekcji konfiguracji w ramach maszyny.

## <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych na obrazach platformy Azure:

|Wydawca|Name (Nazwa)|Wersje|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Oprogramowanie Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient systemu Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 Z DODATKIEM SP3|

> [!IMPORTANT]
> Konfiguracja gościa może prowadzić inspekcję węzłów z obsługiwanym systemem operacyjnym. Aby przeprowadzić inspekcję maszyn wirtualnych korzystających z obrazu niestandardowego, należy zduplikować definicję **DeployIfNotExists** i zmodyfikować sekcję **if** w celu uwzględnienia właściwości obrazu.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

System Windows Server nano Server nie jest obsługiwany w żadnej wersji.

## <a name="guest-configuration-extension-network-requirements"></a>Wymagania dotyczące sieci rozszerzenia konfiguracji gościa

Aby komunikować się z dostawcą zasobów konfiguracji gościa na platformie Azure, maszyny wymagają dostępu wychodzącego do centrów danych platformy Azure na porcie **443**. Jeśli używasz prywatnej sieci wirtualnej na platformie Azure, która nie zezwala na ruch wychodzący, skonfiguruj wyjątki z regułami [sieciowych grup zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
[Tag usługi](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" może służyć do odwoływania się do usługi konfiguracji gościa.

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każde uruchomienie inspekcji według konfiguracji gościa wymaga dwóch definicji zasad, definicji **DeployIfNotExists** i definicji **AuditIfNotExists** . Definicja **DeployIfNotExists** służy do przygotowywania maszyny z agentem konfiguracji gościa i innymi składnikami do obsługi [narzędzi walidacji](#validation-tools).

Definicja zasad **DeployIfNotExists** weryfikuje i koryguje następujące elementy:

- Sprawdź, czy na komputerze została przypisana konfiguracja do obliczenia. Jeśli żadne przypisanie nie jest obecnie dostępne, Pobierz przypisanie i przygotuj maszynę przez:
  - Uwierzytelnianie na maszynie przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji rozszerzenia **Microsoft. GuestConfiguration**
  - Instalowanie [narzędzi i zależności walidacji](#validation-tools) , w razie konieczności

Jeśli przypisanie **DeployIfNotExists** jest niezgodne, może zostać użyte [zadanie korygowania](../how-to/remediate-resources.md#create-a-remediation-task) .

Gdy przypisanie **DeployIfNotExists** jest zgodne, przypisanie zasad **AuditIfNotExists** używa lokalnych narzędzi walidacji w celu ustalenia, czy przypisanie konfiguracji jest zgodne, czy niezgodne. Narzędzie sprawdzania poprawności zapewnia wyniki do klienta konfiguracji gościa. Klient przesyła wyniki z rozszerzeniem gościa i udostępnia je za pośrednictwem dostawcy zasobów gościa konfiguracji.

Azure Policy używa właściwości **complianceStatus** dostawcy zasobów konfiguracji gościa, aby zgłosić zgodność w węźle **zgodność** . Aby uzyskać więcej informacji, zobacz [pobieranie danych zgodności](../how-to/get-compliance-data.md).

> [!NOTE]
> Zasady **DeployIfNotExists** są wymagane do zwracania wyników przez zasady **AuditIfNotExists** . Bez **DeployIfNotExists**zasady **AuditIfNotExists** są wyświetlane jako stan zasobów "0 z 0".

Wszystkie wbudowane zasady konfiguracji gościa znajdują się w inicjatywy do grupy definicje służące do użycia w przypisaniach. Wbudowana inicjatywa o nazwie _\[Preview\]: Inspekcja ustawień zabezpieczeń hasła w systemach Linux i Windows_ zawiera 18 zasad. Istnieje sześć par **DeployIfNotExists** i **AuditIfNotExists** dla systemu Windows i trzech par w systemie Linux. Logika [definicji zasad](definition-structure.md#policy-rule) sprawdza, czy jest oceniany tylko docelowy system operacyjny.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Inspekcja ustawień systemu operacyjnego po liniach bazowych branżowych

Jedna z inicjatyw dostępnych w Azure Policy umożliwia inspekcję ustawień systemu operacyjnego w ramach maszyn wirtualnych po "linii bazowej" od firmy Microsoft. Definicja _\[wersja Zapoznawcza\]: Inspekcja maszyn wirtualnych z systemem Windows, które nie są zgodne z ustawieniami linii bazowej zabezpieczeń platformy Azure,_ obejmuje pełny zestaw reguł inspekcji opartych na ustawieniach Active Directory zasady grupy.

Większość ustawień jest dostępnych jako parametry. Ta funkcja umożliwia dostosowanie funkcji inspekcji w celu dopasowania zasad do wymagań organizacji lub zamapowania zasad na informacje innych firm, takie jak standardy branżowe.

Niektóre parametry obsługują zakres wartości całkowitych. Na przykład parametr maksymalnego wieku hasła można ustawić za pomocą operatora zakresu, aby zapewnić elastyczność dla właścicieli maszyny. Można przeprowadzić inspekcję, że efektywne ustawienie zasady grupy wymagające od użytkowników zmiany hasła nie może być dłuższe niż 70 dni, ale nie powinno być krótsze niż jeden dzień. Zgodnie z opisem w polu dymek informacyjny dla parametru, aby wprowadzić tę zasadę biznesową obowiązującą wartość inspekcji, ustaw wartość "1, 70".

Jeśli zasady są przypisywane przy użyciu szablonu wdrażania Azure Resource Manager, można użyć pliku parametrów do zarządzania tymi ustawieniami z kontroli źródła. Za pomocą narzędzia, takiego jak Git, można zarządzać zmianami zasad inspekcji przy użyciu komentarzy w każdym dokumencie zaewidencjonowania, które potwierdzają, dlaczego przypisanie powinno być wyjątkiem od oczekiwanej wartości.

#### <a name="applying-configurations-using-guest-configuration"></a>Stosowanie konfiguracji przy użyciu konfiguracji gościa

Najnowsza funkcja Azure Policy konfiguruje ustawienia wewnątrz maszyn. Definicja _konfiguruje strefę czasową na maszynach z systemem Windows_ wprowadza zmiany na komputerze przez skonfigurowanie strefy czasowej.

Podczas przypisywania definicji zaczynających się od _konfiguracji_należy również przypisać _wymagania wstępne wdrażania definicji, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych z systemem Windows_. Możesz połączyć te definicje w ramach inicjatywy, jeśli wybierzesz opcję.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Przypisywanie zasad do maszyn poza platformą Azure

Zasady inspekcji dostępne dla konfiguracji gościa obejmują typ zasobu **Microsoft. HybridCompute/Machines** . Wszystkie maszyny dołączone do [usługi Azure ARC dla serwerów](../../../azure-arc/servers/overview.md) , które znajdują się w zakresie przypisania zasad, są automatycznie dołączane.

### <a name="multiple-assignments"></a>Wiele przypisań

Zasady konfiguracji gościa obsługują obecnie tylko jednokrotne przypisanie tego samego przypisania gościa na komputerze, nawet jeśli przypisanie zasad używa różnych parametrów.

## <a name="built-in-resource-modules"></a>Wbudowane moduły zasobów

Podczas instalowania rozszerzenia konfiguracji gościa moduł programu PowerShell "GuestConfiguration" jest dołączony do najnowszej wersji modułów zasobów DSC. Ten moduł można pobrać z Galeria programu PowerShell przy użyciu linku "Pobieranie ręczne" na stronie modułu [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). Dla formatu pliku ". nupkg" można zmienić nazwę na ". zip", aby zdekompresować i przejrzeć.

## <a name="client-log-files"></a>Pliki dziennika klienta

Rozszerzenie konfiguracji gościa zapisuje pliki dzienników w następujących lokalizacjach:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Gdzie `<version>` odnosi się do bieżącego numeru wersji.

### <a name="collecting-logs-remotely"></a>Zdalne zbieranie dzienników

Pierwszym krokiem w rozwiązywaniu problemów z konfiguracjami lub modułami konfiguracji gościa jest użycie polecenia cmdlet `Test-GuestConfigurationPackage`, wykonując czynności opisane w sekcji [Testowanie pakietu konfiguracji gościa](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Jeśli to nie powiodło się, zbieranie dzienników klienta może pomóc zdiagnozować problemy.

#### <a name="windows"></a>System Windows

Aby skorzystać z możliwości polecenia Uruchom maszynę wirtualną platformy Azure do przechwytywania informacji z plików dziennika na maszynach z systemem Windows, może być przydatny Poniższy przykładowy skrypt programu PowerShell. Aby uzyskać więcej informacji, zobacz [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Aby skorzystać z możliwości polecenia Uruchom maszynę wirtualną platformy Azure do przechwytywania informacji z plików dziennika na maszynach z systemem Linux, pomocne może być Poniższy przykład skryptu bash. Aby uzyskać więcej informacji, zobacz [Uruchamianie skryptów powłoki na maszynie wirtualnej z systemem Linux przy użyciu polecenia Run](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Przykłady konfiguracji gościa

Źródło dla wbudowanych inicjatyw konfiguracji gościa jest dostępne w następujących lokalizacjach:

- [Wbudowane definicje zasad — Konfiguracja gościa](../samples/built-in-policies.md#guest-configuration)
- [Wbudowane inicjatywy — konfiguracja gościa](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy przykłady repozytorium GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
