---
title: Wymagania dotyczące systemu Microsoft Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymaganiach sieciowych dotyczących urządzenia Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f97c6174adf454a031e94942843075c457236575
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982982"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe w usłudze Azure Data Box

W tym artykule opisano wymagania systemowe dla usługi Microsoft Azure Data Box i klientom połączenie do urządzenia Data Box. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem usługi Data Box i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania programowe dotyczące hostów połączenie do urządzenia Data Box** — w tym artykule opisano obsługiwanych platform, przeglądarki dla lokalnego Interfejsu w przeglądarce, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które można połączyć do urządzenia Data Box.
* **Wymagania dotyczące sieci dla urządzenia Data Box** — informacje na temat wymagań sieciowych, aby umożliwić optymalne działanie urządzenia Data Box.


## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania zawierają informacje o obsługiwane systemy operacyjne, przeglądarki obsługiwane przez lokalnego Interfejsu w przeglądarce i klienci SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej przedstawiono listę obsługiwanych systemów operacyjnych dla operacji kopiowania danych za pomocą klientów podłączonych do urządzenia Data Box.

| **System operacyjny** | **Wersje** | 
| --- | --- | 
| Windows Server |2008 R2 Z DODATKIEM SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Systemy plików obsługiwane przez klientów systemu Linux

| **Protokoły** | **Wersje** | 
| --- | --- | 
| SMB |2.X lub nowszy |
| NFS | Wszystkie wersje, w tym 4.1|

### <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Poniżej przedstawiono listę typów magazynu obsługiwane urządzenia Data Box.

| **Konto magazynu** | **Uwagi** |
| --- | --- |
| Wdrożenie klasyczne | Standardowa (Standard) |
| Zastosowania ogólne  |Standardowe; obsługiwane są zarówno V1 i V2. |
| Obiekt blob |Obsługiwane są zarówno gorąca i chłodna. |


### <a name="supported-storage-types"></a>Obsługiwane typy

Poniżej przedstawiono listę typów magazynu obsługiwane urządzenia Data Box.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Usługa Azure blokowych obiektów blob | |
| Obiekt blob typu page platformy Azure  | Dane powinny mieć 512 bajtów wyrównane.|
| Azure Files | |


### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web

Poniżej przedstawiono listę przeglądarek sieci web obsługiwane w przypadku lokalnego Interfejsu w przeglądarce.

| **Przeglądarka** | **Wersje** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Google Chrome |Najnowsza wersja |Przetestowane za pomocą przeglądarki Chrome|
| Microsoft Edge |Najnowsza wersja | |
| FireFox | Najnowsza wersja | Przetestowane za pomocą przeglądarki FireFox|
| Internet Explorer |Najnowsza wersja |Jeśli nie możesz się zarejestrować, sprawdź, czy pliki cookie i języka Javascript są włączone. Aby włączyć dostęp do interfejsu użytkownika, należy dodać adres IP urządzenia, aby **Akcje prywatności** tak, aby urządzenia mogą uzyskiwać dostęp do plików cookie. |


## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Centrum danych musi mieć szybkie sieci. Zdecydowanie zaleca się mieć co najmniej jedną 10 GbE połączenia. 10 GbE połączenie nie jest dostępna, 1 GbE danych link może służyć do skopiowania danych, ale dotyczy szybkości kopiowania.

## <a name="next-step"></a>Następny krok

* [Wdrażanie usługi Azure Data Box](data-box-deploy-ordered.md)

