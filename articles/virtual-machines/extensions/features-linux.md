---
title: Azure rozszerzeń maszyny Wirtualnej i funkcje w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co ich Podaj puli zasobów i zwiększyć.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 760f832bc12bccbf1cce77db25bf60413ad9a36b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia maszyn wirtualnych i funkcji w systemie Linux

Rozszerzenia maszyny wirtualnej platformy Azure (VM) są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure. Na przykład, jeśli maszyna wirtualna wymaga ochrony oprogramowania antywirusowego, instalacja oprogramowania lub uruchamiania skryptu wewnątrz niej można użyć rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej platformy Azure można uruchomić z wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i portalu Azure. Rozszerzenia mogą być powiązane z nowego wdrożenia maszyny Wirtualnej lub uruchomienia dowolnego istniejącego systemu.

Ten artykuł zawiera omówienie rozszerzeń maszyny Wirtualnej, wymagań wstępnych dotyczących używania rozszerzenia maszyny Wirtualnej platformy Azure i instrukcje dotyczące wykrywania, zarządzanie i usuwanie rozszerzenia maszyny Wirtualnej. Ten artykuł zawiera informacje uogólniony wielu rozszerzeń maszyny Wirtualnej nie są dostępne, każde z nich potencjalnie unikatowych konfiguracji. Szczegółów dotyczących poszczególnych rozszerzeń można znaleźć w każdy dokument specyficzne dla poszczególnych rozszerzenia.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępnych jest kilka różnych rozszerzeń maszyny Wirtualnej platformy Azure, każde z nich określony przypadek użycia. Oto niektóre przykłady:

