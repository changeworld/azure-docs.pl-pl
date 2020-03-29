---
title: Uruchom ponownie serwer — witryna Azure portal — usługa Azure Database for MySQL
description: W tym artykule opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MySQL przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d7e158124347b302492364df46ccf5b5e78e75c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063303"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Ponowne uruchamianie usługi Azure Database dla serwera MySQL przy użyciu witryny Azure portal
W tym temacie opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MySQL. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację, co powoduje krótką awarię podczas wykonywania operacji przez serwer.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie owo wirtualnych.

Czas potrzebny do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MySQL. Aby skrócić czas ponownego uruchomienia, zaleca się zminimalizowanie aktywności występującej na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Uruchom ponownie serwer

Następujące kroki ponownego uruchomienia serwera MySQL:

1. W witrynie Azure portal wybierz swoją bazę danych platformy Azure dla serwera MySQL.

2. Na pasku narzędzi strony **Przegląd** serwera kliknij przycisk **Uruchom ponownie**.

   ![Usługa Azure Database for MySQL — omówienie — przycisk Uruchom ponownie](./media/howto-restart-server-portal/2-server.png)

3. Kliknij **przycisk Tak,** aby potwierdzić ponowne uruchomienie serwera.

   ![Usługa Azure Database for MySQL — ponowne uruchomienie potwierdzania](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Należy zauważyć, że stan serwera zmienia się na "Ponowne uruchomienie".

   ![Usługa Azure Database for MySQL — stan ponownego uruchamiania](./media/howto-restart-server-portal/4-restarting-status.png)

5. Upewnij się, że ponowne uruchomienie serwera zakończyło się pomyślnie.

   ![Usługa Azure Database for MySQL — powodzenie ponownego uruchamiania](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

[Szybki start: tworzenie bazy danych platformy Azure dla serwera MySQL przy użyciu witryny Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
