---
title: Eksportowanie grup zasobów platformy Azure, która zawiera rozszerzenia maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Eksportowanie szablonów usługi Resource Manager, które zawierają rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: f56cfeeede393dbdb9632ea4120d3a81e89f3f7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61484055"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Eksportowanie grupy zasobów, która zawiera rozszerzenia maszyn wirtualnych

Grupy zasobów platformy Azure mogą być eksportowane do nowego szablonu usługi Resource Manager, które następnie mogą być ponownie wdrażana. Proces eksportowania interpretuje istniejących zasobów i tworzy szablon usługi Resource Manager, po wdrożeniu skutkuje podobne grupy zasobów. Korzystając z opcji eksportu grupy zasobów przed grupa zasobów zawierająca rozszerzenia maszyny wirtualnej, kilka elementów należy rozważyć, takich jak rozszerzenie zgodności i ustawieniach chronionego.

Szczegóły tego dokumentu, sposobu działania procesu eksportu grupy zasobów dotyczących rozszerzeń maszyn wirtualnych, łącznie z listą obsługiwanych rozszerzeń i szczegółowe informacje na temat obsługi zabezpieczonych danych.

## <a name="supported-virtual-machine-extensions"></a>Rozszerzenia obsługiwanych maszyn wirtualnych

Dostępnych jest wiele rozszerzeń maszyny wirtualnej. Nie wszystkie rozszerzenia można wyeksportować do szablonu usługi Resource Manager przy użyciu funkcji "Skrypt automatyzacji". Jeśli rozszerzenie maszyny wirtualnej nie jest obsługiwany, należy ręcznie umieścić do wyeksportowanego szablonu.

Następujące rozszerzenia można wyeksportować za pomocą funkcji skryptów automatyzacji.

| Wewnętrzny ||||
|---|---|---|---|
| Acronis Backup | Agent Windows pomocą usługi Datadog | Stosowanie poprawek dla systemu Linux systemu operacyjnego | Migawki maszyny Wirtualnej w systemie Linux
| Kopia zapasowa Acronis systemu Linux | Rozszerzenia platformy docker | Puppet Agent |
| Bg Info | Rozszerzenie DSC | Szczegółowe informacje o lokacji w trybie 24 x 7 Apm |
| Linux Agent CTM kontrolera zarządzania płytą GŁÓWNĄ | Dynatrace Linux | 24 x 7 Linux serwer lokacji |
| Windows Agent CTM kontrolera zarządzania płytą GŁÓWNĄ | Windows Dynatrace | Lokacja 24 x 7 systemu Windows Server |
| Klient programu chef | System HPE zabezpieczeń aplikacji Defender | Trend Micro DSA |
| Niestandardowy skrypt | IaaS ochrony przed złośliwym kodem | Trend Micro DSA w systemie Linux |
| Rozszerzenie niestandardowego skryptu | Diagnostyka IaaS | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Niestandardowego skryptu dla systemu Linux | Klient programu Chef systemu Linux | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Agent systemu Linux pomocą usługi Datadog | Diagnostyczne systemu Linux | Migawka maszyny wirtualnej |

## <a name="export-the-resource-group"></a>Eksportowanie grupy zasobów

Aby wyeksportować grupy zasobów do szablonu do ponownego wykorzystania, wykonaj następujące czynności:

1. Logowanie się do witryny Azure Portal
2. W Menu Centrum kliknij opcję grupy zasobów
3. Wybierz docelową grupę zasobów z listy
4. W bloku grupy zasobów kliknij skrypt automatyzacji

![Eksportowanie szablonu](./media/export-templates/template-export.png)

Skrypt automatyzacji usługi Azure Resource Manager tworzy szablon usługi Resource Manager, plik parametrów i skrypty wdrażania przykładowe takie jak program PowerShell i wiersza polecenia platformy Azure. W tym momencie wyeksportowanego szablonu można go pobrać, korzystając z przycisku Pobierz dodane jako nowy szablon Biblioteka szablonów lub ponownego wdrażania za pomocą przycisku Wdróż.

## <a name="configure-protected-settings"></a>Konfigurowanie ustawień chronionych

Wiele rozszerzeń maszyny wirtualnej platformy Azure obejmują konfiguracji chronione ustawienia, która szyfruje dane poufne, takie jak poświadczenia i ciągi konfiguracji. Chronione ustawienia nie są eksportowane za pomocą skryptów automatyzacji. Jeśli to konieczne, chronione ustawienia należy ponownie do wyeksportowanego szablonu.

### <a name="step-1---remove-template-parameter"></a>Krok 1. Usuń parametr szablonu

Gdy grupa zasobów jest eksportowany, parametr jednego szablonu zostanie utworzona podawanie wartości do wyeksportowanego chronione ustawienia. Ten parametr może zostać usunięta. Aby usunąć parametr, przejrzyj listę parametrów i Usuń parametr, który wygląda podobnie do tego przykładu w formacie JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 — Pobierz chronione ustawienia właściwości

Ponieważ każdy chroniony ustawienie zawiera zestaw wymaganych właściwości, należy zebrać listę tych właściwości. Każdy parametr konfiguracji chronionych ustawień można znaleźć w [schematu usługi Azure Resource Manager w witrynie GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ten schemat zawiera tylko zestawów parametrów dla rozszerzeń wymienionych w sekcji Przegląd tego dokumentu. 

Z w ramach schematu repozytorium, Wyszukaj żądaną rozszerzenia, w tym przykładzie `IaaSDiagnostics`. Gdy rozszerzenia `protectedSettings` obiektu została znaleziona, zwróć uwagę na każdego parametru. W przykładzie `IaasDiagnostic` rozszerzenia, wymagane są parametry `storageAccountName`, `storageAccountKey`, i `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 — ponowne tworzenie konfiguracji chronionych

W wyeksportowanego szablonu Wyszukaj `protectedSettings` i Zastąp obiekt wyeksportowane ustawienia chronionego nową, zawierający parametry wymaganego rozszerzenia i wartość dla każdego z nich.

W przykładzie `IaasDiagnostic` rozszerzenia, nowej konfiguracji chronionych ustawienie będzie wyglądać następująco:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Zasób rozszerzeniem końcowym przypomina poniższy przykład kodu JSON:

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

Podaj wartości właściwości za pomocą parametrów szablonu, te muszą zostać utworzone. Podczas tworzenia parametrów szablonu dla chronionego, ustawianie wartości upewnij się, że używasz `SecureString` typu parametru, aby wartości poufne są chronione. Aby uzyskać więcej informacji na temat korzystania z parametrów, zobacz [tworzenia usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

W przykładzie `IaasDiagnostic` rozszerzenia, będzie można utworzyć następujące parametry w sekcji Parametry w szablonie usługi Resource Manager.

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

W tym momencie szablonu można wdrożyć przy użyciu dowolnej metody wdrażania szablonu.
