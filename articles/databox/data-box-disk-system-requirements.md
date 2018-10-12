---
title: Wymagania systemowe dysku systemu Microsoft Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe związane z dysku Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: d10ca5d704892ae0a1494d729b46abf0fc06aa64
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092256"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Wymagania systemowe w usłudze Azure dysku Data Box (wersja zapoznawcza)

W tym artykule opisano wymagania systemowe rozwiązania dysku systemu Microsoft Azure Data Box oraz klientów nawiązywania połączenia z dysku Data Box. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem usługi dysku Data Box i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

> [!IMPORTANT]
> Dysku Data Box jest w wersji zapoznawczej. Zapoznaj się z [warunkami użytkowania wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 

Wymagania systemowe obejmują obsługiwanych platform dla klientów łączących się z dysków, kont magazynu obsługiwanych i typów magazynów.


## <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej przedstawiono listę obsługiwanych systemów operacyjnych, aby odblokować dysk i operacji kopiowania danych za pomocą klientów podłączony do dysku Data Box.

| **System operacyjny** | **Przetestowane wersje** |
| --- | --- |
| Windows Server |2008 R2 Z DODATKIEM SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6,9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Inne wymagane oprogramowanie dla klientów Windows

Klienta Windows również należy zainstalować następujące czynności.

| **Oprogramowanie**| **Wersja** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Inne wymagane oprogramowanie dla klientów systemu Linux

Klient systemu Linux narzędzi dysku Data Box instaluje również następujące oprogramowanie wymagane:

- dislocker
- Biblioteki OpenSSL

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

