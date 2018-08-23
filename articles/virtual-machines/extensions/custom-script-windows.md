---
title: Rozszerzenie niestandardowego skryptu platformy Azure dla Windows | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyn wirtualnych Windows przy użyciu rozszerzenia niestandardowego skryptu
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: c00d4d481c992e90597276d0ce8655aef2be731f
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060672"
---
# <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla Windows

Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure Portal lub interfejsu API REST maszyny wirtualnej platformy Azure.

W tym dokumencie przedstawiono sposób korzystania z rozszerzenia niestandardowego skryptu przy użyciu modułu Azure PowerShell, szablony usługi Azure Resource Manager i szczegółowe informacje, rozwiązywanie problemów w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie należy używać rozszerzenia niestandardowego skryptu do uruchomienia Update-AzureRmVM przy użyciu tej samej maszyny Wirtualnej jako parametr, ponieważ będzie czekać na siebie.  
>   
> 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie niestandardowego skryptu dla systemu Linux będą uruchamiane na rozszerzenie rozszerzenia obsługiwane systemu operacyjnego, aby uzyskać więcej informacji, zobacz [artykułu](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenia można użyć na potrzeby dostępu do usługi Azure Blob storage poświadczeń magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacja skryptu może być dowolnego miejsca, tak długo, jak maszyna wirtualna może kierować do tego punktu końcowego, takich jak GitHub, serwer wewnętrzny plików itp.


### <a name="internet-connectivity"></a>Łączność z Internetem
Jeśli musisz pobrać skrypt zewnętrznie takich jak GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieci grupy zabezpieczeń muszą być otwarte porty. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp, za pomocą tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, a następnie nadal potrzebujesz dodatkowych Zapora/sieci zabezpieczeń grupy portów muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik błędów dla tego rozszerzenia jest z powodu błędów składni w skrypcie testu, którego skrypt jest uruchamiany bez błędów, i również umieścić w dodatkowe opcje rejestrowania do skryptu, aby ułatwić znajdowanie, których nie powiodła się.
* Pisanie skryptów, które są idempotentne, więc jeśli pobieranie uruchomiony ponownie więcej niż jeden raz przypadkowo, nie spowoduje zmian w systemie.
* Upewnij się, że skrypty są wymagane dane wejściowe użytkownika podczas uruchamiania.
* 90 minut mogą uzyskać skrypt do uruchomienia, nic dłużej spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie należy umieszczać wewnątrz skrypt jest uruchamiany ponownie, spowoduje to problemy z innymi rozszerzeniami, które są instalowane i po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowana po ponownym uruchomieniu. 
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i uruchamiać skrypty itp. Należy zaplanować ponowny rozruch przy użyciu usługi Windows zaplanowane zadanie lub narzędzi, takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie zostanie uruchomiony tylko skrypt raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, a następnie należy utworzyć zaplanowane zadanie Windows za pomocą rozszerzenia.
* Chcąc Zaplanuj, kiedy skrypt będzie uruchamiany, należy użyć rozszerzenia utworzyć zaplanowane zadanie Windows. 
* Podczas wykonywania skryptu będą widzieć tylko "Przechodzenie" stan rozszerzenia z witryny Azure portal lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie niestandardowego skryptu nie obsługuje natywnie serwery proxy, jednak można użyć narzędzie do transferu plików, który obsługuje serwery proxy w ramach skryptu, takie jak *Curl* 
* Należy pamiętać, innego niż domyślny katalog lokalizacji, które mogą polegać Twoje skrypty lub polecenia, masz logikę obsługującą to.


## <a name="extension-schema"></a>Schemat rozszerzenia

Określa konfigurację rozszerzenia niestandardowego skryptu, elementów, takich jak lokalizacja skryptu i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub je określić w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfiguracji, która zostaje zaszyfrowany i odszyfrowane tylko wewnątrz maszyny wirtualnej. Chronionych konfiguracja jest użyteczna w przypadku, gdy polecenia wykonywania zawiera wpisy tajne, takie jak hasło.

Tych elementów powinien być traktowane jako poufne dane i określony w konfiguracji chronione ustawienia rozszerzeń. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

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
**Uwaga** — tylko jedną wersję rozszerzenia można zainstalować na maszynie Wirtualnej w punkcie w czasie, określanie niestandardowego skryptu dwa razy w tym samym szablonie usługi Resource Manager dla tej samej maszyny Wirtualnej zakończy się niepowodzeniem. 

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.Compute | ciąg |
| type | CustomScriptExtension | ciąg |
| typeHandlerVersion | 1.9 | Int |
| fileUris (np.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tablica |
| commandToExecute (np.) | Program PowerShell - ExecutionPolicy Unrestricted - pliku skonfigurować — muzyka app.ps1 | ciąg |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |

>[!NOTE]
>Nazwy tych właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

#### <a name="property-value-details"></a>Szczegóły dotyczące wartości właściwości
 * `commandToExecute`: (**wymagane**, ciąg) skrypt punktu wejścia do wykonania. Zamiast tego użyj tego pola, jeśli polecenie zawiera wpisy tajne, takie jak hasła lub usługi fileUris jest wielkość liter.
* `fileUris`: (opcjonalne, tablicy ciągów) adresy URL plików mają być pobrane.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. Jeśli określisz poświadczeń magazynu wszystkich `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu

Można ustawić następujące wartości w ustawieniach publiczną lub chronioną, rozszerzenie spowoduje odrzucenie wszystkich konfiguracji, gdzie poniższe wartości są ustawione w ustawieniach zarówno publiczne i chronione.
* `commandToExecute`

Za pomocą ustawień publicznego, może być przydatne do debugowania, ale zdecydowanie zalecane jest używanie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdy skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, czyli jeśli były szyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości przechowywane na maszynie Wirtualnej i jest używany do odszyfrowywania ustawienia (w razie potrzeby) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do rozszerzenia niestandardowego skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który zawiera rozszerzenie niestandardowego skryptu można znaleźć w tym miejscu [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzureRmVMCustomScriptExtension` Polecenia można dodać rozszerzenie niestandardowego skryptu do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [AzureRmVMCustomScriptExtension zestaw ](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Dalsze przykłady

### <a name="using-multiple-script"></a>Za pomocą wielu skryptu
W tym przykładzie ma trzy skrypty, które są używane do utworzenia serwera, wywołania "commandToExecute", które pierwszy skrypt, należy mieć opcje jak inne noszą nazwę, na przykład można mieć główny skrypt, który kontroluje wykonywanie, z powodu błędu w prawo Obsługa, rejestrowanie i zarządzanie stanem.

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
W tym przykładzie warto używać lokalnego serwera SMB dla Twojej lokalizacji skryptu należy zwrócić uwagę, nie trzeba przekazać w innym inne ustawienia, z wyjątkiem *commandToExecute*.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak uruchomić skrypt niestandardowy więcej niż jeden raz z interfejsem wiersza polecenia
Jeśli chcesz uruchomić rozszerzenia niestandardowego skryptu więcej niż jeden raz, tylko można to zrobić w tych warunkach:
1. Parametr 'Name' rozszerzenia jest taka sama jak poprzedniego wdrożenia rozszerzenia.
2. Należy zaktualizować konfiguracji, w przeciwnym razie polecenie będzie ponownie uruchomił nie, na przykład, można dodać we właściwości dynamicznych w do polecenia, takie jak sygnaturę czasową. 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą modułu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowane w plikach znaleźć w następującym katalogu na docelowej maszynie wirtualnej.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki są pobierane do następującego katalogu na docelowej maszynie wirtualnej.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
gdzie `<n>` jest dziesiętną liczbą całkowitą, która może ulec zmianie między wykonaniami rozszerzenia.  `1.*` Wartość odpowiada bieżącej rzeczywiste, `typeHandlerVersion` wartość rozszerzenia.  Na przykład może być rzeczywista katalogu `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Podczas wykonywania `commandToExecute` polecenia rozszerzenia ustawia ten katalog (np. `...\Downloads\2`) jako bieżący katalog roboczy. Umożliwia to użycie ścieżek względnych, aby zlokalizować pliki pobrane za pośrednictwem `fileURIs` właściwości. Zobacz tabelę poniżej przykłady.

Ponieważ ścieżka bezwzględna pobierania może różnić się od wraz z upływem czasu, to lepiej wybrać ścieżki względnej pliku skryptu/w `commandToExecute` string, jeśli to możliwe. Na przykład:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszy segment identyfikatora URI są przechowywane pliki pobrane za pośrednictwem `fileUris` listy właściwości.  Jak pokazano w poniższej tabeli, pobierane pliki są mapowane w podkatalogach pobierania do odzwierciedlenia struktury `fileUris` wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobrane pliki

| Identyfikator URI w fileUris | Względna lokalizacja pobranego | Bezwzględna pobrać lokalizacji * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Tak jak powyżej, ścieżki bezwzględnej katalogów zmienić w okresie istnienia maszyny wirtualnej, ale nie w ramach pojedynczej operacji za pomocą rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
