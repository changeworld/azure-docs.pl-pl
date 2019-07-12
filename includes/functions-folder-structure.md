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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594528"
---
Kod dla wszystkich funkcji w aplikacji określonych funkcji znajduje się w katalogu głównego folderu projektu, który zawiera plik konfiguracji hosta i jego podfolderach co najmniej jeden. Każdy podfolder zawiera kod to oddzielna funkcja. Struktury folderów przedstawiono w następujących reprezentacji:

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

W wersji 2.x środowisko uruchomieniowe usługi Functions, wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam stos języka.  

[Host.json](../articles/azure-functions/functions-host-json.md) plik zawiera konfiguracje specyficzne dla środowiska uruchomieniowego i znajduje się w folderze głównym aplikacji funkcji. A *bin* folder zawiera pakiety i inne pliki biblioteki, których wymaga aplikacja funkcji. Wymagania językowe dla projektu aplikacji funkcji:

* [Biblioteki klas C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skryptu C# (csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#skrypt](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



