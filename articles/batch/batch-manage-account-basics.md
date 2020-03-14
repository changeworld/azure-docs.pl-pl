---
title: Zarządzanie kontem — Azure Batch | Microsoft Docs
description: Dowiedz się, co obejmuje konto Azure Batch
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
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72630a2003b63e60ba79882e1861283173840425
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375189"
---
# <a name="manage-your-batch-account"></a>Zarządzaj kontem w usłudze Batch

Konto usługi Batch jest jednoznacznie zdefiniowanym obiektem w ramach usługi Batch. Całe przetwarzanie jest skojarzone z kontem usługi Batch.

Konto usługi Azure Batch możesz utworzyć za pomocą witryny [Azure Portal](batch-account-create-portal.md) lub programowo, na przykład za pomocą [biblioteki Batch Management .NET](batch-management-dotnet.md). Podczas tworzenia konta możesz skojarzyć konto magazynu platformy Azure w celu przechowywania danych wejściowych i wyjściowych związanych z zadaniami lub aplikacji.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia między konta tej usługi znajdujące się w jednej subskrypcji, ale w różnych regionach usługi Azure.

## <a name="components-of-the-batch-account"></a>Składniki konta wsadowego

Konto usługi Batch umożliwia wydajne Uruchamianie zadań wsadowych o dużej skali równoległych i o wysokiej wydajności (HPC) na platformie Azure. Na zarządzanym koncie:

- aplikacje, z których korzystasz

- Alokacja pul i węzłów w pulach

- Liczba i typy zadań 

- dane wejściowe i wyjściowe. Nie musisz instalować dodatkowego oprogramowania, aby zarządzać zadaniami.

- Po utworzeniu konta w usłudze Batch zostanie wyświetlony monit o przypisanie jego nazwy. Ta nazwa jest jego IDENTYFIKATORem i po przypisaniu nie można zmienić.

- Aby zmienić nazwę konta, należy usunąć je i utworzyć nowe konto w usłudze Batch.

- Konto jest tworzone w ramach subskrypcji, której chcesz użyć.

- Użyj konta, aby identyfikować i pobierać podstawowe i pomocnicze klucze kont z dowolnego konta usługi Batch w ramach subskrypcji.

- Konto utrzymuje informacje o alokacji puli i przydziałach podstawowych.  

- Konto zawiera informacje o lokalizacji.

- Konto identyfikuje konto magazynu.

## <a name="next-steps"></a>Następne kroki

- Utwórz konto usługi Batch przy użyciu [Azure Portal](batch-account-create-portal.md).
- Utwórz konto wsadowe programowo, takie jak w przypadku [biblioteki zarządzania usługą Batch dla platformy .NET](batch-management-dotnet.md).
- [Skonfiguruj lub wyłącz dostęp zdalny do węzłów obliczeniowych w puli Azure Batch](pool-endpoint-configuration.md).
- [Uruchamianie zadań przygotowania i zwolnienia zadań w węzłach obliczeniowych wsadowych](batch-job-prep-release.md)
