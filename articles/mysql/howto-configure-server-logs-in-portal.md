---
title: Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań dla Azure Database for MySQL w Azure Portal
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do powolnych dzienników w Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703439"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Konfigurowanie i dostęp do dzienników wolnych zapytań w Azure Portal

Można skonfigurować, wyświetlić i pobrać [Azure Database for MySQL wolnych dzienników zapytań](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań programu MySQL. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz serwer Azure Database for MySQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   @no__t — Dzienniki serwera 0Select, kliknij, aby skonfigurować @ no__t-1

4. Wybierz nagłówek, **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** w celu wyświetlenia parametrów serwera.

5. Zmień parametry, które należy dostosować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem purpurowym. 

   Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.

   ![Kliknij przycisk Zapisz lub Odrzuć](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Wróć do listy dzienników, klikając **przycisk Zamknij** (ikonę X) na stronie **parametry serwera** .

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać pojedyncze pliki dziennika w okienku Dzienniki serwera.

1. Otwórz Azure Portal.

2. Wybierz serwer Azure Database for MySQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika, jak pokazano poniżej:

   ![Lista dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **MySQL-wolno-< nazwę serwera >-yyyymmddhh. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dzienników są obracane co 24 godziny lub 7,5 GB, w zależności od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj **pola wyszukiwania** , aby szybko zawęzić do określonego dziennika na podstawie daty/godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Pobierz poszczególne pliki dzienników przy użyciu przycisku **Pobierz** (ikona strzałki w dół) obok każdego pliku dziennika w wierszu tabeli, jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-in-portal/5-download.png)

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
- Zobacz [dostęp do dzienników wolnych zapytań w interfejsie wiersza polecenia](howto-configure-server-logs-in-cli.md) , aby dowiedzieć się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-server-logs.md) w Azure Database for MySQL.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania MySQL, zobacz dokumentację programu MySQL w [dzienniku](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).