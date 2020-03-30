---
title: Publikowanie wbudowanych aplikacji na pulpicie wirtualnym systemu Windows — Azure
description: Jak publikować wbudowane aplikacje na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127743"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikowanie wbudowanych aplikacji na pulpicie wirtualnym systemu Windows

W tym artykule dowiesz się, jak publikować aplikacje w środowisku pulpitu wirtualnego systemu Windows.

## <a name="publish-built-in-apps"></a>Publikowanie wbudowanych aplikacji

Aby opublikować wbudowaną aplikację:

1. Połącz się z jedną z maszyn wirtualnych w puli hostów.
2. Pobierz **PackageFamilyName** aplikacji, którą chcesz opublikować, postępując zgodnie z instrukcjami w [tym artykule](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Na koniec uruchom następujące polecenie `<PackageFamilyName>` cmdlet z zastąpionym przez **PackageFamilyName,** które zostały znalezione w poprzednim kroku:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Pulpit wirtualny systemu Windows obsługuje tylko `C:\Program Files\Windows Apps`aplikacje do publikowania z lokalizacjami instalacji, które zaczynają się od .

## <a name="update-app-icons"></a>Aktualizowanie ikon aplikacji

Po opublikowaniu aplikacji będzie miała domyślną ikonę aplikacji systemu Windows zamiast zwykłego obrazu ikony. Aby zmienić ikonę na zwykłą ikonę, umieść obraz ikony, którą chcesz umieścić w udziale sieciowym. Obsługiwane formaty obrazów to PNG, BMP, GIF, JPG, JPEG i ICO.

## <a name="publish-microsoft-edge"></a>Publikowanie programu Microsoft Edge

Proces publikowania przeglądarki Microsoft Edge różni się nieco od procesu publikowania w innych aplikacjach. Aby opublikować usługę Microsoft Edge z domyślną stroną główną, uruchom następujące polecenie cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować kanały informacyjne do organizowania wyświetlania aplikacji użytkownikom w [obszarze Dostosowywanie kanału informacyjnego dla użytkowników pulpitu wirtualnego systemu Windows](customize-feed-for-virtual-desktop-users.md).
- Dowiedz się więcej o funkcji dołączania aplikacji MSIX na [temat Konfigurowanie aplikacji MSIX dołącz](app-attach.md).

