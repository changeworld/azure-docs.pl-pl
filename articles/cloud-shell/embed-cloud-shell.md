---
title: Osadzanie powłoki chmury platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak osadzić usługę Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60614306"
---
# <a name="embed-azure-cloud-shell"></a>Osadzanie powłoki chmury platformy Azure

Osadzanie powłoki w chmurze umożliwia deweloperom i modułom zapisującymi zawartość bezpośrednie otwieranie powłoki Cloud Shell z dedykowanego adresu [URL, shell.azure.com.](https://shell.azure.com) To natychmiast przynosi pełną moc uwierzytelniania, narzędzi i aktualnych narzędzi interfejsu wiersza polecenia platformy Azure/Azure PowerShell w usłudze Cloud Shell.

Przycisk o regularnym rozmiarze

[![Regularne uruchamianie](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

Przycisk o dużym rozmiarze

[![Duża premiera](https://shell.azure.com/images/launchcloudshell@2x.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Porady

Zintegruj przycisk uruchamiania usługi Cloud Shell z plikami znaczników, kopiując następujące pliki:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Kod HTML do osadzenia wyskakujących powłoki chmurowej znajduje się poniżej:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Dostosowywanie środowiska

Ustaw określoną powłokę, powiększając adres URL.

|Środowisko użytkownika   |Adres URL   |
|---|---|
|Ostatnio używana powłoka   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Następne kroki
[Bash w cloud shell szybki start](quickstart.md)<br>
[Program PowerShell w programie Cloud Shell szybki start](quickstart-powershell.md)
