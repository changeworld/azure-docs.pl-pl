---
title: Konfigurowanie i dostęp dzienniki wolnych zapytań dla usługi Azure Database for MySQL w witrynie Azure portal
description: W tym artykule opisano sposób konfigurowania i dostęp powolne dzienników w usłudze Azure Database for MySQL w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b16ac525d41eb2423828a647fdb75fd3f4a80a31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052718"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Konfigurowanie i dzienniki wolnych zapytań dostępu, w witrynie Azure portal

Można skonfigurować, listy i pobrać [— Azure Database for MySQL — dzienniki wolnych zapytań](concepts-server-logs.md) w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Konfigurowanie dostępu do dzienników wolnych zapytań MySQL. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz usługi Azure Database for MySQL server.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. 
   ![Wybierz dzienniki serwera, kliknij, aby skonfigurować](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Wybierz nagłówek **kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika** Aby wyświetlić parametry serwera.

5. Zmień parametry, które trzeba dopasować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione na fioletowo. 

   Po zmianie parametrów, możesz kliknąć **Zapisz**. Możesz też **odrzucić** zmiany.

   ![Kliknij pozycję Zapisz lub Odrzuć](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Wróć do listy dzienniki, klikając **przycisk Zamknij** (ikonę X) na **parametrów serwera** strony.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i pobieranie dzienników
Po rozpoczęciu rejestrowania, można wyświetlić listę dostępnych wolnych zapytań dzienników i Pobierz pojedyncze pliki dziennika w okienku dzienniki serwera.

1. Otwórz witrynę Azure Portal.

2. Wybierz usługi Azure Database for MySQL server.

3. W obszarze **monitorowanie** sekcji na pasku bocznym wybierz opcję **dzienniki serwera**. Strony Wyświetla listę plików dziennika, jak pokazano:

   ![Lista dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika jest **mysql - wolno — < nazwa serwera >-yyyymmddhh.log**. Daty i godziny w polu Nazwa pliku jest czas, gdy dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7.5 GB, zależnie co nastąpi wcześniej.

4. Jeśli to konieczne, należy użyć **pole wyszukiwania** można szybko zawęzić określonego dziennika, w oparciu o daty/godziny. Wyszukiwanie znajduje się na nazwę dziennika.

5. Pobierz pojedyncze pliki dziennika przy użyciu **Pobierz** przycisku (ikona strzałki w dół) obok każdego pliku dziennika, w wierszu tabeli jak pokazano:

   ![Kliknij ikonę pobierania](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [dostępu wolnych zapytań dzienników w interfejsie wiersza polecenia](howto-configure-server-logs-in-cli.md) dowiesz się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolne dzienniki zapytań](concepts-server-logs.md) w usłudze Azure Database for MySQL.
- Aby uzyskać więcej informacji na temat rejestrowanie MySQL i definicjami parametrów, zobacz dokumentację MySQL na [dzienniki](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).