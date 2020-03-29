---
title: Uruchom ponownie serwer — witryna Azure portal — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano, jak można ponownie uruchomić usługę Azure Database for PostgreSQL — pojedynczy serwer przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770088"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Uruchom ponownie usługę Azure Database for PostgreSQL — pojedynczy serwer przy użyciu witryny Azure portal
W tym temacie opisano, jak można ponownie uruchomić usługę Azure Database dla serwera PostgreSQL. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację, co powoduje krótką awarię podczas wykonywania operacji przez serwer.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie owo wirtualnych.
 
Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania PostgreSQL. Aby skrócić czas ponownego uruchomienia, zaleca się zminimalizowanie aktywności występującej na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Uruchom ponownie serwer

Następujące kroki ponownego uruchomienia serwera PostgreSQL:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz swoją usługę Azure Database dla serwera PostgreSQL.

2. Na pasku narzędzi strony **Przegląd** serwera kliknij przycisk **Uruchom ponownie**.

   ![Usługa Azure Database for PostgreSQL — omówienie — przycisk Uruchom ponownie](./media/howto-restart-server-portal/2-server.png)

3. Kliknij **przycisk Tak,** aby potwierdzić ponowne uruchomienie serwera.

   ![Usługa Azure Database for PostgreSQL — ponowne uruchomienie potwierdzania](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Należy zauważyć, że stan serwera zmienia się na "Ponowne uruchomienie".

   ![Usługa Azure Database for PostgreSQL — stan ponownego uruchamiania](./media/howto-restart-server-portal/4-restarting-status.png)

5. Upewnij się, że ponowne uruchomienie serwera zakończyło się pomyślnie.

   ![Usługa Azure Database for PostgreSQL — powodzenie ponownego uruchamiania](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak ustawić parametry w usłudze Azure Database dla postgreSQL](howto-configure-server-parameters-using-portal.md)