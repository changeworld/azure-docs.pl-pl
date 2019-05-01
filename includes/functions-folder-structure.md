---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731247"
---
Kod dla wszystkich funkcji w aplikacji określonych funkcji znajduje się w katalogu głównego folderu projektu, który zawiera plik konfiguracji hosta i jego podfolderach co najmniej jeden. Każdy podfolder zawiera kod to oddzielna funkcja, tak jak w reprezentacji następujące:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

W wersji 2.x środowisko uruchomieniowe usługi Functions, wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam proces roboczy języka.  

[Host.json](../articles/azure-functions/functions-host-json.md) pliku, który zawiera niektóre konfiguracje specyficzne dla środowiska uruchomieniowego, znajduje się w folderze głównym aplikacji funkcji. A `bin` folder zawiera pakiety i inne pliki biblioteki, wymagane przez aplikację funkcji. Wymagania językowe dla projektu aplikacji funkcji:

- [Biblioteki klas C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [Skryptu C# (csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [F#skrypt](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

