---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935786"
---
Kod dla wszystkich funkcji w określonej aplikacji funkcji znajduje się w folderze głównym projektu zawierającym plik konfiguracji hosta i jeden lub więcej podfolderów. Każdy podfolder zawiera kod dla oddzielnej funkcji. Struktura folderów jest pokazana w następującej reprezentacji:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

W wersji 2. x i nowszych w czasie wykonywania funkcji wszystkie funkcje w aplikacji funkcji muszą mieć ten sam stos języka.  

Plik [host. JSON](../articles/azure-functions/functions-host-json.md) zawiera konfiguracje specyficzne dla środowiska uruchomieniowego i znajduje się w folderze głównym aplikacji funkcji. Folder *bin* zawiera pakiety i inne pliki bibliotek wymagane przez aplikację funkcji. Zobacz wymagania specyficzne dla języka dla projektu aplikacji funkcji:

* [C#Biblioteka klas (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skryptu C# (csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#napisy](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
