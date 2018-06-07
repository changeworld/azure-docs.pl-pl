---
title: PowerShell w funkcjach powłoki chmury Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Omówienie funkcji programu PowerShell w powłoce chmury Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: damaerte
ms.openlocfilehash: f099c2dd9b5ab82852f86eb82fb4060321a3d419
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608801"
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Funkcje i narzędzia dla środowiska PowerShell w powłoce chmury Azure (wersja zapoznawcza)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Funkcje i narzędzia dla [Bash](features.md) jest również dostępna.

Uruchamia programu PowerShell w chmurze powłoki (wersja zapoznawcza) na `Windows Server 2016`.

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

PowerShell w chmurze powłoki (wersja zapoznawcza) bezpiecznie i automatycznie służy do uwierzytelniania dostępu do konta dla programu Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Pliki trwałości między sesjami

Aby zachować pliki między sesjami, powłoki chmury przeprowadzi Cię przez dołączenie udział plików Azure firmy Microsoft przy pierwszym uruchomieniu.
Po ukończeniu powłoki chmury będą automatycznie dołączać magazynu (zainstalowany jako `$home\clouddrive`) dla wszystkich przyszłych sesji.
Ponieważ każde żądanie dla powłoki chmury jest przydzielanie tymczasowa maszyna pliki poza Twojej `$home\clouddrive` i stan maszyny nie są zachowywane między sesjami.

[Dowiedz się więcej na temat dołączania udziały plików platformy Azure do chmury powłoki.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Dysku platformy Azure (Azure:)

PowerShell w chmurze powłoki (wersja zapoznawcza) rozpoczyna się w stacji Azure (`Azure:`).
Dysków Azure umożliwia łatwe odnajdowanie i nawigacja zasobów platformy Azure, takich jak obliczeniowych, sieci, itp. magazyn podobny do nawigacji systemu plików.
Można nadal używać znanych [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure) nimi zarządzać.
Wszelkie zmiany wprowadzone do zasobów platformy Azure, albo bezpośrednio w portalu Azure lub za pomocą poleceń cmdlet programu PowerShell systemu Azure, są natychmiast odzwierciedlone w dysku platformy Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Rozpoznawanie kontekstowe
- **Określania zakresu grupy zasobów**: gdy w ramach grupy zasobów ścieżki na dysku platformy Azure (`Azure:`), nazwa grupy zasobów jest automatycznie przekazywane do polecenia cmdlet programu Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: to polecenie cmdlet zwraca listę poleceń zastosowania w kontekście lokalizacji, w obszarze dysk Azure (`Azure:`). Na przykład tylko polecenia związane z magazynowaniem będzie wyświetlana, gdy użytkownik jest w obszarze `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Edytowanie skryptu PowerShell RTF

Jeśli używasz VIM do edycji plików programu PowerShell (`.ps1,.psm1,.psd1`), automatycznie pobrać wyróżnianie składni i obsługę funkcji IntelliSense.
Obsługę funkcji IntelliSense jest implementowany za pośrednictwem vim wtyczkę, która współdziała z lokalnego wystąpienia programu [edytora programu PowerShell usługi](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Użyj `TAB` uzyskanie zakończenia (IntelliSense) na nazwy poleceń, nazwy parametrów i wartości parametrów (jeśli dotyczy).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Rozszerzonego modelu

Przy użyciu [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), można łatwo zainstalować (i zaktualizować) niestandardowe moduły i skrypty z [galerii programu PowerShell](https://www.powershellgallery.com).
Po zakończeniu instalacji moduły automatycznie są zachowywane między sesjami powłoki chmury.

> [!TIP]
> Moduły instalowane przez użytkowników są zapisywane w `$Home\clouddrive\.pscloudshell\WindowsPowerShell` folderu. Łącze symboliczne do ten folder jest tworzony w folderze dokumenty użytkownika (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Zarządzanie maszynami wirtualnymi gościa

Za pomocą dwóch poleceń wbudowanych - `Enter-AzureRmVM` i `Invoke-AzureRmVMCommand`, można zdalnie zarządzać maszynach wirtualnych platformy Azure.
Te polecenia są wbudowane obsługę zdalną środowiska PowerShell i wymaga połączenia programu PowerShell z maszyn wirtualnych platformy Azure.

## <a name="tools"></a>Narzędzia

|**Kategoria**    |**Nazwa**                                 |
|----------------|-----------------------------------------|
|Narzędzia platformy Azure     |[Program Azure PowerShell (6.1.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI (2.0.33)](https://docs.microsoft.com/cli/azure)|
|Edytory tekstów    |vim<br> nano                             |
|Menedżer pakietów |PowerShellGet<br> PackageManagement<br> npm<br> PIP |
|Kontrola źródła  |git                                      |
|Bazy danych       |[Moduł SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Narzędzia do testowania      |Pester                                   |

## <a name="language-support"></a>Obsługa języków

|**Język**|**Wersja**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |8.9.4       |
|PowerShell  |5.1 i [6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Kolejne kroki

[Szybki Start przy użyciu programu PowerShell w chmurze powłoki (wersja zapoznawcza)](quickstart-powershell.md)

[Więcej informacji na temat programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
