---
title: Wymagania systemowe dysku systemu Microsoft Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe związane z dysku Azure Data Box
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
ms.date: 08/28/2018
ms.author: alkohli
ms.openlocfilehash: 2fdd574adf3587f11984bee2a2549d9bcd0c4c0d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126009"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Wymagania systemowe w usłudze Azure dysku Data Box (wersja zapoznawcza)

W tym artykule opisano wymagania systemowe rozwiązania dysku systemu Microsoft Azure Data Box oraz klientów nawiązywania połączenia z dysku Data Box. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem usługi dysku Data Box i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

> [!IMPORTANT]
> Dysku Data Box jest w wersji zapoznawczej. Przejrzyj [warunki użytkowania dotyczące wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 

Wymagania systemowe obejmują obsługiwanych platform dla klientów łączących się z dysków, kont magazynu obsługiwanych i typów magazynów.


## <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej przedstawiono listę obsługiwanych systemów operacyjnych, aby odblokować dysk i operacji kopiowania danych za pomocą klientów podłączony do dysku Data Box.

| **System operacyjny i platformy** | **Wersje** |
| --- | --- |
| Windows Server |2008 R2 Z DODATKIEM SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |4.0|

> [!NOTE] 
> Funkcja BitLocker musi być włączona na komputerach klienckich, które są uruchamiane z dysku Odblokuj narzędzia i służą do kopiowania danych.


## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Poniżej przedstawiono listę typów obsługiwanych magazynu dla dysku Data Box.

| **Konto magazynu** | **Uwagi** |
| --- | --- |
| Wdrożenie klasyczne | Standardowa (Standard) |
| Ogólne zastosowanie  |Standardowe; obsługiwane są zarówno V1 i V2. Obsługiwane są zarówno gorące i chłodne warstwy. |


## <a name="supported-storage-types"></a>Obsługiwane typy

Poniżej przedstawiono listę typów obsługiwanych magazynu dla dysku Data Box.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Usługa Azure blokowych obiektów blob | |
| Obiekt blob typu page platformy Azure  | |


## <a name="next-step"></a>Następny krok

* [Wdrażanie usługi Azure Data Box Disk](data-box-disk-deploy-ordered.md)

