---
title: Automatyczne zwiększanie magazynu w usłudze Azure Database for MySQL za pomocą witryny Azure portal
description: W tym artykule opisano, jak można włączyć automatycznego zwiększenia magazynu dla usługi Azure Database for MySQL za pomocą witryny Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676791"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatyczne zwiększanie magazynu w usłudze Azure Database for MySQL za pomocą witryny Azure portal
W tym artykule opisano sposób konfigurowania usługi Azure Database for MySQL — magazyn serwera można rozwijać bez wywierania wpływu na obciążenie pracą.

Gdy serwer osiągnie limit magazynowania przydzielone, serwer zostanie oznaczony jako tylko do odczytu. Jednak jeśli włączysz magazynu automatyczne zwiększanie, magazynu serwera wzrasta do pomieszczenia rosnącą danych. W przypadku serwerów z mniej niż 100 GB zainicjowano obsługę administracyjną magazynu rozmiar aprowizowanego magazynu jest zwiększana o 5 GB, jak ilość wolnego miejsca jest poniżej większą niż 1 GB lub 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB magazynu aprowizowanego rozmiaru aprowizowanego magazynu zwiększa się o 5% gdy ilość wolnego miejsca jest poniżej 5% rozmiaru aprowizowanego magazynu. Limity pamięci masowej w określonych [tutaj](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) zastosowania.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz automatyczne magazynu rozwoju 

Wykonaj następujące kroki, aby ustawić MySQL server storage automatyczne zwiększanie:

1. W [witryny Azure portal](https://portal.azure.com/), Wybieranie usługi Azure Database for MySQL server.

2. Na stronie serwer MySQL w obszarze **ustawienia** nagłówek, kliknij przycisk **warstwa cenowa** aby otworzyć stronę warstwa cenowa.

3. W sekcji automatycznie wzrost wybrać **tak** umożliwiające rozwój automatycznie magazynu.

    ![Azure Database for MySQL - Settings_Pricing_tier - Auto wzrostu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK** , aby zapisać zmiany.

5. Powiadomienie o potwierdzenie automatycznego wzrostu został pomyślnie włączony.

    ![Azure Database for MySQL — Powodzenie automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak tworzenie alertów dotyczących metryk](howto-alert-on-metric.md).
