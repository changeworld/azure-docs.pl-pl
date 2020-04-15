---
title: Dzienniki inspekcji dostępu — witryna Azure portal — usługa Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania dzienników inspekcji i uzyskiwania do nich dostępu z witryny Azure Database dla mysql z witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380304"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurowanie dzienników inspekcji usługi Azure Database dla usługi MySQL i uzyskiwanie do nich dostępu w portalu Azure

W witrynie Azure Portal można skonfigurować [usługę Azure Database dla dzienników inspekcji MySQL](concepts-audit-logs.md) i ustawień diagnostycznych.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejść przez ten przewodnik, potrzebujesz:

- [Usługa Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

Włączanie i konfigurowanie rejestrowania inspekcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz swoją usługę Azure Database dla serwera MySQL.

1. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **Parametry serwera**.
    ![Parametry serwera](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Zaktualizuj parametr **audit_log_enabled** do on.
    ![Włączanie dzienników inspekcji](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Wybierz [typy zdarzeń,](concepts-audit-logs.md#configure-audit-logging) które mają być rejestrowane, aktualizując **parametr audit_log_events.**
    ![Zdarzenia dziennika inspekcji](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Dodaj wszystkich użytkowników MySQL, którzy mają zostać wykluczeni z rejestrowania, aktualizując parametr **audit_log_exclude_users.** Określ użytkowników, podając ich nazwę użytkownika MySQL.
    ![Dziennik inspekcji wyklucza użytkowników](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.
    ![Zapisywanie](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**.

1. Kliknij na "+ Dodaj ![ustawienie diagnostyczne" Dodaj ustawienie diagnostyczne](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które pochłaniacze danych mają być wysyłane dzienniki inspekcji (konto magazynu, centrum zdarzeń i/lub obszar roboczy usługi Log Analytics).

1. Wybierz "MySqlAuditLogs" jako typ dziennika.
![Konfigurowanie ustawienia diagnostycznego](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu pochłaniacze danych do potoku dzienników inspekcji, można **kliknąć**zapisz .
![Zapisz ustawienie diagnostyczne](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Dostęp do dzienników inspekcji, eksplorując je w sink danych. Może upłynąć do 10 minut, aby pojawili się dzienniki.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md) w usłudze Azure Database for MySQL
- Dowiedz się, jak skonfigurować dzienniki inspekcji w wierszu [polecenia platformy Azure](howto-configure-audit-logs-cli.md)