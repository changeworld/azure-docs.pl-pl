---
title: Eksportowanie grupy zasobów Azure, która zawiera rozszerzenia maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Wyeksportować szablony Menedżera zasobów, które obejmują rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 2dc05fd21df3d408ed57badeec2b5c06e25bf50c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Eksportowanie grupy zasobów, która zawiera rozszerzenia maszyny Wirtualnej

Grupy zasobów platformy Azure mogą być eksportowane do nowego szablonu usługi Resource Manager, które mogą być następnie wdrażane. Proces eksportu interpretuje istniejących zasobów i tworzy szablon usługi Resource Manager która po wdrożeniu powoduje podobne grupy zasobów. Korzystając z opcji eksportu grupy zasobów przed grupę zasobów zawierającą rozszerzenia maszyny wirtualnej, kilka elementów należy rozważyć, takich jak zgodność rozszerzenia i chronionych ustawień.

Szczegóły tego dokumentu działania procesu eksportowania grupy zasobów dotyczących rozszerzenia maszyny wirtualnej, łącznie z listą obsługiwanych rozszerzeń, a szczegóły dotyczące obsługi zabezpieczonych danych.

## <a name="supported-virtual-machine-extensions"></a>Rozszerzenia obsługiwanych maszyn wirtualnych

Dostępnych jest wiele rozszerzeń maszyny wirtualnej. Nie wszystkie rozszerzenia mogą być eksportowane do szablonu usługi Resource Manager za pomocą funkcji "Skrypt automatyzacji". Jeśli rozszerzenie maszyny wirtualnej nie jest obsługiwana, należy ręcznie można umieścić do wyeksportowanego szablonu.

Następujące rozszerzenia można wyeksportować za pomocą funkcji skryptów automatyzacji.

| Wewnętrzny ||||
|---|---|---|---|
| Kopia zapasowa Acronis | Agent Datadog Windows | Stosowanie poprawek dla systemu Linux systemu operacyjnego | Migawki maszyny Wirtualnej w systemie Linux
| Kopia zapasowa Acronis systemu Linux | Rozszerzenie docker | Puppet Agent |
| Bg Info | Rozszerzenie DSC | Szczegółowe informacje o lokacji 24 x 7 Apm |
| Linux Agent BMC CTM | Dynatrace Linux | 24 x 7 Linux serwera lokacji |
| BMC CTM agenta w systemie Windows | Dynatrace systemu Windows | Lokacja 24 x 7 systemu Windows Server |
| Chef klienta | HPE zabezpieczeń aplikacji Defender | Trend Micro DSA |
| Niestandardowego skryptu | IaaS ochrony przed złośliwym oprogramowaniem | Trend Micro DSA Linux |
| Rozszerzenie niestandardowego skryptu | Diagnostyka IaaS | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Skryptu niestandardowego dla systemu Linux | Linux Chef Client | Dostęp do maszyny Wirtualnej dla systemu Linux |
| Datadog agenta systemu Linux | Diagnostyka systemu Linux | Migawki maszyny Wirtualnej |

## <a name="export-the-resource-group"></a>Eksportowanie grupy zasobów

Aby wyeksportować grupy zasobów do szablonów wielokrotnego użytku, wykonaj następujące kroki:

1. Logowanie się do witryny Azure Portal
2. W Menu centralnym kliknij grup zasobów
3. Wybierz z listy docelowej grupy zasobów
4. W bloku grupy zasobów kliknij skryptu automatyzacji

![Eksportowanie szablonu](./media/extensions-export-templates/template-export.png)

Skrypt automatyzacji Azure Resource Manager tworzy szablon usługi Resource Manager, pliku parametrów i skrypty wdrażania przykładowe, takie jak środowiska PowerShell i interfejsu wiersza polecenia Azure. W tym momencie wyeksportowanego szablonu można pobrać za pomocą przycisku pobierania, dodać jako nowy szablon Biblioteka szablonów lub wdrożone za pomocą przycisku Wdróż.

## <a name="configure-protected-settings"></a>Skonfiguruj ustawienia chronionego

Wiele rozszerzeń maszyny wirtualnej platformy Azure obejmują konfiguracji chronionych ustawień, który szyfruje dane poufne, na przykład poświadczenia i parametry konfiguracji. Ustawienia chronionych nie są eksportowane z skryptów automatyzacji. Jeśli konieczne, chronionych ustawień należy ponownie do wyeksportowanego szablonu.

### <a name="step-1---remove-template-parameter"></a>Krok 1 — Usuń parametr szablonu

Po wyeksportowaniu grupy zasobów, tworzona jest parametrem jednego szablonu podawanie wartości do wyeksportowanych ustawieniach chronionych. Ten parametr może zostać usunięta. Aby usunąć parametr, Przejrzyj listy parametrów i Usuń parametr, który wygląda podobnie jak w tym przykładzie JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 - Get chronione ustawienia właściwości

Ponieważ każdy chroniony ustawienie ma zestaw właściwości wymagane, należy zebrać listę tych właściwości. Każdy parametr chronionych ustawień konfiguracji można znaleźć w [schematu usługi Azure Resource Manager w witrynie GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ten schemat zawiera tylko zestawy parametrów dla rozszerzeń wymienionych w sekcji Przegląd tego dokumentu. 

Od w ramach schematu repozytorium, wyszukaj żądane rozszerzenie, w tym przykładzie `IaaSDiagnostics`. Raz rozszerzenia `protectedSettings` obiektu została znaleziona, zanotuj każdego parametru. W przykładzie `IaasDiagnostic` rozszerzenia, wymagają parametry są `storageAccountName`, `storageAccountKey`, i `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 — ponownie utworzyć konfigurację chronionych

W wyeksportowanego szablonu, wyszukaj `protectedSettings` i Zastąp wyeksportowanego ustawiania chronionego obiektu nową zawierający parametry wymagane rozszerzenie i wartość dla każdego z nich.

W przykładzie `IaasDiagnostic` rozszerzenia, nowej konfiguracji chronionych ustawienie będzie wyglądać jak w następującym przykładzie:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Zasób rozszerzeniem końcowym wygląda podobnie do poniższego przykładu JSON:

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

Aby zapewnić wartości właściwości przy użyciu parametrów szablonu, te muszą zostać utworzone. Podczas tworzenia parametrów szablonu dla chronionych ustawiania wartości, upewnij się, że użyj `SecureString` typ parametru, tak aby poufnych wartości są chronione. Aby uzyskać więcej informacji na temat używania parametrów, zobacz [szablonów Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

W przykładzie `IaasDiagnostic` rozszerzenia, następujące parametry zostałyby utworzone w sekcji parametrów szablonu usługi Resource Manager.

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
