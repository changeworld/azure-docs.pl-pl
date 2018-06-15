---
title: Uruchamianie niestandardowych skryptów na maszynach wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Linux przy użyciu v1 niestandardowe rozszerzenie skryptu
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: eac64a5b456eb040bcb1ac01c3c86dfde0847e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942774"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Użyj Azure niestandardowy skrypt rozszerzenie wersji 1 z maszyn wirtualnych systemu Linux
Rozszerzenia niestandardowego skryptu w wersji 1 pobiera i uruchamia skrypty na maszynach wirtualnych Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacja oprogramowania lub innych zadań konfiguracji i zarządzania nimi. Skrypty można pobrać z magazynu Azure lub innej dostępnej lokalizacji internet lub zapewniają je do środowiska wykonawczego rozszerzenia. 

Niestandardowe rozszerzenie skryptu jest zintegrowany z szablonów usługi Azure Resource Manager. Można również uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell, portalu Azure lub interfejsu API REST dla maszyn wirtualnych Azure.

Ten artykuł zawiera szczegóły dotyczące używania niestandardowe rozszerzenie skryptu z wiersza polecenia platformy Azure i sposobu uruchamiania rozszerzenia za pomocą szablonu usługi Azure Resource Manager. Ten artykuł zawiera również kroki rozwiązywania problemów dla systemu Linux.


Istnieją dwa rozszerzenia skryptów niestandardowych Linux:
* Wersja 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* W wersji 2 - Microsoft.Azure.Extensions.CustomScript

Przełącz nowych i istniejących wdrożeń do nowej wersji ([Microsoft.Azure.Extensions.CustomScript](\custom-script-linux.md)) zamiast tego. Nowa wersja ma być zastępczy dzięki wsuwanej konstrukcji. W związku z tym migracji jest tak proste, jak zmiana nazwy i wersji, nie musisz zmienić konfigurację rozszerzenia.

 

### <a name="operating-system"></a>System operacyjny
Obsługiwane dystrybucje systemu Linux:

- CentOS 6.5 i nowsze
- Debian 8 i nowsze
    - Debian 8.7 nie jest dostarczany z Python2 najnowsze obrazy, która dzieli CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13.1 i nowsze
- Oracle Linux 6.4 oraz wyższej
- SUSE Linux Enterprise Server 11 z dodatkiem SP3 lub nowszej
- Ubuntu 12.04 i nowsze

### <a name="script-location"></a>Lokalizacja skryptu

Można użyć rozszerzenia do użycia poświadczeń magazynu obiektów Blob platformy Azure na dostęp do magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacji skryptu można dowolnego miejsca, tak długo, jak długo maszyny Wirtualnej można kierować do tego punktu końcowego, takie jak GitHub, serwer wewnętrzny plików itp.

