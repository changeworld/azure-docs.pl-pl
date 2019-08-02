---
title: Usuń podsieć po usunięciu Azure SQL Database wystąpienia zarządzanego | Microsoft Docs
description: Dowiedz się, jak usunąć usługę Azure Virtual Network po usunięciu Azure SQL Database wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: f6e0b55ad2fbd9b4c45dbd1facaebd4750314c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567541"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Usuń podsieć po usunięciu Azure SQL Database wystąpienia zarządzanego

Ten artykuł zawiera wskazówki dotyczące ręcznego usuwania podsieci po usunięciu ostatniego wystąpienia zarządzanego Azure SQL Database znajdującego się w nim.

SQL Database używa [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) , aby zawierał usunięte wystąpienie zarządzane. Klaster wirtualny utrzymuje przez 12 godzin po usunięciu wystąpienia, aby umożliwić szybkie tworzenie wystąpień zarządzanych w tej samej podsieci. Nie jest naliczana opłata za utrzymywanie pustego klastra wirtualnego. W tym okresie nie można usunąć podsieci skojarzonej z klastrem wirtualnym.

Jeśli nie chcesz czekać 12 godzin i wolisz natychmiast usunąć klaster wirtualny i jego podsieć, możesz to zrobić ręcznie. Ręcznie usuń klaster wirtualny przy użyciu Azure Portal lub interfejsu API klastrów wirtualnych.

> [!NOTE]
> Klaster wirtualny nie powinien zawierać żadnych wystąpień zarządzanych, aby usuwanie powiodło się.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Usuwanie klastra wirtualnego z Azure Portal

Aby usunąć klaster wirtualny przy użyciu Azure Portal, wyszukaj zasoby klastra wirtualnego.

![Zrzut ekranu przedstawiający Azure Portal z wyróżnionym polem wyszukiwania](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po znalezieniu klastra wirtualnego, który chcesz usunąć, wybierz pozycję ten zasób, a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie usunięcia klastra wirtualnego.

![Zrzut ekranu pulpitu nawigacyjnego Azure Portal klastrów wirtualnych z wyróżnioną opcją usuwania](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Obszar powiadomienia Azure Portal pokazuje, że klaster wirtualny został usunięty. Pomyślne usunięcie klastra wirtualnego natychmiast zwolni podsieć do ponownego użycia.

> [!TIP]
> Jeśli w klastrze wirtualnym nie są wyświetlane żadne wystąpienia zarządzane i nie można usunąć klastra wirtualnego, upewnij się, że nie ma trwającego wdrożenia w toku. Obejmuje to uruchomione i anulowane wdrożenia, które nadal są w toku. Karta przeglądanie wdrożeń w grupie zasobów, w której wdrożono wystąpienie, będzie wskazywać wszystkie wdrożenia w toku. W tym przypadku oczekiwanie na ukończenie wdrożenia, Usuń wystąpienie zarządzane, a następnie klaster wirtualny.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Usuwanie klastra wirtualnego przy użyciu interfejsu API

Aby usunąć klaster wirtualny za pomocą interfejsu API, użyj parametrów identyfikatora URI określonych w [metodzie Delete klastrów wirtualnych](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [tworzenie Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego serwera DNS](sql-database-managed-instance-custom-dns.md).
