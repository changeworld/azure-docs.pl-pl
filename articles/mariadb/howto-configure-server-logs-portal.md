---
title: Konfigurowanie i uzyskiwanie dostępu do dzienników serwera dla Azure Database for MariaDB w Azure Portal
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników serwera w Azure Database for MariaDB z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c8be9519d3393330b3022fadd2de6a49e58ecdcf
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703501"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników serwera w Azure Portal

Można skonfigurować, wyświetlić i pobrać [Azure Database for MariaDB wolnych dzienników zapytań](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

2. Wybierz serwer Azure Database for MariaDB.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   @no__t — Dzienniki serwera 0Select, kliknij, aby skonfigurować @ no__t-1

4. Wybierz nagłówek, **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** w celu wyświetlenia parametrów serwera.

5. Zmień parametry, które należy dostosować, w tym włączenie "slow_query_log" do "ON". Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem purpurowym. 

   Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.

   ![Kliknij przycisk Zapisz lub Odrzuć](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Wróć do listy dzienników, klikając **przycisk Zamknij** (ikonę X) na stronie **parametry serwera** .

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać pojedyncze pliki dziennika w okienku Dzienniki serwera. 

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer Azure Database for MariaDB.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika, jak pokazano poniżej:

   ![Lista dzienników](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **MySQL-wolno-< nazwę serwera >-yyyymmddhh. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dzienników są obracane co 24 godziny lub 7,5 GB, w zależności od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj **pola wyszukiwania** , aby szybko zawęzić do określonego dziennika na podstawie daty/godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Pobierz poszczególne pliki dzienników przy użyciu przycisku **Pobierz** (ikona strzałki w dół) obok każdego pliku dziennika w wierszu tabeli, jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**.

1. Kliknij pozycję "+ Dodaj ustawienie diagnostyczne" ![Add ustawienia diagnostyczne @ no__t-1

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które ujścia danych mają wysyłać dzienniki wolnych zapytań (konto magazynu, centrum zdarzeń i/lub Log Analytics obszar roboczy).

1. Wybierz pozycję "MySqlSlowLogs" jako typ dziennika.
![Configure ustawienia diagnostyczne @ no__t-1

1. Po skonfigurowaniu ujścia danych w celu potoku dzienników wolnych zapytań do programu można kliknąć przycisk **Zapisz**.
![Save ustawienia diagnostyczne @ no__t-1

1. Uzyskaj dostęp do dzienników wolnych zapytań, badając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki
- Zobacz [dostęp do dzienników wolnych zapytań w interfejsie wiersza polecenia](howto-configure-server-logs-cli.md) , aby dowiedzieć się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-server-logs.md) w Azure Database for MariaDB.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania, zapoznaj się z dokumentacją MariaDB dotyczącą [dzienników](https://mariadb.com/kb/en/library/slow-query-log-overview/).