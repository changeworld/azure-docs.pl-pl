---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for MySQL
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1857c67a77b9600f3d8f7c222a8e06f899fba728
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774055"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Uruchom ponownie serwer Azure Database for MySQL przy użyciu Azure Portal
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MySQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MySQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Wykonaj ponowne uruchomienie serwera

Wykonaj następujące kroki, aby ponownie uruchomić serwer MySQL:

1. W Azure Portal wybierz serwer Azure Database for MySQL.

2. Na pasku narzędzi na stronie **Przegląd** serwera kliknij pozycję **Uruchom ponownie**.

   ![Azure Database for MySQL — przegląd — przycisk ponownego uruchamiania](./media/howto-restart-server-portal/2-server.png)

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   ![Azure Database for MySQL — Potwierdź ponowne uruchomienie](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   ![Azure Database for MySQL — stan ponownego uruchomienia](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   ![Azure Database for MySQL — pomyślne ponowne uruchomienie](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