- Dotyczą konfiguracji środowiska PowerShell żądany stan maszyny Wirtualnej z rozszerzenia DSC dla systemu Linux. Aby uzyskać więcej informacji, zobacz [Azure żądany stan konfiguracji rozszerzenia](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurowanie monitorowania maszynę Wirtualną za pomocą rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [jak monitorować Maszynę wirtualną systemu Linux](../linux/tutorial-monitoring.md).
- Skonfiguruj Monitorowanie infrastruktury platformy Azure z rozszerzeniem Chef lub Datadog. Aby uzyskać więcej informacji, zobacz [Chef docs](https://docs.chef.io/azure_portal.html) lub [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oprócz rozszerzenia procesu rozszerzenia niestandardowego skryptu jest dostępna dla maszyn wirtualnych w systemach Windows i Linux. Rozszerzenia niestandardowego skryptu dla systemu Linux umożliwia dowolny skrypt Bash do uruchamiania na maszynie Wirtualnej. Niestandardowe skrypty są przydatne w przypadku projektowania wdrożeń platformy Azure, które wymagają konfiguracji oprócz zapewniają jakie natywnego narzędzia Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zapewnić obsługę rozszerzeń na maszynie Wirtualnej, należy z zainstalowanym agentem systemu Linux platformy Azure. Niektóre poszczególnych rozszerzeń są spełnione wymagania wstępne, takich jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny Wirtualnej platformy Azure zarządza interakcje między maszyny Wirtualnej platformy Azure i kontroler sieci szkieletowej Azure. Agent maszyny Wirtualnej jest odpowiedzialny za dużo aspekty funkcjonalne wdrażania i zarządzania nimi maszynach wirtualnych platformy Azure, łącznie z rozszerzeniami maszyny Wirtualnej. Agent maszyny Wirtualnej platformy Azure jest preinstalowany na portalu Azure Marketplace obrazów i można zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny Wirtualnej platformy Azure dla systemu Linux jest określany jako agenta systemu Linux.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Agent obsługiwane wersje

Aby zapewnić najlepsze możliwe środowisko, istnieje co najmniej wersji agenta. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemy operacyjne

Agent systemu Linux jest uruchamiany na wiele systemów operacyjnych, jednak w ramach rozszerzenia ma limit dla systemów operacyjnych tego rozszerzenia. Aby uzyskać więcej informacji, zobacz [w tym artykule] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i może emitować *51 kodu błędu, "Nieobsługiwany system operacyjny"*. Sprawdź w dokumentacji poszczególnych rozszerzenia obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzenia usługi Azure Storage, a rozszerzenie przekazywania stanie są wysyłane do usługi Azure Storage. Jeśli używasz [obsługiwane](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie trzeba zezwolić na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, jak można użyć agenta do przekierowania komunikacji z kontrolerem sieci szkieletowej Azure komunikacji agenta. Jeśli używany jest nie obsługiwaną wersję agenta, należy zezwolić wychodzący dostęp do magazynu Azure w tym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Zablokowanie dostępu do *168.63.129.1* za pomocą zapory gościa, następnie rozszerzeń kończą się niepowodzeniem niezależnie od powyższego.

Aby pobrać pakiety rozszerzeń i raportowania stanu można tylko agentów. Na przykład, jeśli instalacja rozszerzenia musi pobrać skrypt z serwisu GitHub (niestandardowego skryptu) lub wymagany jest dostęp do usługi Azure Storage (kopia zapasowa Azure), następnie dodatkowe zapory i sieci zabezpieczeń grupy porty muszą być otwarte. Różne rozszerzenia mają różne wymagania, ponieważ są one aplikacji we własnym imieniu. Dla rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG Azure dla [magazynu](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Przekierowywanie żądań dotyczących ruchu agenta, Agent systemu Linux ma Obsługa serwerów proxy. Jednak ta obsługa serwerów proxy, nie ma zastosowania rozszerzenia. Należy skonfigurować każde rozszerzenie poszczególnych do pracy z serwera proxy.

## <a name="discover-vm-extensions"></a>Odnajdywanie rozszerzeń maszyny Wirtualnej

Wiele różnych rozszerzeń maszyny Wirtualnej są dostępne do użycia z maszynami wirtualnymi Azure. Aby wyświetlić pełną listę, należy użyć [listy obrazów rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension/image#az-vm-extension-image-list). Poniższy przykład zawiera listę wszystkich dostępnych rozszerzeń w *westus* lokalizacji:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Uruchom rozszerzeń maszyny Wirtualnej

Rozszerzenia maszyny Wirtualnej platformy Azure Uruchom na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub Przywróć łączność w przypadku wdrożonej maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej dołączany także wdrażanie szablonów usługi Azure Resource Manager. Przy użyciu rozszerzeń z szablonami usługi Resource Manager, można wdrożyć i konfigurować bez interwencji po wdrożeniu maszynach wirtualnych platformy Azure.

Następujących metod można uruchomić rozszerzenia dla istniejącej maszyny Wirtualnej.

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Rozszerzenia maszyny Wirtualnej platformy Azure mogą być uruchamiane na istniejącej maszyny Wirtualnej z [zestaw rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension#az-vm-extension-set) polecenia. W poniższym przykładzie uruchamiane rozszerzenia niestandardowego skryptu przed Maszynami wirtualnymi o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Po rozszerzeniu działa poprawnie, wynik jest podobny do poniższego przykładu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyny Wirtualnej może odnosić się do istniejącej maszyny Wirtualnej za pośrednictwem portalu Azure. Wybierz maszynę Wirtualną w portalu, wybierz **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz rozszerzenia, z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami w kreatorze.

Na poniższej ilustracji przedstawiono instalacja rozszerzenia Linux niestandardowego skryptu z portalu Azure:

![Zainstaluj rozszerzenie skryptu niestandardowego](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny Wirtualnej można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu można tworzyć wdrożeń platformy Azure w pełni skonfigurowany. Na przykład następujący kod JSON jest pobierana z szablonem usługi Resource Manager, który wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych Azure SQL, a następnie zainstaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenia maszyny Wirtualnej zapewnia obsługę instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz pełny [szablonu usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [szablonów Authoring Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej, może być konieczne jest stosowanie poufne informacje, takie jak poświadczeń, nazwy konta magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyny Wirtualnej obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowego maszyny Wirtualnej. Każde rozszerzenie ma schematu określonej konfiguracji chronionych, a każdy szczegółowo opisano w dokumentacji konkretnego rozszerzenia.

W poniższym przykładzie przedstawiono wystąpienia rozszerzenia niestandardowego skryptu dla systemu Linux. Wykonanie polecenia zawiera zestaw poświadczeń. Wykonanie polecenia nie są szyfrowane w tym przykładzie:

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

Przenoszenie **polecenie do wykonania** właściwości **chronione** konfiguracja zabezpiecza ciąg wykonywania, jak pokazano w poniższym przykładzie:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak agentów i rozszerzenia aktualizowany?

Agenci i rozszerzenia współużytkować ten sam mechanizm aktualizacji. Niektóre aktualizacje nie wymagają reguł zapory dodatkowe.

Po udostępnieniu aktualizacji jest tylko zainstalowany na Maszynie wirtualnej po zmianie rozszerzenia i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Klucze tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieci

Wydawcy aktualizacje były dostępne dla regionów w różnym czasie, co może mieć maszyn wirtualnych w różnych regionach w różnych wersjach.

#### <a name="agent-updates"></a>Agent aktualizacji

Agent maszyny Wirtualnej systemu Linux zawiera *kodu inicjowania obsługi administracyjnej agenta* i *rozszerzenia obsługi kodu* w jednym pakiecie, który nie może być oddzielony. Możesz wyłączyć *agenta inicjowania obsługi administracyjnej* Jeśli chcesz udostępnić na platformie Azure przy użyciu inicjowania chmury. Aby to zrobić, zobacz [przy użyciu chmury init](../linux/using-cloud-init.md).

Obsługiwane wersje agentów za pomocą funkcji Aktualizacje automatyczne. Jest tylko kod, który może być aktualizowana *rozszerzenia obsługi kodu*, kod inicjowania obsługi administracyjnej. *Kodu inicjowania obsługi administracyjnej agenta* jednokrotnego uruchamiania kodu.

*Rozszerzenia obsługi kodu* odpowiada do komunikowania się z sieci szkieletowej Azure i obsługi operacji rozszerzenia maszyny Wirtualnej, takie jak instalacji raportowania stanu poszczególnych rozszerzeń i aktualizowanie ich usunięcie. Aktualizacji zawiera poprawki zabezpieczeń, poprawki i udoskonalenia *rozszerzenia obsługi kodu*.

Agent jest zainstalowany, zostanie utworzona demon nadrzędnej. Ten element nadrzędny następnie spowoduje utworzenie procesu podrzędnego, który służy do obsługi rozszerzenia. Jeśli aktualizacja jest dostępna dla agenta, zostanie pobrany, nadrzędny zatrzymuje procesu podrzędnego, uaktualnia go, a następnie ponownie go uruchamia. Powinien być wystąpił problem z aktualizacją, proces nadrzędny przywraca poprzedniej wersji podrzędnych.

Proces nadrzędny nie może być automatycznie aktualizowane. Element nadrzędny może być aktualizowana tylko przez distro aktualizacja pakietu.

Aby sprawdzić wersję, jakie są uruchomione, sprawdź `waagent` w następujący sposób:

```bash
waagent --version
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

W poprzednim przykładowe dane wyjściowe, jest nadrzędnym lub "pakiet wdrożony w wersji" *WALinuxAgent 2.2.17*

Agent stanu celem jest automatycznie zaktualizowanej wersji.

Zdecydowanie zaleca się zawsze mieć automatyczne aktualizacje dla agenta, [AutoUpdate.Enabled=y](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent). Nie oznacza to włączone, będzie musiał ręcznie zaktualizować agenta o i nie można uzyskać poprawek usterek i zabezpieczeń.

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Po udostępnieniu aktualizacji rozszerzenia agenta systemu Linux pobiera i uaktualnia rozszerzenia. Aktualizacje automatyczne rozszerzenia są albo *pomocnicza* lub *poprawkę*. Można włączyć lub zrezygnować z rozszerzeniami *pomocnicza* aktualizacji podczas obsługi administracyjnej rozszerzenia. Poniższy przykład pokazuje, jak automatycznie uaktualnić wersje pomocnicze w szablonie usługi Resource Manager z *autoUpgradeMinorVersion ": ma wartość true,"*:

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

Aby uzyskać najnowsze poprawki wersji pomocniczej, zdecydowanie zaleca się zawsze wybrać automatyczne aktualizacje w ramach wdrożeń rozszerzenia. Nie można korzystać z aktualizacji będących poprawkami, zawierających poprawki zabezpieczeń lub klucza.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować rozszerzenia aktualizacji

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identyfikowanie, jeśli rozszerzenie jest ustawiona z autoUpgradeMinorVersion na maszynie Wirtualnej

Można wyświetlić z modelu maszyny Wirtualnej, jeśli rozszerzenie zostało udostępnione "autoUpgradeMinorVersion". Aby sprawdzić, użyj [az maszyny wirtualnej pokazu](/cli/azure/vm#az-vm-show) i podaj nazwę grupy zasobów i maszyn wirtualnych w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Następujące przykładowe dane wyjściowe wskazuje, że *autoUpgradeMinorVersion* ustawiono *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie problemu autoUpgradeMinorVersion

Aby Przejrzyj agenta, zobacz wystąpienia aktualizacji do rozszerzenia dzienniki na maszynie Wirtualnej w */var/log/waagent.log*.

W poniższym przykładzie maszyna wirtualna ma *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* zainstalowane. Poprawka był dostępny do *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

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

Aby wykonać jego zadań podrzędnych, agent musi być uruchamiane jako *głównego*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeń maszyny Wirtualnej

Każde rozszerzenie maszyny Wirtualnej może być rozwiązywania problemów z określonego rozszerzenia. Na przykład użycie skryptu niestandardowego rozszerzenia skryptu wykonywania szczegóły można znaleźć lokalnie na maszynie Wirtualnej, gdy rozszerzenie zostało uruchomione. Wszystkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Poniższe kroki dotyczą wszystkich rozszerzeń maszyny Wirtualnej.

1. Aby Sprawdź dziennik agenta systemu Linux, spójrz na działanie podczas rozszerzenia został aprowizowany w */var/log/waagent.log*

2. Szczegółowe informacje w dzienniku rzeczywiste rozszerzenia   */var/logowania/azure /<extensionName>*

3. W dokumentacji konkretnego rozszerzenia Rozwiązywanie problemów z sekcji kodów błędów, znane problemy itp.

3. Sprawdź dzienniki systemu. Sprawdź, czy inne operacje, które mogą mieć zakłócać rozszerzenia, takie jak długo działające instalacja innej aplikacji, co wymagało Menedżera wyłącznego dostępu.

### <a name="common-reasons-for-extension-failures"></a>Najczęstsze przyczyny niepowodzeń rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki są rozszerzenia CustomScript, Chef i DSC zainstalowanego w ciągu 90 minut). Jeśli wdrożenie przekroczy ten czas, jest oznaczony jako przekroczenie limitu czasu. Przyczyną tego może być spowodowane zasobów maszyn wirtualnych, inne maszyny Wirtualnej konfiguracji/uruchamiania zadania zużywające wysokiej ilości zasobów podczas, gdy rozszerzenie jest w trakcie udostępniania.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia są zależne na jednostki SKU maszyny Wirtualnej, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych sieci wymagania dostępu, na przykład komunikowania się z magazynu Azure lub usługi publiczne. Inne przykłady może być dostęp do repozytoriów pakietu kończy się wolne miejsce na dysku lub ograniczeń zabezpieczeń.

3. Wyłącznego dostępu menedżera. W niektórych przypadkach może się pojawić długotrwała konfiguracji maszyny Wirtualnej i instalacja rozszerzenia powodujące konflikt, których potrzebują wyłącznego dostępu do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny Wirtualnej dla maszyny Wirtualnej, użyj [az get widok wystąpienia maszyny wirtualnej —](/cli/azure/vm#az-vm-get-instance-view) ma zostać zwrócony stan rozszerzenia w następujący sposób:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Wynik jest podobny do następującego przykładowe dane wyjściowe:

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

Stan wykonania rozszerzenia można znaleźć w portalu Azure. Aby wyświetlić stan rozszerzenia, wybierz maszynę Wirtualną, wybrać **rozszerzenia**, następnie wybierz żądany rozszerzenia.

### <a name="rerun-a-vm-extension"></a>Uruchom ponownie rozszerzenia maszyny Wirtualnej

Może to być przypadków, w których trzeba można ponownie uruchomić rozszerzenia maszyny Wirtualnej. Możesz ponownie uruchomić rozszerzenia, usuwając go i następnie ponowne uruchomienie rozszerzenia metodą wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, należy użyć [Usuwanie rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension#az-vm-extension-delete) w następujący sposób:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Można również usunąć rozszerzenie w portalu Azure w następujący sposób:

1. Wybierz maszynę Wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extension-reference"></a>Typowe odwołania do rozszerzenia maszyny Wirtualnej

| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Niestandardowe rozszerzenie skryptu dla systemu Linux |Uruchom skrypty przed maszyny wirtualnej platformy Azure |[Niestandardowe rozszerzenie skryptu dla systemu Linux](custom-script-linux.md) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskać dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyka Azure |Zarządzanie Diagnostyka Azure |[Rozszerzenie Diagnostyka Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenia dostępu do maszyny Wirtualnej platformy Azure |Zarządzaj użytkownikami i poświadczenia |[Rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyny Wirtualnej, zobacz [maszyny wirtualnej platformy Azure — omówienie rozszerzeń i funkcji](overview.md).
