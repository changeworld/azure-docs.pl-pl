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
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095341"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Program PowerShell w użytkownicy usługi Azure Cloud Shell dla Windows

W maju 2018 zmiany zostały wprowadzone [ogłosiła](https://azure.microsoft.com/blog/pscloudshellrefresh/) do programu PowerShell w usłudze Azure Cloud Shell.
Środowisko programu PowerShell usługi Azure Cloud Shell teraz przebiegów [programu PowerShell Core 6](https://github.com/powershell/powershell) w środowisku systemu Linux.
Dzięki tej zmianie może być występują pewne różnice w środowisku PowerShell w usłudze Cloud Shell w porównaniu do czego oczekuje się w programie Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Rozróżnianie wielkości liter systemu plików

W systemie plików jest rozróżniana wielkość liter w Windows, w systemie Linux, systemu plików jest rozróżniana wielkość liter.
Wcześniej `file.txt` i `FILE.txt` uznano być tego samego pliku, ale teraz ich są uznawane za różne pliki.
Należy użyć odpowiedniej wielkości podczas `tab-completing` w systemie plików.
Określonego środowiska PowerShell środowisk, takich jak `tab-completing` nazwy poleceń cmdlet, parametry i wartości, nie jest rozróżniana wielkość liter.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Vs aliasy środowiska Windows PowerShell narzędzia systemu Linux

Niektóre istniejące aliasy programu PowerShell mają takie same nazwy wbudowanego polecenia systemu Linux, takie jak `cat`,`ls`, `sort`, `sleep`itp. W programie PowerShell Core 6 zostały usunięte aliasy, które kolidują z wbudowanego polecenia systemu Linux.
Poniżej przedstawiono typowe aliasy, które zostały usunięte, a także ich równoważne polecenia:  

|Usunąć aliasu   |Equivalent Command   |
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

## <a name="persisting-home"></a>Utrwalanie $HOME

Wcześniej użytkownicy tylko można utrwalić, skrypty i inne pliki w dysku w chmurze.
Teraz katalogu $HOME użytkownika również są utrwalane między sesjami.

## <a name="powershell-profile"></a>Profil programu PowerShell

Domyślnie nie zostanie utworzony profil programu PowerShell.
Aby utworzyć swój profil, Utwórz `PowerShell` katalogu w ramach `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

W obszarze `$HOME/.config/PowerShell`, można utworzyć pliki profilu — `profile.ps1` i/lub `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Co nowego w programie PowerShell Core 6

Aby uzyskać więcej informacji na temat nowości w programie PowerShell Core 6 [dokumentów dotyczących programu PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) i [rozpoczęcie korzystania z programu PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) wpis w blogu.
