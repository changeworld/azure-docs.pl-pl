---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881317"
---
#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

`logstream` Użyj opcji, aby rozpocząć otrzymywanie dzienników przesyłania strumieniowego określonej aplikacji funkcji działającej na platformie Azure, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Możesz również wyświetlić [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) aplikacji funkcji w nowym oknie przeglądarki, dołączając `--browser` opcję, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
