---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182952"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymaj, resetowania lub zatrzymać sesję

Aby zatrzymać sesję tymczasowo, możesz wywołać `Stop()`. Ten sposób przestanie wszelkie obserwatorów i systemów przetwarzania środowiska, nawet wtedy, gdy wywołujesz ProcessFrame(). Następnie należy wywołać `Start()` Aby wznowić przetwarzanie. Po wznowieniu dane środowisko, które już przechwytywane w sesji są obsługiwane.
