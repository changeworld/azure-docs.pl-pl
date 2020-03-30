---
title: Rozszerzenie niestandardowego skryptu platformy Azure dla systemu Windows
description: Automatyzuj zadania konfiguracji maszyny Wirtualnej systemu Windows przy użyciu rozszerzenia Skrypt niestandardowy
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 698fab470cdc8b8d04fa4319fd71c31b58d1c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066889"
---
# <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla systemu Windows

Rozszerzenie skryptów niestandardowych pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań konfiguracyjnych lub zarządzania. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie skryptu niestandardowego integruje się z szablonami usługi Azure Resource Manager i można go uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, portalu Azure lub interfejsu API REST maszyny wirtualnej platformy Azure.

W tym dokumencie opisano, jak używać rozszerzenia skryptu niestandardowego przy użyciu modułu programu Azure PowerShell, szablonów usługi Azure Resource Manager i szczegółowych kroków rozwiązywania problemów w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie należy używać niestandardowego rozszerzenia skryptu do uruchamiania aktualizacji AzVM z tą samą maszyną wirtualną jako jej parametr, ponieważ będzie czekać na siebie.  

### <a name="operating-system"></a>System operacyjny

Niestandardowe rozszerzenie skryptu dla systemu Windows będzie działać na rozszerzeń obsługiwanych rozszerzenia, aby uzyskać więcej informacji, zobacz to [rozszerzenie Azure obsługiwane systemy operacyjne](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenie można skonfigurować tak, aby używało poświadczeń magazynu obiektów Blob platformy Azure w celu uzyskania dostępu do magazynu obiektów Blob platformy Azure. Lokalizacja skryptu może być w dowolnym miejscu, tak długo, jak maszyna wirtualna może kierować do tego punktu końcowego, takich jak GitHub lub wewnętrzny serwer plików.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli chcesz pobrać skrypt zewnętrznie, na przykład z usługi GitHub lub usługi Azure Storage, należy otworzyć dodatkowe porty zapory i sieciowej grupy zabezpieczeń. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG platformy Azure dla [magazynu](../../virtual-network/security-overview.md#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, nadal może być konieczne otwarcie dodatkowych portów zapory i sieciowej grupy zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik awaryjności dla tego rozszerzenia jest z powodu błędów składni w skrypcie, przetestować skrypt działa bez błędu, a także umieścić w dodatkowych logowania do skryptu, aby ułatwić znalezienie, gdzie nie powiodło się.
* Napisz skrypty, które są idempotentne. Gwarantuje to, że jeśli uruchomią się ponownie przypadkowo, nie spowoduje to zmian w systemie.
* Upewnij się, że skrypty nie wymagają udziału użytkownika w trakcie działania.
* Dozwolony czas uruchamiania skryptu wynosi 90 minut — każdy dłuższy czas spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie umieszczaj operacji ponownego uruchamiania wewnątrz skryptu; ta akcja spowoduje problemy z innymi instalowanymi rozszerzeniami. Po ponownym uruchomieniu rozszerzenie nie będzie kontynuować pracy, gdy zostanie uruchomione jeszcze raz.
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie, a następnie zainstalować aplikacje i uruchomić skrypty, można zaplanować ponowne uruchomienie przy użyciu zaplanowanego zadania systemu Windows lub użyć narzędzi, takich jak ROZSZERZENIA DSC, Chef lub rozszerzenia marionetki.
* Rozszerzenie uruchomi skrypt tylko raz. Jeśli chcesz uruchomić skrypt przy każdym rozruchu, musisz użyć rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* Jeśli chcesz zaplanować czas uruchomienia skryptu, użyj rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli potrzebujesz częstszych aktualizacji stanu działającego skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie Skryptu niestandardowego nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia do przesyłania plików, które obsługuje serwery proxy w skrypcie, takie jak *Curl*
* Pamiętaj, że skrypt lub polecenia mogą polegać na lokalizacjach katalogów innych niż domyślne. Przygotuj logikę obsługującą taką sytuację.
* Niestandardowe rozszerzenie skryptu będzie działać na koncie LocalSystem

## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja niestandardowego rozszerzenia skryptu określa takie elementy, jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można przechowywać w plikach konfiguracyjnych, określić ją w wierszu polecenia lub określić w szablonie usługi Azure Resource Manager.

Dane poufne można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywać tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako poufne dane i określone w konfiguracji ustawień chronionych rozszerzeniami. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> ManagedIdentity Właściwość **nie może** być używana w połączeniu z storageAccountName lub storageAccountKey właściwości

> [!NOTE]
> Tylko jedna wersja rozszerzenia może być zainstalowana na maszynie wirtualnej w momencie, określając skrypt niestandardowy dwa razy w tym samym szablonie Menedżera zasobów dla tej samej maszyny Wirtualnej zakończy się niepowodzeniem.

> [!NOTE]
> Możemy użyć tego schematu wewnątrz zasobu VirtualMachine lub jako autonomicznego zasobu. Nazwa zasobu musi być w tym formacie "virtualMachineName/extensionName", jeśli to rozszerzenie jest używane jako samodzielny zasób w szablonie ARM. 

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| wydawca | Microsoft.Compute | ciąg |
| type | CustomScriptExtension (Wysypienie kodu niestandardowego) | ciąg |
| typHandlerVersion | 1.10 | int |
| fileUris (np. | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tablica |
| sygnatura czasowa (np. | 123456789 | 32-bitowa całkowitej liczby |
| commandToExecute (np.) | powershell -ExecutionPolicy Nieograniczony -Plik configure-music-app.ps1 | ciąg |
| storageAccountName (np. | przykłady | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |
| zarządzaneTożerność (np. | { } lub { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } lub { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json obiekt |

>[!NOTE]
>W tych nazwach właściwości rozróżniana jest wielkość liter. Aby uniknąć problemów z wdrażaniem, należy użyć nazw, jak pokazano tutaj.

#### <a name="property-value-details"></a>Szczegóły wartości właściwości

* `commandToExecute`: (**wymagany**, ciąg) skrypt punktu wejścia do wykonania. Użyj tego pola, jeśli polecenie zawiera wpisy tajne, takie jak hasła lub fileUris są poufne.
* `fileUris`: (opcjonalnie, tablica ciągów) adresy URL dla plików do pobrania.
* `timestamp`(opcjonalna 32-bitowa liczba całkowita) służy do wyzwalania ponownego uruchomienia skryptu przez zmianę wartości tego pola.  Każda wartość całkowita jest dopuszczalna; musi być tylko inna niż poprzednia wartość.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. Jeśli określisz poświadczenia magazynu, wszystkie `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu
* `managedIdentity`: (opcjonalnie, obiekt json) [zarządzana tożsamość](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do pobierania plików
  * `clientId`: (opcjonalnie, ciąg) identyfikator klienta tożsamości zarządzanej
  * `objectId`: (opcjonalnie, ciąg) identyfikator obiektu tożsamości zarządzanej

Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, rozszerzenie odrzuci dowolną konfigurację, w której poniższe wartości są ustawione w ustawieniach publicznych i chronionych.

* `commandToExecute`

Za pomocą ustawień publicznych może przydatne do debugowania, ale zaleca się, aby użyć ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdzie skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie Wirtualnej w miarę ich wysyłania, oznacza to, że jeśli ustawienia zostały zaszyfrowane, są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (jeśli to konieczne) w czasie wykonywania.

####  <a name="property-managedidentity"></a>Właściwość: managedIdentity

Kod CustomScript (wersja 1.10) obsługuje [tożsamość zarządzaną](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do pobierania plików z adresów URL podanych w ustawieniu "fileUris". Umożliwia kod customscript dostęp do prywatnych obiektów blob usługi Azure Storage lub kontenerów bez konieczności przekazywania przez użytkownika wpisów tajnych, takich jak tokeny sygnatury dostępu współdzielonego lub klucze konta magazynu.

Aby korzystać z tej funkcji, użytkownik musi dodać tożsamość [przypisaną do systemu](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) lub tożsamości [przypisanej przez użytkownika](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) do maszyny Wirtualnej lub VMSS, w której ma zostać uruchomiony kod CustomScript, i [udzielić dostępu do tożsamości zarządzanej do kontenera lub obiektu blob usługi Azure Storage](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Aby użyć tożsamości przypisanej do systemu na docelowej maszynie Wirtualnej/VMSS, ustaw pole "zarządzana tożsamość" na pusty obiekt json. 

> Przykład:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Aby użyć tożsamości przypisanej przez użytkownika na docelowej maszynie Wirtualnej/VMSS, należy skonfigurować pole "zarządzana tożsamość" przy użyciu identyfikatora klienta lub identyfikatora obiektu tożsamości zarządzanej.

> Przykłady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> ManagedIdentity Właściwość **nie może** być używana w połączeniu z storageAccountName lub storageAccountKey właściwości

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON, który jest szczegółowo opisany w poprzedniej sekcji, może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia skryptu niestandardowego podczas wdrażania. Poniższe przykłady pokazują, jak używać rozszerzenia skryptu niestandardowego:

* [Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Wdrażanie aplikacji dwuwarstwowej w witrynie Windows i usługi Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Polecenia `Set-AzVMCustomScriptExtension` można użyć do dodania rozszerzenia Skrypt niestandardowy do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Więcej przykładów

### <a name="using-multiple-scripts"></a>Korzystanie z wielu skryptów

W tym przykładzie masz trzy skrypty, które są używane do tworzenia serwera. **CommandToExecute** wywołuje pierwszy skrypt, a następnie masz opcje, jak inne są wywoływane. Na przykład można mieć skrypt wzorca, który kontroluje wykonanie, z prawej obsługi błędów, rejestrowania i zarządzania stanem. Skrypty są pobierane do komputera lokalnego do uruchomienia. Na przykład `1_Add_Tools.ps1` w `2_Add_Features.ps1` wywołaniu `.\2_Add_Features.ps1` przez dodanie do skryptu i powtórzenie tego `$settings`procesu dla innych skryptów zdefiniowanych w pliku .

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
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Uruchamianie skryptów z udziału lokalnego

W tym przykładzie można użyć lokalnego serwera SMB dla lokalizacji skryptu. W ten sposób nie trzeba podawać żadnych innych ustawień, z wyjątkiem **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak uruchomić skrypt niestandardowy więcej niż jeden raz za pomocą interfejsu wiersza polecenia

Jeśli chcesz uruchomić rozszerzenie skryptu niestandardowego więcej niż jeden raz, możesz wykonać tę czynność tylko w następujących warunkach:

* Parametr **Nazwa** rozszerzenia jest taki sam jak poprzednie wdrożenie rozszerzenia.
* Zaktualizuj konfigurację, w przeciwnym razie polecenie nie zostanie ponownie wykonane. Do polecenia można dodać właściwość dynamiczną, taką jak sygnatura czasowa.

Alternatywnie można ustawić [właściwość ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) na **true**.

### <a name="using-invoke-webrequest"></a>Korzystanie z invoke-WebRequest

Jeśli używasz [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) w skrypcie, `-UseBasicParsing` należy określić parametr lub pojawi się następujący błąd podczas sprawdzania szczegółowego stanu:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Zestawy skali maszyn wirtualnych

Aby wdrożyć niestandardowe rozszerzenie skryptu w zestawie skalowania, zobacz [Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>Klasyczne maszyny wirtualne

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aby wdrożyć rozszerzenie skryptów niestandardowych na klasycznych maszynach wirtualnych, można użyć portalu Azure lub klasycznych poleceń cmdlet programu Azure PowerShell.

### <a name="azure-portal"></a>Portal Azure

Przejdź do zasobu klasycznej maszyny Wirtualnej. Wybierz **rozszerzenia** w obszarze **Ustawienia**.

Kliknij **przycisk + Dodaj** i na liście zasobów wybierz opcję **Niestandardowe rozszerzenie skryptu**.

Na stronie **Rozszerzenia Instalacyjny** zaznacz lokalny plik programu PowerShell, wypełnij wszystkie argumenty i kliknij przycisk **Ok**.

### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) można użyć do dodania rozszerzenia skryptu niestandardowego do istniejącej maszyny wirtualnej.

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

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane o stanie wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu modułu programu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Dane wyjściowe rozszerzenia są rejestrowane w plikach znalezionych w następującym folderze na docelowej maszynie wirtualnej.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki są pobierane do następującego folderu na docelowej maszynie wirtualnej.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

gdzie `<n>` jest dziesiętną liczeniem, który może ulec zmianie między wykonaniami rozszerzenia.  Wartość `1.*` jest zgodna z `typeHandlerVersion` rzeczywistą, bieżącą wartością rozszerzenia.  Na przykład rzeczywisty katalog `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`może być .  

Podczas wykonywania `commandToExecute` polecenia rozszerzenie ustawia ten katalog (na `...\Downloads\2`przykład) jako bieżący katalog roboczy. Ten proces umożliwia korzystanie z ścieżek względnych, `fileURIs` aby zlokalizować pliki pobrane za pośrednictwem właściwości. Zobacz poniższą tabelę, aby zapoznać się z przykładami.

Ponieważ bezwzględna ścieżka pobierania może się zmieniać w czasie, lepiej wybrać względne ścieżki skryptu/pliku w `commandToExecute` ciągu, gdy tylko jest to możliwe. Przykład:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszym segmencie identyfikatorów `fileUris` URI są przechowywane dla plików pobranych za pośrednictwem listy właściwości.  Jak pokazano w poniższej tabeli, pobrane pliki są mapowane do `fileUris` podkatalogów pobierania, aby odzwierciedlić strukturę wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobranych plików

| Identyfikator URI w plikuUris | Względna pobrana lokalizacja | Bezwzględna pobrana lokalizacja <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Bezwzględne ścieżki katalogów zmieniają się w okresie istnienia maszyny Wirtualnej, ale nie w ramach jednego wykonania rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
