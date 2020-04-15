---
title: Dzienniki inspekcji dostępu — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database dla bazy danych MariaDB
description: W tym artykule opisano sposób konfigurowania dzienników inspekcji i uzyskiwania do nich dostępu w usłudze MariaDB z interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384196"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurowanie dzienników inspekcji i uzyskiwanie do nich dostępu w wierszu polecenia platformy Azure

Można skonfigurować [usługi Azure Database dla dzienników inspekcji MariaDB](concepts-audit-logs.md) z interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejść przez ten przewodnik, potrzebujesz:

- [Usługa Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

Włącz i skonfiguruj rejestrowanie inspekcji, wykonując następujące czynności: 

1. Włącz dzienniki inspekcji, ustawiając parametr **audit_logs_enabled** na "ON". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Wybierz [typy zdarzeń,](concepts-audit-logs.md#configure-audit-logging) które mają być rejestrowane, aktualizując **parametr audit_log_egitvents.**
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Dodaj wszystkich użytkowników MariaDB, które mają zostać wykluczone z rejestrowania, aktualizując **parametr audit_log_exclude_users.** Określ użytkowników, podając ich nazwę użytkownika MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Dodaj wszystkich konkretnych użytkowników MariaDB, które mają zostać uwzględnione do rejestrowania, aktualizując **parametr audit_log_include_users.** Określ użytkowników, podając ich nazwę użytkownika MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md) w usłudze Azure Database for MariaDB
- Dowiedz się, jak skonfigurować dzienniki inspekcji w [witrynie Azure portal](howto-configure-audit-logs-portal.md)