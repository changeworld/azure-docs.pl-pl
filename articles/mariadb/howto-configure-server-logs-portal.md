---
title: Uzyskiwanie dostępu do wolnych dzienników zapytań — witryna Azure portal — usługa Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników wolnych zapytań w usłudze Azure Database dla mariadb z witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531400"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurowanie wolnych dzienników zapytań i uzyskiwanie do nich dostępu z witryny Azure Portal

Można skonfigurować, wyświetlić listę i pobrać [dzienniki wolnych zapytań usługi Azure Database dla mariadb](concepts-server-logs.md) z witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby mieć [usługę Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Wybierz swoją usługę Azure Database dla serwera MariaDB.

3. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   ![Zrzut ekranu przedstawiający opcje dzienników serwera](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Aby wyświetlić parametry serwera, wybierz pozycję **Kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Zmień parametry, które należy dostosować, w tym przekręcając **slow_query_log** **na ON**. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem fioletowym. 

   Po zmianie parametrów wybierz pozycję **Zapisz**. Możesz też odrzucić zmiany.

   ![Zrzut ekranu przedstawiający opcje parametrów serwera](./media/howto-configure-server-logs-portal/3-save-discard.png)

Na stronie **Parametry serwera** można powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetlanie list i dzienników pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać poszczególne pliki dziennika. 

1. Otwórz witrynę Azure Portal.

2. Wybierz swoją usługę Azure Database dla serwera MariaDB.

3. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie znajduje się lista plików dziennika.

   ![Zrzut ekranu przedstawiający stronę Dzienniki serwera z wyróżnioną listą dzienników](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika jest **mysql-slow-< nazwę serwera>-yyyymmddhh.log**. Data i godzina użyta w nazwie pliku to czas, w którym dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7,5 GB, w zależności od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika, na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Aby pobrać poszczególne pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   ![Zrzut ekranu przedstawiający stronę Dzienniki serwera z wyróżnioną ikoną strzałki w dół](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **Monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia** > diagnostyczne**Dodaj ustawienie diagnostyczne**.

   ![Zrzut ekranu przedstawiający opcje ustawień diagnostycznych](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które dane pochłaniają dzienniki kwerend powolnych (konto magazynu, centrum zdarzeń lub obszar roboczy usługi Log Analytics).

1. Wybierz **MySqlSlowLogs** jako typ dziennika.
![Zrzut ekranu przedstawiający opcje konfiguracji ustawień diagnostycznych](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu pochłaniacza danych do potoku dziennika kwerendy **powolnej,** wybierz zapisz .
![Zrzut ekranu przedstawiający opcje konfiguracji ustawień diagnostycznych z wyróżnionym przyciskiem Zapisz](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Dostęp do dzienników kwerend powolny, eksplorując je w ujścia danych, które zostały skonfigurowane. Może upłynąć do 10 minut, aby pojawić się dzienniki.

## <a name="next-steps"></a>Następne kroki
- Zobacz [Dostęp powolne dzienniki kwerend w wierszu polecenia,](howto-configure-server-logs-cli.md) aby dowiedzieć się, jak programowo pobierać powolne dzienniki zapytań.
- Dowiedz się więcej o [powolnych dziennikach zapytań](concepts-server-logs.md) w usłudze Azure Database dla MariaDB.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania, zobacz dokumentację MariaDB w [dziennikach](https://mariadb.com/kb/en/library/slow-query-log-overview/).