---
title: Wymagania dotyczące systemu Microsoft Azure dane pole mocno | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe związane z duże pole danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427742"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Duże pole danych system wymagania dotyczące usługi Azure (wersja zapoznawcza)

W tym artykule opisano wymagania systemowe dla Twojego urządzenia Azure Data Box duże i klientów, połączono z urządzeniem. Zaleca się, że starannie Przejrzyj informacje przed wdrożeniem usługi duże pole danych i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania dotyczące oprogramowania dla hostów, na nawiązywanie połączeń z dużymi pole danych** — w tym artykule opisano obsługiwanych platform, przeglądarki dla lokalnego Interfejsu w przeglądarce, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które można połączyć do urządzenia Data Box.
* **Wymagania dotyczące sieci dla duże pole danych** — informacje na temat wymagania sieciowe, aby umożliwić optymalne działanie duże pole danych urządzenia.

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

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Najszybszy szybkości kopiowania dwa połączenia 40 GbE możesz wykorzystać równolegle (jednym w każdym węźle). Jeśli nie masz dostępnych 40 GbE, zaleca się, że masz co najmniej dwa połączenia 10 GbE (po jednej na węzeł).

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie usługi Azure Data Box](data-box-deploy-ordered.md)
