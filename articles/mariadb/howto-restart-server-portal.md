---
title: Uruchom ponownie serwer — witryna Azure portal — usługa Azure Database for MariaDB
description: W tym artykule opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MariaDB przy użyciu witryny Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534715"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Ponowne uruchamianie usługi Azure Database dla serwera MariaDB przy użyciu witryny Azure portal
W tym temacie opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MariaDB. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację, co powoduje krótką awarię podczas wykonywania operacji przez serwer.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie owo wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MariaDB. Aby skrócić czas ponownego uruchomienia, zaleca się zminimalizowanie aktywności występującej na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Uruchom ponownie serwer

Następujące kroki ponownego uruchomienia serwera MariaDB:

1. W witrynie Azure portal wybierz swoją usługę Azure Database dla serwera MariaDB.

2. Na pasku narzędzi strony **Przegląd** serwera kliknij przycisk **Uruchom ponownie**.

   ![Usługa Azure Database for MariaDB — omówienie — przycisk Uruchom ponownie](./media/howto-restart-server-portal/2-server.png)

3. Kliknij **przycisk Tak,** aby potwierdzić ponowne uruchomienie serwera.

   ![Usługa Azure Database for MariaDB — ponowne uruchomienie potwierdzania](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Należy zauważyć, że stan serwera zmienia się na "Ponowne uruchomienie".

   ![Usługa Azure Database for MariaDB — stan ponownego uruchamiania](./media/howto-restart-server-portal/4-restarting-status.png)

5. Upewnij się, że ponowne uruchomienie serwera zakończyło się pomyślnie.

   ![Usługa Azure Database for MariaDB — powodzenie ponownego uruchamiania](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

[Szybki start: tworzenie usługi Azure Database dla serwera MariaDB przy użyciu portalu Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)