---
title: Automatyczne powiększanie magazynu — witryna Azure portal — usługa Azure Database for MySQL
description: W tym artykule opisano, jak włączyć automatyczne powiększanie magazynu dla usługi Azure Database dla MySQL przy użyciu witryny Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ddbcb0143a4f84d9e0f3dc2fedf780fca6637692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062515"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatyczne powiększanie magazynu w usłudze Azure Database dla mysql przy użyciu portalu Azure
W tym artykule opisano, jak skonfigurować usługę Azure Database dla magazynu serwera MySQL, aby rosła bez wpływu na obciążenie.

Gdy serwer osiągnie limit przydzielonego miejsca, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne powiększanie magazynu, magazyn serwera zwiększa się, aby pomieścić rosnące dane. W przypadku serwerów z pamięcią aprowizowana mniejszą niż 100 GB rozmiar aprowizowanego magazynu zwiększa się o 5 GB, gdy tylko wolny magazyn będzie niższy niż 1 GB lub 10% aprowizowanego miejsca. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Maksymalne limity magazynowania określone [w tym miejscu](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) mają zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz automatyczne zwiększanie pamięci masowej 

Wykonaj następujące kroki, aby ustawić automatyczny wzrost pamięci masowej serwera MySQL:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz istniejącą usługę Azure Database dla serwera MySQL.

2. Na stronie serwera MySQL w obszarze **Nagłówek Ustawienia** kliknij pozycję **Warstwa cenowa,** aby otworzyć stronę warstwy cenowej.

3. W sekcji Automatyczny wzrost wybierz pozycję **Tak,** aby włączyć automatyczne powiększanie magazynu.

    ![Usługa Azure Database for MySQL — Settings_Pricing_tier — automatyczne tworzenie](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że auto grow został pomyślnie włączony.

    ![Usługa Azure Database for MySQL — sukces automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-on-metric.md).
