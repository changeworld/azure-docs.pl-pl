---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379299"
---
Sprawdź `"confidence"` wartości dla każdego wyniku klucz/wartość w węźle. `"pageResults"` Należy również spojrzeć na wyniki `"readResults"` zaufania w węźle, które odpowiadają operacji odczytu tekstu. Zaufanie wyników odczytu nie wpływa na zaufanie wyników wyodrębniania klucz/wartość, dlatego należy sprawdzić oba.
* Jeśli wyniki zaufania dla operacji odczytu są niskie, spróbuj poprawić jakość dokumentów wejściowych (patrz [Wymagania wejściowe).](../overview.md#input-requirements)
* Jeśli wyniki zaufania dla operacji wyodrębniania klucz/wartość są niskie, upewnij się, że analizowane dokumenty są tego samego typu, co dokumenty używane w zestawie szkoleniowym. Jeśli dokumenty w zestawie szkoleniowym mają różnice w wyglądzie, należy rozważyć podzielenie ich na różne foldery i szkolenie jednego modelu dla każdej odmiany.

Wyniki zaufania, które kierujesz, będą zależeć od twojego przypadku użycia, ale ogólnie jest to dobra praktyka, aby kierować wynik 80% lub wyższy. W przypadku bardziej wrażliwych przypadków, takich jak czytanie dokumentacji medycznej lub wyciągów rozliczeniowych, zaleca się wynik 100%.