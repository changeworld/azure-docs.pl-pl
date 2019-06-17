---
title: Migrowanie maszyn po ocenie w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje, jak można uzyskać zaleceń dotyczących migracji maszyny po uruchomieniu oceny za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598335"
---
# <a name="migrate-machines-after-assessment"></a>Migrowanie maszyn po ocenie


[Usługa Azure Migrate](migrate-overview.md) ocenia maszyn lokalnych, aby sprawdzić, czy są one zostać poddana migracji do platformy Azure i oszacowanie rozmiarów i kosztów dla działającej maszyny na platformie Azure. Obecnie usługa Azure Migrate tylko ocenia maszyn pod kątem migracji. Migracja sam obecnie odbywa się przy użyciu innych usług platformy Azure.

W tym artykule opisano, jak uzyskać sugestie dotyczące narzędzia do migracji, po uruchomieniu oceny migracji.

> [!NOTE]
> Sugestia narzędzia migracji nie jest dostępna na platformie Azure Government.

## <a name="migration-tool-suggestion"></a>Sugestia narzędzie do migracji

Aby uzyskać sugestie dotyczące narzędzi migracji, należy wykonać głębokie odnajdywanie środowiska lokalnego. Głębokie odnajdywanie odbywa się przez zainstalowanie agentów na komputerach lokalnych.  

1. Utwórz projekt usługi Azure Migrate, odnajdowanie maszyn lokalnych i tworzenie rozwiązania do oceny migracji. [Dowiedz się więcej](tutorial-assessment-vmware.md).
2. Pobierz i zainstaluj agentów usługi Azure Migrate na każdej maszynie w środowisku lokalnym, dla której chcesz zobaczyć metodą zalecaną migracji. [Wykonaj poniższą procedurę](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) do zainstalowania agentów.
2. Zidentyfikuj swoje maszyn lokalnych, które są odpowiednie na potrzeby migracji lift-and-shift. Są to maszyny wirtualne, które nie wymaga dokonywania żadnych zmian do aplikacji działających na nich i mogą być migrowane, ponieważ jest.
3. W przypadku migracji lift-and-shift zalecane jest używanie usługi Azure Site Recovery. [Dowiedz się więcej](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternatywnie można użyć narzędzi innych firm, które obsługują migrację do platformy Azure.
4. W przypadku maszyn lokalnych, które nie są odpowiednie dla migracji lift-and-shift, oznacza to, jeśli chcesz przeprowadzić migrację określonej aplikacji, a nie całą maszynę Wirtualną, inne narzędzia do migracji można użyć. Na przykład, sugerujemy [usługi Azure Database Migration service](https://azure.microsoft.com/campaigns/database-migration/) Jeśli chcesz przeprowadzić migrację lokalnych baz danych programu SQL Server, MySQL lub Oracle na platformie Azure.


## <a name="review-suggested-migration-methods"></a>Przejrzyj sugerowane migracji metod

1. Zanim będzie możliwe uzyskanie metody sugerowane migracji, należy utworzyć projekt usługi Azure Migrate, odnajdowanie maszyn lokalnych i ocenę migracji. [Dowiedz się więcej](tutorial-assessment-vmware.md).
2. Po utworzeniu oceny wyświetlić go w projekcie > **Przegląd** > **pulpit nawigacyjny**. Kliknij przycisk **Ocena gotowości**

    ![Ocena gotowości](./media/tutorial-assessment-vmware/assessment-report.png)  

3. W **sugerowane narzędzie**, przejrzyj sugestie dotyczące narzędzi można użyć do migracji.

    ![Sugerowane narzędzie](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
