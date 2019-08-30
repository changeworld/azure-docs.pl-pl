---
title: Dowiedz się, jak przeprowadzić inspekcję zawartości maszyny
description: Dowiedz się, jak Azure Policy używa konfiguracji gościa do inspekcji ustawień w ramach maszyny platformy Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b6c9e50334a25b505655a49a02cd98165d04740b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164947"
---
# <a name="understand-azure-policys-guest-configuration"></a>Omówienie usługi Azure Policy gościa konfiguracji

Oprócz inspekcji i [korygowaniem](../how-to/remediate-resources.md) zasobów platformy Azure, Azure Policy może przeprowadzać inspekcję ustawień wewnątrz maszyny. Sprawdzanie poprawności jest wykonywane przez rozszerzenie konfiguracji gościa i klienta. Rozszerzenie, za pomocą klienta, sprawdza poprawność ustawień, takich jak konfiguracja systemu operacyjnego, Konfiguracja aplikacji lub obecności i ustawienia środowiska.

W tej chwili Azure Policy konfiguracja gościa tylko przeprowadza inspekcję ustawienia wewnątrz maszyny.
Nie można jeszcze stosować konfiguracji.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Rozszerzenie i klienta

Aby przeprowadzić inspekcję ustawień wewnątrz maszyny, [rozszerzenie maszyny wirtualnej](../../../virtual-machines/extensions/overview.md) jest włączone. Rozszerzenie pobiera przypisania zasad mających zastosowanie i odpowiedniej definicji konfiguracji.

### <a name="limits-set-on-the-exension"></a>Limity ustawione na exension

Aby ograniczyć rozszerzenie od wpływu na aplikacje działające na komputerze, konfiguracja gościa nie może przekroczyć więcej niż 5% użycia procesora CPU.
Jest to prawdziwe BOH w przypadku konfiguracji dostarczonych przez firmę Microsoft jako "wbudowane" i dla konfiguracji niestandardowych utworzonych przez klientów.

## <a name="register-guest-configuration-resource-provider"></a>Procedura Rejestruj dostawcę zasobów konfiguracji gościa

Zanim użyjesz konfiguracji gościa, należy zarejestrować dostawcę zasobów. Możesz zarejestrować się za pośrednictwem portalu lub za pomocą programu PowerShell. Dostawca zasobów jest automatycznie rejestrowany w przypadku przypisywania zasad konfiguracji gościa za pomocą portalu.

### <a name="registration---portal"></a>Rejestracja — Portal

Aby zarejestrować dostawcę zasobów dla konfiguracji gościa za pośrednictwem witryny Azure portal, wykonaj następujące kroki:

1. Uruchamianie witryny Azure portal, a następnie kliknij polecenie **wszystkich usług**. Wyszukaj i wybierz pozycję **subskrypcje**.

1. Znajdź, a następnie kliknij subskrypcję, dla której chcesz włączyć konfigurację gościa.

1. W menu po lewej stronie **subskrypcji** kliknij **dostawców zasobów**.

