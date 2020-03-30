---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881317"
---
#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dziennika

Użyj `logstream` tej opcji, aby rozpocząć odbieranie dzienników przesyłania strumieniowego określonej aplikacji funkcji uruchomionej na platformie Azure, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Strumień [metryk na żywo](../articles/azure-monitor/app/live-stream.md) dla aplikacji funkcji można również wyświetlić `--browser` w nowym oknie przeglądarki, dołączając tę opcję, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
