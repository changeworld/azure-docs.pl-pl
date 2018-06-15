---
title: Osadź powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Dowiedz się osadzić powłoki chmury Azure.
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2018
ms.locfileid: "27864888"
---
# <a name="embed-azure-cloud-shell"></a>Osadź powłoki w chmurze Azure

Osadzanie powłoki chmury umożliwia deweloperom i zapisywania zawartości bezpośrednio Otwórz powłokę chmury z dedykowany adres URL, [shell.azure.com](https://shell.azure.com). Dzięki temu natychmiast udostępniają pełnych możliwości uwierzytelniania chmury powłoki, narzędzi, i aktualne Azure PowerShell interfejsu wiersza polecenia/Azure tools dla użytkowników.

Regularne przycisk o określonym rozmiarze

[![](https://shell.azure.com/images/launchcloudshell.png "Uruchom powłokę chmury Azure")](https://shell.azure.com)

Duży przycisk o określonym rozmiarze

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Uruchom powłokę chmury Azure")](https://shell.azure.com)

## <a name="how-to"></a>Porady

Integrowanie przycisk uruchamiania powłoki chmury pliki markdown przez skopiowanie następujące czynności:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML do osadzenia wyskakujących powłoki chmura znajduje się poniżej:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Dostosowywanie środowiska

Aby ustawić środowisko konkretna powłoka, rozbudować adres URL.
|Środowisko   |Adres URL   |
|---|---|
|Ostatnio używane powłoki   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>Następne kroki
[Bash w chmurze powłoki Szybki Start](quickstart.md)<br>
[PowerShell w chmurze powłoki Szybki Start](quickstart-powershell.md)
