---
title: Dostosowywanie właściwości RDP przy użyciu programu PowerShell — Azure
description: Jak dostosować właściwości protokołu RDP dla pulpitu wirtualnego systemu Windows przy użyciu poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: helohr
ms.openlocfilehash: 624edaea9a0fb56e34eb83f033dfdab64985bd5c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950711"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów

Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania i przekierowania audio, zapewnia optymalne środowisko dla użytkowników zgodnie z ich potrzebami. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu parametru **-CustomRdpProperty** w poleceniu cmdlet **Set-RdsHostPool** .

Aby uzyskać pełną listę obsługiwanych właściwości i ich wartości domyślne, zobacz [pulpit zdalny ustawienia pliku RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) .

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Dodaj lub Edytuj pojedynczą niestandardową Właściwość RDP

Aby dodać lub edytować pojedynczą niestandardową Właściwość RDP, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Dodawanie lub Edytowanie wielu niestandardowych właściwości RDP

Aby dodać lub edytować wiele niestandardowych właściwości RDP, uruchom następujące polecenia cmdlet programu PowerShell, dostarczając niestandardowe właściwości protokołu RDP jako ciąg rozdzielony średnikami:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Zresetuj wszystkie niestandardowe właściwości RDP

Możesz zresetować pojedyncze niestandardowe właściwości protokołu RDP do wartości domyślnych, postępując zgodnie z instrukcjami w temacie [Dodawanie lub edytowanie pojedynczej niestandardowej właściwości RDP](#add-or-edit-a-single-custom-rdp-property), albo Zresetuj wszystkie niestandardowe właściwości protokołu RDP dla puli hostów, uruchamiając następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu właściwości RDP dla danej puli hostów, można zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Aby to zrobić, przejdź do programu Windows Virtual Desktop how-Toss:

- [Łączenie z systemem Windows 10 i Windows 7](connect-windows-7-and-10.md)
- [Nawiązywanie połączenia z przeglądarki sieci Web](connect-web.md)
