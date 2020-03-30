---
title: Wymagania systemowe platformy Microsoft Azure Data Box| Dokumenty firmy Microsoft
description: Informacje na temat wymagań dotyczących oprogramowania i sieci dla urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259957"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe usługi Azure Data Box

W tym artykule opisano ważne wymagania systemowe dla skrzynki danych platformy Microsoft Azure i dla klientów łączących się z polem danych. Zaleca się dokładne przejrzenie informacji przed wdrożeniem pola danych, a następnie odwołać się do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla hostów łączących się z polem danych** — opisuje obsługiwane platformy, przeglądarki dla lokalnego interfejsu użytkownika sieci Web, klientów SMB i wszelkie dodatkowe wymagania dla hostów, które mogą łączyć się z polem danych.
* **Wymagania dotyczące sieci dla pola danych** — zawiera informacje o wymaganiach sieciowych dla optymalnego działania skrzynki danych.


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

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, do kopiowania danych można użyć łącza danych 1 GbE, ale ma to wpływ na szybkość kopiowania.

### <a name="port-requirements"></a>Wymagania dotyczące portów

W poniższej tabeli wymieniono porty, które muszą zostać otwarte w zaporze, aby umożliwić ruch SMB lub NFS. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzący klient żąda dostępu do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie Data Box wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzących do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Następne kroki

* [Wdrażanie skrzynki danych platformy Azure](data-box-deploy-ordered.md)
