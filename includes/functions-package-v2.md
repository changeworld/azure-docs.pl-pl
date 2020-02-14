---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205746"
---
Dodaj obsługę w preferowanym środowisku programistycznym, korzystając z następujących metod.

| Środowisko deweloperskie  | Typ aplikacji      | Aby dodać obsługę |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#Biblioteka klas      | [Zainstaluj pakiet NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Zalecane jest zainstalowanie [rozszerzenia narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Każdy inny edytor/środowisko IDE     | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portal Azure             | Tylko w trybie online w portalu | Instalowane podczas dodawania powiązania<br /><br /> Zobacz [Aktualizowanie rozszerzeń](../articles/azure-functions/install-update-binding-extensions-manual.md) , aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji. |
