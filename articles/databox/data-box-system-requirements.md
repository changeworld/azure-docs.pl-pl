---
title: Wymagania dotyczące systemu Microsoft Azure Data Box | Dokumentacja firmy Microsoft
description: Informacje na temat wymagań dotyczących oprogramowania i sieci dla urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 3bce5dd1dd34c53276c5486cc255c4cd93bb6080
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242176"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe w usłudze Azure Data Box

W tym artykule opisano wymagania systemowe dla usługi Microsoft Azure Data Box i klientom połączenie do urządzenia Data Box. Zaleca się, że starannie Przejrzyj informacje przed wdrożeniem usługi Data Box i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania programowe dotyczące hostów połączenie do urządzenia Data Box** — w tym artykule opisano obsługiwanych platform, przeglądarki dla lokalnego Interfejsu w przeglądarce, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które można połączyć do urządzenia Data Box.
* **Wymagania dotyczące sieci dla urządzenia Data Box** — informacje na temat wymagań sieciowych, aby umożliwić optymalne działanie urządzenia Data Box.


## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania zawierają informacje o obsługiwane systemy operacyjne, przeglądarki obsługiwane przez lokalnego Interfejsu w przeglądarce i klienci SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Systemy plików obsługiwane przez klientów systemu Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Obsługiwane typy

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępna, łącza 1 GbE danych może służyć do skopiowania danych, ale kopiowania, które ma wpływ szybkości.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie usługi Azure Data Box](data-box-deploy-ordered.md)
