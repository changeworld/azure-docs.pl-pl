---
title: Powiększ magazyn przy użyciu Azure Portal w ramach Azure Database for PostgreSQL-pojedynczego serwera
description: W tym artykule opisano sposób włączania magazynu z obsługą autowzrostu przy użyciu Azure Portal na pojedynczym serwerze Azure Database for PostgreSQL
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 1d028093b030e1f2cf00ceae9297563c36c314c5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142877"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Powiększ magazyn przy użyciu Azure Portal w ramach Azure Database for PostgreSQL-pojedynczego serwera
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for PostgreSQL do wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzieloną liczbę magazynów, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz funkcję autozwiększania magazynu, magazyn serwera zwiększy się, aby uwzględnić rosnące dane. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie jest mniejsza niż 1 GB lub 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu 

Wykonaj następujące kroki, aby ustawić funkcję autowzrostu magazynu serwera PostgreSQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for PostgreSQL.

2. Na stronie serwer PostgreSQL w obszarze **Ustawienia**kliknij pozycję **warstwa cenowa** , aby otworzyć stronę warstwa cenowa.

3. W sekcji **autoprzyrost** wybierz pozycję **tak** , aby włączyć funkcję automatycznego zwiększania rozmiaru magazynu.

    ![Azure Database for PostgreSQL-Settings_Pricing_tier — autowzrostu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK** , aby zapisać zmiany.

5. Powiadomienie zostanie potwierdzone, że funkcja autozwiększania została pomyślnie włączona.

    ![Azure Database for PostgreSQL — powodzenie automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
