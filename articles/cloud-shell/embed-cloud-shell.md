---
title: Osadzanie usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Dowiedz się osadzić usługę Azure Cloud Shell.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614306"
---
# <a name="embed-azure-cloud-shell"></a>Osadzanie usługi Azure Cloud Shell

Osadzanie usługi Cloud Shell umożliwia programistom oraz autorzy zawartości bezpośrednio Otwórz usługę Cloud Shell z dedykowanym adresem URL [shell.azure.com](https://shell.azure.com). Dzięki temu od razu udostępniają pełnych możliwości uwierzytelniania w usłudze Cloud Shell, narzędzi, i aktualne Azure CLI/Azure PowerShell narzędzi dla użytkowników.

Zwykły przycisk wielkości

[![Regularne uruchamianie](https://shell.azure.com/images/launchcloudshell.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)

Duży przycisk wielkości

[![Uruchamianie dużych](https://shell.azure.com/images/launchcloudshell@2x.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Instrukcje

Integrowanie przycisk uruchamiania w usłudze Cloud Shell plików markdown, kopiując następujące czynności:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Kod HTML do osadzenia wyskakującego Cloud Shell znajduje się poniżej:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Dostosowywanie doświadczenia z

Ustaw środowisko konkretna powłoka, rozszerzając adresu URL.

|Środowisko   |Adres URL   |
|---|---|
|Ostatnio używane powłoki   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Kolejne kroki
[Powłoka bash w usłudze Cloud Shell przewodnika Szybki Start](quickstart.md)<br>
[Program PowerShell w usłudze Cloud Shell Przewodnik Szybki Start](quickstart-powershell.md)
