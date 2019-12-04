---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB przy użyciu witryny Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb4e56a3f40573a65b679ee026c22dfc5d6e5fa2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769425"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Uruchom ponownie serwer Azure Database for MariaDB przy użyciu Azure Portal
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MariaDB. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Wykonaj ponowne uruchomienie serwera

Wykonaj następujące kroki, aby ponownie uruchomić serwer MariaDB:

1. W Azure Portal wybierz serwer Azure Database for MariaDB.

2. Na pasku narzędzi na stronie **Przegląd** serwera kliknij pozycję **Uruchom ponownie**.

   ![Azure Database for MariaDB — przegląd — przycisk ponownego uruchamiania](./media/howto-restart-server-portal/2-server.png)

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   ![Azure Database for MariaDB — Potwierdź ponowne uruchomienie](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   ![Azure Database for MariaDB — stan ponownego uruchomienia](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   ![Azure Database for MariaDB — pomyślne ponowne uruchomienie](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Tworzenie serwera Azure Database for MariaDB przy użyciu Azure Portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)