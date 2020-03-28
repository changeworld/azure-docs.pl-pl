---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67182952"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymywanie, resetowanie lub zatrzymywanie sesji

Aby tymczasowo zatrzymać sesję, można `Stop()`wywołać program . Spowoduje to zatrzymanie wszystkich obserwatorów i przetwarzania środowiska, nawet jeśli wywołasz ProcessFrame(). Następnie można `Start()` wywołać, aby wznowić przetwarzanie. Podczas wznawiania dane środowiska już przechwycone w sesji są zachowywane.
