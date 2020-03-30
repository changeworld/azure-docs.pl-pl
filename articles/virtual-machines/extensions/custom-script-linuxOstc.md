---
title: Uruchamianie skryptów niestandardowych na maszynach wirtualnych z systemem Linux na platformie Azure
description: Automatyzuj zadania konfiguracji maszyny Wirtualnej systemu Linux przy użyciu niestandardowego rozszerzenia skryptu w wersji 1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535412"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Użyj rozszerzenia 1 niestandardowego skryptu platformy Azure z maszynami wirtualnymi systemu Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Rozszerzenie niestandardowego skryptu w wersji 1 pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innego zadania konfiguracji/zarządzania. Skrypty można pobrać z usługi Azure Storage lub innej dostępnej lokalizacji internetowej lub udostępnić je do środowiska wykonawczego rozszerzenia.

Rozszerzenie skryptów niestandardowych integruje się z szablonami usługi Azure Resource Manager. Można go również uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure portal lub interfejsu API REST maszyn wirtualnych platformy Azure.

W tym artykule opisano, jak używać rozszerzenia niestandardowego skryptu z interfejsu wiersza polecenia platformy Azure i jak uruchomić rozszerzenie przy użyciu szablonu usługi Azure Resource Manager. W tym artykule przedstawiono również kroki rozwiązywania problemów dla systemów Linux.

Istnieją dwa rozszerzenia skryptów niestandardowych systemu Linux:

* Wersja 1 — Microsoft.OSTCExtensions.CustomScriptForLinux

* Wersja 2 — Microsoft.Azure.Extensions.CustomScript

Zamiast tego przełącz nowe i istniejące wdrożenia, aby użyć nowej wersji ([Microsoft.Azure.Extensions.CustomScript).](custom-script-linux.md) Nowa wersja jest przeznaczona do użycia bezpośrednio w miejsce starszej. Dlatego migracja jest tak łatwa jak zmiana nazwy i wersji — nie trzeba zmieniać konfiguracji rozszerzenia.

### <a name="operating-system"></a>System operacyjny

Obsługiwane dystrybucje Linuksa:

* CentOS 6,5 lub wyższy
* Debian 8 lub wyższy
  * Debian 8.7 nie wysyła z Python2 w najnowszych obrazach, co łamie CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 lub wyższe
