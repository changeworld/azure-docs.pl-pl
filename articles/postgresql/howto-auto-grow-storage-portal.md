---
title: Automatyczne powiększanie magazynu — witryna Azure portal — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak skonfigurować automatyczne powiększanie magazynu przy użyciu portalu Azure w usłudze Azure Database for PostgreSQL — Single Server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769238"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatyczne powiększanie magazynu przy użyciu witryny Azure portal w usłudze Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule opisano, jak skonfigurować usługę Azure Database dla magazynu serwera PostgreSQL, aby rosnąć bez wpływu na obciążenie.

Gdy serwer osiągnie limit przydzielonego miejsca, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne powiększanie magazynu, magazyn serwera zwiększa się, aby pomieścić rosnące dane. W przypadku serwerów z pamięcią aprowizowana mniejszą niż 100 GB rozmiar aprowizowanego magazynu zwiększa się o 5 GB, gdy tylko wolny magazyn będzie niższy niż 1 GB lub 10% aprowizowanego miejsca. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Maksymalne limity magazynowania określone [w tym miejscu](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) mają zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz automatyczne zwiększanie pamięci masowej 

Wykonaj następujące kroki, aby ustawić automatyczny wzrost magazynu serwera PostgreSQL:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz istniejącą usługę Azure Database dla serwera PostgreSQL.

2. Na stronie Serwera PostgreSQL w obszarze **Ustawienia**kliknij **pozycję Warstwa cenowa,** aby otworzyć stronę warstwy cenowej.

3. W sekcji **Automatyczny wzrost** wybierz pozycję **Tak,** aby włączyć automatyczne powiększanie magazynu.

    ![Usługa Azure Database for PostgreSQL — Settings_Pricing_tier — automatyczne wzrosty](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że auto grow został pomyślnie włączony.

    ![Usługa Azure Database for PostgreSQL — sukces automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-on-metric.md).
