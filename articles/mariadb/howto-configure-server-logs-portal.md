---
title: Konfigurowanie i dostęp do dzienników serwera usługi Azure Database dla serwera MariaDB w witrynie Azure Portal
description: W tym artykule opisano sposób konfigurowania i dostęp do dzienników serwera w usłudze Azure Database dla serwera MariaDB z witryny Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4ff2fbd5976a8e203bbc43a87b31ddb1bed63402
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040656"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurowanie i dzienników serwera dostępu w witrynie Azure portal

Można skonfigurować, listy i pobrać [— Azure Database for dzienniki serwera MariaDB](concepts-server-logs.md) w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Konfigurowanie dostępu do dzienników wolnych zapytań. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz usługi Azure Database dla serwera MariaDB.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. 
   ![Wybierz dzienniki serwera, kliknij, aby skonfigurować](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Wybierz nagłówek **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** Aby wyświetlić parametry serwera.

5. Zmień parametry, które należy dopasować, włącznie z włączaniem "slow_query_log" na "Wł.". Wszystkie zmiany wprowadzone w tej sesji są wyróżnione na fioletowo. 

   Po zmianie parametrów, możesz kliknąć **Zapisz**. Możesz też **odrzucić** zmiany.

   ![Kliknij pozycję Zapisz lub Odrzuć](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Wróć do listy dzienniki, klikając **przycisk Zamknij** (ikonę X) na **parametrów serwera** strony.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i pobieranie dzienników
Po rozpoczęciu rejestrowania, można wyświetlić listę dostępnych dzienników i Pobierz pojedyncze pliki dziennika w okienku dzienniki serwera. 

1. Otwórz witrynę Azure Portal.

2. Wybierz usługi Azure Database dla serwera MariaDB.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. Strony Wyświetla listę plików dziennika, jak pokazano:

   ![Lista dzienników](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika jest **mysql - wolno — < nazwa serwera >-yyyymmddhh.log**. Daty i godziny w polu Nazwa pliku jest czas, gdy dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7.5 GB, zależnie co nastąpi wcześniej.

4. Jeśli to konieczne, należy użyć **pole wyszukiwania** można szybko zawęzić określonego dziennika, w oparciu o daty/godziny. Wyszukiwanie znajduje się na nazwę dziennika.

5. Pobierz pojedyncze pliki dziennika przy użyciu **Pobierz** przycisku (ikona strzałki w dół) obok każdego pliku dziennika, w wierszu tabeli jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [dzienniki serwera](concepts-server-logs.md) w usłudze Azure Database dla serwera MariaDB.
- Aby uzyskać więcej informacji na temat rejestrowania i definicjami parametrów, zobacz dokumentację MariaDB na [dzienniki](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!-- - See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
