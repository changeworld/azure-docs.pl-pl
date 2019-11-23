---
title: Skonfiguruj i uzyskaj dostęp do dzienników wolnych zapytań w Azure Database for MySQL z Azure Portal
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do powolnych dzienników w Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 7eeeb729973e484e9acb26f3ac8cc42693f72eea
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841561"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników wolnych zapytań z Azure Portal

Można skonfigurować, wyświetlić i pobrać [Azure Database for MySQL wolnych dzienników zapytań](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań programu MySQL. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz usługi Azure Database for MySQL server.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   ![zrzut ekranu opcji dzienników serwera](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Aby wyświetlić parametry serwera, wybierz **pozycję kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Zmień parametry, które należy dostosować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem purpurowym. 

   Po zmianie parametrów wybierz pozycję **Zapisz**. Możesz też odrzucić zmiany.

   ![Zrzut ekranu przedstawiający opcje parametrów serwera](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stronie **parametry serwera** możesz powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać pojedyncze pliki dziennika.

1. Otwórz witrynę Azure Portal.

2. Wybierz usługi Azure Database for MySQL server.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika.

   ![Zrzut ekranu strony dzienników serwera z wyróżnioną listą dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **MySQL-wolno-< nazwę serwera >-yyyymmddhh. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7,5 GB, zależnie od tego, co nastąpi wcześniej. 

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Aby pobrać pojedyncze pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   ![Zrzut ekranu strony Dzienniki serwera z wyróżnioną ikoną Strzałka w dół](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne** > **Dodaj ustawienia diagnostyczne**.

   ![Zrzut ekranu przedstawiający opcje ustawień diagnostycznych](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które ujścia danych mają wysyłać dzienniki wolnych zapytań (konto magazynu, centrum zdarzeń lub Log Analytics obszar roboczy).

1. W polu Typ dziennika wybierz pozycję **MySqlSlowLogs** .
![zrzut ekranu opcji konfiguracji ustawień diagnostycznych](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu ujścia danych w celu pomyślnego przetworzenia połączeń dzienników wolnych zapytań wybierz pozycję **Zapisz**.
![zrzut ekranu opcji konfiguracji ustawień diagnostycznych z wyróżnioną pozycją Zapisz](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Uzyskaj dostęp do dzienników wolnych zapytań, badając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki
- Zobacz [dostęp do dzienników wolnych zapytań w interfejsie wiersza polecenia](howto-configure-server-logs-in-cli.md) , aby dowiedzieć się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-server-logs.md) w Azure Database for MySQL.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania MySQL, zobacz dokumentację programu MySQL w [dzienniku](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).