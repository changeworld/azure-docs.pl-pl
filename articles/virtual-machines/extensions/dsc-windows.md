---
title: Azure żądanego stanu konfiguracji rozszerzenia obsługi | Dokumentacja firmy Microsoft
description: Przekazywanie i zastosowanie konfiguracji DSC środowiska PowerShell na maszynie Wirtualnej platformy Azure przy użyciu rozszerzenia usługi Konfiguracja DSC
services: virtual-machines-windows
documentationcenter: ''
author: eshaparmar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: esparmar
ms.openlocfilehash: b34314951980f7dbe2269119883dec52a90a0587
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="powershell-dsc-extension"></a>Rozszerzenia usługi Konfiguracja DSC środowiska PowerShell

## <a name="overview"></a>Przegląd

Rozszerzenia usługi Konfiguracja DSC środowiska PowerShell dla systemu Windows publikowana i obsługiwane przez firmę Microsoft. Rozszerzenie przekazywania i zastosowanie konfiguracji DSC środowiska PowerShell na maszynie Wirtualnej platformy Azure. Rozszerzenia DSC wywołuje DSC środowiska PowerShell wprowadzenie otrzymanej konfiguracji DSC na maszynie Wirtualnej. Ten dokument zawiera szczegóły dotyczące obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia DSC maszyny wirtualnej systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenia DSC obsługuje następujące systemu operacyjnego

Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1, klient systemu Windows 7/8.1

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenia konfiguracji DSC dla systemu Windows wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schematu dla części ustawień rozszerzenia usługi Konfiguracja DSC w szablonie usługi Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.73",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.Powershell.DSC | ciąg |
| type | DSC | ciąg |
| typeHandlerVersion | 2,73 | int |

### <a name="settings-property-values"></a>Ustawienia wartości właściwości

| Name (Nazwa) | Typ danych | Opis
| ---- | ---- | ---- |
| settings.wmfVersion | ciąg | Określa wersję Windows Management Framework, która ma zostać zainstalowana na maszynie Wirtualnej. Ustawienie tej właściwości do "najnowszej" zainstaluje najnowszych wersji platformy WMF. Tylko bieżące możliwe wartości dla tej właściwości to "4.0", "5.0" i "najnowsze". Te wartości można podlegają aktualizacji. Wartość domyślna to "najnowsze". |
| settings.configuration.url | ciąg | Określa adres URL lokalizacji, z którego można pobrać pliku zip konfiguracji DSC. Podany adres URL wymaga tokenu sygnatury dostępu Współdzielonego w celu uzyskania dostępu, należy ustawić właściwość protectedSettings.configurationUrlSasToken wartość token sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.script i/lub settings.configuration.function.
| settings.configuration.script | ciąg | Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi być w folderze głównym pliku zip pobranego z adresu URL określonego przez właściwość configuration.url. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.url i/lub settings.configuration.script.
| settings.configuration.function | ciąg | Określa nazwę konfiguracji DSC. Konfigurację o nazwie muszą być zawarte w skrypcie zdefiniowane przez configuration.script. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.url i/lub settings.configuration.function.
| settings.configurationArguments | Collection | Definiuje parametry, których chcesz przekazać do konfiguracji DSC. Ta właściwość nie będą szyfrowane.
| settings.configurationData.url | ciąg | Określa adres URL do pobrania pliku (.pds1) danych konfiguracji do użycia jako dane wejściowe dla danej konfiguracji DSC. Podany adres URL wymaga tokenu sygnatury dostępu Współdzielonego w celu uzyskania dostępu, należy ustawić właściwość protectedSettings.configurationDataUrlSasToken wartość token sygnatury dostępu Współdzielonego.
| settings.privacy.dataEnabled | ciąg | Włącza lub wyłącza gromadzenia danych telemetrii. To tylko możliwe wartości dla tej właściwości są "Włącz", "Wyłączone", ", lub $null. Opuszczenie tej właściwości puste ani mieć wartości null spowoduje włączenie telemetrii
| settings.advancedOptions.forcePullAndApply | wartość logiczna | Włącza rozszerzenia DSC aktualizacji i wprowadza konfiguracji DSC, gdy tryb odświeżania jest ściągania.
| settings.advancedOptions.downloadMappings | Collection | Definiuje alternatywne lokalizacje, aby pobrać zależności, takich jak WMF i .NET

### <a name="protected-settings-property-values"></a>Wartości ustawienia właściwości chronione

| Name (Nazwa) | Typ danych | Opis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | ciąg | Definiuje parametry, których chcesz przekazać do konfiguracji DSC. Ta właściwość będą szyfrowane. |
| protectedSettings.configurationUrlSasToken | ciąg | Określa tokenu sygnatury dostępu Współdzielonego dostępu do adresu URL, zdefiniowane przez configuration.url. Ta właściwość będą szyfrowane. |
| protectedSettings.configurationDataUrlSasToken | ciąg | Określa tokenu sygnatury dostępu Współdzielonego dostępu do adresu URL, zdefiniowane przez configurationData.url. Ta właściwość będą szyfrowane. |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealne w przypadku wdrażania jednego lub więcej maszyn wirtualnych, które wymagają konfiguracji wdrożenia post. Przykładowy szablon usługi Resource Manager zawierający rozszerzenia maszyny Wirtualnej agenta pakietu OMS można znaleźć w [Azure Szybki Start galerii](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczony w katalogu głównego lub najwyższego poziomu szablonu usługi Resource Manager JSON. Umieszczanie konfiguracji JSON ma wpływ na wartość nazwy zasobów i typu. 

Podczas zagnieżdżania rozszerzenia zasobu, JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej. Podczas umieszczania rozszerzenia JSON w elemencie głównym szablonu, nazwy zasobu zawiera odwołanie do nadrzędnego maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.  


## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Interfejsu wiersza polecenia Azure może służyć do wdrożenia rozszerzenia maszyny Wirtualnej agenta pakietu OMS na istniejącej maszyny wirtualnej. Zamień na klucz OMS i identyfikator pakietu OMS z obszaru roboczego OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.73 --protected-settings '{}' \
  --settings '{}'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pakiet rozszerzenia jest pobierany i wdrożone do tej lokalizacji na maszynie Wirtualnej Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Rozszerzenie pliku stanu zawiera sub stanu i kodów stanu powodzenie lub błąd oraz szczegółowe informacje o błędzie i opis dla każdego rozszerzenia, uruchom.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Rozszerzenie danych wyjściowych dzienników są rejestrowane do następującego katalogu:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 1000 | Błąd rodzajowy | Komunikat dla tego błędu jest zapewniana przez określony wyjątek w dziennikach rozszerzenia |
| 52 | Błąd instalacji rozszerzenia | Komunikat dla tego błędu jest zapewniana przez określony wyjątek |
| 1002 | Błąd instalacji WMF | Błąd podczas instalowania WMF. |
| 1004 | Nieprawidłowy spakowany pakiet | Nieprawidłowy zip; Błąd rozpakowywania zip |
| 1100 | Błąd argumentu | Wskazuje na problem w danych wejściowych dostarczonych przez użytkownika. W komunikacie o błędzie jest zapewniana przez określony wyjątek|


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).