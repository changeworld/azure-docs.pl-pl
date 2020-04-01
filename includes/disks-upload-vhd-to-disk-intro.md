---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420956"
---
W tym artykule wyjaśniono, jak przekazać dysk VHD z komputera lokalnego na dysk zarządzany platformy Azure lub skopiować dysk zarządzany do innego regionu przy użyciu programu AzCopy. Ten proces, bezpośrednie przesyłanie, umożliwia również przesłanie dysku VHD o rozmiarze do 32 TiB bezpośrednio na dysk zarządzany. Obecnie przesyłanie bezpośrednie jest obsługiwane dla standardowych dysków twardych, standardowych dysków SSD i dysków zarządzanych w wersji premium. Nie jest jeszcze obsługiwany dla dysków ultra.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. Podczas przekazywania dysku twardego z zewnętrznego źródła na platformę Azure szybkość zależy od przepustowości lokalnej. Podczas przekazywania lub kopiowania z maszyny Wirtualnej platformy Azure przepustowość będzie taka sama jak standardowe dyski twarde.