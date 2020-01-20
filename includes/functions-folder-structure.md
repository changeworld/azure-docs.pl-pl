---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279566"
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
