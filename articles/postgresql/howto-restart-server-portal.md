---
title: Uruchom ponownie usługi Azure Database for PostgreSQL przy użyciu witryny Azure portal
description: W tym artykule opisano, jak można uruchomić ponownie usługi Azure Database for PostgreSQL przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: bf73120e462b740de5d2245f8a647896ac61f2c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420497"
---
# <a name="restart-azure-database-for-postgresql-server-using-the-azure-portal"></a>Uruchom ponownie usługi Azure Database for PostgreSQL przy użyciu witryny Azure portal
W tym temacie opisano, jak można uruchomić ponownie usługi Azure Database for postgresql w warstwie serwera. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację powoduje krótki przestój serwera wykonuje operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzania wcześniej żądanej operacji, takich jak skalowanie rdzeni wirtualnych.
 
Czas wymagany do ukończenia ponownego uruchomienia, zależy od procesu odzyskiwania PostgreSQL. Celu skrócenia czasu ponownego uruchomienia, zalecane jest minimalizacja ilości działań mających miejsce na serwerze przed ponownym uruchomieniu.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Wykonania ponownego uruchomienia serwera

Poniższe kroki, uruchom ponownie serwer PostgreSQL:

1. W [witryny Azure portal](https://portal.azure.com/), Wybieranie usługi Azure Database for postgresql w warstwie serwera.

2. Na pasku narzędzi serwera **Przegląd** kliknij **ponowne uruchomienie**.

   ![Azure Database dla PostgreSQL — Przegląd — ponowne uruchamianie przycisku](./media/howto-restart-server-portal/2-server.png)

3. Kliknij przycisk **tak** aby upewnić się, ponowne uruchomienie serwera.

   ![Upewnij się, usługa Azure Database for PostgreSQL — ponowne uruchomienie](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Sprawdź, czy stan serwera zmienia się na "Ponowne uruchamianie".

   ![Azure Database for PostgreSQL — stan ponownego uruchomienia](./media/howto-restart-server-portal/4-restarting-status.png)

5. Upewnij się, ponownego uruchomienia serwera zakończy się pomyślnie.

   ![Azure Database for PostgreSQL — Powodzenie z ponownym uruchomieniu](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sposób ustawiania parametrów w usłudze Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)