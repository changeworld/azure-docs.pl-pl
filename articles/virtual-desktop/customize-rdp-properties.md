---
title: Dostosowywanie właściwości protokołu RDP przy użyciu programu PowerShell — platformy Azure
description: Jak dostosować RDP właściwości dla Windows pulpitu wirtualnego przy użyciu poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082653"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie właściwości protokołu Remote Desktop Protocol puli hosta

Dostosowywanie właściwości protokołu RDP (Remote Desktop) puli hosta, takich jak środowisko wielu monitorów i przekierowywania dźwięku, pozwala na dostarczanie zapewnić optymalne działanie dla użytkowników zależnie od ich potrzeb. Właściwości protokołu RDP przy użyciu Windows pulpitu wirtualnego można dostosować **- CustomRdpProperty** parametru w **RdsHostPool zestaw** polecenia cmdlet.

Zobacz [ustawienia pliku Remote Desktop RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) poznania pełnej listy obsługiwanych właściwości i ich wartości domyślne.

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Dodawanie lub edytowanie pojedynczej właściwości niestandardowej protokołu RDP

Aby dodać lub edytować pojedynczej właściwości niestandardowej protokołu RDP, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Get-RDSRemoteApp przy użyciu nazwy i FriendlyName wyróżnione.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Dodać lub edytować wiele niestandardowych właściwości protokołu RDP

Aby dodać lub edytować wiele niestandardowych właściwości protokołu RDP, uruchom następujące polecenia cmdlet programu PowerShell, zapewniając właściwości protokołu RDP niestandardowe jako ciąg rozdzielonych średnikami:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Get-RDSRemoteApp przy użyciu nazwy i FriendlyName wyróżnione.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetuj wszystkie niestandardowe właściwości protokołu RDP

Możesz zresetować poszczególnych niestandardowych właściwości protokołu RDP do wartości domyślnych, postępując zgodnie z instrukcjami w [apletu Dodaj lub Edytuj pojedynczej właściwości niestandardowej protokołu RDP](#add-or-edit-a-single-custom-rdp-property), lub możesz zresetować wszystkie niestandardowe właściwości protokołu RDP dla puli hosta, uruchamiając następujące Polecenia cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Get-RDSRemoteApp przy użyciu nazwy i FriendlyName wyróżnione.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz czy dostosowaniu właściwości protokołu RDP dla puli danego hosta, możesz zalogować się do klienta Windows pulpitu wirtualnego do testowania w ramach sesji użytkownika. Aby to zrobić, przejdź do nawiązywania połączenia z wirtualnego How-tos pulpitu Windows:

- [Łączenie z systemem Windows 10 i Windows 7](connect-windows-7-and-10.md)
- [Łączenie z przeglądarki sieci web](connect-web.md)
