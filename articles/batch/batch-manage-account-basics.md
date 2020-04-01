---
title: Zarządzanie kontem — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, co obejmuje konto usługi Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397265"
---
# <a name="manage-your-batch-account"></a>Zarządzanie kontem usługi Batch

Konto usługi Batch jest jednoznacznie zdefiniowanym obiektem w ramach usługi Batch. Całe przetwarzanie jest skojarzone z kontem usługi Batch.

Konto usługi Azure Batch możesz utworzyć za pomocą witryny [Azure Portal](batch-account-create-portal.md) lub programowo, na przykład za pomocą [biblioteki Batch Management .NET](batch-management-dotnet.md). Podczas tworzenia konta możesz skojarzyć konto magazynu platformy Azure w celu przechowywania związanych z pracą danych wejściowych i wyjściowych lub aplikacji.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

## <a name="components-of-the-batch-account"></a>Składniki konta batch

Konto usługi Batch umożliwia wydajne uruchamianie zadań wsadowych na dużą skalę w obliczeniach równoległych i wysokowydajnych (HPC) na platformie Azure. W ramach zarządzanego konta:

- Aplikacje, które uruchamiasz

- Alokacja pul i węzłów w pulach

- Liczba i typy zadań 

- Wprowadzanie i dane wyjściowe. Nie trzeba instalować dodatkowego oprogramowania do zarządzania zadaniami.

- Podczas tworzenia konta usługi Batch zostaniesz poproszony o przypisanie do niego nazwy. Ta nazwa jest jego identyfikatorem i po przypisaniu nie można zmienić.

- Aby zmienić nazwę konta, należy je usunąć i utworzyć nowe konto usługi Batch.

- Konto jest tworzone w ramach subskrypcji, której chcesz użyć.

- Użyj konta, aby zidentyfikować i pobrać klucze konta podstawowego i pomocniczego z dowolnego konta usługi Batch w ramach subskrypcji.

- Konto przechowuje informacje o alokacji puli i przydziałach podstawowych.  

- Konto zawiera informacje o lokalizacji.

- Konto identyfikuje Twoje konto magazynu.

## <a name="next-steps"></a>Następne kroki

- Utwórz konto usługi Batch przy użyciu [portalu Azure](batch-account-create-portal.md).
- Programowo utwórz konto usługi Batch, na przykład w [bibliotece .NET usługi Zarządzanie wsadowe](batch-management-dotnet.md).
- [Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli usługi Azure Batch](pool-endpoint-configuration.md).
- [Uruchamianie zadań przygotowania zadań i zwalniania zadań w węzłach obliczeniowych wsadowych](batch-job-prep-release.md)

