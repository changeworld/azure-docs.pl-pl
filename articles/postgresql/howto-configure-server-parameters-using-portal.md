---
title: Konfigurowanie parametrów serwera — witryna Azure portal — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano sposób konfigurowania parametrów postgres w usłudze Azure Database dla postgreSQL za pośrednictwem witryny Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763679"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurowanie parametrów serwera w bazie danych platformy Azure dla postgreSQL — pojedynczy serwer za pośrednictwem witryny Azure portal 
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera usługi Azure Database dla postgreSQL za pośrednictwem witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Wyświetlanie i edytowanie parametrów
1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **USTAWIENIA** wybierz pozycję **Parametry serwera**. Strona zawiera listę parametrów, ich wartości i opisów.
![Strona przeglądowa dla parametrów](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Wybierz przycisk **rozwijany,** aby wyświetlić możliwe wartości dla wyliczonych parametrów typu, takich jak client_min_messages.
![Wyliczaj rozwijane](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Wybierz lub umieść wskaźnik myszy na przycisku **i** (informacje), aby wyświetlić zakres możliwych wartości parametrów liczbowych, takich jak cpu_index_tuple_cost.
![przycisk informacji](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. W razie potrzeby użyj **pola wyszukiwania,** aby zawęzić do określonego parametru. Wyszukiwanie znajduje się na nazwie i opisie parametrów.
![Wyniki wyszukiwania](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Zmień wartości parametrów, które chcesz dostosować. Wszystkie zmiany wprowadzone w sesji są wyróżnione kolorem fioletowym. Po zmianie wartości można wybrać pozycję **Zapisz**. Możesz też **odrzucić** zmiany.
![Zapisywanie lub odrzucanie zmian](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Jeśli zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystko do wartości domyślnych, wybierając **opcję Resetuj wszystko do wartości domyślnej**.
![Resetowanie wszystkich do ustawień domyślnych](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:
- [Omówienie parametrów serwera w usłudze Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurowanie parametrów przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)
