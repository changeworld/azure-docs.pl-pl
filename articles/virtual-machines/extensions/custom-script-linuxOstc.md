---
title: Uruchamianie skryptów niestandardowych na maszynach wirtualnych z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Linux za pomocą niestandardowego rozszerzenia skryptu w wersji 1
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
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: fe3803b7dc75ab13831a5e42d4b1a96f5aa894e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60800306"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Azure niestandardowy skrypt rozszerzenia w wersji 1 za pomocą maszyn wirtualnych systemu Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Rozszerzenie niestandardowego skryptu w wersji 1, pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrażaniu, instalacja oprogramowania lub innych zadań konfiguracji i zarządzania nimi. Skrypty można pobrać z usługi Azure Storage lub w innej lokalizacji internet dostępny, lub można udostępnić je w czasie wykonywania rozszerzenia.

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager. Można również uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure portal lub interfejsu API REST dla maszyn wirtualnych platformy Azure.

Ten artykuł szczegółowo opisuje sposób korzystania z rozszerzenia niestandardowego skryptu z wiersza polecenia platformy Azure oraz sposobu uruchamiania rozszerzenia za pomocą szablonu usługi Azure Resource Manager. Ten artykuł zawiera również kroki rozwiązywania problemów w systemach Linux.

Istnieją dwa rozszerzenia skryptu niestandardowego systemu Linux:

* Wersja 1 — Microsoft.OSTCExtensions.CustomScriptForLinux

* Wersja 2 — Microsoft.Azure.Extensions.CustomScript

Przełącz nowych i istniejących wdrożeń do nowej wersji ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) zamiast tego. Nowa wersja ma być zamiennikiem. W związku z tym migracja jest równie proste jak zmiana nazwy i wersji, nie trzeba zmienić konfigurację rozszerzenia.

### <a name="operating-system"></a>System operacyjny

Obsługiwane dystrybucje systemu Linux:

* CentOS 6.5 lub nowszy
* Debian 8 i nowsze
  * Debian 8.7 nie jest dostarczany z Python2 najnowsze obrazy, co spowoduje przerwanie CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 lub nowszy
