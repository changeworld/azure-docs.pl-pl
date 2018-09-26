---
title: Zrozumienie, jak usługi Azure Policy przeprowadza audyty wewnątrz maszyny wirtualnej
description: Dowiedz się, jak korzysta z usługi Azure Policy konfiguracji gościa na Przeprowadź inspekcję ustawienia w ramach maszyny wirtualnej platformy Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: ca96aea8f359f1df7da48f84a3317a2d8c7b52e4
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167945"
---
# <a name="understand-azure-policys-guest-configuration"></a>Omówienie usługi Azure Policy gościa konfiguracji

Oprócz przeprowadzania inspekcji i [korygowanie](../how-to/remediate-resources.md) zasobów platformy Azure, usługi Azure Policy jest w stanie inspekcji ustawień na maszynie wirtualnej. Sprawdzanie poprawności jest wykonywane przez rozszerzenie konfiguracji gościa i klienta. Rozszerzenie, za pomocą klienta, sprawdza poprawność ustawień, takich jak konfiguracja systemu operacyjnego, Konfiguracja aplikacji lub obecności i ustawienia środowiska.

> [!IMPORTANT]
> Obecnie tylko **wbudowanych** zasady są obsługiwane przy użyciu konfiguracji gościa.

## <a name="extension-and-client"></a>Rozszerzenie i klienta

Inspekcja ustawień na maszynie wirtualnej [rozszerzenie maszyny wirtualnej](../../../virtual-machines/extensions/overview.md) jest włączona. Rozszerzenie pobiera przypisania zasad mających zastosowanie i odpowiedniej definicji konfiguracji.

### <a name="register-guest-configuration-resource-provider"></a>Procedura Rejestruj dostawcę zasobów konfiguracji gościa

Zanim użyjesz konfiguracji gościa, należy zarejestrować dostawcę zasobów. Można to zrobić za pośrednictwem portalu lub za pomocą programu PowerShell.

#### <a name="registration---portal"></a>Rejestracja — Portal

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem witryny Azure portal, wykonaj następujące kroki:

1. Uruchamianie witryny Azure portal, a następnie kliknij polecenie **wszystkich usług**. Wyszukaj i wybierz pozycję **subskrypcje**.

1. Znajdź, a następnie kliknij subskrypcję, dla której chcesz włączyć konfigurację gościa.

1. W menu po lewej stronie **subskrypcji** kliknij **dostawców zasobów**.

1. Filtruj lub przewiń do momentu zlokalizowania **Microsoft.GuestConfiguration**, następnie kliknij przycisk **zarejestrować** na tym samym wierszu.

#### <a name="registration---powershell"></a>Rejestracja — PowerShell

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell
Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Narzędzia do sprawdzania poprawności

Wewnątrz maszyny wirtualnej gościa konfiguracji klient korzysta z lokalnego narzędzia przeprowadzić inspekcji.

W poniższej tabeli przedstawiono listę narzędzi lokalnego, używane we wszystkich obsługiwanych systemach operacyjnych:

|System operacyjny|Narzędzie sprawdzania poprawności|Uwagi|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Program chef InSpec](https://www.chef.io/inspec/)| Język Ruby i Python są instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych na obrazach platformy Azure:

|Wydawca|Name (Nazwa)|Wersje|
|-|-|-|
|Canonical|Ubuntu Server|14.04 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 Z DODATKIEM SP3|

> [!IMPORTANT]
> Konfiguracja gościa nie jest obecnie obsługiwane w przypadku niestandardowych maszyn wirtualnych z obrazów.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

W poniższej tabeli wymieniono systemy operacyjne, które nie są obsługiwane:

|System operacyjny|Uwagi|
|-|-|
|Klient systemu Windows | Systemy operacyjne klienta (takich jak Windows 7 i Windows 10) nie są obsługiwane.
|Windows Server 2016 Nano Server | Nieobsługiwane.|

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każdy inspekcji, wykonywane przez gościa konfiguracji wymaga dwiema definicjami zasad **DeployIfNotExists** i **AuditIfNotExists**. **DeployIfNotExists** służy do przygotowywania maszyny wirtualnej za pomocą agenta gościa, konfiguracji i inne składniki do obsługi [narzędzia do sprawdzania poprawności](#validation-tools).

**DeployIfNotExists** definicji zasad sprawdza i naprawia następujące czynności:

- Upewnij się, że maszyna wirtualna została przypisana konfiguracji w celu oceny. W przypadku aktualnie nie przypisania uzyskać przydział i przygotowywanie maszyny wirtualnej za pomocą:
  - Uwierzytelnianie na maszynę wirtualną przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji **Microsoft.GuestConfiguration** rozszerzenia
  - Instalowanie [narzędzia do sprawdzania poprawności](#validation-tools) i zależności, jeśli to konieczne

Gdy **DeployIfNotExists** jest zgodne, **AuditIfNotExists** definicji zasad używa lokalna Weryfikacja narzędzi w celu ustalenia, czy przypisanie przypisanej konfiguracji jest zgodne lub Niezgodne. Narzędzie sprawdzania poprawności zapewnia wyniki do klienta konfiguracji gościa, która przekazuje je do rozszerzenia gościa, aby był dostępny za pośrednictwem dostawcy zasobów gościa konfiguracji.

Usługa Azure Policy korzysta z dostawców zasobów gościa konfiguracji **complianceStatus** właściwości raportu zgodności w **zgodności** węzła. Aby uzyskać więcej informacji, zobacz [pobierania danych zgodności](../how-to/getting-compliance-data.md).

> [!NOTE]
> Dla każdej definicji konfiguracji gościa zarówno **DeployIfNotExists** i **AuditIfNotExists** definicje zasad, musi istnieć.

Wszystkie wbudowane zasady konfiguracji gościa znajdują się w inicjatywy do grupy definicje służące do użycia w przypisaniach. Wbudowane inicjatywę o nazwie *[wersja zapoznawcza]: ustawienia zabezpieczeń inspekcji hasła wewnątrz maszyn wirtualnych z systemem Linux i Windows* zawiera zasady 18. Sześć **DeployIfNotExists** i **AuditIfNotExists** pary dla Windows i trzy pary dla systemu Linux. W każdym przypadku logiki wewnątrz definicji zapewnia tylko docelowego systemu operacyjnego jest obliczana na podstawie [reguła zasad](definition-structure.md#policy-rule) definicji.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md)
- Przegląd [struktura definicji zasad](definition-structure.md)
- Przegląd [zrozumienia efektów zasad](effects.md)
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md)
- Dowiedz się, jak [uzyskać dane na temat zgodności](../how-to/getting-compliance-data.md)
- Odkryj jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/index.md)