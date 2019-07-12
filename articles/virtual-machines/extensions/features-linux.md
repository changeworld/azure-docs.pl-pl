---
title: Funkcje dla systemu Linux i rozszerzeniach maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co dane substancje mogą zapewnić lub które poprawić.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3f22da9eabc6f539ef37009f565f073b9de89319
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706740"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux

Rozszerzenia maszyn wirtualnych (VM) to małych aplikacji, które zapewniają po wdrożeniu konfiguracji oraz zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład, czy maszyna wirtualna wymaga instalacji oprogramowania, oprogramowanie antywirusowe ochrony, aby uruchomić skrypt wewnątrz niej, można użyć rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchomić z wiersza polecenia platformy Azure, programu PowerShell, szablony usługi Azure Resource Manager i witryny Azure portal. Rozszerzenia mogą być powiązane z wdrażaniem nowej maszyny Wirtualnej lub uruchamiać dowolnego istniejącego systemu.

Ten artykuł zawiera przegląd rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące korzystania z rozszerzenia maszyny Wirtualnej platformy Azure i uzyskać wskazówki dotyczące sposobu wykrywania, zarządzanie i usuwanie rozszerzeń maszyn wirtualnych. Ten artykuł zawiera informacje uogólniony, ponieważ wiele rozszerzeń maszyn wirtualnych są dostępne, każdy z potencjalnie unikatowej konfiguracji. Szczegóły specyficzne dla rozszerzenia można znaleźć w każdym dokumencie specyficzne dla poszczególnych rozszerzeń.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Kilka różnych rozszerzenia maszyny Wirtualnej platformy Azure są dostępne, każdy z określonym przypadek użycia. Oto niektóre przykłady:

