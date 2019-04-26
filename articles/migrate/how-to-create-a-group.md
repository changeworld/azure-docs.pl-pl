---
title: Grupowanie maszyn oceny za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób grupowania maszyn przed uruchomieniem oceny za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544931"
---
# <a name="group-machines-for-assessment"></a>Grupowanie maszyn do oceny

W tym artykule opisano sposób tworzenia grupy komputerów do oceny przez [usługi Azure Migrate](migrate-overview.md). Usługa Azure Migrate ocenia maszyn w grupie, aby sprawdzić, czy są one zostać poddana migracji do platformy Azure i oszacowanie rozmiarów i kosztów dla działającej maszyny na platformie Azure. Jeśli znasz maszyn, które muszą być migrowane razem, należy ręcznie utworzyć grupy w usłudze Azure Migrate, przy użyciu następującej metody. Jeśli nie masz pewności bardzo dotyczące maszyn, które muszą być zgrupowane razem, aby utworzyć grupy za pomocą funkcji wizualizacji zależności w usłudze Azure Migrate. [Dowiedz się więcej.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="create-a-group"></a>Tworzenie grupy

1. W **Przegląd** projektu Azure Migrate, w obszarze Zarządzanie, kliknij przycisk **grup** > **+ grupa**i podaj nazwę grupy.
2. Dodaj co najmniej jednej maszyny do grupy, a następnie kliknij przycisk **Utwórz**.
3. Opcjonalnie można wybrać do uruchomienia nowego rozwiązania do oceny dla grupy.

    ![Tworzenie grupy](./media/how-to-create-a-group/create-group.png)

Po utworzeniu grupy możesz zmodyfikować go, wybierając grupę na **grup** stronie, a następnie dodanie lub usunięcie maszyn.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [mapowania zależności maszyn](how-to-create-group-machine-dependencies.md) do tworzenia grup o dużej pewności.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
