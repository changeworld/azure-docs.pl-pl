---
title: Odmów dostępu do sieci publicznej — witryna Azure portal — usługa Azure Database dla bazy danych MariaDB
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej przy użyciu witryny Azure portal dla usługi Azure Database dla bazy danych MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375241"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Odmów dostępu do sieci publicznej w bazie danych platformy Azure dla mariadb przy użyciu witryny Azure portal

W tym artykule opisano, jak skonfigurować usługę Azure Database dla serwera MariaDB, aby odmówić wszystkich konfiguracji publicznych i zezwolić tylko na połączenia za pośrednictwem prywatnych punktów końcowych w celu dalszego zwiększenia zabezpieczeń sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, potrzebujesz:

* [Usługa Azure Database dla mariadb](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ustawianie odmowy dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić odmowę dostępu do sieci publicznej serwera MariaDB:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz istniejącą usługę Azure Database dla serwera MariaDB.

1. Na stronie serwera MariaDB w obszarze **Ustawienia**kliknij pozycję **Zabezpieczenia połączenia,** aby otworzyć stronę konfiguracji zabezpieczeń połączenia.

1. W obszarze Odmów dostępu do sieci publicznej wybierz pozycję **Tak,** aby włączyć odmowę dostępu publicznego dla serwera MariaDB.

    ![Usługa Azure Database dla mariadb odmowy dostępu do sieci](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie potwierdzi, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Usługa Azure Database dla mariadb odmowa dostępu do sieci sukcesu](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-metric.md).