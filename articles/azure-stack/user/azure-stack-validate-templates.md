---
title: Narzędzie sprawdzania poprawności szablonu umożliwia sprawdzanie szablonów dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Sprawdzanie szablonów we wdrożeniach w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 02ceb6cbcbf824f8bf830c66bc9899c20f6ed822
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484054"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Wyszukaj szablony usługi Azure Stack przy użyciu narzędzia sprawdzania poprawności szablonu

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można użyć narzędzia do sprawdzania poprawności szablonu, aby sprawdzić, czy usługi Azure Resource Manager [szablony](azure-stack-arm-templates.md) są gotowe do wdrożenia w usłudze Azure Stack. Narzędzie sprawdzania poprawności szablonu jest dostępny jako część pakietu narzędzi usługi Azure Stack. Pobierz narzędzia usługi Azure Stack przy użyciu kroków opisanych w [Pobierz narzędzia z serwisu GitHub](azure-stack-powershell-download.md) artykułu.

## <a name="overview"></a>Przegląd

Aby sprawdzić poprawność szablonu, trzeba możliwości kompilacji w chmurze pierwszy plik, a następnie uruchom narzędzie sprawdzania poprawności. Możesz użyć następujących modułów programu PowerShell z poziomu narzędzi usługi Azure Stack:

- W **CloudCapabilities** folderu:<br>         `AzureRM.CloudCapabilities.psm1` Tworzy plik JSON możliwości chmury, reprezentujący usług i wersji w chmurze Azure Stack.
- W **TemplateValidator** folderu:<br>
`AzureRM.TemplateValidator.psm1` użyto pliku JSON możliwości chmury, aby przetestować szablony na potrzeby wdrażania w usłudze Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Tworzenie plików funkcji w chmurze

Przed użyciem modułu sprawdzania poprawności szablonu uruchomić **AzureRM.CloudCapabilities** modułu programu PowerShell do tworzenia pliku JSON.

>[!NOTE]
>Jeśli zaktualizowanie systemu klienta zintegrowane lub Dodaj nowe usługi lub rozszerzenia wirtualnego, należy ponownie uruchomić ten moduł.

1. Upewnij się, że masz łączność z usługą Azure Stack. Te kroki można wykonać z hosta usługi Azure Stack development zestawu lub użyć [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) połączyć ze swojej stacji roboczej.
2. Importuj **AzureRM.CloudCapabilities** modułu programu PowerShell:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Użyj `Get-CloudCapabilities` polecenia cmdlet do pobierania wersji usługi i Utwórz plik JSON możliwości chmury. Jeśli nie określisz **- OutputPath**, plik AzureCloudCapabilities.Json jest tworzony w bieżącym katalogu. Użyj swojej rzeczywistej lokalizacji:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sprawdzanie poprawności szablonów

Wykonaj następujące kroki, aby sprawdzić poprawność szablonów przy użyciu **AzureRM.TemplateValidator** modułu programu PowerShell. Można użyć własnych szablonów lub zweryfikować [szablony szybkiego startu platformy Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importuj **AzureRM.TemplateValidator.psm1** modułu programu PowerShell:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Uruchom moduł sprawdzania poprawności szablonu:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Szablon sprawdzania poprawności ostrzeżenia i błędy są rejestrowane w konsoli programu PowerShell i plik HTML w katalogu źródłowym. Poniższy zrzut ekranu przedstawia przykład raportu weryfikacji:

![Szablon raportu sprawdzania poprawności](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametry

Moduł sprawdzania poprawności szablonu obsługuje następujące parametry.

| Parametr | Opis | Wymagane |
| ----- | -----| ----- |
| TemplatePath | Określa ścieżkę do rekursywnie Znajdź szablony usługi Azure Resource Manager | Yes | 
| TemplatePattern | Określa nazwę pliki szablonów do dopasowania. | Nie |
| CapabilitiesPath | Określa ścieżkę do pliku JSON możliwości chmury | Yes | 
| IncludeComputeCapabilities | Obejmuje ocena zasobów rozwiązania IaaS, takich jak rozmiarów maszyn wirtualnych i rozszerzenia maszyn wirtualnych | Nie |
| IncludeStorageCapabilities | Obejmuje ocena zasobów magazynu, takich jak typy jednostek SKU | Nie |
| Raport | Określa nazwę wygenerowanego raportu HTML | Nie |
| Pełne | Rejestruje błędy i ostrzeżenia w konsoli | Nie|

### <a name="examples"></a>Przykłady

W tym przykładzie, sprawdza poprawność wszystkich [szablony szybkiego startu platformy Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) pobrane do lokalnego magazynu. Przykład sprawdza poprawność, rozmiarów maszyn wirtualnych i rozszerzenia Azure Stack Development Kit możliwościami:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów do usługi Azure Stack](azure-stack-arm-templates.md)
- [Tworzenie szablonów dla usługi Azure Stack](azure-stack-develop-templates.md)
