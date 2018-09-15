---
title: Desired State Configuration dla platformy Azure — omówienie
description: Dowiedz się, jak używać programu obsługi rozszerzenia Microsoft Azure dla programu PowerShell Desired State Configuration (DSC). Artykuł zawiera wymagania wstępne, architektury oraz poleceń cmdlet.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
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
ms.author: robreed
ms.openlocfilehash: 18d6478763fd6551cc8baac6ea54e8d91f1a28e6
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629972"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Wprowadzenie do procedury obsługi rozszerzenia Azure Desired State Configuration

Agent maszyny Wirtualnej platformy Azure i skojarzonych rozszerzeń są częścią usługi infrastruktury Microsoft Azure. Składniki oprogramowania, które rozszerzyć funkcjonalność maszyny Wirtualnej i uprościć różnych operacji zarządzania maszyny Wirtualnej są rozszerzenia maszyny Wirtualnej.

Główny przypadek użycia, rozszerzenie Azure Desired State Configuration (DSC) jest uruchomienie maszyny Wirtualnej w celu [usługi Azure Automation DSC](../../automation/automation-dsc-overview.md). Uruchamianie maszyny Wirtualnej zapewnia [korzyści](/powershell/dsc/metaconfig#pull-service) zawierające bieżące zarządzanie konfiguracji maszyny Wirtualnej i integracji z innymi narzędziami operacyjne, takie jak monitorowania platformy Azure.

Można użyć rozszerzenia DSC, niezależnie od usługi Automation DSC. Jednak wymaga pojedynczej akcji, która występuje podczas wdrażania. Trwającą raportowania lub Zarządzanie konfiguracją jest dostępna, innym niż lokalnie na maszynie Wirtualnej.

Ten artykuł zawiera informacje o obu scenariuszach: za pomocą rozszerzenia DSC do dołączenia do usługi Automation, a za pomocą rozszerzenia DSC jako narzędzie do przypisywania konfiguracje do maszyn wirtualnych przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- **Maszyna lokalna**: aby wchodzić w interakcje z rozszerzeniem maszyny Wirtualnej platformy Azure, należy użyć witryny Azure portal lub programu Azure PowerShell SDK.
- **Agent gościa**: maszyna wirtualna platformy Azure, który jest skonfigurowany za pomocą konfiguracji DSC musi być system operacyjny obsługujący zarządzania Windows Framework (WMF) 4.0 lub nowszy. Aby uzyskać pełną listę obsługiwanych wersji systemu operacyjnego, zobacz [historię wersji rozszerzenia DSC](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Terminy i pojęcia

W tym przewodniku założono znajomość następujących pojęć:

- **Konfiguracja**: dokumentu konfiguracji DSC.
- **Węzeł**: docelowy dla konfiguracji DSC. W tym dokumencie *węzła* zawsze odwołuje się do maszyny Wirtualnej platformy Azure.
- **Dane konfiguracji**: plik psd1, który ma wpływ dane konfiguracji.

## <a name="architecture"></a>Architektura

Rozszerzenie DSC usługi Azure używa framework agenta maszyny Wirtualnej platformy Azure do dostarczania, wprowadź w życie i sporządzić raport na temat konfiguracji DSC uruchomionych na maszynach wirtualnych platformy Azure. Rozszerzenie DSC akceptuje dokumentu konfiguracji, jak i zestaw parametrów. Jeśli plik nie zostanie podany, [domyślne skryptu konfiguracji](#default-configuration-script) jest osadzony z rozszerzeniem. Skrypt konfiguracji domyślnej jest używana tylko po to, aby ustawić metadanych w [Local Configuration Manager](/powershell/dsc/metaconfig).

Gdy rozszerzenia jest wywoływana po raz pierwszy, instaluje wersję platformy WMF za pomocą następujących reguł:

- System operacyjny maszyny Wirtualnej platformy Azure w przypadku systemu Windows Server 2016, nie podjęto żadnej akcji. System Windows Server 2016 ma już najnowszą wersję programu PowerShell jest zainstalowane.
- Jeśli **wmfVersion** właściwość zostanie określona, tej wersji programu WMF jest zainstalowany, chyba że ta wersja jest niezgodna z systemem operacyjnym maszyny Wirtualnej.
- Jeśli nie **wmfVersion** właściwość zostanie określona, jest zainstalowany najnowszy odpowiedniej wersji programu WMF.

Instalowanie programu WMF wymaga ponownego uruchomienia komputera. Po ponownym uruchomieniu komputera, rozszerzenie pliki do pobrania pliku zip, który jest określony w **modulesUrl** właściwość, jeśli podano. Jeśli ta lokalizacja znajduje się w usłudze Azure Blob storage, możesz określić token sygnatury dostępu Współdzielonego w **sasToken** właściwości dostępu do tego pliku. Po pobraniu i rozpakowania .zip, funkcja konfiguracji jest zdefiniowany w **configurationFunction** przebiegów, aby wygenerować plik MOF. Następnie rozszerzenie uruchamia `Start-DscConfiguration -Force` przy użyciu pliku MOF wygenerowany. Rozszerzenie przechwytuje dane wyjściowe i zapisuje go w kanale stanu platformy Azure.

### <a name="default-configuration-script"></a>Skrypt konfiguracji domyślnej

Rozszerzenie DSC usługi Azure zawiera domyślny skrypt konfiguracyjny, który ma być używany podczas Ci przy dołączeniu maszyny Wirtualnej do usługi Azure Automation DSC. Parametry skryptu są wyrównane konfigurowalne właściwości [Local Configuration Manager](/powershell/dsc/metaconfig). Aby uzyskać Parametry skryptu, zobacz [domyślne skryptu konfiguracji](dsc-template.md#default-configuration-script) w [Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager](dsc-template.md). Aby uzyskać pełny skrypt, zobacz [szablonu szybkiego startu platformy Azure w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozszerzenie DSC w szablonach usługi Resource Manager

W większości przypadków szablony wdrażania usługi Resource Manager są oczekiwane sposób pracy z rozszerzeniem DSC. Aby uzyskać więcej informacji i przykłady, które mają zostać objęte rozszerzenia DSC szablony wdrażania usługi Resource Manager, zobacz [Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rozszerzenie DSC poleceń cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, które są używane do zarządzania rozszerzenie DSC najlepiej sprawdzają się w interaktywne rozwiązywania problemów i scenariuszy zbierania informacji. Można użyć poleceń cmdlet pakietu, opublikować i monitorować wdrożenia rozszerzenia DSC. Polecenia cmdlet dla rozszerzenia DSC nie są jeszcze zaktualizowane do pracy z [domyślne skryptu konfiguracji](#default-configuration-script).

**AzureRmVMDscConfiguration Publikuj** polecenie cmdlet przyjmuje w pliku konfiguracji, szuka zależnych zasobów DSC, a następnie tworzy plik zip. Plik zip zawiera plik konfiguracji i zasobów DSC, które są wymagane do konfiguracji wprowadź w życie. Polecenia cmdlet można również utworzyć pakiet lokalnie, używając *- OutputArchivePath* parametru. W przeciwnym razie polecenie cmdlet publikuje pliku .zip do magazynu obiektów blob i zabezpiecza go przy użyciu tokenu sygnatury dostępu Współdzielonego.

Skrypt konfiguracyjny ps1, który tworzy polecenie cmdlet znajduje się w pliku zip w katalogu głównym folderu archiwum. Folder modułu jest umieszczana w folderze archiwum w ramach zasobów.

**AzureRmVMDscExtension zestaw** polecenia cmdlet wprowadza ustawienia, które wymaga rozszerzenia DSC programu PowerShell do obiektu konfiguracji maszyny Wirtualnej.

**Get AzureRmVMDscExtension** polecenie cmdlet pobiera stan rozszerzenia DSC określonej maszyny Wirtualnej.

**Get AzureRmVMDscExtensionStatus** polecenie cmdlet pobiera stan konfiguracji DSC, która jest wprowadzany przez program obsługi rozszerzenia DSC. Tę akcję można wykonać na jednej maszynie Wirtualnej lub grupy maszyn wirtualnych.

**AzureRmVMDscExtension Usuń** polecenie cmdlet usuwa procedury obsługi rozszerzenia z określonej maszyny Wirtualnej. To polecenie cmdlet jest *nie* usunąć konfigurację, odinstaluj program WMF lub zmienić ustawienia zastosowane na maszynie Wirtualnej. Powoduje tylko usunięcie procedury obsługi rozszerzenia. 

Ważne informacje na temat poleceń cmdlet rozszerzenia DSC usługi Resource Manager:

- Polecenia cmdlet usługi Azure Resource Manager są synchroniczne.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *wersji*, i *lokalizacji*wszystkich wymaganych parametrów.
- *ArchiveResourceGroupName* jest parametrem opcjonalnym. Ten parametr można określić, kiedy konta magazynu należy do innej grupie zasobów niż ta, której tworzona jest maszyna wirtualna.
- Użyj **AutoUpdate** przełącznika do automatycznego aktualizowania procedury obsługi rozszerzenia do najnowszej wersji, gdy będzie ona dostępna. Ten parametr może potencjalnie spowodować ponowne uruchomienie na maszynie Wirtualnej, po wydaniu nowej wersji programu WMF.

### <a name="get-started-with-cmdlets"></a>Wprowadzenie do poleceń cmdlet

Rozszerzenie DSC usługi Azure może być bezpośrednio skonfigurować maszyn wirtualnych platformy Azure podczas wdrażania dokumentów konfiguracji DSC. Ten krok nie Zarejestruj węzeł do automatyzacji. Ten węzeł jest *nie* centralnie zarządzanych.

Poniższy przykład pokazuje prosty przykład konfiguracji. Zapisz konfigurację lokalnie jako IisInstall.ps1.

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

Następujące polecenia umieścić skrypt IisInstall.ps1 na określoną maszynę Wirtualną. Polecenia również wykonać konfiguracji, a następnie raportować o stanie.

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

## <a name="azure-portal-functionality"></a>Funkcjonalność portalu platformy Azure

Aby zdefiniować DSC w portalu

1. Przejdź do maszyny Wirtualnej.
2. W obszarze **Ustawienia** wybierz pozycję **Rozszerzenia**.
3. Nowa strona jest tworzona, wybierz **+ Dodaj**, a następnie wybierz pozycję **PowerShell Desired State Configuration**.
4. Kliknij przycisk **Utwórz** w dolnej części strony informacji rozszerzenia.

Portal zbiera następujące dane wejściowe:

- **Moduły konfiguracji lub skrypt**: to pole jest obowiązkowe (formularza nie zostało zaktualizowane dla [domyślne skryptu konfiguracji](#default-configuration-script)). Konfiguracja modułów i skryptów wymagają pliku .ps1, który zawiera skrypt konfiguracji lub plik zip ze skryptem konfiguracji .ps1 w katalogu głównym. Jeśli używasz pliku zip, wszystkimi zasobami zależnymi musi zawierać moduł folderów .zip. Można utworzyć pliku .zip, za pomocą **Publish AzureVMDscConfiguration - OutputArchivePath** polecenia cmdlet, który znajduje się w zestawie SDK programu PowerShell platformy Azure. Plik zip jest przekazywane do magazynu obiektów blob użytkownika i chronione przez tokenu sygnatury dostępu Współdzielonego.

- **Modułu kwalifikowaną nazwę konfiguracji**: może zawierać wiele funkcji konfiguracji w pliku .ps1. Wprowadź nazwę skryptu .ps1 konfiguracji, a następnie \\ i nazwą funkcji konfiguracji. Na przykład, jeśli skryptu .ps1 ma configuration.ps1 nazwy i konfiguracji jest **IisInstall**, wprowadź **configuration.ps1\IisInstall**.

- **Argumenty konfiguracji**: Jeśli funkcja Konfiguracja przyjmuje argumenty, należy wprowadzić je w tym miejscu w formacie **argumentName1 = wartość1, argumentName2 = wartość2**. Ten format jest inny format, w której argumenty konfiguracji są akceptowane w poleceń cmdlet programu PowerShell lub szablonów usługi Resource Manager.

- **Plik PSD1 danych konfiguracji**: to pole jest opcjonalne. Jeśli konfiguracja wymaga pliku danych konfiguracji w psd1, to pole służy do wybierz pole danych, a następnie przekaż go do magazynu obiektów blob użytkownika. Plik danych konfiguracji jest zabezpieczony przez tokenu sygnatury dostępu Współdzielonego w magazynie obiektów blob.

- **Wersja WMF**: Określa wersję programu Windows Management Framework (WMF), ma być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości najnowsze instaluje najnowszą wersję programu WMF. Obecnie jedyną możliwą wartości dla tej właściwości są 4.0, 5.0, 5.1 i najnowszych. Te wartości możliwe jest zależna od aktualizacji. Wartość domyślna to **najnowsze**.

- **Zbieranie danych**: Określa, jeśli rozszerzenie zbierać dane telemetryczne. Aby uzyskać więcej informacji, zobacz [zbierania danych rozszerzenia DSC usługi Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Wersja**: Określa numer wersji rozszerzenia DSC do zainstalowania. Aby uzyskać informacje o wersji, zobacz [historię wersji rozszerzenia DSC](/powershell/dsc/azuredscexthistory).

- **Automatyczne uaktualnienie wersji pomocniczej**: to pole mapuje **AutoUpdate** przełącznika w poleceniach cmdlet i umożliwia rozszerzenie Aby automatycznie zaktualizowana do najnowszej wersji podczas instalacji. **Tak** zleca procedury obsługi rozszerzenia do użycia najnowszej dostępnej wersji i **nie** wymusi **wersji** określony do zainstalowania. Wybieranie ani **tak** ani **nie** jest taka sama, jak wybór **nie**.

## <a name="logs"></a>Dzienniki

Dzienniki dla rozszerzenia są przechowywane w następującej lokalizacji: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
- Sprawdź [szablonu usługi Resource Manager dla rozszerzenia DSC](dsc-template.md).
- Przeglądaj więcej funkcji, w którym można zarządzać za pomocą programu PowerShell DSC, a inne zasoby DSC, [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Aby uzyskać szczegółowe informacje o przekazywaniu parametrów poufnych w konfiguracji, zobacz [Zarządzanie poświadczeniami w bezpieczny sposób za pomocą procedury obsługi rozszerzenia DSC](dsc-credentials.md).