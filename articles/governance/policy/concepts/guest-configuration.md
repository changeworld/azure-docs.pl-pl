---
title: Dowiedz się, jak inspekcji zawartości maszyny wirtualnej
description: Dowiedz się, jak korzysta z usługi Azure Policy konfiguracji gościa na Przeprowadź inspekcję ustawienia w ramach maszyny wirtualnej platformy Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c79a4907e277c337509bd362653cfb100c4bd39c
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137439"
---
# <a name="understand-azure-policys-guest-configuration"></a>Omówienie usługi Azure Policy gościa konfiguracji

Oprócz przeprowadzania inspekcji i [korygowanie](../how-to/remediate-resources.md) zasobów platformy Azure, usługi Azure Policy można przeprowadzać inspekcję ustawień na maszynie wirtualnej. Sprawdzanie poprawności jest wykonywane przez rozszerzenie konfiguracji gościa i klienta. Rozszerzenie, za pomocą klienta, sprawdza poprawność ustawień, takich jak konfiguracja systemu operacyjnego, Konfiguracja aplikacji lub obecności i ustawienia środowiska.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Rozszerzenie i klienta

Inspekcja ustawień na maszynie wirtualnej [rozszerzenie maszyny wirtualnej](../../../virtual-machines/extensions/overview.md) jest włączona. Rozszerzenie pobiera przypisania zasad mających zastosowanie i odpowiedniej definicji konfiguracji.

### <a name="register-guest-configuration-resource-provider"></a>Procedura Rejestruj dostawcę zasobów konfiguracji gościa

Zanim użyjesz konfiguracji gościa, należy zarejestrować dostawcę zasobów. Możesz zarejestrować się za pośrednictwem portalu lub za pomocą programu PowerShell. Dostawca zasobów jest rejestrowane automatycznie, jeśli przypisanie zasad konfiguracji gościa jest wykonywane za pośrednictwem portalu.

#### <a name="registration---portal"></a>Rejestracja — Portal

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem witryny Azure portal, wykonaj następujące kroki:

1. Uruchamianie witryny Azure portal, a następnie kliknij polecenie **wszystkich usług**. Wyszukaj i wybierz pozycję **subskrypcje**.

1. Znajdź, a następnie kliknij subskrypcję, dla której chcesz włączyć konfigurację gościa.

1. W menu po lewej stronie **subskrypcji** kliknij **dostawców zasobów**.

1. Filtruj lub przewiń do momentu zlokalizowania **Microsoft.GuestConfiguration**, następnie kliknij przycisk **zarejestrować** na tym samym wierszu.

#### <a name="registration---powershell"></a>Rejestracja — PowerShell

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Narzędzia do sprawdzania poprawności

Wewnątrz maszyny wirtualnej gościa konfiguracji klient korzysta z lokalnego narzędzia przeprowadzić inspekcji.

W poniższej tabeli przedstawiono listę narzędzi lokalnego, używane we wszystkich obsługiwanych systemach operacyjnych:

