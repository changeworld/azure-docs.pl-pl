---
title: Usuwanie podsieci po usunięciu wystąpienia zarządzanego
description: Dowiedz się, jak usunąć sieć wirtualną platformy Azure po usunięciu wystąpienia zarządzanego usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820462"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Usuwanie podsieci po usunięciu wystąpienia zarządzanego usługi Azure SQL Database

Ten artykuł zawiera wskazówki dotyczące ręcznego usuwania podsieci po usunięciu ostatniego wystąpienia zarządzanego usługi Azure SQL Database, które znajduje się w niej.

Wystąpienia zarządzane są wdrażane w [klastrach wirtualnych](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Każdy klaster wirtualny jest skojarzony z podsiecią. Klaster wirtualny będzie się powtarzał zgodnie z projektem przez 12 godzin po usunięciu ostatniego wystąpienia, aby umożliwić sprawniejsze tworzenie wystąpień zarządzanych w tej samej podsieci. Nie ma żadnych opłat za utrzymywanie pustego klastra wirtualnego. W tym okresie nie można usunąć podsieci skojarzonej z klastrem wirtualnym.

Jeśli nie chcesz czekać 12 godzin i wolisz szybciej usunąć klaster wirtualny i jego podsieć, możesz to zrobić ręcznie. Usuń klaster wirtualny ręcznie przy użyciu witryny Azure portal lub interfejsu API klastrów wirtualnych.

> [!IMPORTANT]
> - Klaster wirtualny nie powinien zawierać żadnych wystąpień zarządzanych, aby usunięcie zakończyło się pomyślnie. 
> - Usunięcie klastra wirtualnego jest długotrwałą operacją trwającą około 1,5 godziny (zobacz [Operacje zarządzania wystąpieniami zarządzanymi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) dla aktualnego czasu usuwania klastra wirtualnego), podczas której klaster wirtualny będzie nadal widoczny w portalu do czasu zakończenia tego procesu.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Usuwanie klastra wirtualnego z witryny Azure portal

Aby usunąć klaster wirtualny przy użyciu witryny Azure portal, wyszukaj zasoby klastra wirtualnego.

![Zrzut ekranu przedstawiający witrynę Azure portal z wyróżnionym polem wyszukiwania](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po zlokalizowaniu klastra wirtualnego, który chcesz usunąć, zaznacz ten zasób i wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie usunięcia klastra wirtualnego.

![Zrzut ekranu przedstawiający pulpit nawigacyjny klastrów wirtualnych portalu platformy Azure z wyróżnioną opcją Usuń](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Powiadomienia portalu Azure będą wyświetlane potwierdzenie, że żądanie usunięcia klastra wirtualnego zostało pomyślnie przesłane. Sama operacja usuwania będzie trwać około 1,5 godziny, podczas których klaster wirtualny będzie nadal widoczny w portalu. Po zakończeniu procesu klaster wirtualny nie będzie już widoczny, a skojarzona z nim podsieć zostanie zwolniona do ponownego użycia.

> [!TIP]
> Jeśli w klastrze wirtualnym nie ma żadnych wystąpień zarządzanych i nie można usunąć klastra wirtualnego, upewnij się, że nie masz trwającego wdrożenia wystąpienia w toku. Obejmuje to rozpoczęte i anulowane wdrożenia, które są nadal w toku. Dzieje się tak, ponieważ te operacje będą nadal używać klastra wirtualnego blokującego go przed usunięciem. Przeglądanie wdrożeń kartę grupy zasobów wystąpienie zostało wdrożone, aby wskazać wszystkie wdrożenia w toku. W takim przypadku poczekaj na zakończenie wdrożenia, usuń wystąpienie zarządzane, a następnie klaster wirtualny.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Usuwanie klastra wirtualnego przy użyciu interfejsu API

Aby usunąć klaster wirtualny za pośrednictwem interfejsu API, użyj parametrów identyfikatora URI określonych w [metodzie usuwania klastrów wirtualnych](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- W samouczku, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia zarządzanego bazy danych Azure SQL](sql-database-managed-instance-get-started.md)Database .
- W przypadku problemów z systemem DNS zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md).
