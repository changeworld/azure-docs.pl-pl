---
title: Dostosowywanie właściwości protokołu RDP za pomocą programu PowerShell — Azure
description: Jak dostosować właściwości RDP dla pulpitu wirtualnego systemu Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128068"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie właściwości protokołu pulpitu zdalnego dla puli hostów

Dostosowywanie właściwości protokołu RDP (Remote Desktop Protocol) puli hostów, takich jak obsługa wielu monitorów i przekierowanie dźwięku, pozwala zapewnić optymalne środowisko dla użytkowników w zależności od ich potrzeb. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu parametru **-CustomRdpProperty** w pokręcie cmdlet **Set-RdsHostPool.**

Pełna lista obsługiwanych właściwości i ich wartości domyślnych można znaleźć w [obsługiwanych ustawieniach pliku RDP.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Domyślne właściwości RDP

Domyślnie opublikowane pliki RDP zawierają następujące właściwości:

|Właściwości RDP | Komputery stacjonarne | Remoteapps |
|---|---| --- |
| Tryb wielomonitorowy | Enabled (Włączony) | Nie dotyczy |
| Włączone przekierowania dysków | Napędy, schowek, drukarki, porty COM, urządzenia USB i karty inteligentne| Dyski, schowek i drukarki |
| Zdalny tryb audio | Graj lokalnie | Graj lokalnie |

Wszystkie właściwości niestandardowe zdefiniowane dla puli hostów zastąpią te wartości domyślne.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Dodawanie lub edytowanie pojedynczej niestandardowej właściwości RDP

Aby dodać lub edytować jedną niestandardową właściwość RDP, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Zrzut ekranu przedstawiający polecenie cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną podświetleną nazwą i nazwą przyjazną.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Dodawanie lub edytowanie wielu niestandardowych właściwości RDP

Aby dodać lub edytować wiele niestandardowych właściwości RDP, uruchom następujące polecenia cmdlet programu PowerShell, udostępniając niestandardowe właściwości RDP jako ciąg oddzielony średnikami:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Zrzut ekranu przedstawiający polecenie cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną podświetleną nazwą i nazwą przyjazną.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetowanie wszystkich niestandardowych właściwości RDP

Można zresetować poszczególne niestandardowe właściwości RDP do ich wartości domyślnych, postępując zgodnie z instrukcjami w [add lub edit a single custom RDP property](#add-or-edit-a-single-custom-rdp-property), lub można zresetować wszystkie niestandardowe właściwości RDP dla puli hostów, uruchamiając następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Zrzut ekranu przedstawiający polecenie cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną podświetleną nazwą i nazwą przyjazną.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu właściwości PROTOKOŁU RDP dla danej puli hostów, możesz zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Te dwa następne instrukcje powiedzą Ci, jak połączyć się z sesją przy użyciu wybranego klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
