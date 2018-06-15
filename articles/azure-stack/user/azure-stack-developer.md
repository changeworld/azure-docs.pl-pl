---
title: Tworzenie aplikacji dla platformy Azure stosu | Dokumentacja firmy Microsoft
description: Programowanie zagadnienia dotyczące aplikacji prototypowania na stosie Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199198"
---
# <a name="develop-for-azure-stack"></a>Opracowywanie zawartości dla usługi Azure Stack

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Możesz rozpocząć pracę opracowywanie aplikacji dzisiaj, nawet jeśli nie masz dostępu do środowiska Azure stosu. Ponieważ stos Azure oferuje usług Microsoft Azure, które działają w centrum danych, podobne procesów i narzędzi można użyć do opracowywania względem stosu Azure, jak w systemie Azure. Pewne przygotowania i za pomocą wskazówki zawarte w następujących tematach umożliwia Azure emulować w środowisku Azure stosu.

* Na platformie Azure można utworzyć szablony usługi Azure Resource Manager, które są dostępne do wdrożenia na stosie Azure. Zobacz [zagadnienia dotyczące szablonów](azure-stack-develop-templates.md) wskazówki dotyczące tworzenia szablonów zapewniające przenośność.
* Ma różnic w dostępności usługi i przechowywanie wersji usługi Azure i stosu Azure. Można użyć [modułu zasad stosu Azure](azure-stack-policy-module.md) do ograniczania typów dostępności i zasobów usługi Azure do co to jest dostępne w stosie Azure. Ograniczający usług gwarantuje, że aplikacji nie korzysta z usług dostępne dla stosu Azure.
* [Szablony szybkiego startu stosu Azure](https://github.com/Azure/AzureStack-QuickStart-Templates) są typowe przykłady scenariuszy, które pokazują, jak utworzyć szablony, które mogą być wdrażane na platformie Azure oraz Azure stosu.
