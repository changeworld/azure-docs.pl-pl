---
title: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b51f0c1e0c6ef713bf8d3ff0a124300f446a9373
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306812"
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Wskazówki dotyczące odzyskiwania po awarii dla danych w Data Lake Store

Azure Data Lake Store udostępnia magazyn lokalnie nadmiarowy (LRS). W związku z tym dane na Twoim koncie usługi Azure Data Lake Store są odporne na przejściowe awarie sprzętowe w centrum danych dzięki zautomatyzowanym replikom. Zapewniają one trwałość i wysoką dostępność usługi Azure Data Lake Store oraz zgodność z warunkami jej umowy SLA. Ten artykuł zawiera wskazówki na temat jeszcze lepiej chronić swoje dane z rzadko awariami lub przypadkowymi usunięciami.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii
Jest to bardzo ważne, aby każdy klient przygotował własny plan odzyskiwania po awarii. Przeczytaj informacje w tym artykule, aby utworzyć plan odzyskiwania po awarii. Poniżej przedstawiono niektóre zasoby, które mogą pomóc w tworzeniu własnego planu.

* [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Odporność platformy Azure — wskazówki techniczne](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Najlepsze praktyki
Zalecamy kopiowanie danych o kluczowym znaczeniu do innego konta usługi Data Lake Store w innym regionie z częstotliwością dostosowaną do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, takie jak [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Jeśli wystąpi awaria regionalna, można uzyskać dostęp do danych w regionie, do którego je skopiowano. Można monitorować [pulpit nawigacyjny kondycji usług platformy Azure](https://azure.microsoft.com/status/), aby określić stan usług platformy Azure na całym świecie.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Uszkodzenie lub przypadkowe usunięcie danych — wskazówki
Usługa Azure Data Lake Store zapewnia odporność danych za pośrednictwem automatycznych replik, ale nie zapobiega uszkodzeniu ani przypadkowemu usunięciu danych przez aplikację (lub deweloperów/użytkowników).

### <a name="best-practices"></a>Najlepsze praktyki
Aby zapobiec przypadkowemu usunięciu, zalecamy ustawienie najpierw prawidłowych zasad dostępu do konta usługi Data Lake Store.  Obejmuje to stosowanie [blokad zasobów platformy Azure](../azure-resource-manager/resource-group-lock-resources.md) do blokowania ważnych zasobów oraz stosowanie kontroli dostępu na poziomie konta i pliku przy użyciu dostępnych [funkcji zabezpieczeń usługi Data Lake Store](data-lake-store-security-overview.md). Zalecamy również regularne tworzenie kopii kluczowych danych usług [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) lub [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) na innym koncie usługi Data Lake Store, w innym folderze lub w ramach innej subskrypcji platformy Azure.  Umożliwi to odzyskanie danych po ich uszkodzeniu lub usunięciu. Azure Data Factory to usługa przydatna w przypadku cyklicznego tworzenia i wdrażania potoków przepływu danych.

Organizacje mogą włączyć opcję [rejestrowania diagnostycznego](data-lake-store-diagnostic-logs.md) konta usługi Azure Data Lake Store, aby zbierać ślady inspekcji dostępu do danych, które zawierają informacje o osobach, które mogły usunąć lub zaktualizować plik.

## <a name="next-steps"></a>Kolejne kroki
* [Rozpoczynanie pracy z usługą Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)

