---
title: Rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure dla Windows | Dokumentacja firmy Microsoft
description: Wprowadzono Windows rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 769305cc3d838832f8f445ac9623a1724603f968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307904"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure dla Windows

Usługa Azure rozszerzenie maszyny Wirtualnej diagnostyki wydajności pomaga diagnostycznych zbieranie danych dotyczących wydajności z maszyn wirtualnych Windows. Rozszerzenie wykonuje analizę i zapewnia raportu dotyczącego wyników i zalecenia, aby zidentyfikować i rozwiązać problemy z wydajnością dla maszyny wirtualnej. To rozszerzenie instaluje narzędzie rozwiązywania problemów, o nazwie [program PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Jeśli chcesz uruchomić diagnostykę na maszynie Wirtualnej w witrynie Azure portal dla maszyn wirtualnych-classic, zaleca się używać nowego środowiska. Aby uzyskać więcej informacji, zobacz [Diagnostyka wydajności maszyn wirtualnych platformy Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Wymagania wstępne

To rozszerzenie można zainstalować na Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Można go również zainstalować na Windows 8.1 i Windows 10.

## <a name="extension-schema"></a>Schemat rozszerzenia
Następujące dane JSON zawiera schemat dla rozszerzenia maszyny Wirtualnej diagnostyki wydajności platformy Azure. To rozszerzenie wymaga nazwy i klucza konta magazynu do przechowywania danych wyjściowych diagnostyki i raportu. Te wartości są poufne. Klucz konta magazynu powinny być przechowywane w chronionym konfiguracji. Dane rozszerzenie chronione ustawienia maszyny Wirtualnej platformy Azure są szyfrowane, a tylko jest odszyfrowywany docelowej maszyny wirtualnej. Należy pamiętać, że **storageAccountName** i **storageAccountKey** jest rozróżniana wielkość liter. Inne wymagane parametry są wymienione w poniższej sekcji.

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

|   **Nazwa**   |**Wartość / przykład**|       **Opis**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Wersja interfejsu API.
|Wydawcy|Microsoft.Azure.Performance.Diagnostics|Przestrzeń nazw wydawcy dla rozszerzenia.
|type|AzurePerformanceDiagnostics|Typ rozszerzenia maszyny Wirtualnej.
|typeHandlerVersion|1.0|Wersja programu obsługi rozszerzenia.
|performanceScenario|podstawowe|Scenariusz wydajności, do których chcesz przechwytywać dane. Prawidłowe wartości to: **podstawowe**, **vmslow**, **migracji**, i **niestandardowe**.
|traceDurationInSeconds|300|Czas trwania ślady, jeśli wybrano dowolne spośród opcji śledzenia.
|perfCounterTrace|p|Możliwość włączenia śledzenia licznika wydajności. Prawidłowe wartości to **p** lub wartość pusta. Jeśli nie chcesz przechwycić ślad, pozostaw wartość jako pusty.
|networkTrace|n|Opcję, aby włączyć śledzenie sieci. Prawidłowe wartości to **n** lub wartość pusta. Jeśli nie chcesz przechwycić ślad, pozostaw wartość jako pusty.
|xperfTrace|x|Opcję, aby włączyć narzędzia XPerf śledzenia. Prawidłowe wartości to **x** lub wartość pusta. Jeśli nie chcesz przechwycić ślad, pozostaw wartość jako pusty.
|storPortTrace|s|Opcja Włącz StorPort śledzenia. Prawidłowe wartości to **s** lub wartość pusta. Jeśli nie chcesz przechwycić ślad, pozostaw wartość jako pusty.
|srNumber|123452016365929|Numer biletu pomocy technicznej, jeśli jest dostępny. Pozostaw wartość jako pusty, jeśli nie masz go.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Bieżąca data i godzina w formacie Utc. Jeśli są przy użyciu portalu, aby zainstalować to rozszerzenie, nie musisz podać tę wartość.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Unikatowy identyfikator maszyny Wirtualnej.
|storageAccountName|mystorageaccount|Nazwa konta magazynu do przechowywania dzienników diagnostycznych i wyników.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Klucz konta magazynu.

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Wykonaj te instrukcje, aby zainstalować rozszerzenie na maszynach wirtualnych Windows:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę wirtualną, w którym chcesz zainstalować to rozszerzenie.

    ![Zrzut ekranu usługi Azure portal, z maszynami wirtualnymi z wyróżnioną pozycją](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Wybierz **rozszerzenia** bloku, a następnie wybierz **Dodaj**.

    ![Zrzut ekranu rozszerzenia blok Dodaj wyróżnione](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Wybierz **Diagnostyka wydajności Azure**, zapoznaj się z warunkami i wybierz **Utwórz**.

    ![Zrzut ekranu nowego zasobu ekran, podświetlony Diagnostyka wydajności platformy Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Podaj wartości parametrów dla instalacji, a następnie wybierz pozycję **OK** można zainstalować rozszerzenia. Aby uzyskać więcej informacji o obsługiwanych scenariuszach, zobacz [jak za pomocą programu PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Zrzut ekranu zainstalować rozszerzenie okno dialogowe](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Jeśli instalacja zakończy się pomyślnie, zobaczysz komunikat informujący o tym stanie.

    ![Zrzut ekranu z inicjowania obsługi administracyjnej powiodło się. komunikat](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Jest uruchamiane rozszerzenie podczas aprowizowania zakończyła się pomyślnie. W ciągu dwóch minut lub mniej, aby wykonać podstawowy scenariusz. W innych sytuacjach działa przez czas określony podczas instalacji.

## <a name="remove-the-extension"></a>Usuwanie rozszerzenia
Aby usunąć rozszerzenie z maszyny wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz maszynę wirtualną, z którego chcesz usunąć to rozszerzenie, a następnie wybierz **rozszerzenia** bloku. 
2. Wybierz (**...** ) dla wpisu rozszerzenie diagnostyki wydajności z listy, a następnie wybierz pozycję **Odinstaluj**.

    ![Zrzut ekranu rozszerzenia blok Odinstaluj wyróżniony](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Można również wybrać wpis rozszerzenia i wybierz **Odinstaluj** opcji.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager. To uruchamia rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure podczas wdrażania szablonu usługi Azure Resource Manager. Poniżej przedstawiono przykładowy szablon:

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
`Set-AzVMExtension` Polecenie może służyć do wdrażania rozszerzenia maszyny Wirtualnej diagnostyki wydajności platformy Azure do istniejącej maszyny wirtualnej.

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

## <a name="information-on-the-data-captured"></a>Informacje na temat danych przechwyconych
Narzędzie to program PerfInsights zbiera różne dzienniki, konfiguracji i danych diagnostycznych, w zależności od wybranego scenariusza. Aby uzyskać więcej informacji, zobacz [dokumentacji program PerfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Wyświetlanie i udostępnianie wyników

Dane wyjściowe z rozszerzenia można znaleźć w pliku zip, który przekazywane na konto magazynu określone podczas instalacji i udostępniane przez 30 dni, przy użyciu funkcji [dostępu sygnatury Współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Ten plik zip zawiera dzienniki diagnostyczne i raport o wyniki i zalecenia. Linku sygnatury dostępu Współdzielonego do pliku zip, dane wyjściowe można znaleźć w pliku tekstowym o nazwie *zipfilename*_saslink.txt w folderze **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<wersji >**. Każdy, kto posiada ten link jest w stanie pobrać plik zip.

Aby ułatwić ze specjalistą pomocy technicznej nad biletu pomocy technicznej, firma Microsoft może używać tego linku sygnatury dostępu Współdzielonego można pobrać danych diagnostycznych.

Aby wyświetlić raport, Wyodrębnij plik zip, a następnie otwórz **Report.html program PerfInsights** pliku.

Należy także można pobrać plik zip bezpośrednio z portalu, wybierając rozszerzenie.

![Zrzut ekranu Diagnostyka wydajności szczegółowy stan](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Linku sygnatury dostępu Współdzielonego, wyświetlana w portalu może nie działać czasami. Może to być spowodowane przez nieprawidłowo sformułowany adres URL podczas wykonywania operacji kodowania i dekodowania. Zamiast tego możesz uzyskać link bezpośrednio z pliku *_saslink.txt z maszyny Wirtualnej.

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

- Stan wdrożenia rozszerzenia (w obszarze powiadomień) może wyświetlać "Wdrażanie jest w toku", nawet jeśli rozszerzenie nie zostanie pomyślnie aprowizowane.

    Ten problem można zignorować, tak długo, jak stan rozszerzenia wskazuje, czy rozszerzenia nie zostanie pomyślnie aprowizowane.
- Można rozwiązać pewne problemy podczas instalacji przy użyciu dzienników rozszerzenia. Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następującym katalogu:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
