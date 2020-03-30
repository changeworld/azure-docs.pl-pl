---
title: Odmów dostępu do sieci publicznej — witryna Azure portal — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej przy użyciu witryny Azure portal dla usługi Azure Database dla pojedynczego serwera PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375124"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odmów dostępu do sieci publicznej w bazie danych platformy Azure dla pojedynczego serwera PostgreSQL przy użyciu witryny Azure portal

W tym artykule opisano, jak skonfigurować usługę Azure Database dla pojedynczego serwera PostgreSQL, aby odmówić wszystkich konfiguracji publicznych i zezwolić tylko na połączenia za pośrednictwem prywatnych punktów końcowych w celu dalszego zwiększenia zabezpieczeń sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, potrzebujesz:

* Usługa [Azure Database dla pojedynczego serwera PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ustawianie odmowy dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić postgreSQL pojedynczy serwer Odmów dostępu do sieci publicznej:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz istniejącą usługę Azure Database dla pojedynczego serwera PostgreSQL.

1. Na stronie PostgreSQL Single server (Pojedynczy serwer PostgreSQL) w obszarze **Ustawienia**kliknij pozycję **Zabezpieczenia połączenia,** aby otworzyć stronę konfiguracji zabezpieczeń połączenia.

1. W **obszarze Odmów dostępu do sieci publicznej**wybierz pozycję **Tak,** aby włączyć odmowę dostępu publicznego dla pojedynczego serwera PostgreSQL.

    ![Usługa Azure Database dla pojedynczego serwera PostgreSQL Odmawia dostęp do sieci](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie potwierdzi, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Usługa Azure Database for PostgreSQL Single server Deny network access success](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-on-metric.md).