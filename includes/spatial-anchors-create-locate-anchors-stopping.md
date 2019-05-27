---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110643"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymaj, resetowania lub zatrzymać sesję

Aby zatrzymać sesję tymczasowo, możesz wywołać `Stop()`. Ten sposób przestanie wszelkie obserwatorów i systemów przetwarzania środowiska, nawet wtedy, gdy wywołujesz ProcessFrame(). Następnie należy wywołać `Start()` Aby wznowić przetwarzanie. Po wznowieniu dane środowisko, które już przechwytywane w sesji są obsługiwane.
