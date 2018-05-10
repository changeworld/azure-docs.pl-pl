---
title: Azure niestandardowe rozszerzenie skryptu dla systemu Windows | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Windows przy użyciu rozszerzenia niestandardowego skryptu
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 34c16b686a50994862bef14cefec1a4799a343c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="custom-script-extension-for-windows"></a>Niestandardowe rozszerzenie skryptu dla systemu Windows

Niestandardowe rozszerzenie skryptu pobiera i uruchamia skrypty na maszynach wirtualnych Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure Portal lub interfejsu API REST maszyny wirtualnej platformy Azure.

Ten dokument zawiera szczegóły dotyczące sposobu używania niestandardowe rozszerzenie skryptu przy użyciu modułu Azure PowerShell, szablony usługi Azure Resource Manager i szczegóły dotyczące rozwiązywania problemów z w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie należy używać niestandardowe rozszerzenie skryptu uruchamiać AzureRmVM aktualizacji z tej samej maszyny Wirtualnej jako jego parametr, ponieważ będzie czekać na samym sobie.  
>   
> 

### <a name="operating-system"></a>System operacyjny

Niestandardowe rozszerzenie skryptu dla systemu Linux będzie uruchamiany na rozszerzenia rozszerzenie obsługiwane systemem operacyjnym, aby uzyskać więcej informacji, zobacz ten [artykułu](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

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
* Jeśli skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i uruchamiać skrypty itp. Należy zaplanować ponowny rozruch przy użyciu systemu Windows zaplanowane zadanie, lub narzędzi, takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie zostanie uruchomiony tylko skrypt raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, a następnie należy użyć rozszerzenia, aby utworzyć zaplanowane zadanie systemu Windows.
* Jeśli chcesz zaplanować skrypt będzie uruchamiany, należy użyć rozszerzenia utworzyć zaplanowane zadanie systemu Windows. 
* Podczas wykonywania skryptu, będą widoczne są tylko "Przechodzenie" stan rozszerzenia z portalu Azure lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, należy utworzyć własne rozwiązania.
* Niestandardowe rozszerzenie skryptu nie obsługuje serwerów proxy, jednak można korzystać narzędzie transferu plików, które obsługuje serwery proxy w skrypcie, takich jak *Curl* 
* Należy pamiętać o z systemem innym niż domyślny katalog lokalizacje, które mogą polegać skryptu lub polecenia, mieć logiki do obsługi to.


## <a name="extension-schema"></a>Schemat rozszerzenia

Niestandardowe rozszerzenie skryptu konfiguracji określa lokalizację i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub określ go w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfigurację, która zostaje zaszyfrowany i odszyfrowane tylko na maszynie wirtualnej. Chronione konfiguracja jest użyteczna, jeśli wykonanie polecenia obejmuje tajemnice, takie jak hasła.

Te elementy powinny traktowane jako poufne dane i określony w konfiguracji chronionych ustawień rozszerzenia. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Uwaga** -tylko jedna wersja rozszerzenia można zainstalować na maszynie Wirtualnej w punkcie w czasie, określając skryptu niestandardowego dwa razy w tym samym szablonie usługi Resource Manager dla tej samej maszyny Wirtualnej zakończy się niepowodzeniem. 

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.Compute | ciąg |
| type | CustomScriptExtension | ciąg |
| typeHandlerVersion | 1.9 | int |
| fileUris (np.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tablica |
| commandToExecute (np.) | PowerShell - ExecutionPolicy Unrestricted - pliku skonfigurować muzyka app.ps1 | ciąg |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |

>[!NOTE]
>Te nazwy właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

#### <a name="property-value-details"></a>Szczegóły wartości właściwości
 * `commandToExecute`: (**wymagane**, ciąg) wykonywania skryptu punktu wejścia. Zamiast tego użyj tego pola, jeśli polecenia zawiera klucze tajne, takie jak hasła lub fileUris Twojego jest wielkość liter.
* `fileUris`: (opcjonalne, tablicy ciągów) w adresach URL dla pliki do pobrania.
* `storageAccountName`: (opcjonalne, ciąg) nazwa konta magazynu. Jeśli określisz poświadczeń magazynu wszystkich `fileUris` muszą być adresami URL dla obiektów blob Azure.
* `storageAccountKey`: (opcjonalne, ciąg) klucz dostępu konta magazynu

Można ustawić następujące wartości w ustawieniach publiczne lub chronione, rozszerzenie spowoduje odrzucenie żadnej konfiguracji, w którym poniższe wartości są ustawiane w ustawieniach zarówno publiczne i chronione.
* `commandToExecute`

Przy użyciu ustawienia publicznego, może być przydatne dla debugowania, ale zdecydowanie zalecane jest użycie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, w którym skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane za pomocą klucza znane tylko do platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, tj. Jeśli zostały zaszyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości są przechowywane na maszynie Wirtualnej i używany do odszyfrowywania ustawienia (Jeśli to konieczne) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager niestandardowe rozszerzenie skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który zawiera niestandardowe rozszerzenie skryptu można znaleźć w tym miejscu [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzureRmVMCustomScriptExtension` Polecenia można dodać rozszerzenia niestandardowego skryptu do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [AzureRmVMCustomScriptExtension zestawu ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Dalsze przykłady

### <a name="using-multiple-script"></a>Używanie wielu skryptu
W tym przykładzie ma trzy skrypty, które są używane do tworzenia serwera, wywołania "commandToExecute", który pierwszy skryptu, możesz mieć opcje w sposób nazywane są inne, na przykład masz wzorca skrypt, który kontroluje wykonywanie, z powodu błędu w prawo Obsługa, rejestrowanie i zarządzanie stanem.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Uruchamianie skryptów z udziałem lokalnym
W tym przykładzie może zajść potrzeba korzystania z lokalnego serwera SMB dla lokalizacji skryptu należy pamiętać, nie trzeba przekazać w innym inne ustawienia, z wyjątkiem *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak uruchomić niestandardowego skryptu więcej niż jeden raz z interfejsu wiersza polecenia
Jeśli chcesz uruchomić rozszerzenia niestandardowego skryptu więcej niż raz, tylko można to zrobić w tych warunkach:
1. Parametr 'Name' rozszerzenia jest taka sama jak poprzedniego wdrożenia rozszerzenia.
2. Należy zaktualizować konfiguracji, w przeciwnym razie polecenie będzie ponownie uruchomił nie, na przykład możesz można dodać we właściwości dynamicznych w polecenia, takich jak sygnatury czasowej. 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu modułu Azure PowerShell. Aby wyświetlić stan wdrażania rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia jest rejestrowane w plikach znajdują się w następującym katalogu na docelowej maszynie wirtualnej.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki są pobierane do następującego katalogu na docelowej maszynie wirtualnej.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
gdzie `<n>` jest dziesiętną liczbą całkowitą, która może zmienić pomiędzy wykonaniami rozszerzenia.  `1.*` Wartość odpowiada bieżącej rzeczywiste, `typeHandlerVersion` wartość rozszerzenia.  Na przykład może być rzeczywiste katalogu `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Podczas wykonywania `commandToExecute` polecenia rozszerzenia ustawia ten katalog (np. `...\Downloads\2`) jako bieżący katalog roboczy. Dzięki temu używanie ścieżek względnych, aby zlokalizować pliki pobrane za pośrednictwem `fileURIs` właściwości. Poniższa tabela przykłady.

Ponieważ ścieżka bezwzględna pobierania może się różnić w czasie, zaleca się opt ścieżek względnych skryptów i plików w `commandToExecute` string, jeśli to możliwe. Na przykład:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszy segment identyfikatora URI został zachowany na potrzeby plików pobranych za pośrednictwem `fileUris` listy właściwości.  Jak pokazano w poniższej tabeli, pobierane pliki są mapowane do pobierania podkatalogów w celu odzwierciedlenia struktury `fileUris` wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobrane pliki

| Identyfikator URI w fileUris | Względne położenie pobrany | Bezwzględna pobrać lokalizację * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Powyżej, ścieżki bezwzględnej katalogów zmienić okresu istnienia maszyny wirtualnej, ale nie w ramach pojedynczego uruchomienia rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
