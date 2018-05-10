---
title: Konfiguracją żądanego stanu dla platformy Azure — omówienie
description: Dowiedz się, jak używać programu obsługi rozszerzenia Microsoft Azure dla programu PowerShell żądanego stanu konfiguracji (DSC). Artykuł zawiera wymagania wstępne, architektura i polecenia cmdlet.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 356356288823c86c77e46fdfba2cf81a7ef11e0d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Wprowadzenie do obsługi rozszerzenia konfiguracji żądanego stanu usługi Azure

Agent maszyny Wirtualnej i skojarzonych rozszerzeń są częścią usługi infrastruktury platformy Microsoft Azure. Rozszerzenia maszyny Wirtualnej są składniki oprogramowania, które rozszerzyć funkcjonalność maszyny Wirtualnej i uproszczenia różne operacje zarządzania maszyny Wirtualnej.

Przypadek użycia serwera podstawowego do rozszerzenia konfiguracji żądanego stanu Azure (DSC) bootstrap maszyny Wirtualnej, aby [usługi Konfiguracja DSC automatyzacji Azure](../../automation/automation-dsc-overview.md). Uruchamianie maszyn wirtualnych zapewnia [korzyści](/powershell/dsc/metaconfig#pull-service) zawierające bieżące zarządzanie konfiguracji maszyny Wirtualnej i integracja z innymi narzędziami operacyjne, takie jak monitorowania Azure.

Można użyć rozszerzenia DSC niezależnie od usługi Konfiguracja DSC automatyzacji. Jednak wymaga pojedynczej akcji, która występuje podczas wdrażania. Stałe raportowania lub Zarządzanie konfiguracją są niedostępne, innych niż lokalnie w maszynie Wirtualnej.

Ten artykuł zawiera informacje o scenariuszach zarówno: przy użyciu rozszerzenia usługi Konfiguracja DSC do dołączenia do automatyzacji i przy użyciu rozszerzenia usługi Konfiguracja DSC jako narzędzie dla przypisywanie konfiguracji do maszyn wirtualnych przy użyciu zestawu SDK platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- **Komputer lokalny**: Aby pracować interaktywnie z rozszerzenia maszyny Wirtualnej Azure, należy użyć portalu Azure lub zestawu SDK programu PowerShell usługi Azure.
- **Agent gościa**: Azure maszyny Wirtualnej skonfigurowaną przy konfiguracji DSC musi być systemu operacyjnego, który obsługuje Windows Management Framework (WMF) 4.0 lub nowszy. Aby uzyskać pełną listę obsługiwanych wersji systemu operacyjnego, zobacz [Historia wersji rozszerzenia DSC](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Definicje terminów i pojęć

W tym przewodniku założono znajomość następujące kwestie:

- **Konfiguracja**: dokument konfiguracji A DSC.
- **Węzeł**: miejsce docelowe dla konfiguracji DSC. W tym dokumencie *węzła* zawsze odwołuje się do maszyny Wirtualnej platformy Azure.
- **Dane konfiguracji**: plik psd1, który ma wpływ danych konfiguracji.

## <a name="architecture"></a>Architektura

Rozszerzenia usługi Konfiguracja DSC Azure używa framework Agent maszyny Wirtualnej do dostarczania, wprowadza i raport o konfiguracji DSC uruchomionych na maszynach wirtualnych platformy Azure. Rozszerzenia DSC akceptuje dokument konfiguracji i zestaw parametrów. Jeśli plik nie zostanie podany, [domyślne skryptu konfiguracji](#default-configuration-script) jest osadzony z rozszerzeniem. Skrypt konfiguracji domyślnej jest używana tylko w celu określenia metadanych [lokalny program Configuration Manager](/powershell/dsc/metaconfig).

Jeśli rozszerzenie jest wywoływana po raz pierwszy, instaluje wersję programu WMF za pomocą następujących reguł:

- Jeśli system operacyjny maszyny Wirtualnej Azure jest Windows Server 2016, nie podjęto żadnej akcji. Windows Server 2016 ma już najnowszą wersję programu PowerShell jest zainstalowane.
- Jeśli **wmfVersion** właściwość zostanie określony, ta wersja platformy WMF jest zainstalowany, chyba że ta wersja jest niezgodna z systemem operacyjnym maszyny Wirtualnej.
- Jeśli nie **wmfVersion** określona właściwość, jest zainstalowana najnowsza wersja dotyczy wersja programu WMF.

Instalowanie WMF wymaga ponownego uruchomienia. Po ponownym uruchomieniu, rozszerzenie pobiera plik zip, który jest określony w **modulesUrl** właściwości, jeśli zostanie podana. Jeśli ta lokalizacja znajduje się w magazynie obiektów Blob platformy Azure, możesz określić tokenu sygnatury dostępu Współdzielonego w **sasToken** właściwości dostępu do tego pliku. Po pobraniu zip i rozpakowane, funkcja konfiguracji zdefiniowana w **configurationFunction** działa, aby wygenerować plik MOF. Następnie uruchamia rozszerzenia `Start-DscConfiguration -Force` przy użyciu pliku MOF wygenerowany. Rozszerzenie przechwytuje dane wyjściowe i zapisuje go w kanale Azure stanu.

### <a name="default-configuration-script"></a>Skrypt konfiguracji domyślnej

Rozszerzenia usługi Konfiguracja DSC Azure obejmuje skrypt konfiguracji domyślnej, który ma być użyta, gdy ci przy dołączeniu do usługi Konfiguracja DSC automatyzacji Azure maszyny Wirtualnej. Parametry skryptu są zgodne z można skonfigurować właściwości [lokalny program Configuration Manager](/powershell/dsc/metaconfig). Parametry skryptu, zobacz [domyślne skryptu konfiguracji](extensions-dsc-template.md#default-configuration-script) w [konfiguracji żądanego stanu rozszerzenia przy użyciu szablonów usługi Azure Resource Manager](extensions-dsc-template.md). Pełna skryptu, zobacz [szablonów Szybki Start Azure w serwisie GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozszerzenia usługi Konfiguracja DSC w szablonach usługi Resource Manager

W większości przypadków szablony wdrażania Menedżera zasobów są oczekiwane sposób pracy z rozszerzenia usługi Konfiguracja DSC. Aby uzyskać więcej informacji oraz przykłady obejmują rozszerzenia usługi Konfiguracja DSC w szablonach wdrożenia usługi Resource Manager, zobacz [konfiguracji żądanego stanu rozszerzenia przy użyciu szablonów usługi Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rozszerzenia DSC poleceń cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, które są używane do zarządzania rozszerzenia DSC najlepiej są używane w interakcyjne Rozwiązywanie problemów i scenariusze zbierania informacji. Poleceń cmdlet służy do pakietu, publikowanie i monitorować wdrożenia rozszerzenia usługi Konfiguracja DSC. Polecenia cmdlet dla rozszerzenia DSC nie są jeszcze zaktualizowany do pracy z [domyślne skryptu konfiguracji](#default-configuration-script).

**AzureRmVMDscConfiguration publikowania** polecenie cmdlet ma w pliku konfiguracji, szuka zasoby zależne DSC, a następnie tworzy plik zip. Plik zip zawiera konfigurację i zasoby niezbędne wprowadzenie konfiguracji DSC. Polecenia cmdlet można również utworzyć pakiet lokalnie, używając *- OutputArchivePath* parametru. W przeciwnym razie polecenie cmdlet publikuje plik zip do magazynu obiektów blob, a następnie zabezpiecza z tokenem sygnatury dostępu Współdzielonego.

Skrypt konfiguracji .ps1, który tworzy polecenia cmdlet znajduje się w pliku zip w katalogu głównym folderu archiwum. Folder moduł znajduje się w folderze archiwum w zasobach.

**AzureRmVMDscExtension zestaw** polecenia cmdlet injects ustawienia, które wymaga rozszerzenia DSC środowiska PowerShell do obiektu konfiguracji maszyny Wirtualnej.

**Get AzureRmVMDscExtension** polecenie cmdlet pobiera stan rozszerzenia usługi Konfiguracja DSC określonej maszyny Wirtualnej.

**Get AzureRmVMDscExtensionStatus** polecenie cmdlet pobiera stan konfiguracji DSC jest wprowadzany przez program obsługi rozszerzenia usługi Konfiguracja DSC. Tę akcję można wykonać na jednej maszynie Wirtualnej lub w grupie maszyn wirtualnych.

**AzureRmVMDscExtension Usuń** polecenie cmdlet usuwa rozszerzenia obsługi z określonej maszyny Wirtualnej. To polecenie cmdlet jest *nie* Usuń konfigurację, odinstaluj WMF lub zmienić ustawienia zastosowane na maszynie Wirtualnej. Powoduje usunięcie tylko obsługi rozszerzenia. 

Ważne informacje o poleceniach cmdlet rozszerzenia usługi Konfiguracja DSC usługi Resource Manager:

- Polecenia cmdlet Menedżera zasobów systemu Azure są synchroniczne.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *wersji*, i *lokalizacji*są wszystkie wymagane parametry.
- *ArchiveResourceGroupName* jest parametrem opcjonalnym. Można określić tego parametru, jeśli Twoje konto magazynu należy do innej grupie zasobów niż miejsce tworzenia maszyny Wirtualnej.
- Użyj **AutoUpdate** przełącznika automatycznie zaktualizować obsługi rozszerzenia do najnowszej wersji, gdy jest ona dostępna. Ten parametr może potencjalnie spowodować ponowne uruchomienie na maszynie Wirtualnej po wydaniu nowej wersji programu WMF.

### <a name="get-started-with-cmdlets"></a>Wprowadzenie do poleceń cmdlet

Rozszerzenia usługi Konfiguracja DSC Azure umożliwia dokumenty konfiguracji DSC bezpośrednio skonfigurować podczas wdrażania maszyn wirtualnych platformy Azure. Ten krok nie zarejestrować węzła do automatyzacji. Ten węzeł jest *nie* centralnie zarządzanego.

Poniższy przykład przedstawia prosty przykład konfiguracji. Zapisz konfigurację lokalnie jako IisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Następujące polecenia Umieść skrypt IisInstall.ps1 na określoną maszynę Wirtualną. Polecenia również wykonać konfigurację, a następnie wysyłać raporty o stanie.

```powershell
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate $true -ConfigurationName 'IISInstall'
```

## <a name="azure-portal-functionality"></a>Funkcje portalu Azure

Aby skonfigurować usługi Konfiguracja DSC w portalu:

1. Przejdź do maszyny Wirtualnej.
2. W obszarze **ustawienia**, wybierz pozycję **rozszerzenia**.
3. Nowa strona, która jest tworzona, wybierz **+ Dodaj**, a następnie wybierz **konfiguracji żądanego stanu środowiska PowerShell**.
4. Kliknij przycisk **Utwórz** w dolnej części strony informacji rozszerzenia.

Portalu zbiera następujące dane wejściowe:

- **Moduły konfiguracji lub skryptu**: to pole jest obowiązkowe (formularza nie został jeszcze zaktualizowany do [domyślne skryptu konfiguracji](#default-configuration-script)). Moduły konfiguracji i skrypty wymagają pliku .ps1, który zawiera skrypt konfiguracji lub przy użyciu skryptu konfiguracji .ps1 w katalogu głównym pliku zip. Jeśli plik zip, wszystkimi zasobami zależnymi musi być uwzględniona w folderach modułu w zip. Można utworzyć pliku zip, używając **Publish AzureVMDscConfiguration - OutputArchivePath** polecenia cmdlet, który znajduje się w zestawie SDK Azure PowerShell. Plik zip jest przekazywane do usługi magazynu obiektów blob użytkownika i zabezpieczone przez tokenu sygnatury dostępu Współdzielonego.

- **Kwalifikowanej nazwy konfiguracji**: mogą zawierać wiele funkcji konfiguracyjnych w pliku .ps1. Wprowadź nazwę skryptu ps1 konfiguracji, a następnie \\ i nazwą funkcji konfiguracji. Na przykład, jeśli skrypt ps1 ma configuration.ps1 nazwy i konfiguracja jest **IisInstall**, wprowadź **configuration.ps1\IisInstall**.

- **Argumenty konfiguracji**: Jeśli funkcja Konfiguracja przyjmuje argumenty, wprowadź je tutaj w formacie **argumentName1 = wartość1, argumentName2 = wartość2**. Ten format jest inny format akceptowała konfiguracji argumenty polecenia cmdlet programu PowerShell lub szablony Menedżera zasobów.

- **Plik PSD1 danych konfiguracji**: to pole jest opcjonalne. Jeśli konfiguracja wymaga plik danych konfiguracji w psd1, użyj tego pola, aby wybrać pola danych i przekaż go do magazynu obiektów blob użytkownika. Plik danych konfiguracji jest zabezpieczony przez tokenu sygnatury dostępu Współdzielonego w magazynie obiektów blob.

- **Wersja platformy WMF**: Określa wersję programu Windows Management Framework (WMF), który powinien być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości najnowsze instaluje najnowszą wersję programu WMF. Obecnie są to tylko możliwe wartości dla tej właściwości, 4.0, 5.0, 5.1 i najnowszych. Te wartości można podlegają aktualizacji. Wartość domyślna to **najnowsze**.

- **Zbieranie danych**: Określa, czy rozszerzenia będzie zbierać dane telemetryczne. Aby uzyskać więcej informacji, zobacz [zbierania danych rozszerzenia usługi Konfiguracja DSC Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Wersja**: Określa wersję rozszerzenia usługi Konfiguracja DSC do zainstalowania. Aby uzyskać informacje o wersji, zobacz [Historia wersji rozszerzenia DSC](/powershell/dsc/azuredscexthistory).

- **Automatyczne uaktualnienie wersji pomocniczej**: to pole jest mapowany na **AutoUpdate** przełącznika w polecenia cmdlet i umożliwia rozszerzenie automatycznie zaktualizowane do najnowszej wersji podczas instalacji. **Tak** wyśle instrukcje do obsługi rozszerzenia, aby użyć najnowszej dostępnej wersji i **nr** wymusi **wersji** określony do zainstalowania. Zaznaczenie nie **tak** ani **nr** jest taka sama jak wybranie **nr**.

## <a name="logs"></a>Dzienniki

Dzienniki rozszerzenia są przechowywane w następującej lokalizacji: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji o konfiguracji DSC środowiska PowerShell, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
- Sprawdź [szablonu usługi Resource Manager dla rozszerzenia DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Więcej funkcji, którą można zarządzać za pomocą usługi Konfiguracja DSC środowiska PowerShell, a więcej zasobów DSC, Przeglądaj [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Szczegółowe informacje na temat przekazywania poufnych parametrów w konfiguracji, zobacz [Zarządzanie poświadczenia bezpiecznie z obsługą rozszerzenia DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).