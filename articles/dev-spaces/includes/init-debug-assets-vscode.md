---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Zainicjuj zasoby debugowania z rozszerzeniem kodzie VS
Najpierw należy skonfigurować projekt kodu, więc kodzie VS będzie komunikować się z naszym środowisko projektowe na platformie Azure. Rozszerzenie kodzie VS spacji deweloperów Azure udostępnia polecenia pomocnika, aby ustawić konfigurację debugowania. 

Otwórz **palety polecenia** (przy użyciu **widok | Polecenie palety** menu) i użyj automatycznego zakończenia wpisz i wybierz polecenie: `Azure Dev Spaces: Create configuration files for connected development`. 

Spowoduje to dodanie konfiguracji debugowania spacji deweloperów platformy Azure w obszarze `.vscode` folderu.

![](../media/common/command-palette.png)

> [!Important]
> Z powodu usterki Zamknij i otwórz ponownie kodzie VS przed kontynuowaniem.