---
title: Eksportowanie grup zasobów platformy Azure zawierających rozszerzenia maszyn wirtualnych
description: Eksportuj szablony Menedżera zasobów, które zawierają rozszerzenia maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253912"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych

Grupy zasobów platformy Azure można wyeksportować do nowego szablonu Menedżera zasobów, który następnie można ponownie wdrożyć. Proces eksportowania interpretuje istniejące zasoby i tworzy szablon Menedżera zasobów, który po wdrożeniu powoduje utworzenie podobnej grupy zasobów. Podczas korzystania z opcji eksportu grupy zasobów względem grupy zasobów zawierającej rozszerzenia maszyny wirtualnej należy wziąć pod uwagę kilka elementów, takich jak zgodność z rozszerzeniami i ustawienia chronione.

Ten dokument zawiera szczegółowe informacje o tym, jak działa proces eksportowania grupy zasobów w odniesieniu do rozszerzeń maszyn wirtualnych, w tym listę obsługiwanych rozszerzeń oraz szczegółowe informacje na temat obsługi zabezpieczonych danych.

## <a name="supported-virtual-machine-extensions"></a>Obsługiwane rozszerzenia maszyn wirtualnych

Dostępnych jest wiele rozszerzeń maszyny wirtualnej. Nie wszystkie rozszerzenia można wyeksportować do szablonu Menedżera zasobów przy użyciu funkcji "Skrypt automatyzacji". Jeśli rozszerzenie maszyny wirtualnej nie jest obsługiwane, musi zostać ręcznie umieszczone z powrotem do wyeksportowanego szablonu.

Następujące rozszerzenia można wyeksportować za pomocą funkcji skryptu automatyzacji.

| Wewnętrzny ||||
|---|---|---|---|
| Kopia zapasowa Acronis | Datadog Windows Agent | Łatanie systemu operacyjnego dla linuksa | Migawka maszyny Wirtualnej z systemem Linux
| Acronis Backup Linux | Rozszerzenie platformy Docker | Agent lalek |
| Informacje bg | Rozszerzenie DSC | Strona 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Strona 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Witryna 24x7 Windows Server |
| Klient szefa kuchni | HPE Security Application Defender | Trend Micro DSA |
| Niestandardowy skrypt | Ochrona przed złośliwym oprogramowaniem IaaS | Trend Micro DSA Linux |
| Rozszerzenie niestandardowego skryptu | Diagnostyka IaaS | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Niestandardowy skrypt dla systemu Linux | Klient szefa kuchni Linuksa | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Datadog Linux Agent | Diagnostyka Linuksa | Migawka maszyny wirtualnej |

## <a name="export-the-resource-group"></a>Eksportowanie grupy zasobów

Aby wyeksportować grupę zasobów do szablonu wielokrotnego użycia, wykonaj następujące czynności:

1. Logowanie się do witryny Azure Portal
2. W menu Centrum kliknij polecenie Grupy zasobów
3. Wybierz docelową grupę zasobów z listy
4. W bloku Grupa zasobów kliknij pozycję Skrypt automatyzacji

![Eksportowanie szablonów](./media/export-templates/template-export.png)

Skrypt automatyzacji usługi Azure Resource Manager tworzy szablon Menedżera zasobów, plik parametrów i kilka przykładowych skryptów wdrażania, takich jak PowerShell i Azure CLI. W tym momencie wyeksportowany szablon można pobrać za pomocą przycisku pobierania, dodać jako nowy szablon do biblioteki szablonów lub ponownie wdrożyć za pomocą przycisku wdrażania.

## <a name="configure-protected-settings"></a>Konfigurowanie chronionych ustawień

Wiele rozszerzeń maszyny wirtualnej platformy Azure zawiera konfigurację ustawień chronionych, która szyfruje poufne dane, takie jak poświadczenia i ciągi konfiguracji. Chronione ustawienia nie są eksportowane za pomocą skryptu automatyzacji. W razie potrzeby chronione ustawienia należy ponownie wstawić do wyeksportowanego szablonu.

### <a name="step-1---remove-template-parameter"></a>Krok 1 - Usuń parametr szablonu

Podczas eksportowania grupy zasobów tworzony jest pojedynczy parametr szablonu, aby zapewnić wartość eksportowanych ustawień chronionych. Ten parametr można usunąć. Aby usunąć parametr, przejrzyj listę parametrów i usuń parametr, który wygląda podobnie do tego przykładu JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 - Uzyskaj właściwości ustawień chronionych

Ponieważ każde ustawienie chronione ma zestaw wymaganych właściwości, należy zebrać listę tych właściwości. Każdy parametr konfiguracji ustawień chronionych można znaleźć w [schemacie usługi Azure Resource Manager w usłudze GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ten schemat zawiera tylko zestawy parametrów dla rozszerzeń wymienionych w sekcji przeglądu tego dokumentu. 

W tym przykładzie `IaaSDiagnostics`wyszukać żądane rozszerzenie z repozytorium schematu. Po zlokalizowaniu `protectedSettings` obiektu rozszerzeń należy wziąć pod uwagę każdy parametr. W `IaasDiagnostic` przykładzie rozszerzenia parametry wymagają `storageAccountName`to `storageAccountKey`, `storageAccountEndPoint`i .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 - Ponowne utworzenie chronionej konfiguracji

W wyeksportowanym `protectedSettings` szablonie wyszukaj i zastąp eksportowany obiekt ustawień chronionych nowym obiektem zawierającym wymagane parametry rozszerzenia i wartość dla każdego z nich.

W przykładzie `IaasDiagnostic` rozszerzenia nowa konfiguracja ustawień chronionych będzie wyglądać następująco:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Ostateczny zasób rozszerzenia wygląda podobnie do następującego przykładu JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Jeśli do zapewnienia wartości właściwości są używane parametry szablonu, należy je utworzyć. Podczas tworzenia parametrów szablonu dla chronionych wartości `SecureString` ustawień należy użyć typu parametru, aby wartości poufne były zabezpieczone. Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

W przykładzie `IaasDiagnostic` rozszerzenia zostaną utworzone następujące parametry w sekcji parametrów szablonu Menedżera zasobów.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

W tym momencie szablon można wdrożyć przy użyciu dowolnej metody wdrażania szablonu.
