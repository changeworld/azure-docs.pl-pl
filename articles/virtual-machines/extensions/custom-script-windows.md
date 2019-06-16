---
title: Rozszerzenie niestandardowego skryptu platformy Azure dla Windows | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyn wirtualnych Windows przy użyciu rozszerzenia niestandardowego skryptu
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: gwallace
ms.openlocfilehash: b71ba69bcf4965ea607e097c392573e77aab6865
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408289"
---
# <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla Windows

Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacja oprogramowania lub innych konfiguracji lub zadania związane z zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager i mogą być uruchamiane przy użyciu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure portal lub interfejsu API REST dla maszyny wirtualnej platformy Azure.

W tym dokumencie przedstawiono sposób korzystania z rozszerzenia niestandardowego skryptu przy użyciu modułu Azure PowerShell, szablony usługi Azure Resource Manager i szczegółowe informacje, rozwiązywanie problemów w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie należy używać rozszerzenia niestandardowego skryptu uruchamiać AzVM aktualizacji za pomocą tej samej maszyny Wirtualnej jako jego parametr, ponieważ będzie czekać na siebie.  

### <a name="operating-system"></a>System operacyjny

Niestandardowy skrypt rozszerzenia dla Windows będzie uruchomić rozszerzenia rozszerzenia obsługiwane OSs, aby uzyskać więcej informacji, zobacz to [rozszerzenie platformy Azure obsługiwane systemy operacyjne](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Lokalizacja skryptu

Można skonfigurować rozszerzenia dostęp usługi Azure Blob storage do za pomocą poświadczeń magazynu obiektów Blob platformy Azure. Lokalizacja skryptu może być dowolnym miejscu, tak długo, jak maszyna wirtualna może kierować do tego punktu końcowego, takich jak GitHub lub serwer plików wewnętrznych.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli musisz pobrać skrypt zewnętrznie takich jak z usługi GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieciowej grupy zabezpieczeń porty muszą być otwarte. Na przykład, jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](../../virtual-network/security-overview.md#service-tags).

W przypadku skryptu na serwerze lokalnym, może nadal potrzebujesz dodatkowej zapory i sieciowej grupy zabezpieczeń porty muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik błędów dla tego rozszerzenia jest z powodu błędów składni w skrypcie testu, którego skrypt jest uruchamiany bez błędów, i również umieścić w dodatkowe opcje rejestrowania do skryptu, aby ułatwić znajdowanie, których nie powiodła się.
* Napisać skrypty, które są idempotentne. Gwarantuje to, że jeśli ponownie przypadkowo działają, nie spowoduje zmian w systemie.
* Upewnij się, że skrypty nie wymagają udziału użytkownika w trakcie działania.
* Dozwolony czas uruchamiania skryptu wynosi 90 minut — każdy dłuższy czas spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie umieszczaj operacji ponownego uruchamiania wewnątrz skryptu; ta akcja spowoduje problemy z innymi instalowanymi rozszerzeniami. Po ponownym uruchomieniu rozszerzenie nie będzie kontynuować pracy, gdy zostanie uruchomione jeszcze raz.
* Jeśli masz skrypt, który będzie spowodować ponowne uruchomienie komputera, a następnie zainstalować aplikacje i uruchamiać skrypty, można zaplanować ponowny rozruch przy użyciu usługi Windows zaplanowane zadanie lub użyj narzędzi takich jak rozszerzenia DSC, Chef lub Puppet.
* Rozszerzenie uruchomi skrypt tylko raz. Jeśli chcesz uruchomić skrypt przy każdym rozruchu, musisz użyć rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* Jeśli chcesz zaplanować czas uruchomienia skryptu, użyj rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli potrzebujesz częstszych aktualizacji stanu działającego skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie niestandardowego skryptu nie obsługuje natywnie serwery proxy, jednak można użyć narzędzie do transferu plików, który obsługuje serwery proxy w ramach skryptu, takie jak *Curl*
* Pamiętaj, że skrypt lub polecenia mogą polegać na lokalizacjach katalogów innych niż domyślne. Przygotuj logikę obsługującą taką sytuację.

## <a name="extension-schema"></a>Schemat rozszerzenia

Określa konfigurację rozszerzenia niestandardowego skryptu, elementów, takich jak lokalizacja skryptu i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub je określić w szablonie usługi Azure Resource Manager.

Poufne dane można przechowywać w chronionym konfiguracji, która zostaje zaszyfrowany i odszyfrowane tylko wewnątrz maszyny wirtualnej. Chronionych konfiguracja jest użyteczna w przypadku, gdy polecenia wykonywania zawiera wpisy tajne, takie jak hasło.

Tych elementów powinien być traktowane jako poufne dane i określony w konfiguracji chronione ustawienia rozszerzeń. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

```json
{
    "apiVersion": "2018-06-01",
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
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Tylko jedna wersja rozszerzenia można zainstalować na maszynie Wirtualnej w punkcie w czasie, określanie niestandardowego skryptu w dwukrotnie tego samego szablonu usługi Resource Manager dla tej samej maszyny Wirtualnej zakończą się niepowodzeniem.

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (np.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tablica |
| Sygnatura czasowa (np.) | 123456789 | 32-bitowa liczba całkowita |
| commandToExecute (np.) | Program PowerShell - ExecutionPolicy Unrestricted - pliku skonfigurować — muzyka app.ps1 | string |
| storageAccountName (np.) | examplestorageacct | string |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Nazwy tych właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

#### <a name="property-value-details"></a>Szczegóły dotyczące wartości właściwości

* `commandToExecute`: (**wymagane**, ciąg) skrypt punktu wejścia do wykonania. Zamiast tego użyj tego pola, jeśli polecenie zawiera wpisy tajne, takie jak hasła lub usługi fileUris jest wielkość liter.
* `fileUris`: (opcjonalne, tablicy ciągów) adresy URL plików mają być pobrane.
* `timestamp` Użycie (opcjonalne, 32-bitowa liczba całkowita), w tym polu tylko do wyzwolenia ponownie skryptu, zmieniając wartość tego pola.  Dowolna liczba całkowita jest dopuszczalne; tylko musi być inna niż poprzednia wartość.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. Jeśli określisz poświadczeń magazynu wszystkich `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu

Można ustawić następujące wartości w ustawieniach publiczną lub chronioną, rozszerzenie spowoduje odrzucenie wszystkich konfiguracji, gdzie poniższe wartości są ustawione w ustawieniach zarówno publiczne i chronione.

* `commandToExecute`

Za pomocą ustawień publicznego, może być przydatne do debugowania, ale zaleca się używanie chronione ustawienia.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdy skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, oznacza to, jeśli zostały zaszyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości przechowywane na maszynie Wirtualnej i jest używany do odszyfrowywania ustawienia (w razie potrzeby) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON, który jest szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do rozszerzenia niestandardowego skryptu są uruchamiane podczas wdrażania. Poniższe przykłady pokazują, jak za pomocą rozszerzenia niestandardowego skryptu:

* [Samouczek: Wdrażanie rozszerzenia maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Wdrażanie dwóch aplikacji warstwy na Windows i bazą danych Azure SQL](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzVMCustomScriptExtension` Polecenia można dodać rozszerzenie niestandardowego skryptu do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [AzVMCustomScriptExtension zestaw](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Dodatkowe przykłady

### <a name="using-multiple-scripts"></a>Używania wielu skryptów

W tym przykładzie masz trzy skrypty, które są używane do tworzenia serwera. **CommandToExecute** wywołuje pierwszy skrypt, można skorzystać z opcji na jak inne są wywoływane. Na przykład można mieć główny skrypt, który kontroluje wykonywanie, obsługa błędów w prawo, rejestrowanie i zarządzanie stanem. Skrypty są pobierane do komputera lokalnego do uruchamiania. Na przykład w `1_Add_Tools.ps1` możesz wywołać `2_Add_Features.ps1` , dodając `.\2_Add_Features.ps1` aby skrypt i powtórz ten proces dla innych skryptów należy zdefiniować w `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Uruchamianie skryptów z udziałem lokalnym

W tym przykładzie warto używać lokalnego serwera SMB dla Twojej lokalizacji skryptu. Dzięki temu nie trzeba podać inne ustawienia, z wyjątkiem **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak uruchomić skrypt niestandardowy więcej niż jeden raz z interfejsem wiersza polecenia

Jeśli chcesz więcej niż jeden raz uruchomić rozszerzenia niestandardowego skryptu, można wykonać tylko tę akcję w tych warunkach:

* Rozszerzenie **nazwa** parametru jest taka sama jak poprzedniego wdrożenia rozszerzenia.
* Zaktualizuj konfigurację, w przeciwnym razie nie będzie ponownie wykonać tego polecenia. W poleceniu, takich jak sygnaturę czasową, można dodać właściwości dynamicznych.

Alternatywnie, można ustawić [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) właściwości **true**.

### <a name="using-invoke-webrequest"></a>Za pomocą Invoke-WebRequest

Jeśli używasz [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) w skrypcie, należy określić parametr `-UseBasicParsing` lub w przeciwnym razie zostanie wyświetlony następujący błąd podczas sprawdzania szczegółowy stan:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>Klasyczne maszyny wirtualne

Aby wdrożyć rozszerzenie niestandardowego skryptu w klasycznych maszyn wirtualnych, można użyć witryny Azure portal lub poleceń cmdlet programu Azure PowerShell w klasycznym.

### <a name="azure-portal"></a>Azure Portal

Przejdź do zasobu klasycznej maszyny Wirtualnej. Wybierz **rozszerzenia** w obszarze **ustawienia**.

Kliknij przycisk **+ Dodaj** i na liście zasobów wybierz **rozszerzenia niestandardowego skryptu**.

Na **zainstalować rozszerzenie** stronie wybierz plik lokalny program PowerShell i wypełnij żadnych argumentów i kliknij **Ok**.

### <a name="powershell"></a>PowerShell

Użyj [AzureVMCustomScriptExtension zestaw](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) polecenia cmdlet można dodać rozszerzenie niestandardowego skryptu do istniejącej maszyny wirtualnej.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą modułu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Rozszerzenie wyjście jest rejestrowane plików znalezionych w następującym folderze na docelowej maszynie wirtualnej.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki są pobierane w następującym folderze na docelowej maszynie wirtualnej.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

gdzie `<n>` jest dziesiętną liczbą całkowitą, która może ulec zmianie między wykonaniami rozszerzenia.  `1.*` Wartość odpowiada bieżącej rzeczywiste, `typeHandlerVersion` wartość rozszerzenia.  Na przykład może być rzeczywista katalogu `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Podczas wykonywania `commandToExecute` polecenia rozszerzenia ustawia ten katalog (na przykład `...\Downloads\2`) jako bieżący katalog roboczy. Ten proces umożliwia użycie ścieżek względnych, aby zlokalizować pliki pobrane za pośrednictwem `fileURIs` właściwości. Zobacz tabelę poniżej przykłady.

Ponieważ ścieżka bezwzględna pobierania może różnić się od wraz z upływem czasu, to lepiej wybrać ścieżki względnej pliku skryptu/w `commandToExecute` string, jeśli to możliwe. Na przykład:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszy segment identyfikatora URI są przechowywane przez pliki pobrane za pośrednictwem `fileUris` listy właściwości.  Jak pokazano w poniższej tabeli, pobierane pliki są mapowane w podkatalogach pobierania do odzwierciedlenia struktury `fileUris` wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobrane pliki

| Identyfikator URI w fileUris | Względna lokalizacja pobranego | Bezwzględna pobrać lokalizacji <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> ścieżki bezwzględnej katalogów zmiany w okresie istnienia maszyny wirtualnej, ale nie w ramach pojedynczej operacji za pomocą rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Również mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
