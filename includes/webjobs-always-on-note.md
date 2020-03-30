---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424167"
---
> [!NOTE]
> Aplikacja internetowa może limit czasu po 20 minutach braku aktywności. Tylko żądania do rzeczywistej aplikacji sieci web zresetować czasomierz. Wyświetlanie konfiguracji aplikacji w witrynie Azure portal lub żądania`https://<app_name>.scm.azurewebsites.net`do witryny zaawansowanych narzędzi ( ) nie resetować czasomierza. Jeśli aplikacja działa ciągłych lub zaplanowanych (Timer trigger) WebJobs, włącz **zawsze włączone,** aby upewnić się, że WebJobs działają niezawodnie. Ta funkcja jest dostępna tylko w [warstwach cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Podstawowe, Standardowe i Premium .
