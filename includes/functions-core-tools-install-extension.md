---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
Podczas opracowywania funkcji lokalnie, można zainstalować rozszerzenia, które należy za pomocą narzędzi Azure podstawowe funkcje z terminala lub z wiersza polecenia. Następujące `func extensions install` polecenie powoduje zainstalowanie bazy danych Azure rozwiązania Cosmos rozszerzenia powiązania:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Zastąp `<taget_version>` z określoną wersją pakietu. Prawidłowe wersje są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org).
