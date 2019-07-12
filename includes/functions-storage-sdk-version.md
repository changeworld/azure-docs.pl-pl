---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608309"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Wersja usługi Azure Storage SDK w funkcjach 1.x

W przypadku funkcji 1.x magazynu wyzwalaczy i powiązań Użyj 7.2.1 wersję zestawu SDK usługi Azure Storage ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pakietu NuGet). Jeśli odwołujesz się inną wersję zestawu SDK usługi Storage i powiązać typu zestawu SDK usługi Storage w podpisie funkcji, środowisko uruchomieniowe usługi Functions może zgłaszać, nie można powiązać jej do tego typu. Rozwiązanie jest, aby upewnić się, że odwołania projektu [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
