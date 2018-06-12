---
title: Sprawdź, czy szablony stosu Azure za pomocą narzędzia sprawdzania poprawności szablonu | Dokumentacja firmy Microsoft
description: Sprawdź szablony do wdrożenia na stosie Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61c893848176a89b4b6ed8d7a46f27bdeff5cec1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294495"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Wyszukaj szablony stosu Azure za pomocą narzędzia sprawdzania poprawności szablonu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można użyć narzędzia sprawdzania poprawności szablonu, aby sprawdzić, czy z usługi Azure Resource Manager [szablony](azure-stack-arm-templates.md) przystąpić do wdrażania usługi Azure stosu. Narzędzie sprawdzania poprawności szablonu jest dostępna jako część pakietu narzędzi Azure stosu. Pobieranie narzędzia Azure stosu przy użyciu procedury opisanej w [Pobierz narzędzia z witryny GitHub](azure-stack-powershell-download.md) artykułu.

## <a name="overview"></a>Przegląd

Można zweryfikować szablonu, trzeba kompilacji chmurę możliwości pierwszy plik, a następnie uruchom narzędzie sprawdzania poprawności. Można używać następujących modułów programu PowerShell z narzędzia Azure stosu:

- W **CloudCapabilities** folderu:<br>         AzureRM.CloudCapabilities.psm1 tworzy plik JSON możliwości chmury reprezentujący usług i wersji w chmurze Azure stosu.
- W **TemplateValidator** folderu:<br>
AzureRM.TemplateValidator.psm1 używa pliku JSON możliwości chmury do testowania szablonów dla wdrożenia w stosie Azure.

## <a name="build-the-cloud-capabilities-file"></a>Kompiluj plik możliwości w chmurze

Przed użyciem szablonu modułu sprawdzania poprawności, należy uruchomić modułu programu AzureRM.CloudCapabilities PowerShell, aby utworzyć plik JSON.

>[!NOTE]
>Aktualizacji systemu zintegrowanego, lub Dodaj nowe usługi lub rozszerzenia wirtualnego, ten moduł należy uruchomić ponownie.

1. Upewnij się, że masz łączność stosu Azure. Te czynności można wykonać z hosta zestawu Azure stosu rozwoju lub użyć [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) nawiązywania połączenia ze stacji roboczej.
2. Zaimportuj moduł programu AzureRM.CloudCapabilities PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Użyj polecenia cmdlet Get-CloudCapabilities, aby pobrać wersje usługi i Utwórz plik JSON możliwości chmury. Jeśli nie określisz **- OutputPath**, plik AzureCloudCapabilities.Json jest tworzony w bieżącym katalogu. Użyj rzeczywistego lokalizacji:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sprawdź poprawność szablonów

Aby sprawdzić szablony przy użyciu modułu AzureRM.TemplateValidator PowerShell, wykonaj następujące kroki. Możesz użyć własnych szablonów lub zweryfikować [szablonów Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Zaimportuj moduł programu PowerShell AzureRM.TemplateValidator.psm1:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Uruchom moduł sprawdzania poprawności szablonu:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Szablon sprawdzania poprawności ostrzeżenia i błędy są rejestrowane w konsoli programu PowerShell i plik HTML w katalogu źródłowym. Następujący zrzut ekranu przedstawia przykład raportu weryfikacji:

![Raport weryfikacji szablonu](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametry

Moduł sprawdzania poprawności szablon obsługuje następujące parametry.

| Parametr | Opis | Wymagane |
| ----- | -----| ----- |
| TemplatePath | Określa ścieżkę do rekursywnie szablonów usługi Azure Resource Manager | Yes | 
| TemplatePattern | Określa nazwę plików szablonów do dopasowania. | Nie |
| CapabilitiesPath | Określa ścieżkę do pliku JSON możliwości w chmurze | Yes | 
| IncludeComputeCapabilities | Obejmuje ocenę zasobów IaaS, takich jak rozmiarów maszyn wirtualnych i rozszerzeń maszyny Wirtualnej | Nie |
| IncludeStorageCapabilities | Obejmuje ocenę zasobów magazynu, takich jak typy jednostki SKU | Nie |
| Raport | Określa nazwę wygenerowanego raportu HTML | Nie |
| Pełne | Dzienniki błędów i ostrzeżeń w konsoli | Nie|

### <a name="examples"></a>Przykłady

W tym przykładzie weryfikuje wszystkie [szablonów Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates) pobrane do lokalnego magazynu. Przykład weryfikuje również rozmiary maszyn wirtualnych i rozszerzenia możliwościami Azure stosu Development Kit.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów Azure stosu](azure-stack-arm-templates.md)
- [Tworzenie szablonów dla stosu Azure](azure-stack-develop-templates.md)
