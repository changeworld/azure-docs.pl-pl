---
title: Automatyczne powiększanie magazynu — witryna Azure portal — usługa Azure Database for MariaDB
description: W tym artykule opisano, jak włączyć automatyczne powiększanie magazynu dla usługi Azure Database dla MariaDB przy użyciu witryny Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528901"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatyczne powiększanie magazynu w usłudze Azure Database dla mariadb przy użyciu portalu Azure
W tym artykule opisano, jak skonfigurować usługę Azure Database dla magazynu serwera MariaDB, aby rosnąć bez wpływu na obciążenie.

Gdy serwer osiągnie limit przydzielonego miejsca, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne powiększanie magazynu, magazyn serwera zwiększa się, aby pomieścić rosnące dane. W przypadku serwerów z pamięcią aprowizowana mniejszą niż 100 GB rozmiar aprowizowanego magazynu zwiększa się o 5 GB, gdy tylko wolny magazyn będzie niższy niż 1 GB lub 10% aprowizowanego miejsca. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Maksymalne limity magazynowania określone [w tym miejscu](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) mają zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz automatyczne zwiększanie pamięci masowej 

Wykonaj następujące kroki, aby ustawić automatyczny wzrost magazynu serwera MariaDB:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz istniejącą usługę Azure Database dla serwera MariaDB.

2. Na stronie serwera MariaDB w obszarze **Nagłówek Ustawienia** kliknij pozycję **Warstwa cenowa,** aby otworzyć stronę warstwy cenowej.

3. W sekcji Automatyczny wzrost wybierz pozycję **Tak,** aby włączyć automatyczne powiększanie magazynu.

    ![Usługa Azure Database for MariaDB — Settings_Pricing_tier — automatyczne tworzenie](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że auto grow został pomyślnie włączony.

    ![Usługa Azure Database for MariaDB — sukces automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-metric.md).
