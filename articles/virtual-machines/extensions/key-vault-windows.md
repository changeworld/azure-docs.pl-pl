---
title: Klucz magazynu rozszerzenia maszyny Wirtualnej dla systemu Windows | Dokumentacja firmy Microsoft
description: Wdróż agenta wykonywania automatycznego odświeżania usługi Key Vault kluczy tajnych na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej usługi Key Vault dla systemu Windows

## <a name="overview"></a>Przegląd

Rozszerzenia klucza magazynu z maszyny Wirtualnej zapewnia automatyczne odświeżanie kluczy tajnych przechowywane w magazynie kluczy Azure. W szczególności monitorów rozszerzenia listę obserwowanych certyfikaty przechowywane w magazynów kluczy, a po wykryciu zmiany, pobiera i instaluje odpowiednie certyfikaty. Rozszerzenie klucza magazynu z maszyny Wirtualnej jest opublikowana i obsługiwane przez firmę Microsoft, w obecnie na maszynach wirtualnych systemu Windows. Ten dokument zawiera szczegóły dotyczące obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia maszyny Wirtualnej z magazynu kluczy dla systemu Windows. 

## <a name="prerequisites"></a>Wymagania wstępne

Rozszerzenia klucza magazynu z maszyny Wirtualnej zależy od zarządzanych usługi tożsamości rozszerzenia maszyny Wirtualnej, w celu samodzielnego uwierzytelnienia w usłudze Key Vault. Zapoznaj się z dokumentacją dotyczącą rozszerzenia maszyny Wirtualnej MSI, aby uzyskać dodatkowe szczegóły.

### <a name="operating-system"></a>System operacyjny

Rozszerzenia klucza magazynu z maszyny Wirtualnej obsługuje obecnie wszystkie wersje systemu Windows.

| Dystrybucja | Wersja |
|---|---|
| Windows | Podstawowe |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenia maszyny Wirtualnej z magazynu kluczy dla systemu Windows wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat dla rozszerzenia maszyny Wirtualnej z magazynu kluczy. Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są uznawane za informacje bez wpływu na zabezpieczenia. Rozszerzenie wymaga listy monitorowanych klucze tajne, częstotliwość sondowania i miejsce docelowe magazynu certyfikatów. W szczególności:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | data |
| Wydawcy | Microsoft.Azure.KeyVault.Edp | ciąg |
| type | KeyVaultForWindows | ciąg |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MOJE | ciąg |
| certificateStoreLocation  | REJESTRÓW LOCAL_MACHINE | ciąg |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Tablica ciągów


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealne w przypadku wdrażania jednego lub więcej maszyn wirtualnych, które wymagają po wdrożeniu Odśwież certyfikatów. Rozszerzenie można wdrożyć na poszczególnych maszynach wirtualnych lub maszyn wirtualnych skalowanie zestawów. Konfiguracji i schematu są wspólne dla obu typów szablonu. 

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej musi być zagnieżdżone wewnątrz fragmentu zasobu maszyny wirtualnej, szablonu, w szczególności `"resources": []` obiektu maszyny wirtualnej.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Wdrożenia usługi Azure PowerShell

Program Azure PowerShell może służyć do wdrożenia rozszerzenia klucza magazynu z maszyny Wirtualnej na istniejącej maszyny wirtualnej lub zestaw skali maszyny wirtualnej. 

* Aby wdrożyć rozszerzenie na maszynie Wirtualnej:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Aby wdrożyć rozszerzenie na zestaw skali maszyny Wirtualnej, na przykład jako część klastra sieci szkieletowej usług:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Należy zwrócić uwagę na następujące ograniczenia/wymagania:
- Wdrożenie musi odbywać się w regionie południowo-środkowe Stany
- Ograniczenia usługi Key Vault:
    - Musi istnieć w czasie wdrożenia 
    - Musi się znajdować w tym samym regionie i grupy zasobów jako wdrożenia
    - Musi być włączony do wdrożenia i wdrażanie szablonu

## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Przykłady dotyczące wdrażania rozszerzeń klucza magazynu z maszyny Wirtualnej w wiersza polecenia platformy Azure będzie dostępna wkrótce. 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu programu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie za pomocą programu Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

Dane wyjściowe wykonania rozszerzenie jest zarejestrowane w następującym pliku:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
