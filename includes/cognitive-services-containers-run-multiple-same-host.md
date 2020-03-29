---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704255"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z narażonymi portami, upewnij się, że każdy kontener z innym portem narażonym. Na przykład uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Możesz mieć ten kontener i inny kontener usług Azure Cognitive Services uruchomiony na hoście razem. Można również mieć wiele kontenerów tego samego kontenera usług Cognitive Services uruchomione.
