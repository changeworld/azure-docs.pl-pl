---
title: Usuwanie podsieci po usunięcie usługi Azure SQL Database wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć sieci wirtualnej platformy Azure po usunięcie usługi Azure SQL Database wystąpienie zarządzane.
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
ms.openlocfilehash: ec5d99e160e739f59e2bf2ea369fe83e9900a1f1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295313"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Usuwanie podsieci po usunięcie usługi Azure SQL Database wystąpienie zarządzane

Ten artykuł zawiera wskazówki dotyczące sposobu ręcznie usunąć podsieć po usunięcie ostatniej usługi Azure SQL Database managed wystąpienia znajdujących się w nim.

SQL Database przy użyciu [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) zawierać usunięto wystąpienie zarządzane. Klaster wirtualny będzie się powtarzał przez 12 godzin po usunięciu wystąpienia, aby umożliwić szybkie tworzenie zarządzanych wystąpień w tej samej podsieci. Brak opłat za utrzymywanie pusty klaster wirtualny. W tym okresie nie można usunąć podsieć skojarzona z klaster wirtualny.

Jeśli nie chcesz czekać na 12 godzin, a chcesz natychmiast usunąć klaster wirtualny i jej podsieci, możesz to zrobić ręcznie. Usuń klaster wirtualny ręcznie przy użyciu witryny Azure portal lub klastry wirtualne interfejsu API.

> [!NOTE]
> Klaster wirtualny powinna zawierać żadnych wystąpień zarządzanych do usunięcia zakończy się powodzeniem.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Usuń klaster wirtualny w witrynie Azure portal

Aby usunąć klaster wirtualny za pomocą witryny Azure portal, wyszukiwanie zasobów klastra wirtualnego.

![Zrzut ekranu witryny Azure Portal, za pomocą pola wyszukiwania wyróżniony](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po zlokalizowaniu klaster wirtualny ma zostać usunięty, wybierz ten zasób, a wybierz **Usuń**. Monit o potwierdzenie usunięcia klaster wirtualny.

![Zrzut ekranu witryny Azure Portal, wirtualne klastrów pulpitu nawigacyjnego, z podświetloną opcją Delete](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Obszar powiadomień portalu platformy Azure pokazuje potwierdzenie, że klaster wirtualny został usunięty. Skuteczne usunięcie klastra wirtualnego natychmiast zwalnia podsieci do ponownego wykorzystania.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Usuń klaster wirtualny za pomocą interfejsu API

Aby usunąć klaster wirtualny za pomocą interfejsu API, użyj parametrów identyfikatora URI określonego w [klastrów wirtualnych delete, Metoda](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Dowiedz się więcej o [architektura łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- Aby uzyskać samouczek, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych DNS](sql-database-managed-instance-custom-dns.md).