1. Filtruj lub przewiń do momentu zlokalizowania **Microsoft.GuestConfiguration**, następnie kliknij przycisk **zarejestrować** na tym samym wierszu.

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
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Program chef InSpec](https://www.chef.io/inspec/)| Język Ruby i Python są instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="validation-frequency"></a>Częstotliwość walidacji

Klient konfiguracji gościa sprawdza nową zawartość co 5 minut. Po odebraniu przypisania gościa ustawienia są sprawdzane w przedziale 15 minut. Wyniki są wysyłane do dostawcy zasobów konfiguracji gościa zaraz po zakończeniu inspekcji. Po wystąpieniu [wyzwalacza oceny](../how-to/get-compliance-data.md#evaluation-triggers) zasad stan maszyny jest zapisywana w dostawcy zasobów konfiguracji gościa. Powoduje Azure Policy ocenę Azure Resource Manager właściwości. Ocena Azure Policy na żądanie Pobiera najnowszą wartość z dostawcy zasobów konfiguracji gościa. Jednak nie wyzwala on nowej inspekcji konfiguracji w ramach maszyny.

## <a name="supported-client-types"></a>Typy obsługiwanych klientów

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
> Konfiguracja gościa może prowadzić inspekcję węzłów z obsługiwanym systemem operacyjnym. Aby przeprowadzić inspekcję maszyn wirtualnych korzystających z obrazu niestandardowego, należy zduplikować definicję **DeployIfNotExists** i zmodyfikować sekcję **if** w celu uwzględnienia właściwości obrazu.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

System Windows Server nano Server nie jest obsługiwany w żadnej wersji.

## <a name="guest-configuration-extension-network-requirements"></a>Wymagania dotyczące sieci rozszerzenia konfiguracji gościa

Aby komunikować się z dostawcą zasobów konfiguracji gościa na platformie Azure, maszyny wymagają dostępu wychodzącego do centrów danych platformy Azure na porcie **443**. Jeśli używasz prywatnej sieci wirtualnej na platformie Azure i nie zezwolisz na ruch wychodzący, należy skonfigurować wyjątki przy użyciu reguł [sieciowej grupy zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . W tym momencie tag usługi nie istnieje dla Azure Policy konfiguracji gościa.

W przypadku list adresów IP można pobrać [Microsoft Azure zakresy adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP. Musisz zezwolić na dostęp wychodzący do adresów IP w regionach, w których wdrożono maszyny wirtualne.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera zakresy adresów IP, które są używane w centrach danych Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i magazynu. Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Dobrym pomysłem jest pobranie nowego pliku XML co tydzień. Następnie zaktualizuj swoją witrynę, aby prawidłowo identyfikować usługi działające na platformie Azure. Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizacji anonsu usługi Border Gateway Protocol (BGP) w pierwszym tygodniu każdego miesiąca.

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każde uruchomienie inspekcji według konfiguracji gościa wymaga dwóch definicji zasad, definicji **DeployIfNotExists** i definicji **AuditIfNotExists** . Definicja **DeployIfNotExists** służy do przygotowywania maszyny z agentem konfiguracji gościa i innymi składnikami do obsługi [narzędzi walidacji](#validation-tools).

**DeployIfNotExists** definicji zasad sprawdza i naprawia następujące elementy:

- Sprawdź, czy na komputerze została przypisana konfiguracja do obliczenia. Jeśli żadne przypisanie nie jest obecnie dostępne, Pobierz przypisanie i przygotuj maszynę przez:
  - Uwierzytelnianie na maszynie przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji **Microsoft.GuestConfiguration** rozszerzenia
  - Instalowanie [narzędzia do sprawdzania poprawności](#validation-tools) i zależności, jeśli to konieczne

Jeśli przypisanie **DeployIfNotExists** jest niezgodne, może zostać użyte [zadanie korygowania](../how-to/remediate-resources.md#create-a-remediation-task) .

Gdy przypisanie **DeployIfNotExists** jest zgodne, przypisanie zasad **AuditIfNotExists** używa lokalnych narzędzi walidacji w celu ustalenia, czy przypisanie konfiguracji jest zgodne, czy niezgodne.
Narzędzie sprawdzania poprawności zapewnia wyniki do klienta konfiguracji gościa. Klient przesyła wyniki z rozszerzeniem gościa i udostępnia je za pośrednictwem dostawcy zasobów gościa konfiguracji.

Usługa Azure Policy korzysta z dostawców zasobów gościa konfiguracji **complianceStatus** właściwości raportu zgodności w **zgodności** węzła. Aby uzyskać więcej informacji, zobacz [pobierania danych zgodności](../how-to/getting-compliance-data.md).

> [!NOTE]
> Zasady **DeployIfNotExists** są wymagane do zwracania wyników przez zasady **AuditIfNotExists** .
> Bez **DeployIfNotExists**zasady **AuditIfNotExists** są wyświetlane jako stan zasobów "0 z 0".

Wszystkie wbudowane zasady konfiguracji gościa znajdują się w inicjatywy do grupy definicje służące do użycia w przypisaniach. Wbudowana inicjatywa o nazwie *[wersja zapoznawcza]: Inspekcja ustawień zabezpieczeń hasła w systemach Linux i* Windows zawiera 18 zasad. Sześć **DeployIfNotExists** i **AuditIfNotExists** pary dla Windows i trzy pary dla systemu Linux. W każdym przypadku logiki wewnątrz definicji sprawdza tylko element docelowy system operacyjny jest obliczana na podstawie [reguła zasad](definition-structure.md#policy-rule) definicji.

### <a name="multiple-assignments"></a>Wiele przypisań

Zasady konfiguracji gościa obsługują obecnie tylko jednokrotne przypisanie tego samego przypisania gościa na komputerze, nawet jeśli przypisanie zasad używa różnych parametrów.

## <a name="client-log-files"></a>Pliki dziennika klienta

Rozszerzenie konfiguracji gościa zapisuje pliki dzienników w następujących lokalizacjach:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Gdzie `<version>` odwołuje się do bieżącego numeru wersji.

## <a name="guest-configuration-samples"></a>Przykłady konfiguracji gościa

Przykłady konfiguracji gościa zasad są dostępne w następujących lokalizacjach:

- [Indeks przykładów — konfiguracja gościa](../samples/index.md#guest-configuration)
- [Azure Policy przykłady repozytorium GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/index.md).
