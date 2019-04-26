---
title: Konfigurowanie parametrów serwera w usłudze Azure Database for PostgreSQL za pomocą witryny Azure portal
description: W tym artykule opisano jak skonfigurować parametry serwera w usłudze Azure Database for PostgreSQL w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0d0626c48ecebdead604aab93ab0602c698d0d77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421452"
---
# <a name="configure-server-parameters-in-azure-portal"></a>Konfigurowanie parametrów serwera w witrynie Azure portal
Można wyświetlać, Pokaż i zaktualizować parametry konfiguracji dla usługi Azure Database for postgresql w warstwie serwera za pomocą witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, czego potrzebujesz:
- [Usługa Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Wyświetlanie i edytowanie parametrów
1. Otwórz [portal Azure](https://portal.azure.com).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W obszarze **ustawienia** zaznacz **parametrów serwera**. Strona zawiera listę parametrów, wartości i opisy.
![Strona przeglądu parametrów](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Wybierz **listę rozwijaną** przycisk, aby wyświetlić możliwe wartości dla parametrów typu wyliczenia, takich jak client_min_messages.
![Wyliczanie listy w dół](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Wybierz lub Najedź kursorem na **i** przycisk (informacje), aby wyświetlić zakres możliwych wartości dla parametrów liczbowych, takich jak cpu_index_tuple_cost.
![przycisk informacje](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Jeśli to konieczne, należy użyć **pole wyszukiwania** umożliwiają zawężenie określonego parametru. Wyszukiwanie znajduje się na nazwę i opis parametrów.
![Wyniki wyszukiwania](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Zmiany wartości parametrów, który chcesz dostosować. Wszystkie zmiany wprowadzone w sesji są wyróżnione na fioletowo. Po zmianie wartości, można wybrać **Zapisz**. Możesz też **odrzucić** zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Jeśli masz zapisane nowe wartości dla parametrów, zawsze możesz przywrócić wszystko, co do wartości domyślnych, wybierając **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej:
- [Omówienie parametry serwera w usłudze Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurowanie parametrów przy użyciu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)
