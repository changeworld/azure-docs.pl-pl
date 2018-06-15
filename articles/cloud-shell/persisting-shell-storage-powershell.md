---
title: Utrwalanie plików w programie PowerShell w powłoce chmury Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące jak powłoki chmury Azure będzie się powtarzał plików.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154402"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Sposób działania programu PowerShell w powłoce chmury Azure (wersja zapoznawcza)
PowerShell w chmurze powłoki (wersja zapoznawcza) będzie się powtarzał plików za pomocą następujących metod: 
* Instalowanie udziału plików Azure określony jako `clouddrive` w Twojej `$Home` katalogu interakcji bezpośredniego udziału plików.

## <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` udziały plików platformy Azure
`Get-CloudDrive` Polecenie pobiera informacje o udziałów plików na platformę Azure obecnie zainstalowanych w wyniku `clouddrive` w powłoce chmury. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Odinstaluj `clouddrive`
Można odinstalować na udział plików na platformę Azure, który jest zainstalowany w chmurze powłoki w dowolnym momencie. Jeśli usunięto udział plików na platformę Azure, wyświetli się monit o utworzy i zainstaluje nowy udział plików na platformę Azure w następnej sesji.

`Dismount-CloudDrive` Polecenia odinstalowuje udziału plików na platformę Azure z bieżącego konta magazynu. Odinstalowywanie `clouddrive` kończy bieżącej sesji. Użytkownik będzie monitowany o utworzy i zainstaluje nowy udział plików na platformę Azure podczas następnej sesji.
![Running Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Kolejne kroki
[Przewodnik Szybki start po programie PowerShell](quickstart-powershell.md) <br>
[Więcej informacji na temat plików platformy Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Więcej informacji na temat tagów magazynu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>