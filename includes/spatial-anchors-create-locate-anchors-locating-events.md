---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232485"
---
Po utworzeniu usługi obserwatora `AnchorLocated` zdarzenie zostanie zastosowana do każdego zakotwiczenia zażądano. To zdarzenie jest uruchamiana, gdy znajduje się kotwica, czy zakotwiczenie nie można odnaleźć. Jeśli ta sytuacja występuje, przyczyna będzie widoczny w stanie. Po przetworzeniu wszystkich kotwic dla obserwatora znaleziono lub nie został znaleziony, a następnie `LocateAnchorsCompleted` zdarzenia będą uruchamiane.
