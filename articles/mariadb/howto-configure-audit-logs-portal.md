---
title: Konfigurowanie i dzienniki inspekcji dostępu do usługi Azure Database dla serwera MariaDB w witrynie Azure portal
description: W tym artykule opisano sposób konfigurowania i uzyskać dostęp do dzienników inspekcji w usłudze Azure Database dla serwera MariaDB z witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c9ee106972cc78a08709d2ce55d2dfddc96edbf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079238"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurowanie i dostęp do dzienników inspekcji w witrynie Azure portal

Można skonfigurować [— Azure Database for dzienników inspekcji MariaDB](concepts-audit-logs.md) i ustawień diagnostycznych w witrynie Azure portal.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków w tym przewodniku, potrzebne są:

- [Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Skonfiguruj rejestrowanie inspekcji

Włącz i skonfiguruj rejestrowanie inspekcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz usługi Azure Database dla serwera MariaDB.

1. W obszarze **ustawienia** sekcji na pasku bocznym wybierz opcję **parametrów serwera**.
    ![Parametry serwera](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizacja **audit_log_enabled** parametru na wartość ON.
    ![Włączanie dzienników inspekcji](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Wybierz zdarzenia, które mają być rejestrowane, aktualizując **audit_log_events** parametru.
    ![Zdarzenia dziennika inspekcji](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Dodaj wszystkich użytkowników MariaDB, które mają być wykluczone z rejestrowania, aktualizując **audit_log_exclude_users** parametru. Określ użytkowników, zapewniając ich nazw użytkowników MariaDB.
    ![Użytkownicy wykluczania dziennika inspekcji](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po zmianie parametrów, możesz kliknąć **Zapisz**. Możesz też **odrzucić** zmiany.
    ![Zapisz](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **ustawień diagnostycznych**.

1. Kliknij pozycję "+ Dodaj ustawienie diagnostyczne" ![Dodaj ustawienie diagnostyczne](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostyczne.

1. Określ dane, które wychwytywanie do wysyłania dzienników inspekcji (konto magazynu, Centrum zdarzeń i/lub obszar roboczy usługi Log Analytics).

1. Wybierz "MySqlAuditLogs" jako typ dziennika.
![Konfiguruj ustawienie diagnostyczne](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu ujścia danych, aby przekazać dzienniki inspekcji, aby kliknąć **Zapisz**.
![Zapisz ustawienie diagnostyczne](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Dostęp do dzienników inspekcji, eksplorując w ujścia danych, które zostały skonfigurowane. Może upłynąć do 10 minut, zanim dzienniki będą widoczne.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dzienniki inspekcji](concepts-audit-logs.md) w usłudze Azure Database dla serwera MariaDB.