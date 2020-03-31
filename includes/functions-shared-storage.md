---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963663"
---
Aby zmaksymalizować wydajność, należy użyć osobnego konta magazynu dla każdej aplikacji funkcji. Jest to szczególnie ważne, gdy masz funkcje trwałe funkcje lub usługi Event Hub wyzwalane, które generują dużą liczbę transakcji magazynu. Gdy logika aplikacji współdziała z usługą Azure Storage, bezpośrednio (przy użyciu SDK magazynu) lub za pośrednictwem jednego z powiązań magazynu, należy użyć dedykowanego konta magazynu. Na przykład jeśli masz funkcję wyzwalane przez Centrum zdarzeń zapisywania niektórych&mdash;danych do magazynu obiektów blob, użyj dwóch kont magazynu jeden dla aplikacji funkcji i inny dla obiektów blob są przechowywane przez funkcję.