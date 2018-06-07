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
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675279"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Wersja zestawu SDK usługi Magazyn Azure w funkcjach 1.x

W funkcjach 1.x, magazynu wyzwalaczy i powiązań Użyj wersji 7.2.1 zestawu SDK usługi Magazyn Azure ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pakietu NuGet). Jeśli możesz odwoływać się do innej wersji zestawu SDK usługi Magazyn i powiązanie z typem zestawu SDK usługi Magazyn w podpisu funkcji, środowisko uruchomieniowe Functions może zgłosić, że nie można powiązać typu. Rozwiązanie to upewnij się, że odwołania projektu [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
