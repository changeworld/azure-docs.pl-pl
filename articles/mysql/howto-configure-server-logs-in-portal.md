---
title: Konfigurowanie i dostępu do dzienników serwera bazy danych Azure dla programu MySQL w portalu Azure
description: W tym artykule opisano sposób konfigurowania i dostęp do dzienników serwera w bazie danych Azure dla programu MySQL z portalu Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b68d92688b18e69f74c0e0a9b2e572795a288579
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurowanie i dzienników serwera dostępu w portalu Azure

Można skonfigurować, listy i pobrać [bazy danych Azure do dzienników serwera MySQL](concepts-server-logs.md) z portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- [Azure bazy danych MySQL serwera](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Konfigurowanie dostępu do dziennika powolne zapytania MySQL. 

1. Zaloguj się w [Portalu Azure](http://portal.azure.com/).

2. Wybierz bazy danych Azure, aby serwer MySQL.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz **dzienniki serwera**. 
   ![Wybierz dzienniki serwera, kliknij, aby skonfigurować](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Wybierz nagłówek **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** można znaleźć w temacie parametry serwera.

5. Zmień parametry, które należy skorygować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione na fioletowo. 

   Po zmianie parametrów, możesz kliknąć **zapisać**. Można także **odrzucić** zmiany.

   ![Kliknij przycisk Zapisz lub Odrzuć](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Wróć do listy dzienników, klikając **przycisk Zamknij** (X ikona) na **parametry serwera** strony.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i Pobierz dzienniki
Po rozpoczęciu rejestrowania, można wyświetlić listę dostępnych dzienników i pobrać osobnych plikach dziennika w okienku dzienniki serwera. 

1. Otwórz witrynę Azure Portal.

2. Wybierz bazy danych Azure, aby serwer MySQL.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz **dzienniki serwera**. Strona zawiera listę plików dziennika, jak pokazano:

   ![Lista dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika jest **mysql — wolno — < nazwa serwera >-yyyymmddhh.log**. Data i godzina w polu Nazwa pliku jest czas, gdy dziennik został wystawiony. Pliki dzienników są obracane co 24 godziny lub 7.5 GB zależności miało miejsce wcześniej.

4. Jeśli to konieczne, użyj **pole wyszukiwania** do szybkiego zawężania określonego dziennika według daty/godziny. Wyszukiwanie znajduje się na nazwę dziennika.

5. Pobierz pojedyncze pliki dziennika przy użyciu **Pobierz** przycisk (ikona strzałki w dół) obok każdego pliku dziennika, w wierszu tabeli, jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [Access Server Logs WE CLI](howto-configure-server-logs-in-cli.md) Aby dowiedzieć się, jak pobrać dzienniki programowo.
- Dowiedz się więcej o [dzienniki serwera](concepts-server-logs.md) w bazie danych Azure dla programu MySQL. 
- Aby uzyskać więcej informacji o definicji parametrów i MySQL rejestrowania, zobacz dokumentację MySQL na [dzienniki](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

