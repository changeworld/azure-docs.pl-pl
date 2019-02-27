---
title: Uruchom ponownie usługi Azure Database dla serwera MySQL przy użyciu witryny Azure portal
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MySQL za pomocą witryny Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 6cf6679dc6398b112ffc964f50986b2ab30aba47
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882488"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Uruchom ponownie usługi Azure Database dla serwera MySQL przy użyciu witryny Azure portal
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MySQL. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację powoduje krótki przestój serwera wykonuje operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzania wcześniej żądanej operacji, takich jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia, zależy od procesu odzyskiwania MySQL. Celu skrócenia czasu ponownego uruchomienia, zalecane jest minimalizacja ilości działań mających miejsce na serwerze przed ponownym uruchomieniu.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Wykonania ponownego uruchomienia serwera

Poniższe kroki, uruchom ponownie serwer MySQL:

1. W witrynie Azure portal wybierz usługi Azure Database for MySQL server.

2. Na pasku narzędzi serwera **Przegląd** kliknij **ponowne uruchomienie**.

   ![Usługa Azure Database for MySQL - Overview - Uruchom ponownie przycisk](./media/howto-restart-server-portal/2-server.png)

3. Kliknij przycisk **tak** aby upewnić się, ponowne uruchomienie serwera.

   ![Upewnij się, usługa Azure Database for MySQL — ponowne uruchomienie](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Sprawdź, czy stan serwera zmienia się na "Ponowne uruchamianie".

   ![Azure Database for MySQL — stan ponownego uruchomienia](./media/howto-restart-server-portal/4-restarting-status.png)

5. Upewnij się, ponownego uruchomienia serwera zakończy się pomyślnie.

   ![Azure Database for MySQL — Powodzenie z ponownym uruchomieniu](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybki start: Tworzenie usługi Azure Database dla serwera MySQL przy użyciu witryny Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)