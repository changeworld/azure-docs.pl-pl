---
title: Wymagania systemowe microsoft azure data box heavy| Dokumenty firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymaganiach sieciowych dla urządzenia Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260074"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Wymagania systemowe azure data box heavy

W tym artykule opisano ważne wymagania systemowe dla urządzenia azure data box ciężkich i dla klientów łączących się z urządzeniem. Zaleca się dokładne przejrzenie informacji przed wdrożeniem narzędzia Data Box Heavy, a następnie odsyłanie do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla hostów łączących się z Data Box Heavy** - opisuje obsługiwane platformy, przeglądarki dla lokalnego interfejsu użytkownika sieci web, klientów SMB i wszelkie dodatkowe wymagania dla hostów, które mogą łączyć się z polem danych.
* **Wymagania dotyczące sieci dla urządzenia Data Box Heavy** — zawierają informacje o wymaganiach sieciowych dla optymalnego działania urządzenia Data Box Heavy.

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania obejmują informacje o obsługiwanych systemach operacyjnych, obsługiwanych przeglądarkach dla lokalnego interfejsu użytkownika sieci web i klientach SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Obsługiwane systemy plików dla klientów systemu Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci Web

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Dla najszybszych prędkości kopiowania, dwa połączenia 40 GbE mogą być używane równolegle (jeden na węzeł). Jeśli nie masz dostępnych 40 GbE, zaleca się, aby mieć co najmniej dwa połączenia 10 GbE (po jednym na węzeł).

### <a name="port-requirements"></a>Wymagania dotyczące portów

W poniższej tabeli wymieniono porty, które muszą zostać otwarte w zaporze, aby umożliwić ruch SMB lub NFS. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzący klient żąda dostępu do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie Data Box Heavy wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzące do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie skrzynki danych platformy Azure](data-box-deploy-ordered.md)
