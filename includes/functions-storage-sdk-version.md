---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132383"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Wersja usługi Azure Storage SDK w funkcjach 1.x

W przypadku funkcji 1.x magazynu wyzwalaczy i powiązań Użyj 7.2.1 wersję zestawu SDK usługi Azure Storage ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pakietu NuGet). Jeśli odwołujesz się inną wersję zestawu SDK usługi Storage i powiązać typu zestawu SDK usługi Storage w podpisie funkcji, środowisko uruchomieniowe usługi Functions może zgłaszać, nie można powiązać jej do tego typu. Rozwiązanie jest, aby upewnić się, że odwołania projektu [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
