---
title: Microsoft Azure Data Box Disk wymagania systemowe | Microsoft Docs
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: e17fbcebc128a7169953ed05de617efa25fc25f8
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172717"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Wymagania systemowe Azure Data Box Disk

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania Data Box Disk Microsoft Azure i dla klientów nawiązujących połączenie z Data Box Disk. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem Data Box Disk, a następnie odwoływanie się do niego w miarę potrzeb podczas wdrażania i kolejnej operacji.

Wymagania systemowe obejmują obsługiwane platformy dla klientów łączących się z dyskami, obsługiwane konta magazynu i typy magazynów.

::: zone-end

::: zone target="chromeless"

## <a name="prerequisites"></a>Wymagania wstępne

Masz dostępny komputer kliencki, z którego możesz skopiować dane. Na komputerze klienckim wymagane jest:

- Uruchom obsługiwany system operacyjny.
- Inne wymagane oprogramowanie jest zainstalowane.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej znajduje się lista systemów operacyjnych obsługiwanych przez operacje odblokowywania dysków i kopiowania danych za pośrednictwem klientów podłączonych do Data Box Disk.

| **System operacyjny** | **Przetestowane wersje** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64-bitowe) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04 16.04, 18.04 <br> 8,11, 9 <br> 7.0 <br> 6,5, 6,9, 7,0, 7,5 |  

## <a name="other-required-software-for-windows-clients"></a>Inne wymagane oprogramowanie dla klientów z systemem Windows

W przypadku klienta systemu Windows należy również zainstalować następujące elementy:

| **Oprogramowanie**| **Wersja** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Struktura zarządzania systemu Windows |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Inne wymagane oprogramowanie dla klientów z systemem Linux

W przypadku klienta systemu Linux zestaw narzędzi Data Box Disk zainstaluje następujące wymagane oprogramowanie:

- Odblokowanie
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Obsługiwane połączenie

Komputer kliencki zawierający dane musi mieć port USB 3,0 lub nowszy. Dyski łączą się z tym klientem przy użyciu dostarczonego kabla.

## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Poniżej znajduje się lista obsługiwanych typów magazynów dla Data Box Disk.

| **Konto magazynu** | **Uwagi** |
| --- | --- |
| Klasyczny | Standardowa (Standard) |
| Ogólne zastosowanie  |Standardowa Obsługiwane są zarówno wersje V1, jak i v2. Obsługiwane są zarówno warstwy gorąca, jak i chłodna. |
| Konto magazynu obiektów BLOB | |

>[!NOTE]
> Azure Data Lake Storage konta generacji 2 nie są obsługiwane.


## <a name="supported-storage-types-for-upload"></a>Obsługiwane typy magazynów do przekazania

Poniżej znajduje się lista typów magazynów obsługiwanych na potrzeby przekazywania na platformę Azure przy użyciu Data Box Disk.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Blokowy obiekt blob platformy Azure | |
| Obiekt BLOB na stronie platformy Azure  | |
| Azure Files  | |
| Dyski zarządzane | |


## <a name="next-step"></a>Następny krok

* [Wdrażanie Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

