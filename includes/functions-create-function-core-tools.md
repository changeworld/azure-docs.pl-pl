---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987981"
---
## <a name="create-a-function"></a>Tworzenie funkcji

Poniższe polecenie powoduje utworzenie funkcji wyzwalanej przez protokół HTTP o nazwie `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```