### <a name="internet-connectivity"></a>Połączenie z Internetem
Jeśli potrzebujesz można pobrać skryptu zewnętrznie, takich jak usługi GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieci grupa zabezpieczeń porty muszą być otwarte. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG Azure dla [magazynu](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, a następnie nadal potrzebujesz dodatkowych zapory i sieci zabezpieczeń grupy porty muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik awaryjności dla tego rozszerzenia jest z powodu błędów składni w skrypcie testu, którego skrypt jest uruchamiany bez błędów, i również umieścić w dodatkowe opcje rejestrowania do skryptu, aby ułatwić znajdowanie, których nie powiodło się.
* Napisać skrypty, które są idempotentności, więc jeśli uzyskać Uruchom ponownie więcej niż raz przypadkowo, nie spowoduje zmiany w systemie.
* Upewnij się, że skrypty nie wymagają danych wejściowych użytkownika podczas uruchamiania.
* Występuje w ciągu 90 minut dozwolone w przypadku skryptu uruchamianego, niczego dłużej spowoduje postanowienia rozszerzenia nie powiodło się.
* Nie należy umieszczać wewnątrz skrypt jest uruchamiany ponownie, powoduje to problemy z innymi rozszerzeniami, które są instalowane i post ponowny rozruch, rozszerzenia nie będzie kontynuowana po ponownym uruchomieniu. 
* Jeśli skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i uruchamiać skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania Cron lub za pomocą narzędzi takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie zostanie uruchomiony tylko wtedy skrypt raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, a następnie można użyć [obrazu init chmury](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) i użyj [skryptów na rozruch](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modułu. Alternatywnie można użyć skryptu, aby utworzyć jednostkę usługi Systemd.
* Jeśli chcesz zaplanować skrypt będzie uruchamiany, należy użyć rozszerzenia można utworzyć zadania usługi Cron. 
* Podczas wykonywania skryptu, będą widoczne są tylko "Przechodzenie" stan rozszerzenia z portalu Azure lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, należy utworzyć własne rozwiązania.
* Niestandardowe rozszerzenie skryptu nie obsługuje serwerów proxy, jednak można korzystać narzędzie transferu plików, które obsługuje serwery proxy w skrypcie, takich jak *Curl*. 
* Należy pamiętać o z systemem innym niż domyślny katalog lokalizacje, które mogą polegać skryptu lub polecenia, mieć logiki do obsługi to.



## <a name="extension-schema"></a>Schemat rozszerzenia

Niestandardowe rozszerzenie skryptu konfiguracji określa lokalizację i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub określ go w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfigurację, która zostaje zaszyfrowany i odszyfrowane tylko na maszynie wirtualnej. Chronione konfiguracja jest użyteczna, jeśli wykonanie polecenia obejmuje tajemnice, takie jak hasła.

Te elementy powinny traktowane jako poufne dane i określony w konfiguracji chronionych ustawień rozszerzenia. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

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

| Name (Nazwa) | Wartość / przykład | Typ danych | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.OSTCExtensions | ciąg |
| type | CustomScriptForLinux | ciąg |
| typeHandlerVersion | 1.5 | int |
| fileUris (np.) | https://github.com/MyProject/Archive/MyPythonScript.py | tablica |
| commandToExecute (np.) | Python MyPythonScript.py < Moje param1 > | ciąg |
| enableInternalDNSCheck | true | wartość logiczna |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |

### <a name="property-value-details"></a>Szczegóły wartości właściwości
* `fileUris`: (opcjonalne, tablicy ciągów) uri Lista skryptów
* `enableInternalDNSCheck`: (opcjonalne, wartość logiczna) domyślna to True, wartość False, aby wyłączyć sprawdzenie DNS.
* `commandToExecute`: (opcjonalne, ciąg) wykonywania skryptu punktu wejścia
* `storageAccountName`: (opcjonalne, ciąg) nazwa konta magazynu
* `storageAccountKey`: (opcjonalne, ciąg) klucz dostępu konta magazynu

Można ustawić następujące wartości w ustawieniach publiczne lub chronione, nie może mieć wartości poniżej zestawu w ustawieniach zarówno publiczne i chronione.
* `commandToExecute`

Przy użyciu ustawienia publicznego, może być przydatne dla debugowania, ale zdecydowanie zalecane jest użycie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, w którym skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane za pomocą klucza znane tylko do platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, tj. Jeśli zostały zaszyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości są przechowywane na maszynie Wirtualnej i używany do odszyfrowywania ustawienia (Jeśli to konieczne) w czasie wykonywania.


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager niestandardowe rozszerzenie skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. 


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
>Te nazwy właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas korzystania z wiersza polecenia platformy Azure do uruchomienia niestandardowe rozszerzenie skryptu, Utwórz plik konfiguracji lub plików. Co najmniej musi mieć "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg w formacie JSON. Dzięki temu konfiguracji należy określić podczas wykonywania i bez pliku osobną konfiguracją.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-no-script-file"></a>Publiczna Konfiguracja z żadnego pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publiczne i chronione

Aby określić plik skryptu identyfikatora URI jest używany plik publicznej konfiguracji. Aby określić polecenie do uruchomienia jest używany plik konfiguracji chronionych.

Plik konfiguracji publicznego:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Plik chroniony konfiguracji:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

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
Po uruchomieniu niestandardowe rozszerzenie skryptu skrypt jest tworzony lub pobrany do katalogu, który jest podobny do poniższego przykładu. Dane wyjściowe polecenia jest także zapisane w tym katalogu w `stdout` i `stderr` plików. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Rozwiązywanie problemów, najpierw, sprawdź dziennik agenta systemu Linux, upewnij się, rozszerzenie zostanie wykonane, sprawdź:

```bash
/var/log/waagent.log 
```

Należy szukać wykonywania rozszerzenia, wygląda mniej więcej tak:
```text
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
Niektóre informacje, które należy zwrócić uwagę:
1. Włącz to, gdy polecenie zacznie działać.
2. Pobieranie odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, nie plików skryptów określony w fileUris.
3. Można również sprawdzić plik dziennika, który zapisuje na "/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log".

Następnym krokiem jest sprawdzenie pliku dziennika, jest to format:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Należy szukać wykonywania induvidual, wygląda mniej więcej tak:
```text
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
W tym miejscu można wyświetlić:
* Uruchamianie polecenia Enable jest ten dziennik
* Ustawienia przekazane do rozszerzenia
* Rozszerzenie pobierania pliku i jego wynikach.
* Uruchamianego polecenia i wynik.

Można również pobrać stanu wykonywania niestandardowe rozszerzenie skryptu przy użyciu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco następujący tekst:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Kolejne kroki
Aby wyświetlić kod, bieżących problemach i wersji, zobacz [repozytorium rozszerzenia CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

