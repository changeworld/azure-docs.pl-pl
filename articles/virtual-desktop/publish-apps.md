---
title: Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop — Azure
description: Jak opublikować nowoczesne aplikacje na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: 37fc089ab68bfb3cd26e1656ba84610fe8a47bc6
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367271"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop

W tym artykule przedstawiono sposób publikowania aplikacji w środowisku pulpitu wirtualnego systemu Windows.

## <a name="publish-built-in-apps"></a>Publikowanie wbudowanych aplikacji

Aby opublikować wbudowaną aplikację:

1. Nawiąż połączenie z jedną z maszyn wirtualnych w puli hostów.
2. Pobierz **PackageFamilyName** aplikacji, którą chcesz opublikować, postępując zgodnie z instrukcjami w [tym artykule](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Na koniec Uruchom następujące polecenie cmdlet z `<PackageFamilyName>` zastąpione przez **PackageFamilyName** , które zostały znalezione w poprzednim kroku:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Pulpit wirtualny systemu Windows obsługuje tylko publikowanie aplikacji z lokalizacjami instalacji, które zaczynają się od `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Aktualizowanie ikon aplikacji

Po opublikowaniu aplikacji będzie ona mieć domyślną ikonę aplikacji systemu Windows zamiast zwykłego obrazu ikony. Aby zmienić ikonę ikony na zwykłą, Umieść obraz ikony w udziale sieciowym. Obsługiwane formaty obrazów to PNG, BMP, GIF, JPG, JPEG i ICO.

## <a name="publish-microsoft-edge"></a>Publikuj Microsoft Edge

Proces używany do publikowania przeglądarki Microsoft Edge jest nieco inny niż proces publikowania dla innych aplikacji. Aby opublikować program Microsoft Edge z domyślną stroną główną, Uruchom to polecenie cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować źródła danych, aby zorganizować, jak aplikacje są wyświetlane dla użytkowników w obszarze [Dostosowywanie źródła dla użytkowników pulpitu wirtualnego systemu Windows](customize-feed-for-virtual-desktop-users.md).
- Dowiedz się więcej na temat funkcji dołączania aplikacji MSIX w obszarze [Konfiguracja dołączania aplikacji MSIX](app-attach.md).

