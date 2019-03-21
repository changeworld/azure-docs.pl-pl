---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907701"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymaj, resetowania lub zatrzymać sesję

Aby zatrzymać sesję tymczasowo, możesz wywołać Stop(). Ten sposób przestanie wszelkie obserwatorów i systemów przetwarzania środowiska, nawet wtedy, gdy wywołujesz ProcessFrame(). Następnie należy wywołać Start(), aby wznowić przetwarzanie. Po wznowieniu dane środowisko, które już przechwytywane w sesji są obsługiwane.
