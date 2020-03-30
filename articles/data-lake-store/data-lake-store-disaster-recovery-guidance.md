---
title: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966054"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące odzyskiwania danych w usłudze Azure Data Lake Storage Gen1

Usługa Azure Data Lake Storage Gen1 zapewnia magazyn o pojemności lokalnej (LRS). W związku z tym dane na koncie Usługi Data Lake Storage Gen1 jest odporna na przejściowe awarie sprzętu w centrum danych za pośrednictwem replik automatycznych. Zapewnia to trwałość i wysoką dostępność, spełniając umowy SLA gen1 magazynu danych lake. Ten artykuł zawiera wskazówki dotyczące dalszego ochrony danych przed rzadkimi awariami w całym regionie lub przypadkowymi usunięciami.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Przeczytaj informacje zawarte w tym artykule, aby utworzyć plan odzyskiwania po awarii. Poniżej przedstawiono niektóre zasoby, które mogą pomóc w tworzeniu własnego planu.

* [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Odporność platformy Azure — wskazówki techniczne](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Najlepsze rozwiązania
Zaleca się skopiowanie krytycznych danych do innego konta Data Lake Storage Gen1 w innym regionie z częstotliwością dostosowaną do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, takie jak [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Jeśli wystąpi regionalna awaria, można uzyskać dostęp do danych w regionie, w którym dane zostały skopiowane. Możesz monitorować [pulpit nawigacyjny kondycji usługi Azure,](https://azure.microsoft.com/status/) aby określić stan usługi platformy Azure na całym świecie.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Uszkodzenie lub przypadkowe usunięcie danych — wskazówki
Podczas gdy usługa Data Lake Storage Gen1 zapewnia odporność danych za pośrednictwem automatycznych replik, nie uniemożliwia to aplikacji (lub deweloperom/użytkownikom) uszkadzania danych ani przypadkowego ich usuwania.

### <a name="best-practices"></a>Najlepsze rozwiązania
Aby zapobiec przypadkowemu usunięciu, zaleca się najpierw ustawienie prawidłowych zasad dostępu dla konta Data Lake Storage Gen1.  Obejmuje to stosowanie [blokad zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md) w celu zablokowania ważnych zasobów, a także stosowanie kontroli dostępu na poziomie konta i pliku przy użyciu dostępnych funkcji zabezpieczeń Usługi Data Lake Storage [Gen1.](data-lake-store-security-overview.md) Firma Microsoft zaleca również rutynowe tworzenie kopii krytycznych danych przy użyciu [usługi ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) w innym koncie, folderze lub subskrypcji usługi Azure usługi Data Lake Storage Gen1.  Umożliwi to odzyskanie danych po ich uszkodzeniu lub usunięciu. Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Organizacje mogą również włączyć [rejestrowanie diagnostyczne](data-lake-store-diagnostic-logs.md) konta Usługi Data Lake Storage Gen1 w celu zbierania dzienników inspekcji dostępu do danych, które zawierają informacje o tym, kto mógł usunąć lub zaktualizować plik.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)