* Oracle Linux 6.4 lub nowszy
* SUSE Linux Enterprise Server 11 zwamionu z dodatku SP3 lub nowszego
* Ubuntu 12.04 i wyższe

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenia można użyć poświadczeń magazynu obiektów Blob platformy Azure, aby uzyskać dostęp do magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacja skryptu może być dowolna, o ile maszyna wirtualna może kierować do tego punktu końcowego, takiego jak GitHub, wewnętrzny serwer plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli chcesz pobrać skrypt zewnętrznie, takich jak GitHub lub Usługi Azure Storage, a następnie dodatkowe porty zapory/sieciowej grupy zabezpieczeń muszą zostać otwarte. Na przykład, jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi Azure NSG dla [magazynu](../../virtual-network/security-overview.md#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, nadal może być konieczne otwarcie dodatkowych portów zapory/sieciowej grupy zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty nie wymagają danych wejściowych użytkownika podczas ich uruchamiania.
* Jest 90 minut dozwolone dla skryptu do uruchomienia, nic dłużej spowoduje nieuwzwolenie rozszerzenia.
* Nie umieszczaj ponownie uruchamianych ponownie wewnątrz skryptu, spowoduje to problemy z innymi rozszerzeniami, które są instalowane, a po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowane po ponownym uruchomieniu. 
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie, a następnie zainstalować aplikacje i uruchomić skrypty itp. Należy zaplanować ponowne uruchomienie przy użyciu zadania Cron lub za pomocą narzędzi, takich jak DSC lub Chef, rozszerzenia lalek.
* Rozszerzenie uruchomi skrypt tylko raz, jeśli chcesz uruchomić skrypt przy każdym rozruchu, możesz użyć [obrazu init w chmurze](../linux/using-cloud-init.md) i użyć modułu [Skrypty na rozruch.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Alternatywnie można użyć skryptu do utworzenia jednostki serwisowej Systemd.
* Jeśli chcesz zaplanować, kiedy skrypt zostanie uruchomiony, należy użyć rozszerzenia do utworzenia zadania Cron.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli chcesz częstsze aktualizacje stanu uruchomionego skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie Skrypt niestandardowy nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia do przesyłania plików, które obsługuje serwery proxy w skrypcie, takie jak *Curl*.
* Należy pamiętać o nie domyślnych lokalizacji katalogów, które skrypty lub polecenia mogą polegać na, mają logikę do obsługi tego.

## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja niestandardowego rozszerzenia skryptu określa takie elementy, jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można przechowywać w plikach konfiguracyjnych, określić ją w wierszu polecenia lub określić w szablonie usługi Azure Resource Manager. 

Dane poufne można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywać tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako poufne dane i określone w konfiguracji ustawień chronionych rozszerzeniami. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| wydawca | Microsoft.OSTCWysekciańczenia | ciąg |
| type | CustomScriptForLinux (Tekst o językuu niestandardowym) | ciąg |
| typHandlerVersion | 1.5 | int |
| fileUris (np. | https://github.com/MyProject/Archive/MyPythonScript.py | tablica |
| commandToExecute (np.) | python MyPythonScript.py \<my-param1\> | ciąg |
| włączInternalDNSSWykadowanie | true | wartość logiczna |
| storageAccountName (np. | przykłady | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |

### <a name="property-value-details"></a>Szczegóły wartości właściwości

* `fileUris`: (opcjonalnie, tablica ciągów) lista uri skryptów
* `enableInternalDNSCheck`: (opcjonalnie, bool) domyślnie jest True, ustawiona na False, aby wyłączyć sprawdzanie DNS.
* `commandToExecute`: (opcjonalnie, ciąg) skrypt punktu wejścia do wykonania
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu

Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, nie może mieć te wartości poniżej ustawione w ustawieniach publicznych i chronionych.

* `commandToExecute`

Za pomocą ustawień publicznych może przydatne do debugowania, ale zdecydowanie zaleca się, aby użyć ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdzie skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie Wirtualnej w miarę ich wysyłania, czyli jeśli ustawienia zostały zaszyfrowane, są zapisywane zaszyfrowane na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (jeśli to konieczne) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia skryptu niestandardowego podczas wdrażania szablonu usługi Azure Resource Manager.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>W tych nazwach właściwości rozróżniana jest wielkość liter. Aby uniknąć problemów z wdrażaniem, należy użyć nazw, jak pokazano tutaj.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas korzystania z narzędzia Azure CLI do uruchamiania rozszerzenia skryptu niestandardowego należy utworzyć plik konfiguracyjny lub pliki. Co najmniej musisz mieć "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg sformatowany json. Dzięki temu konfiguracja ma być określona podczas wykonywania i bez oddzielnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-no-script-file"></a>Konfiguracja publiczna bez pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Publiczne i chronione pliki konfiguracyjne

Użyj publicznego pliku konfiguracji, aby określić identyfikator URI pliku skryptu. Chroniony plik konfiguracyjny służy do określania polecenia do uruchomienia.

Publiczny plik konfiguracyjny:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chroniony plik konfiguracyjny:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu rozszerzenia skryptu niestandardowego skrypt jest tworzony lub pobierany do katalogu podobnego do poniższego przykładu. Dane wyjściowe polecenia są również `stdout` zapisywane w tym katalogu i `stderr` plikach.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Aby rozwiązać problem, najpierw sprawdź dziennik agenta systemu Linux, upewnij się, że rozszerzenie jest uruchomiony, sprawdź:

```bash
/var/log/waagent.log
```

Należy szukać wykonania rozszerzenia, będzie wyglądać mniej więcej tak:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Kilka punktów, na które należy zwrócić uwagę:

1. Włącz jest, gdy polecenie zaczyna działać.
1. Download odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, a nie plików skryptów określonych w fileUris.
1. Można również sprawdzić, który plik dziennika jest wypisywać do`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Następnym krokiem jest, aby przejść sprawdzić plik dziennika, jest to format:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Należy szukać indywidualnego wykonania, będzie to wyglądać mniej więcej tak:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Tutaj możesz zobaczyć:

* Polecenie Włącz, które jest uruchamiane, to ten dziennik
* Ustawienia przekazane do rozszerzenia
* Plik pobierania rozszerzenia i wynik tego.
* Uruchamiane polecenie i wynik.

Można również pobrać stan wykonywania rozszerzenia skryptu niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kod, bieżące problemy i wersje, zobacz [Repozytorium rozszerzenia Języka Niestandardowego](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
