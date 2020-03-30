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
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198426"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Wersja zestawu SDK usługi Azure Storage w funkcji 1.x

W funkcji 1.x wyzwalacze magazynu i powiązania używają wersji 7.2.1 zestawu Azure Storage SDK[(WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet pakietu). Jeśli odwołujesz się do innej wersji SDK magazynu i wiążesz się z typem SDK magazynu w podpisie funkcji, środowisko wykonawcze functions może zgłosić, że nie może powiązać z tym typem. Rozwiązaniem jest upewnienie się, że projekt odwołuje się do [systemu WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
