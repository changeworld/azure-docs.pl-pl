---
title: Przekazywanie pliku VHD do laboratoriów devtest platformy Azure przy użyciu programu PowerShell | Dokumenty firmy Microsoft
description: Ten artykuł zawiera przewodnik, który pokazuje, jak przekazać plik VHD do usługi Azure DevTest Labs przy użyciu programu PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759759"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu programu PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W laboratoriach DevTest Azure pliki VHD mogą być używane do tworzenia obrazów niestandardowych, które są używane do inicjowania obsługi administracyjnej maszyn wirtualnych. Poniższe kroki umożliwiają przekazywanie pliku VHD do konta magazynu w laboratorium za pomocą programu PowerShell. Po przesłaniu pliku VHD w [sekcji Następne kroki](#next-steps) wymieniono kilka artykułów ilustrujących sposób tworzenia niestandardowego obrazu z przesłanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [Wprowadzenie do dysków zarządzanych](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przebiegają przez przekazywanie pliku VHD do platformy Azure DevTest Labs przy użyciu programu PowerShell. 

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz żądane laboratorium.  

1. Na bloku laboratorium wybierz opcję **Konfiguracja**. 

1. W bloku **Konfiguracji** laboratorium wybierz opcję **Obrazy niestandardowe (VED)**.

1. W bloku **Obrazy niestandardowe** wybierz **pozycję +Dodaj**. 

1. Na **bloku Obraz niestandardowy** wybierz pozycję **VHD**.

1. Na bloku **VHD** wybierz **pozycję Przekaż dysk VHD za pomocą programu PowerShell**.

    ![Przekazywanie dysku VHD za pomocą programu PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. W programie Przekaż obraz przy użyciu bloku **programu PowerShell** skopiuj wygenerowany skrypt programu PowerShell do edytora tekstu.

1. Zmodyfikuj parametr **LocalFilePath** polecenia cmdlet **Add-AzureVhd,** aby wskazać lokalizację pliku VHD, który chcesz przekazać.

1. W wierszu programu PowerShell uruchom polecenie cmdlet **Add-AzureVhd** (ze zmodyfikowanym parametrem **LocalFilePath).**

> [!WARNING] 
> 
> Proces przesyłania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w laboratoriach devtest azure z pliku VHD przy użyciu portalu Azure](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w laboratoriach devtest platformy Azure na podstawie pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
