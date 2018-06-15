---
title: Uruchamianie niestandardowych skryptów na maszynach wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Linux przy użyciu niestandardowe rozszerzenie skryptu
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906831"
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Niestandardowe rozszerzenie skryptu Azure za pomocą maszyn wirtualnych systemu Linux
Niestandardowe rozszerzenie skryptu pobiera i uruchamia skrypty na maszynach wirtualnych Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacja oprogramowania lub innych zadań konfiguracji i zarządzania nimi. Skrypty można pobrać z magazynu Azure lub innej dostępnej lokalizacji internet lub zapewniają je do środowiska wykonawczego rozszerzenia. 

Niestandardowe rozszerzenie skryptu jest zintegrowany z szablonów usługi Azure Resource Manager. Można również uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell, portalu Azure lub interfejsu API REST dla maszyn wirtualnych Azure.

Ten artykuł zawiera szczegóły dotyczące używania niestandardowe rozszerzenie skryptu z wiersza polecenia platformy Azure i sposobu uruchamiania rozszerzenia za pomocą szablonu usługi Azure Resource Manager. Ten artykuł zawiera również kroki rozwiązywania problemów dla systemu Linux.

## <a name="extension-configuration"></a>Konfiguracja rozszerzenia
Niestandardowe rozszerzenie skryptu konfiguracji określa lokalizację i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub określ go w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfigurację, która zostaje zaszyfrowany i odszyfrowane tylko na maszynie wirtualnej. Chronione konfiguracja jest użyteczna, jeśli wykonanie polecenia obejmuje tajemnice, takie jak hasła.

### <a name="public-configuration"></a>Publiczna Konfiguracja
Schemat publicznej konfiguracji ma następującą składnię.

>[!NOTE]
>Te nazwy właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

* **commandToExecute** (wymagane, string): działanie skryptu punktu wejścia.
* **fileUris** (opcjonalne, tablicy ciągów): adresy URL plików do pobrania.
* **Sygnatura czasowa** (opcjonalne, liczba całkowita): sygnatura czasowa skryptu. Tylko, jeśli chcesz wyzwolić Uruchom ponownie skryptu, należy zmienić wartość tego pola.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Konfiguracja chronionych
Schemat konfiguracji chronionego ma następującą składnię.

>[!NOTE]
>Te nazwy właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

* **commandToExecute** (opcjonalne, ciąg): działanie skryptu punktu wejścia. Użyj tego pola, jeśli polecenie zawiera klucze tajne, takie jak hasła.
* **storageAccountName** (opcjonalne, ciąg): Nazwa konta magazynu. Jeśli określisz poświadczeń magazynu plik wszystkie identyfikatory URI musi być adresy URL dla obiektów blob Azure.
* **storageAccountKey** (opcjonalne, ciąg): klucz dostępu konta magazynu.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas korzystania z wiersza polecenia platformy Azure do uruchomienia niestandardowe rozszerzenie skryptu, Utwórz plik konfiguracji lub plików. Co najmniej pliki konfiguracji zawierają identyfikator URI pliku i wykonywania polecenia skryptu.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg w formacie JSON. Dzięki temu konfiguracji należy określić podczas wykonywania i bez pliku osobną konfiguracją.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-script-file"></a>Publiczna Konfiguracja z pliku skryptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Publiczna Konfiguracja z żadnego pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publiczne i chronione

Aby określić plik skryptu identyfikatora URI jest używany plik publicznej konfiguracji. Aby określić polecenie do uruchomienia jest używany plik konfiguracji chronionych.

Plik konfiguracji publicznego:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Plik chroniony konfiguracji:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Niestandardowe rozszerzenie skryptu Azure można uruchomić w czasie wdrażania maszyny wirtualnej za pomocą szablonu usługi Resource Manager. Aby to zrobić, należy dodać prawidłowo sformatowane JSON do szablonu wdrożenia.

### <a name="resource-manager-examples"></a>Przykłady usługi Resource Manager

#### <a name="public-configuration"></a>Publiczna Konfiguracja

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Wykonanie polecenia w chronionym konfiguracji

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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Pełny przykład, zobacz [pokaz magazynu utworów muzycznych .NET](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Po uruchomieniu niestandardowe rozszerzenie skryptu skrypt jest tworzony lub pobrany do katalogu, który jest podobny do poniższego przykładu. Dane wyjściowe polecenia jest także zapisane w tym katalogu w `stdout` i `stderr` plików.

```bash
/var/lib/waagent/custom-script/download/0/
```

Rozszerzenie skryptu Azure tworzy dziennika, który można znaleźć tutaj:

```bash
/var/log/azure/custom-script/handler.log
```

Można również pobrać stanu wykonywania niestandardowe rozszerzenie skryptu przy użyciu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco następujący tekst:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje na inne rozszerzenia skryptu maszyny Wirtualnej, zobacz [Przegląd rozszerzenie skryptu Azure dla systemu Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

