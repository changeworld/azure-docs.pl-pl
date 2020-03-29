---
title: Zarządzanie dziennikami — witryna Azure portal — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano sposób konfigurowania dzienników serwera (.log) w usłudze Azure Database dla postgreSQL — pojedynczy serwer z witryny Azure portal i uzyskiwania do nich dostępu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763695"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurowanie i uzyskiwanie dostępu do bazy danych platformy Azure dla postgreSQL — dzienniki pojedynczego serwera z witryny Azure portal

Można skonfigurować, wyświetlić listę i pobrać [dzienniki usługi Azure Database for PostgreSQL](concepts-server-logs.md) z witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby mieć [usługę Azure Database dla serwera PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dzienników zapytań i dzienników błędów. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 

   ![Zrzut ekranu przedstawiający opcje dzienników serwera](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Aby wyświetlić parametry serwera, wybierz pozycję **Kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Zmień parametry, które należy dostosować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem fioletowym.

   Po zmianie parametrów wybierz pozycję **Zapisz**. Możesz też odrzucić zmiany. 

   ![Zrzut ekranu przedstawiający opcje parametrów serwera](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stronie **Parametry serwera** można powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetlanie list i dzienników pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych dzienników i pobrać poszczególne pliki dziennika. 

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie znajduje się lista plików dziennika.

   ![Zrzut ekranu przedstawiający stronę Dzienniki serwera z wyróżnioną listą dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **postgresql-yyyy-mm-dd_hh0000.log**. Data i godzina użyta w nazwie pliku to czas, w którym dziennik został wystawiony. Pliki dziennika obracają się co godzinę lub 100 MB, w zależności od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika, na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

   ![Zrzut ekranu przedstawiający stronę Dzienniki serwera z wyróżnionym polem wyszukiwania i wynikami](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Aby pobrać poszczególne pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   ![Zrzut ekranu przedstawiający stronę Dzienniki serwera z wyróżnioną ikoną strzałki w dół](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Następne kroki
- Zobacz [Dzienniki serwera programu Access w pliku CLI,](howto-configure-server-logs-using-cli.md) aby dowiedzieć się, jak programowo pobierać dzienniki.
- Dowiedz się więcej o [dziennikach serwera](concepts-server-logs.md) w usłudze Azure Database for PostgreSQL. 
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania PostgreSQL, zobacz dokumentację PostgreSQL dotyczącą [raportowania błędów i rejestrowania](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

