---
title: Usuwanie sieci wirtualnej, po usunięcie bazy danych SQL Azure wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć sieci wirtualnej, po usunięcie bazy danych SQL Azure wystąpienie zarządzane.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411989"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Usuwanie podsieci po usunięcie bazy danych SQL Azure wystąpienie zarządzane

Ten artykuł zawiera wskazówki na temat sposobu ręcznie usunąć podsieci po usunięcie ostatniej usługi Azure SQL Database managed wystąpienia znajdujących się w nim.

[Klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) , obsługuje zawarte usunięto wystąpienie zarządzane, będą przechowywane przez 12 godzin przed usunięciem wystąpienia. Klaster wirtualny jest życiu zgodnie z projektem, aby umożliwić szybsze tworzenie wystąpień zarządzanych w tej samej podsieci. W tym okresie nie można usunąć podsieć skojarzona z klaster wirtualny.

Natychmiastowe wersja podsieci używane przez klaster wirtualny puste jest możliwy za pośrednictwem ręczne usuwanie klaster wirtualny. Usunięcie klastra wirtualnego można osiągnąć za pomocą witryny Azure portal lub klastry wirtualne interfejsu API.

> [!NOTE]
> Klaster wirtualny powinna zawierać żadnych wystąpień zarządzanych do usunięcia zakończy się powodzeniem.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Usuń klaster wirtualny z witryny Azure portal

Aby usunąć klaster wirtualny za pomocą witryny Azure portal, wyszukiwanie zasobów klastra wirtualnego przy użyciu wbudowanych wyszukiwania.

![Wyszukaj klaster wirtualny.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po znalezieniu klaster wirtualny, który chcesz usunąć, wybierz ten zasób, a następnie wybierz opcję Usuń. Użytkownik jest monitowany o potwierdzenie usunięcia klaster wirtualny.

![Usuń klaster wirtualny.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Potwierdzenie, że usunięto klaster wirtualny znajduje się w powiadomieniach portalu Azure. Skuteczne usunięcie klastra wirtualnego natychmiast zwalnia podsieci do dalszych ponownego wykorzystania.

## <a name="delete-virtual-cluster-using-api"></a>Usuń klaster wirtualny za pomocą interfejsu API

Aby usunąć wirtualny klastra przy użyciu interfejsu API parametry identyfikatora URI określonego w [klastrów wirtualnych delete, Metoda](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Dowiedz się więcej o [architektura łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- Aby uzyskać samouczek, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych DNS](sql-database-managed-instance-custom-dns.md).