- Dotyczy konfiguracji środowiska PowerShell żądanego stanu maszyny Wirtualnej za pomocą rozszerzenia DSC dla systemu Linux. Aby uzyskać więcej informacji, zobacz [rozszerzenia konfiguracji żądanego stanu platformy Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurowanie, monitorowanie maszyny wirtualnej za pomocą rozszerzenia maszyny Wirtualnej agenta monitorowania Microsoft. Aby uzyskać więcej informacji, zobacz [monitorowanie maszyny Wirtualnej z systemem Linux](../linux/tutorial-monitoring.md).
- Konfigurowanie, monitorowanie infrastruktury platformy Azure przy użyciu programu Chef lub pomocą usługi Datadog rozszerzenie. Aby uzyskać więcej informacji, zobacz [dokumentacja programu Chef](https://docs.chef.io/azure_portal.html) lub [blogu pomocą usługi Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oprócz rozszerzenia procesu rozszerzenie niestandardowego skryptu jest dostępna dla maszyn wirtualnych systemów Windows i Linux. Rozszerzenie niestandardowego skryptu dla systemu Linux pozwala dowolnego skryptu powłoki systemowej do uruchomienia na maszynie Wirtualnej. Niestandardowe skrypty są przydatne w przypadku projektowania wdrożenia platformy Azure, które wymagają konfiguracji oprócz jakie natywnych narzędzi platformy Azure może zapewnić. Aby uzyskać więcej informacji, zobacz [rozszerzenia skryptu niestandardowego maszyny Wirtualnej systemu Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do obsługi rozszerzenia na maszynie Wirtualnej, należy zainstalować agenta systemu Linux dla platformy Azure. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takich jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny Wirtualnej platformy Azure zarządza interakcje między Maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny Wirtualnej jest odpowiedzialny za wiele aspektów funkcjonalnych wdrażania i zarządzania nimi maszyn wirtualnych platformy Azure, w tym uruchamianie rozszerzeń maszyn wirtualnych. Agent maszyny Wirtualnej platformy Azure jest preinstalowany w portalu Azure Marketplace, obrazy i można ją zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny Wirtualnej platformy Azure dla systemu Linux jest znana jako funkcja agentów systemu Linux.

Aby uzyskać informacje na temat obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Wersje obsługiwanych agentach

Aby zapewnić możliwie najlepsze środowisko, istnieją co najmniej wersji agenta. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemy operacyjne

Agenta systemu Linux jest uruchamiany w wielu systemach operacyjnych, jednak w ramach rozszerzenia ma limit dla systemów operacyjnych z tego rozszerzenia. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i może emitować *51 kodu błędu, nieobsługiwany system operacyjny*. W dokumentacji poszczególnych rozszerzenie obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z usługi Azure Storage repozytorium rozszerzeń i przekazywanie stanu rozszerzenia są wysyłane do usługi Azure Storage. Jeśli używasz [obsługiwane](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie trzeba zezwolić na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, ponieważ agent można używać do przekierowania komunikacji z kontrolerem sieci szkieletowej platformy Azure do komunikacji agenta. Jeśli korzystasz z nieobsługiwanej wersji agenta, należy zezwolić na dostęp ruchu wychodzącego do usługi Azure storage w danym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Zablokowanie dostępu do *168.63.129.16* przy użyciu zapory gościa, następnie rozszerzenia kończą się niepowodzeniem niezależnie od powyższych.

Agentów należy używać tylko do pobierania pakietów rozszerzeń i stanu raportowania. Na przykład, czy instalacja rozszerzenia musi pobrać skrypt z usługi GitHub (niestandardowego skryptu) wymagany jest dostęp do usługi Azure Storage (usługa Azure Backup), następnie dodatkowe zapory i sieci zabezpieczeń grupy portów muszą być otwarte. Inne rozszerzenia mają różne wymagania, ponieważ są one aplikacje w ich własnych po prawej stronie. Dla rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Aby przekierować żądania ruchu agenta, Agent systemu Linux ma Obsługa serwerów proxy. Jednak ta obsługa serwerów proxy, nie ma zastosowania rozszerzenia. Należy skonfigurować każde rozszerzenie poszczególnych do pracy z serwerem proxy.

## <a name="discover-vm-extensions"></a>Odkryj rozszerzeń maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, należy użyć [listy obrazów rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension/image#az-vm-extension-image-list). Poniższy przykład wyświetla listę wszystkich dostępnych rozszerzeń w *westus* lokalizacji:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzenia maszyn wirtualnych

Rozszerzenia maszyn wirtualnych platformy Azure, uruchom na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub odzyskać łączność już wdrożonej maszyny wirtualnej. Rozszerzenia maszyn wirtualnych dołączany także wdrożeń szablonu usługi Azure Resource Manager. Za pomocą rozszerzeń przy użyciu szablonów usługi Resource Manager, maszyn wirtualnych platformy Azure można wdrożona i skonfigurowana bez interwencji po wdrożeniu.

Następujące metody może służyć do uruchamiania rozszerzenie istniejącej maszyny Wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Rozszerzenia maszyn wirtualnych platformy Azure mogą być uruchamiane względem istniejącej maszyny Wirtualnej za pomocą [zestaw rozszerzeń maszyn wirtualnych az](/cli/azure/vm/extension#az-vm-extension-set) polecenia. W poniższym przykładzie uruchamiane rozszerzenie niestandardowego skryptu dla maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Gdy rozszerzenia działa poprawnie, dane wyjściowe będą podobne do poniższego przykładu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyn wirtualnych można zastosować do istniejącej maszyny Wirtualnej w witrynie Azure portal. Wybierz maszynę Wirtualną w portalu, wybierz pozycję **rozszerzenia**, a następnie wybierz **Dodaj**. Wybierz rozszerzenie, z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze.

Na poniższej ilustracji przedstawiono instalacja rozszerzenia skryptu niestandardowego systemu Linux w witrynie Azure portal:

![Zainstaluj rozszerzenie niestandardowego skryptu](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyn wirtualnych można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu, można utworzyć w pełni skonfigurowany wdrożeń platformy Azure. Na przykład następujący kod JSON jest zajęta z szablonu usługi Resource Manager, który wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i usługi Azure SQL database, a następnie instaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenie maszyny Wirtualnej zajmuje się instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz pełne [szablonu usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej, może być konieczne uwzględnienie informacji poufnych, takich jak poświadczenia, nazwy kont magazynu i kluczy dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowej maszyny Wirtualnej. Każde rozszerzenie ma schemat konkretnej konfiguracji chronionych, a każdy jest szczegółowo opisane w dokumentacji rozszerzenia.

Poniższy przykład pokazuje wystąpienie rozszerzenie niestandardowego skryptu dla systemu Linux. Polecenie do wykonania obejmuje zestaw poświadczeń. W tym przykładzie nie są szyfrowane polecenia do wykonania:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Przenoszenie **polecenie do wykonania** właściwości **chronione** konfiguracja zabezpiecza ciągu wykonywania, jak pokazano w poniższym przykładzie:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak agentów i rozszerzenia zostaje zaktualizowana?

Agenci i rozszerzenia należy udostępnić ten sam mechanizm aktualizacji. Niektóre aktualizacje wymagają dodatkowe reguły zapory.

Gdy aktualizacja jest dostępna, jej jest ona instalowana tylko na maszynie Wirtualnej po zmianie rozszerzenia i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Wpisy tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieciowy

Wydawcy aktualizacje były dostępne w regionach w różnym czasie, więc prawdopodobnie masz maszyny wirtualne w różnych regionach w różnych wersjach.

#### <a name="agent-updates"></a>Aktualizacje agenta

Agent maszyny Wirtualnej systemu Linux zawiera *kod aprowizacji agenta* i *rozszerzenia obsługi kodu* w jednym pakiecie, który nie może być oddzielony. Możesz wyłączyć *aprowizacji agenta* gdy zachodzi potrzeba przepis na platformie Azure przy użyciu pakietu cloud-init. Aby to zrobić, zobacz [przy użyciu pakietu cloud-init](../linux/using-cloud-init.md).

Obsługiwane wersje agentów za pomocą funkcji Aktualizacje automatyczne. Tylko kod, który może być aktualizowana jest *kod obsługi rozszerzenia*, kod inicjowania obsługi administracyjnej. *Kod aprowizacji agenta* jednokrotnego uruchamiania kodu.

*Rozszerzenia obsługi kodu* odpowiada za komunikację przy użyciu usługi Azure Service fabric i obsługi operacji rozszerzenia maszyny Wirtualnej, takie jak zainstalowaniu zgłaszające stan poszczególnych rozszerzeń i aktualizowanie ich usunięcie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *rozszerzenia obsługi kodu*.

Agent jest zainstalowany, zostanie utworzona demona nadrzędnej. Tego elementu nadrzędnego spowoduje utworzenie następnie proces podrzędny, która jest używana do obsługi rozszerzeń. Jeśli jest dostępna aktualizacja agenta, zostanie pobrany, nadrzędny zatrzymuje proces podrzędny, uaktualnia go, a następnie ponownie go uruchamia. Powinno być problem z aktualizacją, proces nadrzędny wycofanie do poprzedniej wersji podrzędnych.

Proces nadrzędny nie może być automatycznie aktualizowane. Element nadrzędny można aktualizować tylko przez aktualizację pakietu dystrybucji.

Aby sprawdzić, której wersji używasz, zapoznaj się z `waagent` w następujący sposób:

```bash
waagent --version
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

W powyższym przykładzie danych wyjściowych, jest nadrzędnym lub "pakiet wdrożony w wersji" *WALinuxAgent 2.2.17*

Agent cel — stan jest automatycznie zaktualizowanej wersji.

Zdecydowanie zaleca się posiadanie aktualizacje automatyczne agenta, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Nie mających oznacza to włączone, należy zachować ręczna aktualizacja agenta, a nie uzyskać poprawki usterek i zabezpieczeń.

#### <a name="extension-updates"></a>Aktualizacje rozszerzenia

Po udostępnieniu aktualizacji rozszerzenia agenta systemu Linux pliki do pobrania i uaktualnia rozszerzenia. Aktualizacje automatyczne rozszerzenia są albo *pomocnicza* lub *poprawkę*. Można zgodzić się na lub zrezygnować z rozszerzenia *pomocnicza* aktualizacji podczas aprowizacji rozszerzenia. Poniższy przykład pokazuje, jak automatycznie uaktualnić wersje pomocnicze w szablonie usługi Resource Manager za pomocą *autoUpgradeMinorVersion ": ma wartość true,"* :

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Aby uzyskać najnowszą wersję pomocniczą poprawki, zdecydowanie zaleca się zawsze wybierz automatyczną aktualizację we wdrożeniach rozszerzenia. Aktualizacji będących poprawkami, wykonujących poprawki zabezpieczeń lub klucz nie wyraził na nią zgody.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować rozszerzenia aktualizacje

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identyfikowanie, jeśli rozszerzenie jest ustawiony za pomocą autoUpgradeMinorVersion na maszynie Wirtualnej

Można wyświetlić w modelu maszyny Wirtualnej rozszerzenie została przygotowana z użyciem "autoUpgradeMinorVersion". Aby sprawdzić, użyj [az vm show](/cli/azure/vm#az-vm-show) i podaj nazwę grupy zasobów i maszynę Wirtualną w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Następujące przykładowe dane wyjściowe pokazuje, że *autoUpgradeMinorVersion* ustawiono *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie po wystąpieniu autoUpgradeMinorVersion

Aby zobaczyć, kiedy wystąpiła aktualizacja do rozszerzenia, przejrzyj agenta dzienniki na maszynie Wirtualnej o */var/log/waagent.log*.

W poniższym przykładzie maszyna wirtualna ma *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* zainstalowane. Poprawka była dostępna do *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Do wykonywania swoich zadań, agent musi być uruchamiane jako *głównego*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych

Każde rozszerzenie maszyny Wirtualnej może mieć Rozwiązywanie problemów w określone rozszerzenia. Na przykład korzystając z rozszerzenia niestandardowego skryptu, szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie Wirtualnej, w którym zostało uruchomione rozszerzenia. Wszystkie kroki rozwiązywania problemów specyficznych dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Następujące kroki dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić w dzienniku agenta systemu Linux, Przyjrzyj się działanie po rozszerzenie zostało aprowizowane w */var/log/waagent.log*

2. Sprawdź dzienniki rzeczywiste rozszerzenia, aby uzyskać więcej szczegółów w */var/logowania/azure/\<extensionName >*

3. W dokumentacji specyficzne dla rozszerzenia Rozwiązywanie problemów z sekcje, aby uzyskać kody błędów, znane problemy dotyczące itp.

3. Sprawdź dzienniki systemu. Sprawdź, czy inne operacje, które mogą mieć zakłócać rozszerzenie, np. długotrwałe instalacja innej aplikacji, która wymaganego wyłącznego dostępu menedżera.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny błędów rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki są rozszerzenia CustomScript, Chef i DSC, które mają 90 minut). Jeśli wdrożenie przekroczy ten czas, jest oznaczana do przekroczenia limitu czasu. Przyczyną tego może być z powodu zasobów maszyn wirtualnych z innych maszyn wirtualnych konfiguracji/uruchamiania zadania zużywające wysokiej ilości zasobów, podczas gdy rozszerzenie jest próby aprowizacji.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia mają zależności jednostek SKU maszyny Wirtualnej, taką jak obrazy HPC. Rozszerzenia mogą wymagać pewnych sieci wymagania dotyczące dostępu, takich jak komunikacja do usługi Azure Storage lub usług publicznych. Inne przykłady może być dostęp do repozytoriów pakietu, wyczerpuje się miejsce na dysku lub ograniczenia zabezpieczeń.

3. Dostęp do Menedżera wyłączne pakietu. W niektórych przypadkach mogą wystąpić długotrwałe konfiguracji maszyny Wirtualnej, a instalacja rozszerzenia usługi powodujące konflikt, których potrzebują wyłączny dostęp do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po wykonaniu rozszerzenia maszyny Wirtualnej względem maszyny Wirtualnej za pomocą [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) zostać zwrócony stan rozszerzenia w następujący sposób:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Dane wyjściowe będą podobne do następujących przykładowych danych wyjściowych:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Stan wykonywania rozszerzenia można także znaleźć w witrynie Azure portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę Wirtualną, wybrać **rozszerzenia**, następnie wybierz żądany rozszerzenia.

### <a name="rerun-a-vm-extension"></a>Uruchom ponownie rozszerzenie maszyny Wirtualnej

Można wykluczyć sytuacji, w których trzeba można ponownie uruchomić rozszerzenia maszyny Wirtualnej. Możesz ponownie uruchomić rozszerzenia, usuwając go, a następnie ponowne uruchomienie rozszerzenia za pomocą metody wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, należy użyć [Usuń rozszerzenie maszyny wirtualnej az](/cli/azure/vm/extension#az-vm-extension-delete) w następujący sposób:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Możesz również usunąć rozszerzenie w witrynie Azure portal w następujący sposób:

1. Wybierz maszynę Wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extension-reference"></a>Częsta rozszerzenia maszyny Wirtualnej

| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie niestandardowego skryptu dla systemu Linux |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Rozszerzenie niestandardowego skryptu dla systemu Linux](custom-script-linux.md) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskać dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie diagnostyki platformy Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie dostępu do maszyny Wirtualnej platformy Azure |Zarządzanie użytkownikami i poświadczeń |[Rozszerzenie dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure](overview.md).