* Oracle Linux 6.4 oraz wyższej
* SUSE Linux Enterprise Server 11 SP3 lub nowszy
* Ubuntu 12.04 lub nowszy

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenia można użyć na potrzeby dostępu do usługi Azure Blob storage poświadczeń magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacja skryptu może być dowolnego miejsca, tak długo, jak maszyna wirtualna może kierować do tego punktu końcowego, takich jak GitHub, serwer wewnętrzny plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli musisz pobrać skrypt zewnętrznie takich jak GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieci grupy zabezpieczeń muszą być otwarte porty. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp, za pomocą tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](../../virtual-network/security-overview.md#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, a następnie nadal potrzebujesz dodatkowych Zapora/sieci zabezpieczeń grupy portów muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty są wymagane dane wejściowe użytkownika podczas uruchamiania.
* 90 minut mogą uzyskać skrypt do uruchomienia, nic dłużej spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie należy umieszczać wewnątrz skrypt jest uruchamiany ponownie, spowoduje to problemy z innymi rozszerzeniami, które są instalowane i po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowana po ponownym uruchomieniu. 
* Jeśli masz skrypt, który powoduje ponowne uruchomienie, zainstaluj aplikacje i uruchom skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania Cron lub narzędzi, takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie tylko uruchamianie skryptu, jeden raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, wówczas można użyć [obraz pakietu cloud-init](../linux/using-cloud-init.md) i użyj [skryptów na rozruch](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modułu. Alternatywnie służy skrypt tworzenia jednostki usługi Systemd.
* Chcąc Zaplanuj, kiedy skrypt będzie uruchamiany, należy użyć rozszerzenia do tworzenia zadania Cron.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie niestandardowego skryptu nie obsługuje natywnie serwery proxy, jednak można użyć narzędzie do transferu plików, który obsługuje serwery proxy w ramach skryptu, takie jak *Curl*.
* Należy pamiętać, innego niż domyślny katalog lokalizacji, które mogą polegać Twoje skrypty lub polecenia, masz logikę obsługującą to.

## <a name="extension-schema"></a>Schemat rozszerzenia

Określa konfigurację rozszerzenia niestandardowego skryptu, elementów, takich jak lokalizacja skryptu i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub je określić w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfiguracji, która zostaje zaszyfrowany i odszyfrowane tylko wewnątrz maszyny wirtualnej. Chronionych konfiguracja jest użyteczna w przypadku, gdy polecenia wykonywania zawiera wpisy tajne, takie jak hasło.

Tych elementów powinien być traktowane jako poufne dane i określony w konfiguracji chronione ustawienia rozszerzeń. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

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
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | string |
| type | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (np.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (np.) | python MyPythonScript.py \<Moje param1\> | string |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (np.) | examplestorageacct | string |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Szczegóły dotyczące wartości właściwości

* `fileUris`: (opcjonalne, tablicy ciągów) na liście uri skryptów
* `enableInternalDNSCheck`: (opcjonalne, wartość logiczna) domyślna wartość to True, ustawienie wartości False powoduje wyłączenie sprawdzania DNS.
* `commandToExecute`: (opcjonalnie, ciąg) skrypt punktu wejścia do wykonania
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu

Można ustawić następujące wartości w ustawieniach publiczna lub chroniona, nie może mieć następujące wartości poniżej zestaw w ustawieniach zarówno publiczne i chronione.

* `commandToExecute`

Za pomocą ustawień publicznego, może być przydatne do debugowania, ale zdecydowanie zalecane jest używanie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdy skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, czyli jeśli były szyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości przechowywane na maszynie Wirtualnej i jest używany do odszyfrowywania ustawienia (w razie potrzeby) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do rozszerzenia niestandardowego skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager.

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
>Nazwy tych właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas korzystania z wiersza polecenia platformy Azure do uruchamiania rozszerzenia niestandardowego skryptu, Utwórz plik konfiguracji lub plików. Jako minimum konieczne jest posiadanie "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg w formacie JSON. Dzięki temu konfiguracji należy określić podczas wykonywania i bez osobnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-no-script-file"></a>Konfiguracja publiczna z żadnego pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Usługa Azure polecenia interfejsu wiersza polecenia:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publiczne i chronione

Plik konfiguracji publicznego umożliwia określenie pliku skryptu identyfikatora URI. Plik konfiguracji chronionych umożliwia określenie polecenia do uruchomienia.

Plik konfiguracji publicznej:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Plik konfiguracji chronionych:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Usługa Azure polecenia interfejsu wiersza polecenia:

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

Po uruchomieniu rozszerzenia niestandardowego skryptu skryptu jest utworzony lub pobrany do katalogu, który jest podobny do poniższego przykładu. Dane wyjściowe polecenia jest także zapisywane do tego katalogu w `stdout` i `stderr` plików.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Rozwiązywanie problemów, najpierw sprawdź w dzienniku agenta systemu Linux, upewnij się, rozszerzenie zostanie wykonane, sprawdź:

```bash
/var/log/waagent.log
```

Wykonywanie rozszerzenia należy szukać, będzie on wyglądać mniej więcej tak:

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

1. Włącz to podczas uruchamiania polecenia.
1. Pobierania związana z oczekiwaną Pobieranie pakietu rozszerzenia CustomScript z platformy Azure, nie pliki skryptów określone w fileUris.
1. Możesz też sprawdzić plik dziennika, który zapisuje się na `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Następnym krokiem jest do sprawdzania pliku dziennika, jest to format:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Powinien wyglądać do wykonywania pojedynczych, będzie on wyglądać mniej więcej tak:

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

W tym miejscu możesz zobaczyć:

* Włącz uruchamianie polecenia jest ten dziennik
* Ustawienia przekazane do rozszerzenia
* Rozszerzenie, pobierania pliku i wynik tego obiektu.
* Polecenia są uruchamiane i wynik.

Możesz również pobrać stanu wykonywania rozszerzenia niestandardowego skryptu za pomocą wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają podobnie jak następujący tekst:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Kolejne kroki

Aby wyświetlić kod, bieżących problemach i wersji, zobacz [repozytorium za pomocą rozszerzenia CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
