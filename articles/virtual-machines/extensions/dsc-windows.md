---
title: Azure Desired State Configuration rozszerzenia obsługi | Dokumentacja firmy Microsoft
description: Przekazywanie i zastosowanie konfiguracji DSC programu PowerShell na Maszynie wirtualnej platformy Azure za pomocą rozszerzenia DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 9f81e2b7537a5ecc6778baa93a1bab23dd30ff8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475511"
---
# <a name="powershell-dsc-extension"></a>Rozszerzenie DSC programu PowerShell

## <a name="overview"></a>Omówienie

Rozszerzenie DSC programu PowerShell dla Windows publikowana i obsługiwane przez firmę Microsoft. Rozszerzenie przekazuje i ma zastosowanie konfiguracji DSC programu PowerShell na Maszynie wirtualnej platformy Azure. Wywołuje rozszerzenia DSC programu PowerShell DSC wprowadzenie otrzymana Konfiguracja DSC na maszynie Wirtualnej. W tym dokumencie przedstawiono obsługiwanych platform, konfiguracji i opcje wdrażania rozszerzenia DSC maszyny wirtualnej dla Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC obsługuje następujące systemu operacyjnego

System Windows Server 2019 r, system Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1, klienta Windows 7/8.1/10

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSC dla Windows wymaga, że docelowej maszyny wirtualnej jest w stanie komunikować się z platformą Azure i lokalizacja pakietu konfiguracji (plik zip), jeśli jest on przechowywany w lokalizacji poza platformą Azure. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla ustawień część rozszerzenia DSC w szablonu usługi Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
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
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Wartości właściwości ustawień

| Name (Nazwa) | Typ danych | Opis
| ---- | ---- | ---- |
| settings.wmfVersion | string | Określa wersję Windows Management Framework, który powinien być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości do "najnowszej" zainstaluje najbardziej zaktualizowanej wersji programu WMF. Jedyną możliwe wartości dla tej właściwości to "4.0", "5.0" lub "najnowsza". Te wartości możliwe jest zależna od aktualizacji. Wartość domyślna to "najnowszy". |
| settings.configuration.url | string | Określa lokalizację adresu URL, z której można pobrać plik zip konfiguracji DSC. Adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, należy ustawić właściwość protectedSettings.configurationUrlSasToken na wartość tokenu sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.script i/lub settings.configuration.function.
| settings.configuration.script | string | Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi być w folderze głównym pliku zip pobranego z adresu URL określonego przez właściwość configuration.url. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.url i/lub settings.configuration.script.
| settings.configuration.function | string | Określa nazwę konfiguracji DSC. Konfiguracja o nazwie musi być zawarty w skrypcie, zdefiniowane przez configuration.script. Ta właściwość jest wymagana, jeśli zdefiniowano settings.configuration.url i/lub settings.configuration.function.
| settings.configurationArguments | Collection | Określa wszelkie parametry, które chcesz przekazać do konfiguracji DSC. Ta właściwość nie będą szyfrowane.
| settings.configurationData.url | string | Określa adres URL do pobrania pliku (.pds1) danych konfiguracji do użycia jako dane wejściowe dla danej konfiguracji DSC. Adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, należy ustawić właściwość protectedSettings.configurationDataUrlSasToken na wartość tokenu sygnatury dostępu Współdzielonego.
| settings.privacy.dataEnabled | string | Włącza lub wyłącza zbieranie danych telemetrycznych. Tylko to możliwe wartości dla tej właściwości to "Włącz", "Disable", ", lub $null. Pozostawienie tej właściwości, puste ani mieć wartości null spowoduje włączenie telemetrii
| settings.advancedOptions.forcePullAndApply | Bool | To ustawienie jest przeznaczone do zwiększenia środowisko pracy z rozszerzeniem, aby zarejestrować węzłów za pomocą usługi Azure Automation DSC.  Jeśli wartość jest `$true`, rozszerzenie będzie czekać na pierwszym uruchomieniu konfiguracji pobierane z usługi przed zwróceniem Powodzenie/niepowodzenie.  Jeśli ustawiono wartość $false, stanie zwróconym przez rozszerzenie będzie odwoływać się tylko do tego, czy węzeł został zarejestrowany za pomocą usługi Azure Automation stan konfiguracji pomyślnie i konfiguracja węzła nie zostanie uruchomiona podczas rejestracji.
| settings.advancedOptions.downloadMappings | Collection | Określa alternatywne lokalizacje, aby pobrać zależności, np. programu WMF i .NET

### <a name="protected-settings-property-values"></a>Chronione wartości ustawienia właściwości

| Name (Nazwa) | Typ danych | Opis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | Określa wszelkie parametry, które chcesz przekazać do konfiguracji DSC. Ta właściwość będą szyfrowane. |
| protectedSettings.configurationUrlSasToken | string | Określa tokenu sygnatury dostępu Współdzielonego dostępu do adresu URL zdefiniowane przez configuration.url. Ta właściwość będą szyfrowane. |
| protectedSettings.configurationDataUrlSasToken | string | Określa tokenu sygnatury dostępu Współdzielonego dostępu do adresu URL zdefiniowane przez configurationData.url. Ta właściwość będą szyfrowane. |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager.
Szablony są idealnym rozwiązaniem w przypadku wdrażania maszyn wirtualnych, które wymagają konfiguracji po wdrożeniu.
Przykładowy szablon usługi Resource Manager, zawierającego rozszerzenia DSC programu Windows można znaleźć na [w galerii platformy Azure Szybki Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pakiet rozszerzenia jest pobierana i wdrożone do tej lokalizacji na maszynie Wirtualnej platformy Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Rozszerzenie pliku stanu zawiera sub stanu i kodów powodzenie lub błąd stanu oraz szczegółowy komunikat o błędzie i opis dla każdego rozszerzenia, uruchom.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Dzienniki wyjściowe rozszerzenia są rejestrowane do następującego katalogu:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 1000 | Ogólny błąd | Komunikat dla tego błędu jest dostarczany przez określony wyjątek w dziennikach rozszerzenia |
| 52 | Błąd instalacji rozszerzenia | Komunikat dla tego błędu jest dostarczany przez określony wyjątek |
| 1002 | Błąd podczas instalacji programu WMF | Wystąpił błąd podczas instalowania programu WMF. |
| 1004 | Nieprawidłowy spakowany pakiet | Nieprawidłowe archiwum zip; Błąd podczas rozpakowywania pliku zip |
| 1100 | Błąd argumentu | Wskazuje na problem w danych wejściowych dostarczonych przez użytkownika. W komunikacie o błędzie są dostarczane przez określony wyjątek|


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
