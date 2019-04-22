---
title: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878115"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące odzyskiwania po awarii dla danych w usłudze Azure Data Lake magazynu Gen1

Azure Data Lake Storage Gen1 udostępnia magazyn lokalnie nadmiarowy (LRS). Dzięki temu dane na koncie usługi Data Lake Storage Gen1 są odporne na przejściowe awarie sprzętowe w centrum danych dzięki zautomatyzowanym replikom. Dzięki temu trwałości i wysokiej dostępności, spełniające umowę SLA do Gen1 magazynu usługi Data Lake. Ten artykuł zawiera wskazówki na temat jeszcze lepiej chronić swoje dane z rzadko awariami lub przypadkowymi usunięciami.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Przeczytaj informacje w tym artykule, aby utworzyć plan odzyskiwania po awarii. Poniżej przedstawiono niektóre zasoby, które mogą pomóc w tworzeniu własnego planu.

* [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Odporność platformy Azure — wskazówki techniczne](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Najlepsze praktyki
Zaleca się, że kopiujesz danych o kluczowym znaczeniu do innego konta Data Lake Storage Gen1 w innym regionie z częstotliwością dostosowaną do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, takie jak [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Jeśli wystąpi awaria regionalna, można następnie uzyskać dostęp do danych w regionie, w których dane zostały skopiowane. Możesz monitorować [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/) do określenia stanu usługi platformy Azure na całym świecie.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Uszkodzenie lub przypadkowe usunięcie danych — wskazówki
Data Lake Storage Gen1 zapewnia odporność danych za pośrednictwem automatycznych replik, jednocześnie nie ogranicza aplikację (lub deweloperów/użytkowników) z uszkodzenia danych ani przypadkowemu usunięciu.

### <a name="best-practices"></a>Najlepsze praktyki
Aby zapobiec przypadkowemu usunięciu, zalecamy ustawienie najpierw prawidłowych zasad dostępu dla konta usługi Data Lake Storage Gen1.  Obejmuje to stosowanie [blokad zasobów platformy Azure](../azure-resource-manager/resource-group-lock-resources.md) do blokowania ważnych zasobów oraz stosowanie konta i pliku kontroli dostępu na poziomie przy użyciu dostępnych [funkcje zabezpieczeń Data Lake Storage Gen1](data-lake-store-security-overview.md). Zalecamy również regularne tworzenie kopii kluczowych danych [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [programu Azure PowerShell](data-lake-store-get-started-powershell.md) lub [usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) w innej Data Lake Storage Gen1 konto, folder lub subskrypcji platformy Azure.  Umożliwi to odzyskanie danych po ich uszkodzeniu lub usunięciu. Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Organizacje mogą włączyć [rejestrowania diagnostycznego](data-lake-store-diagnostic-logs.md) dla swojego konta Data Lake Storage Gen1 zbierać ślady inspekcji dostępu do danych, który zawiera informacje o który mógł zostać usunięty lub zaktualizowany plik.

## <a name="next-steps"></a>Kolejne kroki
* [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)

