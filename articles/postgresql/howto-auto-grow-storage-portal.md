---
title: Automatyczne zwiększanie magazynu przy użyciu witryny Azure portal w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można włączyć automatycznego zwiększenia magazynu w witrynie Azure portal w usłudze Azure Database dla PostegreSQL — pojedynczy serwer
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676761"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatyczne zwiększanie magazynu przy użyciu witryny Azure portal w usłudze Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule opisano sposób konfigurowania usługi Azure Database for postgresql — magazyn serwera można rozwijać bez wywierania wpływu na obciążenie pracą.

Gdy serwer osiągnie limit magazynowania przydzielone, serwer zostanie oznaczony jako tylko do odczytu. Jednak jeśli włączysz magazynu automatyczne zwiększanie, magazynu serwera wzrasta do pomieszczenia rosnącą danych. W przypadku serwerów z mniej niż 100 GB zainicjowano obsługę administracyjną magazynu rozmiar aprowizowanego magazynu jest zwiększana o 5 GB, jak ilość wolnego miejsca jest poniżej większą niż 1 GB lub 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB magazynu aprowizowanego rozmiaru aprowizowanego magazynu zwiększa się o 5% gdy ilość wolnego miejsca jest poniżej 5% rozmiaru aprowizowanego magazynu. Limity pamięci masowej w określonych [tutaj](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) zastosowania.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz automatyczne magazynu rozwoju 

Wykonaj następujące kroki, aby ustawić rosnąć automatycznie magazynu serwera PostgreSQL:

1. W [witryny Azure portal](https://portal.azure.com/), Wybieranie usługi Azure Database for postgresql w warstwie serwera.

2. Na stronie serwera postgresql — w obszarze **ustawienia**, kliknij przycisk **warstwa cenowa** o otwarcie strony warstwy cenowej.

3. W **automatycznego wzrostu** zaznacz **tak** umożliwiające rozwój automatycznie magazynu.

    ![Azure Database for PostgreSQL - Settings_Pricing_tier - Auto wzrostu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK** , aby zapisać zmiany.

5. Powiadomienie o potwierdzenie automatycznego wzrostu został pomyślnie włączony.

    ![Azure Database for PostgreSQL — Powodzenie automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak tworzenie alertów dotyczących metryk](howto-alert-on-metric.md).
