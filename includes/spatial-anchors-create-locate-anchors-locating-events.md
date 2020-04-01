---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180065"
---
Po utworzeniu `AnchorLocated` obserwatora wydarzenie zostanie podpalone dla każdej żądanej kotwicy. To zdarzenie jest uruchamiane, gdy znajduje się kotwica lub jeśli nie można jej zlokalizować. Jeśli taka sytuacja się stanie, przyczyna zostanie podana w stanie. Po przetworzeniu, znalezieniu lub nie znalezieniu wszystkich `LocateAnchorsCompleted` kotwic dla obserwatora, zdarzenie zostanie odpalone. Istnieje limit 35 identyfikatorów na obserwatora. 