|System operacyjny|Narzędzie sprawdzania poprawności|Uwagi|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Program chef InSpec](https://www.chef.io/inspec/)| Język Ruby i Python są instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="validation-frequency"></a>Częstotliwość sprawdzania poprawności

Klient Configuration Gość sprawdza nowej zawartości co 5 minut. Po otrzymaniu przydziału gościa ustawienia są sprawdzane w 15-minutowych interwałach. Wyniki są wysyłane do dostawcy zasobów konfiguracji gościa, zaraz po ukończeniu inspekcji. Gdy zasady [wyzwalacza oceny](../how-to/get-compliance-data.md#evaluation-triggers) występuje i stan maszyny są zapisywane na potrzeby dostawcy zasobów gościa konfiguracji. To powoduje, że usługi Azure Policy do oceny właściwości usługi Azure Resource Manager. Ocena zasad platformy Azure na żądanie pobiera najnowszą wartość z dostawcy zasobów gościa konfiguracji. Jednak go nie wyzwala nowy inspekcji konfiguracji maszyny wirtualnej.

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych na obrazach platformy Azure:

|Wydawca|Name (Nazwa)|Wersje|
|-|-|-|
|Canonical|Ubuntu Server|14.04 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient systemu Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 Z DODATKIEM SP3|

> [!IMPORTANT]
> Konfiguracja gościa można przeprowadzać inspekcję węzły obsługiwanego systemu operacyjnego. Jeśli chcesz przeprowadzić inspekcję maszyn wirtualnych korzystających z niestandardowego obrazu, należy zduplikować **DeployIfNotExists** definicji i modyfikować **Jeśli** sekcji, aby uwzględnić właściwości obrazu.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

Windows Server Nano Server nie jest obsługiwane w dowolnej wersji.

### <a name="guest-configuration-extension-network-requirements"></a>Wymagania dotyczące sieci rozszerzenia konfiguracji gościa

Aby komunikować się z dostawcą zasobów gościa konfiguracji na platformie Azure, maszyny wirtualne wymagają dostęp ruchu wychodzącego do centrów danych platformy Azure na porcie **443**. Jeśli używasz prywatnej sieci wirtualnej na platformie Azure, a nie zezwolić na ruch wychodzący, wyjątki musi być skonfigurowany przy użyciu [sieciowej grupy zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) reguły. W tej chwili tag usługi nie istnieje dla konfiguracji gościa zasad platformy Azure.

Dla listy adresów IP, możesz pobrać [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowana co tydzień i ma aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Musisz zezwolić na dostęp ruchu wychodzącego do adresów IP w regionach, w których maszyny wirtualne są wdrażane.

> [!NOTE]
> Plik XML adres IP centrum danych platformy Azure zawiera listę zakresów adresów IP, które są używane w centrach danych platformy Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i storage. Tydzień jest publikowany zaktualizowany plik. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień. To dobry pomysł, aby pobrać nowy plik XML, co tydzień. Następnie należy zaktualizować lokację do prawidłowo identyfikować usługi uruchomione na platformie Azure. Użytkownicy usługi Azure ExpressRoute należy zauważyć, że ten plik jest używany do aktualizacji anonsu protokołu BGP (Border Gateway) miejsca platformy Azure w pierwszym tygodniu każdego miesiąca.

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każdy inspekcji uruchamiane przez gościa konfiguracji wymaga dwiema definicjami zasad **DeployIfNotExists** definicji i **inspekcji** definicji. **DeployIfNotExists** definicja jest używana do przygotowywanie maszyny wirtualnej za pomocą agenta gościa, konfiguracji i inne składniki do obsługi [narzędzia do sprawdzania poprawności](#validation-tools).

**DeployIfNotExists** definicji zasad sprawdza i naprawia następujące elementy:

- Sprawdzanie poprawności konfiguracji w celu oceny zostały przypisane maszyny wirtualnej. W przypadku aktualnie nie przypisania uzyskać przydział i przygotowywanie maszyny wirtualnej za pomocą:
  - Uwierzytelnianie na maszynę wirtualną przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji **Microsoft.GuestConfiguration** rozszerzenia
  - Instalowanie [narzędzia do sprawdzania poprawności](#validation-tools) i zależności, jeśli to konieczne

Jeśli **DeployIfNotExists** przypisanie jest niezgodne, [zadań korygowania](../how-to/remediate-resources.md#create-a-remediation-task) mogą być używane.

Gdy **DeployIfNotExists** przypisanie jest zgodne, **inspekcji** przypisania zasad używa lokalna Weryfikacja narzędzi w celu ustalenia, czy przypisanie konfiguracji zgodne lub niezgodne.
Narzędzie sprawdzania poprawności zapewnia wyniki do klienta konfiguracji gościa. Klient przesyła wyniki z rozszerzeniem gościa i udostępnia je za pośrednictwem dostawcy zasobów gościa konfiguracji.

Usługa Azure Policy korzysta z dostawców zasobów gościa konfiguracji **complianceStatus** właściwości raportu zgodności w **zgodności** węzła. Aby uzyskać więcej informacji, zobacz [pobierania danych zgodności](../how-to/getting-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** zasady są wymagane dla **inspekcji** zasad w celu zwracania wyników.
> Bez **DeployIfNotExists**, **inspekcji** zasad zawiera "0 0" zasobów, jak w przypadku stanu.

Wszystkie wbudowane zasady konfiguracji gościa znajdują się w inicjatywy do grupy definicje służące do użycia w przypisaniach. Wbudowane inicjatywę o nazwie *[wersja zapoznawcza]: Przeprowadź inspekcję ustawienia zabezpieczeń hasła wewnątrz maszyn wirtualnych z systemem Linux i Windows* zawiera zasady 18. Sześć **DeployIfNotExists** i **inspekcji** pary dla Windows i trzy pary dla systemu Linux. W każdym przypadku logiki wewnątrz definicji sprawdza tylko element docelowy system operacyjny jest obliczana na podstawie [reguła zasad](definition-structure.md#policy-rule) definicji.

## <a name="client-log-files"></a>Pliki dziennika klienta

Rozszerzenie konfiguracji gościa zapisuje pliki dziennika w następujących lokalizacjach:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Gdzie `<version>` odwołuje się do bieżący numer wersji.

## <a name="guest-configuration-samples"></a>Przykłady konfiguracji gościa

Przykłady dla konfiguracji gościa zasad są dostępne w następujących lokalizacjach:

- [Indeks przykładów - gościa konfiguracji](../samples/index.md#guest-configuration)
- [Usługa Azure Policy przykłady repozytorium GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md).
- Dowiedz się, jak [Pobierz dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md).
- Przejrzyj grupy zarządzania jest [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/index.md).
