---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure dla systemu Linux
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według tego, co zapewniają lub ulepszają.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250519"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i witryny Azure Portal. Rozszerzenia można połączyć w pakiecie z nowym wdrożeniem nowej maszyny wirtualnej lub uruchamiać w dowolnym istniejącym systemie.

Ten artykuł zawiera omówienie rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące używania rozszerzeń maszyn wirtualnych platformy Azure oraz wskazówki dotyczące wykrywania rozszerzeń maszyn wirtualnych, zarządzania nimi i ich usuwania. Ten artykuł zawiera uogólnione informacje, ponieważ dostępnych jest wiele rozszerzeń maszyn wirtualnych, z których każdy ma potencjalnie unikatową konfigurację. Szczegóły specyficzne dla rozszerzenia można znaleźć w każdym dokumencie specyficznym dla poszczególnych rozszerzeń.

## <a name="use-cases-and-samples"></a>Przypadki użycia i próbki

Dostępnych jest kilka różnych rozszerzeń maszyn wirtualnych platformy Azure, z których każdy ma określony przypadek użycia. Oto niektóre przykłady:

- Zastosuj konfiguracje żądanego stanu programu PowerShell do maszyny Wirtualnej z rozszerzeniem DSC dla systemu Linux. Aby uzyskać więcej informacji, zobacz [rozszerzenie konfiguracji żądanego stanu platformy Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Skonfiguruj monitorowanie maszyny Wirtualnej za pomocą rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Jak monitorować maszynę wirtualną z systemem Linux](../linux/tutorial-monitoring.md).
- Skonfiguruj monitorowanie infrastruktury platformy Azure za pomocą rozszerzenia Chef lub Datadog. Aby uzyskać więcej informacji, zobacz [dokumenty szefa kuchni](https://docs.chef.io/azure_portal.html) lub blog [Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oprócz rozszerzeń specyficznych dla procesu rozszerzenie skryptu niestandardowego jest dostępne zarówno dla maszyn wirtualnych systemu Windows, jak i systemu Linux. Rozszerzenie skryptu niestandardowego dla systemu Linux umożliwia uruchamianie dowolnego skryptu Bash na maszynie wirtualnej. Skrypty niestandardowe są przydatne do projektowania wdrożeń platformy Azure, które wymagają konfiguracji wykraczającej poza to, co może zapewnić natywne narzędzia platformy Azure. Aby uzyskać więcej informacji, zobacz [Rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsłużyć rozszerzenie na maszynie Wirtualnej, potrzebujesz zainstalowanego agenta systemu Azure Linux. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takie jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny wirtualnej platformy Azure zarządza interakcjami między maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej jest odpowiedzialny za wiele funkcjonalnych aspektów wdrażania maszyn wirtualnych platformy Azure i zarządzania nimi, w tym uruchamianie rozszerzeń maszyn wirtualnych. Agent maszyny wirtualnej platformy Azure jest preinstalowany w obrazach usługi Azure Marketplace i można go zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny Wirtualnej platformy Azure dla systemu Linux jest znany jako agent systemu Linux.

Aby uzyskać informacje na temat obsługiwanych systemów operacyjnych i instrukcji instalacji, zobacz [agent maszyny wirtualnej platformy Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Obsługiwane wersje agentów

Aby zapewnić najlepsze możliwe wrażenia, istnieją minimalne wersje agenta. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane oses

Agent systemu Linux działa na wielu systemach operacyjnych, jednak struktura rozszerzeń ma limit dla systemów operacyjnych, które rozszerzenia. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i mogą emitować *kod błędu 51, "Nieobsługicony system operacyjny".* Sprawdź indywidualną dokumentację rozszerzenia pod kątem możliwości obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzenia usługi Azure Storage, a przekazywanie stanu rozszerzenia jest księgowane w usłudze Azure Storage. Jeśli używasz [obsługiwanej](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie trzeba zezwalać na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, podobnie jak można użyć agenta, aby przekierować komunikację do kontrolera sieci szkieletowej platformy Azure dla komunikacji agenta. Jeśli korzystasz z nieobjętej do sieci wersji agenta, musisz zezwolić na dostęp wychodzący do magazynu platformy Azure w tym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Jeśli zablokowano dostęp do *168.63.129.16* przy użyciu zapory gościa, rozszerzenia nie powiedzie się niezależnie od powyższych.

Agenci mogą być używane tylko do pobierania pakietów rozszerzeń i stanu raportowania. Jeśli na przykład instalacja rozszerzenia musi pobrać skrypt z usługi GitHub (skrypt niestandardowy) lub będzie wymagać dostępu do usługi Azure Storage (Azure Backup), należy otworzyć dodatkowe porty zapory/sieciowej grupy zabezpieczeń. Różne rozszerzenia mają różne wymagania, ponieważ są aplikacje w sobie. W przypadku rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG platformy Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Aby przekierować żądania ruchu agenta, agent systemu Linux ma obsługę serwera proxy. Jednak ta obsługa serwera proxy nie stosuje rozszerzeń. Należy skonfigurować każde indywidualne rozszerzenie do pracy z serwerem proxy.

## <a name="discover-vm-extensions"></a>Odnajduj rozszerzenia maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, użyj [listy obrazów rozszerzenia AZ VM](/cli/azure/vm/extension/image#az-vm-extension-image-list). Poniższy przykład zawiera listę wszystkich dostępnych rozszerzeń w lokalizacji *westus:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzeń maszyn wirtualnych

Rozszerzenia maszyn wirtualnych platformy Azure są uruchamiane na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany konfiguracji lub odzyskać łączność na już wdrożonej maszynie wirtualnej. Rozszerzenia maszyn wirtualnych można również łączyć z wdrożeniami szablonów usługi Azure Resource Manager. Za pomocą rozszerzeń z szablonami Usługi Resource Manager, maszyny wirtualne platformy Azure można wdrożyć i skonfigurować bez interwencji po wdrożeniu.

Następujące metody mogą służyć do uruchamiania rozszerzenia względem istniejącej maszyny Wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Rozszerzenia maszyn wirtualnych platformy Azure można uruchomić na istniejącej maszynie wirtualnej za pomocą polecenia [az vm extension set.](/cli/azure/vm/extension#az-vm-extension-set) W poniższym przykładzie uruchomi się rozszerzenie skryptu niestandardowego dla maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*. Zastąp przykładową nazwę grupy zasobów, nazwę maszyny\/Wirtualnej i skrypt do uruchomienia (https: /raw.githubusercontent.com/me/project/hello.sh) własnymi informacjami. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Gdy rozszerzenie działa poprawnie, dane wyjściowe są podobne do następującego przykładu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal Azure

Rozszerzenia maszyn wirtualnych można zastosować do istniejącej maszyny Wirtualnej za pośrednictwem witryny Azure portal. Wybierz maszynę wirtualną w portalu, wybierz pozycję **Rozszerzenia,** a następnie wybierz pozycję **Dodaj**. Wybierz żądane rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami kreatora.

Na poniższej ilustracji przedstawiono instalację rozszerzenia skryptu niestandardowego systemu Linux z witryny Azure portal:

![Instalowanie niestandardowego rozszerzenia skryptu](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyn wirtualnych można dodać do szablonu usługi Azure Resource Manager i wykonać z wdrożeniem szablonu. Podczas wdrażania rozszerzenia z szablonem można utworzyć w pełni skonfigurowane wdrożenia platformy Azure. Na przykład następujący JSON jest pobierana z szablonu Menedżera zasobów, który wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych SQL platformy Azure, a następnie instaluje aplikację .NET Core na każdej maszynie wirtualnej. Rozszerzenie maszyny Wirtualnej zajmuje się instalacją oprogramowania.

Aby uzyskać więcej informacji, zobacz pełny [szablon Menedżera zasobów](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Aby uzyskać więcej informacji na temat tworzenia szablonów Usługi Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Bezpieczne dane rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej może być konieczne uwzględnienie poufnych informacji, takich jak poświadczenia, nazwy kont magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych zawiera chroniona konfiguracja, która szyfruje dane i tylko odszyfrowuje je wewnątrz docelowej maszyny Wirtualnej. Każde rozszerzenie ma określony schemat konfiguracji chronione, a każdy jest szczegółowo opisany w dokumentacji specyficznej dla rozszerzenia.

W poniższym przykładzie pokazano wystąpienie rozszerzenia skryptu niestandardowego dla systemu Linux. Polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie polecenie do wykonania nie jest szyfrowane:

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

Przeniesienie **polecenia do wykonania** właściwości do **chronionej** konfiguracji zabezpiecza ciąg wykonania, jak pokazano w poniższym przykładzie:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>W jaki sposób agenci i rozszerzenia są aktualizowane?

Agenci i rozszerzenia współużytkuje ten sam mechanizm aktualizacji. Niektóre aktualizacje nie wymagają dodatkowych reguł zapory.

Gdy aktualizacja jest dostępna, jest instalowana na maszynie Wirtualnej tylko wtedy, gdy nastąpi zmiana rozszerzeń i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Tajemnice systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieciowy

Wydawcy udostępniają aktualizacje regionom o różnych porach, dzięki czemu można mieć maszyny wirtualne w różnych regionach w różnych wersjach.

#### <a name="agent-updates"></a>Aktualizacje agentów

Agent maszyny wirtualnej systemu Linux zawiera *kod agenta aprowizującego* i *kod obsługi rozszerzeń* w jednym pakiecie, którego nie można rozdzielić. Agenta *inicjowania obsługi administracyjnej* można wyłączyć, gdy chcesz aprowizować na platformie Azure przy użyciu funkcji cloud-init. Aby to zrobić, zobacz [za pomocą cloud-init](../linux/using-cloud-init.md).

Obsługiwane wersje agentów mogą używać aktualizacji automatycznych. Jedynym kodem, który można zaktualizować, jest *kod obsługi rozszerzeń,* a nie kod inicjowania obsługi administracyjnej. *Kod agenta inicjowania obsługi administracyjnej* jest kodem uruchamiającym.

*Kod obsługi rozszerzeń* jest odpowiedzialny za komunikowanie się z siecią szkieletową platformy Azure i obsługę operacji rozszerzeń maszyn wirtualnych, takich jak instalacje, stan raportowania, aktualizowanie poszczególnych rozszerzeń i ich usuwanie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *kodu obsługi rozszerzeń*.

Po zainstalowaniu agenta tworzony jest demon nadrzędny. Ten element nadrzędny następnie spawns proces podrzędny, który jest używany do obsługi rozszerzeń. Jeśli aktualizacja jest dostępna dla agenta, jest pobierana, rodzic zatrzymuje proces podrzędny, uaktualnia go, a następnie uruchamia ponownie. Jeśli wystąpi problem z aktualizacją, proces nadrzędny wycofuje się do poprzedniej wersji podrzędnej.

Nie można automatycznie zaktualizować procesu nadrzędnego. Element nadrzędny można zaktualizować tylko przez aktualizację pakietu dystrybucji.

Aby sprawdzić, jaka wersja `waagent` jest uruchomiona, sprawdź w następujący sposób:

```bash
waagent --version
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

W poprzednim przykładzie danych wyjściowych nadrzędnym lub "wersja wdrożona pakiet" jest *WALinuxAgent-2.2.17*

"Agent stanu celu" jest wersją automatycznej aktualizacji.

Zdecydowanie zaleca się, aby zawsze mieć automatyczną aktualizację dla agenta, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Brak tej włączonej oznacza, że musisz ręcznie aktualizować agenta, a nie otrzymywać poprawek błędów i zabezpieczeń.

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Gdy aktualizacja rozszerzenia jest dostępna, agent systemu Linux pobiera i uaktualnia rozszerzenie. Automatyczne aktualizacje rozszerzeń są *niewielkie* lub *poprawki*. Możesz wyrazić zgodę na rozszerzenia *Drobne* aktualizacje lub zrezygnować z nich podczas udostępniania rozszerzenia. W poniższym przykładzie pokazano, jak automatycznie uaktualnić wersje pomocnicze w szablonie Menedżera zasobów za pomocą *autoUpgradeMinorVersion": true,'*:

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

Aby uzyskać najnowsze drobne poprawki błędów w wersji, zdecydowanie zaleca się, aby zawsze wybierać automatyczną aktualizację w wdrożeniach rozszerzeń. Nie można zrezygnować z aktualizacji poprawek, które zawierają zabezpieczenia lub poprawki błędów kluczy.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować aktualizacje rozszerzenia

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Określenie, czy rozszerzenie jest ustawione z autoUpgradeMinorVersion na maszynie Wirtualnej

Z modelu maszyny Wirtualnej można zobaczyć, czy rozszerzenie zostało zainicjowane za pomocą funkcji "autoUpgradeMinorVersion". Aby to sprawdzić, użyj [programu AZ VM Show](/cli/azure/vm#az-vm-show) i podaj grupę zasobów i nazwę maszyny Wirtualnej w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Poniższe przykładowe dane wyjściowe pokazują, że *autoUpgradeMinorVersion* jest ustawiona na *true:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie, kiedy wystąpiła funkcja autoUpgradeMinorVersion

Aby zobaczyć, kiedy nastąpiła aktualizacja rozszerzenia, przejrzyj dzienniki agenta na maszynie Wirtualnej pod adresem */var/log/waagent.log*.

W poniższym przykładzie maszyna wirtualna miała zainstalowany *system Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025.* Poprawka była dostępna dla *firmy Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

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

Aby wykonać swoje zadania, agent musi działać jako *główny*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych

Każde rozszerzenie maszyny Wirtualnej może mieć kroki rozwiązywania problemów specyficzne dla rozszerzenia. Na przykład podczas korzystania z rozszerzenia skryptu niestandardowego szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie Wirtualnej, gdzie zostało uruchomione rozszerzenie. Wszelkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficznej dla rozszerzenia.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta systemu Linux, przyjrzyj się aktywności, gdy rozszerzenie było aprowizowane w */var/log/waagent.log*

2. Sprawdź rzeczywiste dzienniki rozszerzeń, aby uzyskać więcej informacji w */var/log/azure/\<extensionName>*

3. Sprawdź sekcje rozwiązywania problemów z dokumentacją dotyczącą rozszerzenia, aby uzyskać kody błędów, znane problemy itp.

3. Spójrz na dzienniki systemowe. Sprawdź, czy inne operacje, które mogły zakłócać rozszerzenie, takie jak długotrwała instalacja innej aplikacji, która wymagała wyłącznego dostępu menedżera pakietów.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny awarii rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki to rozszerzenia CustomScript, Chef i DSC, które mają 90 minut). Jeśli wdrożenie przekracza ten czas, jest oznaczony jako limit czasu. Przyczyną tego może być ze względu na maszyny wirtualne o niskiej ilości zasobów, inne konfiguracje maszyn wirtualnych/uruchamianie zadań zużywających duże ilości zasobów, podczas gdy rozszerzenie próbuje aprowizować.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia mają zależności od jednostek SKU maszyn wirtualnych, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych wymagań dotyczących dostępu do sieci, takich jak komunikowanie się z usługą Azure Storage lub usług publicznych. Inne przykłady mogą to być dostęp do repozytoriów pakietów, brak miejsca na dysku lub ograniczenia zabezpieczeń.

3. Ekskluzywny dostęp do menedżera pakietów. W niektórych przypadkach może wystąpić długotrwała konfiguracja maszyny Wirtualnej i instalacji rozszerzenia powodujące konflikt, gdzie oba one potrzebują wyłącznego dostępu do menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny Wirtualnej na maszynie Wirtualnej użyj [az vm get-instance-view,](/cli/azure/vm#az-vm-get-instance-view) aby zwrócić stan rozszerzenia w następujący sposób:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Dane wyjściowe są podobne do następujących przykładowych danych wyjściowych:

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

Stan wykonania rozszerzenia można również znaleźć w witrynie Azure portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz **pozycję Rozszerzenia**, a następnie wybierz żądane rozszerzenie.

### <a name="rerun-a-vm-extension"></a>Ponowne przygorzenie rozszerzenia maszyny Wirtualnej

Mogą wystąpić przypadki, w których rozszerzenie maszyny Wirtualnej musi zostać ponownie uruchom. Rozszerzenie można ponownie uruchomić, usuwając je, a następnie ponownie uruchomić rozszerzenie przy pomocą wybranej metody wykonywania. Aby usunąć rozszerzenie, użyj [rozszerzenia az vm delete](/cli/azure/vm/extension#az-vm-extension-delete) w następujący sposób:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Można również usunąć rozszerzenie w witrynie Azure portal w następujący sposób:

1. Wybierz maszynę wirtualną.
2. Wybierz **pozycję Rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **pozycję Odinstaluj**.

## <a name="common-vm-extension-reference"></a>Wspólne odwołanie do rozszerzenia maszyny Wirtualnej

| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie skryptu niestandardowego dla systemu Linux |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Rozszerzenie skryptu niestandardowego dla systemu Linux](custom-script-linux.md) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskaj dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie diagnostyką platformy Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie programu Azure VM Access |Zarządzanie użytkownikami i poświadczeniami |[Rozszerzenie VM Access dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure.](overview.md)
