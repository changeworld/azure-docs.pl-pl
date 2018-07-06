---
title: Użytkownicy usługi Azure Cloud Shell dla Windows | Dokumentacja firmy Microsoft
description: Przewodnik dla użytkowników, którzy nie są zaznajomieni z systemami Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861767"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Program PowerShell w użytkownicy usługi Azure Cloud Shell dla Windows

W maju 2018 zmiany zostały wprowadzone [ogłosiła](https://azure.microsoft.com/blog/pscloudshellrefresh/) do programu PowerShell w usłudze Azure Cloud Shell.  Środowisko programu PowerShell w usłudze Azure Cloud Shell jest obecnie program PowerShell Core 6 w systemie Linux.
Dzięki tej zmianie istnieją pewne aspekty programu PowerShell w usłudze Cloud Shell, która może różnić się od oczekiwanych w programie Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Rozróżnianie wielkości liter

W Windows system plików jest rozróżniana wielkość liter.  W systemie Linux systemu plików jest rozróżniana wielkość liter.
Oznacza to, że wcześniej `file.txt` i `FILE.txt` zostały uznane za tego samego pliku po ich są uznawane za różne pliki.
Należy użyć odpowiedniej wielkości podczas `tab` korzystanie w systemie plików.  Określonego środowiska PowerShell środowisk, takich jak `tab` poleceń cmdlet, nie jest rozróżniana wielkość liter. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Vs alias programu Windows PowerShell narzędzia systemu Linux

Istniejące polecenia w systemie Linux, takie jak `ls`, `sort`, i `sleep` pierwszeństwo przed ich aliasy środowiska PowerShell.  Poniżej przedstawiono typowe aliasy usunięte, a także równoważne polecenia:  

|Usunąć aliasu   |Równoważne polecenia   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Utrwalanie $home vs $home\clouddrive

Dla użytkowników, który trwały, skrypty i inne pliki w dysku w chmurze katalogiem $HOME teraz są utrwalane między sesjami.

## <a name="powershell-profile"></a>Profil programu PowerShell

Domyślnie nie zostanie utworzony profil programu PowerShell.  Aby utworzyć swój profil, Utwórz `PowerShell` katalogu w ramach `$HOME/.config`.  W `$HOME/.config/PowerShell`, można utworzyć swój profil w obszarze nazwy `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Co nowego w programie PowerShell Core 6

Aby uzyskać więcej informacji na temat nowości w programie PowerShell Core 6 [dokumentów dotyczących programu PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) i [rozpoczęcie korzystania z programu PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) wpis w blogu
