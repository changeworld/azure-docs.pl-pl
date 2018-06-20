---
title: Migracja maszyny po ocenie przy migracji Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób uzyskać zalecenia dotyczące migrowania maszyn po uruchomieniu oceny w usłudze Azure migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 571bd2424d1d38e6c0048a95b263dda000477e44
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221883"
---
# <a name="migrate-machines-after-assessment"></a>Migrowanie maszyn po ocenie


[Azure migracji](migrate-overview.md) ocenia lokalnymi maszynami, aby sprawdzić, czy są odpowiednie dla migracji na platformie Azure i zapewnia kosztów i zmiany rozmiaru uzyskać szacunkowe wartości do uruchamiania komputera w systemie Azure. Obecnie migracji Azure tylko ocenia maszyny do migracji. Migracja się aktualnie jest wykonywane przy użyciu innych usług Azure.

W tym artykule opisano, jak uzyskać sugestie dotyczące narzędzia do migracji, po uruchomieniu oceny migracji.

## <a name="migration-tool-suggestion"></a>Sugestia narzędzia migracji

Aby sugestie dotyczące narzędzi migracji, należy wykonać głębokiego ujawniania środowiska lokalnego. Głębokie odnajdywanie odbywa się przez zainstalowanie agentów na komputerach lokalnych.  

1. Utwórz projekt platformy Azure migracji, Odkryj maszyny lokalnej, a oceny migracji. [Dowiedz się więcej](tutorial-assessment-vmware.md).
2. Pobierz i zainstaluj agentów programu Azure migracji na każdej maszynie lokalnej, dla którego chcesz wyświetlić metody zalecane migracji. [Wykonaj poniższą procedurę](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) do instalacji agentów.
2. Zidentyfikuj maszynach lokalnych odpowiednie do migracji przyrostu shift. Są to maszyn wirtualnych, które nie wymaga żadnych zmian do aplikacji działających na nich i mogą być migrowane, ponieważ jest.
3. W przypadku migracji przyrostu shift zalecamy przy użyciu usługi Azure Site Recovery. [Dowiedz się więcej](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternatywnie można użyć narzędzia innych firm, które obsługują migrację do usługi Azure.
4. Jeśli masz maszyny lokalne, które nie są odpowiednie dla migracji przyrostu i shift, oznacza to, jeśli chcesz przeprowadzić migrację specyficzne dla aplikacji, a nie całą maszynę Wirtualną, służy inne narzędzia do migracji. Na przykład zalecamy [migracja bazy danych usługi Azure service](https://azure.microsoft.com/campaigns/database-migration/) Jeśli chcesz przeprowadzić migrację lokalnych baz danych programu SQL Server, MySQL lub Oracle na platformie Azure.


## <a name="review-suggested-migration-methods"></a>Przegląd metod sugerowane migracji

1. Zanim będzie można pobrać metody sugerowane migracji, należy utworzyć projekt Azure migracji, Odkryj maszyny lokalnej, a następnie uruchom oceny migracji. [Dowiedz się więcej](tutorial-assessment-vmware.md).
2. Po utworzeniu oceny go wyświetlić, w projekcie > **omówienie** > **pulpitu nawigacyjnego**. Kliknij przycisk **oceny gotowości**

    ![Ocena gotowości](./media/tutorial-assessment-vmware/assessment-report.png)  

3. W **sugerowane narzędzia**, przeglądanie sugestii dla narzędzi można użyć do migracji.

    ![Sugerowane narzędzie](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
