---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014798"
---
Po utworzeniu `AnchorLocated` obserwatora zdarzenie zostanie wyzwolone dla każdej zażądanej zakotwiczenia. To zdarzenie jest wyzwalane, gdy zakotwiczenie jest zlokalizowane lub nie można zlokalizować zakotwiczenia. W takiej sytuacji przyczyna zostanie określona w stanie. Gdy wszystkie kotwice obserwatora zostaną przetworzone, znaleziono lub nie zostaną znalezione, `LocateAnchorsCompleted` zdarzenie zostanie wyzwolone. Istnieje limit 35 identyfikatorów dla obserwatora. 
