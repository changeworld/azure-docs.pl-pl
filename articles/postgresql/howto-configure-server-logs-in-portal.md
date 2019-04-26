---
title: Konfigurowanie i dostęp do dzienników serwera database for PostgreSQL w witrynie Azure Portal
description: W tym artykule opisano sposób konfigurowania i dostęp do dzienników serwera w usłudze Azure Database for PostgreSQL w witrynie Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 509c3af66e8228f142126dae6938ad74daf1d7ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421916"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurowanie i dzienników serwera dostępu w witrynie Azure portal

Można skonfigurować, listy i pobrać [— Azure Database for postgresql w warstwie dzienniki serwera](concepts-server-logs.md) w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Usługa Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Konfigurowanie dostępu do dzienników zapytań oraz dzienniki błędów. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. 

   ![Wybierz dzienniki serwera, a następnie wybierz pozycję "Kliknij tutaj, aby umożliwić..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Wybierz nagłówek **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** Aby wyświetlić parametry serwera.

5. Zmień parametry, które trzeba dopasować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione na fioletowo.

   Po zmianie parametrów, możesz kliknąć **Zapisz**. Możesz też **odrzucić** zmiany. 

   ![Długą listę parametrów o zmiany, aby zapisać lub odrzucić](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Wróć do listy dzienniki, klikając **przycisk Zamknij** (ikonę X) na **parametrów serwera** strony.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i pobieranie dzienników
Po rozpoczęciu rejestrowania, można wyświetlić listę dostępnych dzienników i Pobierz pojedyncze pliki dziennika w okienku dzienniki serwera. 

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. Strony Wyświetla listę plików dziennika, jak pokazano:

   ![Lista dzienników serwera](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika jest **postgresql rrrr mm-dd_hh0000.log**. Daty i godziny w polu Nazwa pliku jest czas, gdy dziennik został wystawiony. Obróć pliki dziennika, co godzinę lub rozmiarze 100 MB, osiągnięta jako pierwsza.

4. Jeśli to konieczne, należy użyć **pole wyszukiwania** można szybko zawęzić określonego dziennika, w oparciu o daty/godziny. Wyszukiwanie znajduje się na nazwę dziennika.

   ![Przykład wyszukiwania nazwy dzienników](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Pobierz pojedyncze pliki dziennika przy użyciu **Pobierz** przycisku (ikona strzałki w dół) obok każdego pliku dziennika, w wierszu tabeli jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [uzyskiwanie dostępu do dzienników serwera w interfejsie wiersza polecenia](howto-configure-server-logs-using-cli.md) dowiesz się, jak programowo pobrać dzienniki.
- Dowiedz się więcej o [dzienniki serwera](concepts-server-logs.md) w usłudze Azure DB dla PostgreSQL. 
- Aby uzyskać więcej informacji dotyczących rejestrowania PostgreSQL i definicjami parametrów, zobacz dokumentację PostgreSQL na [rejestrowania i raportowania błędów](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

