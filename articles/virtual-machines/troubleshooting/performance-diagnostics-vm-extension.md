---
title: Rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure dla systemu Windows| Dokumenty firmy Microsoft
description: Wprowadza rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure dla systemu Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057547"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure dla systemu Windows

Rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure pomaga zbierać dane diagnostyczne wydajności z maszyn wirtualnych systemu Windows. Rozszerzenie wykonuje analizę i zawiera raport z ustaleń i zaleceń do identyfikowania i rozwiązywania problemów z wydajnością na maszynie wirtualnej. To rozszerzenie instaluje narzędzie do rozwiązywania problemów o nazwie [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Jeśli chcesz uruchomić diagnostykę na maszynie wirtualnej z witryny Azure portal dla maszyn wirtualnych innych niż klasyczne, zaleca się użycie nowego środowiska. Aby uzyskać więcej informacji, zobacz [Diagnostyka wydajności dla maszyn wirtualnych platformy Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Wymagania wstępne

To rozszerzenie można zainstalować w systemach Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Można go również zainstalować w systemach Windows 8.1 i Windows 10.

## <a name="extension-schema"></a>Schemat rozszerzenia
Poniższy JSON pokazuje schemat rozszerzenia maszyny wirtualnej diagnostyki wydajności platformy Azure. To rozszerzenie wymaga nazwy i klucza dla konta magazynu do przechowywania danych wyjściowych diagnostyki i raportu. Te wartości są wrażliwe. Klucz konta magazynu powinien być przechowywany w konfiguracji ustawień chronionych. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i są odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy zauważyć, **że storageAccountName** i **storageAccountKey** są rozróżniane. Inne wymagane parametry są wymienione w poniższej sekcji.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Wartości właściwości

|   **Nazwa**   |**Wartość / Przykład**|       **Opis**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Wersja interfejsu API.
|wydawca|Microsoft.Azure.Performance.Diagnostics|Obszar nazw wydawcy dla rozszerzenia.
|type|Diagnostyka azureperformance|Typ rozszerzenia maszyny Wirtualnej.
|typHandlerVersion|1.0|Wersja programu obsługi rozszerzeń.
|performanceScenario|Podstawowe|Scenariusz wydajności, dla którego mają być przechwytywać dane. Prawidłowe wartości to: **basic**, **vmslow**, **azurefiles**i **custom**.
|traceDurationInSekunds|300|Czas trwania śladów, jeśli zaznaczone są opcje śledzenia.
|perfCounterTrace (perfCounterTrace)|p|Opcja, aby włączyć śledzenie licznika wydajności. Prawidłowe wartości to **wartość p** lub pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość jako pustą.
|SiećTrace|n|Opcja włączania śledzenia sieci. Prawidłowe wartości to **wartość n** lub pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość jako pustą.
|xperfTrace (krytyka xperftrace)|x|Opcja, aby włączyć XPerf Trace. Prawidłowe wartości to wartość **x** lub pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość jako pustą.
|storPortTrace|s|Opcja włączenia śledzenia StorPort. Prawidłowe wartości to **s** lub pusta wartość. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość jako pustą.
|numer srNumer|123452016365929|Numer biletu pomocy technicznej, jeśli jest dostępny. Pozostaw wartość jako pustą, jeśli jej nie masz.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Bieżąca data w utc. Jeśli używasz portalu do zainstalowania tego rozszerzenia, nie trzeba podawać tej wartości.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Unikatowy identyfikator maszyny Wirtualnej.
|storageAccountName|mystorageaccount|Nazwa konta magazynu do przechowywania dzienników diagnostycznych i wyników.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc==|Klucz dla konta magazynu.

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Postępuj zgodnie z poniższymi instrukcjami, aby zainstalować rozszerzenie na maszynach wirtualnych systemu Windows:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz maszynę wirtualną, na której chcesz zainstalować to rozszerzenie.

    ![Zrzut ekranu przedstawiający witrynę Azure portal z wyróżnioną pozycją Maszyn wirtualnych](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Wybierz ostrze **Rozszerzenia** i wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający ostrze Rozszerzenia z wyróżnionym przyciskiem Dodaj](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Wybierz **pozycję Diagnostyka wydajności platformy Azure,** przejrzyj warunki i wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający nowy ekran zasobów z wyróżnioną wyróżnioną diagnostyką wydajności platformy Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Podaj wartości parametrów instalacji i wybierz **przycisk OK,** aby zainstalować rozszerzenie. Aby uzyskać więcej informacji na temat obsługiwanych scenariuszy, zobacz [Jak używać perfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Zrzut ekranu przedstawiający okno dialogowe Instalowanie rozszerzenia](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Po pomyślnym zakończeniu instalacji zostanie wyświetlony komunikat informujący o tym stanie.

    ![Zrzut ekranu przedstawiający komunikat po pomyślnym zainicjowaniu obsługi administracyjnej](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Rozszerzenie jest uruchamiane po pomyślnym zainicjowaniu obsługi administracyjnej. Trwa dwie minuty lub mniej, aby zakończyć dla scenariusza podstawowego. W przypadku innych scenariuszy przebiega przez czas określony podczas instalacji.

## <a name="remove-the-extension"></a>Usuwanie rozszerzenia
Aby usunąć rozszerzenie z maszyny wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz maszynę wirtualną, z której chcesz usunąć to rozszerzenie, a następnie wybierz **rozszerzeń** bloku. 
2. Wybierz (**...**) dla rozszerzenia diagnostyki wydajności wpis z listy i wybierz przycisk **Odinstaluj**.

    ![Zrzut ekranu przedstawiający ostrze Rozszerzenia z wyróżnioną podświetleną odinstaluj](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Można również wybrać wpis rozszerzenia i wybrać opcję **Odinstaluj.**

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager. Spowoduje to wykonanie rozszerzenia maszyny Wirtualnej diagnostyki wydajności platformy Azure podczas wdrażania szablonu usługi Azure Resource Manager. Oto przykładowy szablon:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell
Polecenie `Set-AzVMExtension` może służyć do wdrażania rozszerzenia maszyny Wirtualnej diagnostyki wydajności platformy Azure na istniejącej maszynie wirtualnej.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informacje o przechwyconych danych
Narzędzie PerfInsights zbiera różne dzienniki, konfigurację i dane diagnostyczne, w zależności od wybranego scenariusza. Aby uzyskać więcej informacji, zobacz [dokumentację PerfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Wyświetlanie i udostępnianie wyników

Dane wyjściowe z rozszerzenia można znaleźć w pliku zip, który został przekazany do konta magazynu określonego podczas instalacji i jest udostępniany przez 30 dni przy użyciu [sygnatur dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Ten plik zip zawiera dzienniki diagnostyczne i raport z ustaleniami i zaleceniami. Łącze Sygnatury dostępu Współdzielonego do wyjściowego pliku zip można znaleźć w pliku tekstowym o nazwie *zipfilename*_saslink.txt w folderze **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<version>**. Każdy, kto ma ten link, może pobrać plik zip.

Aby pomóc inżynierowi pomocy technicznej pracującemu nad biletem pomocy technicznej, firma Microsoft może użyć tego łącza Sygnatury dostępu Współdzielonego do pobrania danych diagnostycznych.

Aby wyświetlić raport, wyodrębnij plik zip i otwórz plik **PerfInsights Report.html.**

Możesz również pobrać plik zip bezpośrednio z portalu, wybierając rozszerzenie.

![Zrzut ekranu przedstawiający szczegółowy stan diagnostyki wydajności](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Łącze sygnatury dostępu Współdzielonego wyświetlane w portalu może czasami nie działać. Może to być spowodowane przez nieprawidłowo sformułowany adres URL podczas operacji kodowania i dekodowania. Zamiast tego można uzyskać łącze bezpośrednio z pliku *_saslink.txt z maszyny Wirtualnej.

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

- Stan wdrożenia rozszerzenia (w obszarze powiadomień) może wyświetlać "Wdrażanie w toku", nawet jeśli rozszerzenie jest pomyślnie aprowidzone.

    Ten problem można bezpiecznie zignorować, tak długo, jak stan rozszerzenia wskazuje, że rozszerzenie zostało pomyślnie zainicjowane.
- Niektóre problemy można rozwiązać podczas instalacji przy użyciu dzienników rozszerzeń. Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
