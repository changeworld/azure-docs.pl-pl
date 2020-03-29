---
title: Usługa Azure Cloud Shell dla użytkowników systemu Windows | Dokumenty firmy Microsoft
description: Przewodnik dla użytkowników, którzy nie znają systemów Linux
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
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204141"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Program PowerShell w usłudze Azure Cloud Shell dla użytkowników systemu Windows

W maju 2018 r. [ogłoszono](https://azure.microsoft.com/blog/pscloudshellrefresh/) zmiany w programie PowerShell w usłudze Azure Cloud Shell.
Środowisko programu PowerShell w usłudze Azure Cloud Shell uruchamia teraz [program PowerShell Core 6](https://github.com/powershell/powershell) w środowisku systemu Linux.
Dzięki tej zmianie mogą występować pewne różnice w doświadczeniu programu PowerShell w usłudze Cloud Shell w porównaniu z oczekiwanymi w programie Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Wielkość liter w systemie plików

W systemie Windows jest niewrażliwy na wielkość liter, podczas gdy w systemie Linux system plików jest rozróżniany.
Wcześniej `file.txt` i `FILE.txt` były uważane za ten sam plik, ale teraz są one uważane za różne pliki.
W systemie plików należy `tab-completing` używać odpowiedniej obudowy.
W programie PowerShell `tab-completing` określone środowiska, takie jak nazwy poleceń cmdlet, parametry i wartości, nie są rozróżniane wielkość liter.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Aliasy programu Windows PowerShell i narzędzia systemu Linux

Niektóre istniejące aliasy programu PowerShell mają takie same nazwy `cat`jak`ls` `sort`wbudowane polecenia Linuksa, takie jak , , , `sleep`itp. W programie PowerShell Core 6 usunięto aliasy, które zderzają się z wbudowanymi poleceniami systemu Linux.
Poniżej znajdują się typowe aliasy, które zostały usunięte, a także ich równoważne polecenia:  

|Usunięto alias   |Równoważne polecenie   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Utrzymujące się $HOME

Wcześniej użytkownicy mogli utrwalić tylko skrypty i inne pliki na dysku w chmurze.
Teraz $HOME katalogu użytkownika jest również zachowywane w sesjach.

## <a name="powershell-profile"></a>Profil programu PowerShell

Domyślnie profil programu PowerShell użytkownika nie jest tworzony.
Aby utworzyć profil, `PowerShell` utwórz `$HOME/.config`katalog w obszarze .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

W `$HOME/.config/PowerShell`obszarze , można utworzyć `profile.ps1` pliki `Microsoft.PowerShell_profile.ps1`profilowe - i/lub .

## <a name="whats-new-in-powershell-core-6"></a>Co nowego w programie PowerShell Core 6

Aby uzyskać więcej informacji na temat nowości w programie PowerShell Core 6, odwołaj się do [dokumentów programu PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) i wpisu w blogu Wprowadzenie do programu [PowerShell Core.](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
