---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597890"
---
Udziały plików platformy Azure są wdrażane na *kontach magazynu,* które są obiektami najwyższego poziomu reprezentującymi współużytkową pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob, kolejki lub tabele. Wszystkie zasoby magazynu, które są wdrażane na koncie magazynu współużytkują limity, które mają zastosowanie do tego konta magazynu. Aby wyświetlić bieżące limity dla konta magazynu, zobacz [Cele skalowalności i wydajności usługi Azure Files](../articles/storage/files/storage-files-scale-targets.md).

Istnieją dwa główne typy kont magazynu, które będą używane dla wdrożeń usługi Azure Files: 
- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2):** konta magazynu GPv2 umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na standardzie/dysku twardym (opartym na dyskach twardych). Oprócz przechowywania udziałów plików platformy Azure konta magazynu GPv2 mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 
- **Konta magazynu FileStorage:** Konta magazynu FileStorage umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dysku premium/półprzewodnikowym (SSD). Konta FileStorage mogą być używane tylko do przechowywania udziałów plików platformy Azure; żadne inne zasoby magazynu (kontenery obiektów blob, kolejki, tabele itp.) nie mogą być wdrażane na koncie FileStorage.

Istnieje kilka innych typów kont magazynu, które można natknąć się w witrynie Azure portal, powershell lub interfejsu wiersza polecenia. Dwa typy kont magazynu, BlockBlobStorage i BlobStorage kont magazynu, nie może zawierać udziałów plików platformy Azure. Pozostałe dwa typy kont magazynu, które mogą być widoczne, to ogólne przeznaczenie w wersji 1 (GPv1) i klasyczne konta magazynu, które mogą zawierać udziały plików platformy Azure. Chociaż GPv1 i klasyczne konta magazynu mogą zawierać udziały plików platformy Azure, większość nowych funkcji usługi Azure Files są dostępne tylko na kontach magazynu GPv2 i FileStorage. Dlatego zaleca się używać tylko kont magazynu GPv2 i FileStorage dla nowych wdrożeń oraz uaktualnić GPv1 i klasyczne konta magazynu, jeśli już istnieją w twoim środowisku.  