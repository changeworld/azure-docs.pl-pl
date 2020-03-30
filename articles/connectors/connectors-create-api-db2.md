---
title: Uzyskiwanie dostępu do zasobów IBM DB2 i zarządzanie nimi
description: Odczytywanie, edytowanie, aktualizowanie i zarządzanie zasobami IBM DB2 przez tworzenie zautomatyzowanych przepływów pracy przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651386"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Uzyskiwanie dostępu do zasobów IBM DB2 i zarządzanie nimi przy użyciu usługi Azure Logic Apps

Dzięki [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznikowi IBM DB2](/connectors/db2/)można tworzyć zautomatyzowane zadania i przepływy pracy na podstawie zasobów przechowywanych w bazie danych DB2. Przepływy pracy mogą łączyć się z zasobami w bazie danych, odczytywać i wystawiać tabele bazy danych, dodawać wiersze, zmieniać wiersze, usuwać wiersze i nie tylko. Można dołączyć akcje w aplikacjach logiki, które otrzymują odpowiedzi z bazy danych i udostępnić dane wyjściowe dla innych akcji.

W tym artykule pokazano, jak można utworzyć aplikację logiki, która wykonuje różne operacje bazy danych. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Obsługiwane platformy i wersje

Łącznik DB2 zawiera klienta firmy Microsoft, który komunikuje się ze zdalnymi serwerami DB2 w sieci TCP/IP. Tego łącznika można używać do uzyskiwania dostępu do baz danych w chmurze, takich jak IBM DB2 dla systemu Windows działających w wirtualizacji platformy Azure. Po [zainstalowaniu i skonfigurowaniu lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md)można również uzyskać dostęp do lokalnych baz danych DB2.

Złącze IBM DB2 obsługuje te platformy i wersje IBM DB2 wraz z produktami zgodnymi z IBM DB2, które obsługują program SQLAM (Distributed Relational Database Architecture) SQL Access Manager (SQLAM) w wersjach 10 i 11:

| Platforma | Wersja | 
|----------|---------|
| IBM DB2 dla z/OS | 11.1, 10.1 |
| IBM DB2 dla i | 7.3, 7.2, 7.1 |
| IBM DB2 dla LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Obsługiwane operacje bazy danych

Łącznik IBM DB2 obsługuje następujące operacje bazy danych, które są mapowane na odpowiednie akcje w łączniku:

| Operacja bazy danych | Akcja łącznika |
|--------------------|------------------|
| Lista tabel bazy danych | Pobierz tabele |
| Odczyt jednego wiersza przy użyciu funkcji SELECT | Pobierz wiersz |
| Odczytywanie wszystkich wierszy przy użyciu funkcji SELECT | Pobierz wiersze |
| Dodawanie jednego wiersza za pomocą INSERT | Wstaw wiersz |
| Edytowanie jednego wiersza przy użyciu funkcji UPDATE | Aktualizuj wiersz |
| Usuwanie jednego wiersza za pomocą funkcji DELETE | Usuń wiersz |
|||

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Baza danych IBM DB2 w chmurze lub lokalnie

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do bazy danych DB2. Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, wybierz oddzielny wyzwalacz, na przykład wyzwalacz **cyklu.**
Przykłady w tym artykule użyć wyzwalacza **cyklu.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Dodawanie akcji DB2 — pobierz tabele

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki, jeśli nie jest jeszcze otwarty.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania wpisz "db2" jako filtr. W tym przykładzie na liście akcji wybierz tę akcję: **Pobierz tabele (Wersja zapoznawcza)**

   ![Wybierz akcję](./media/connectors-create-api-db2/select-db2-action.png)

   Zostanie wyświetlony monit o podanie szczegółów połączenia bazy danych DB2.

1. Wykonaj kroki tworzenia połączeń dla [baz danych w chmurze](#cloud-connection) lub lokalnych baz [danych.](#on-premises-connection)

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Łączenie się z chmurą DB2

Aby skonfigurować połączenie, podaj te szczegóły połączenia po wyświetleniu monitu, wybierz pozycję **Utwórz**, a następnie zapisz aplikację logiki:

| Właściwość | Wymagany | Opis |
|----------|----------|-------------|
| **Łączenie za pośrednictwem bramy lokalnej** | Nie | Dotyczy tylko połączeń lokalnych. |
| **Nazwa połączenia** | Tak | Nazwa połączenia, na przykład "MyLogicApp-DB2-connection" |
| **Serwer** | Tak | Numer portu dwukropek adresu lub aliasu serwera DB2, na przykład "myDB2server.cloudapp.net:50000" <p><p>**Uwaga:** Ta wartość jest ciągiem reprezentującym adres lub alias TCP/IP w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP. |
| **baza danych** | Tak | Nazwa bazy danych <p><p>**Uwaga:** Ta wartość jest ciągiem reprezentującym nazwę relacyjnej bazy danych DRDA (RDBNAM): <p>- DB2 dla z/OS akceptuje ciąg 16-bajtowy, w którym baza danych jest znana jako "IBM DB2 for z/OS" lokalizacji. <br>- DB2 dla i akceptuje ciąg 18-bajtowy, gdzie baza danych jest znana jako "IBM DB2 for i" relacyjnej bazy danych. <br>- DB2 dla LUW akceptuje ciąg 8-bajtowy. |
| **Nazwę użytkownika** | Tak | Twoja nazwa użytkownika bazy danych <p><p>**Uwaga:** Ta wartość jest ciągiem, którego długość jest oparta na określonej bazie danych: <p><p>- DB2 dla z/OS akceptuje ciąg 8-bajtowy. <br>- DB2 dla i akceptuje ciąg 10-bajtowy. <br>- DB2 dla Systemu Linux lub UNIX akceptuje ciąg 8-bajtowy. <br>- DB2 dla systemu Windows akceptuje ciąg 30-bajtowy. |
| **Hasło** | Tak | Twoje hasło do bazy danych |
||||

Przykład:

![Szczegóły połączenia dla baz danych w chmurze](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Łączenie się z lokalną bazą danych DB2

Przed utworzeniem połączenia musi być już zainstalowana lokalna brama danych. W przeciwnym razie nie można zakończyć konfigurowania połączenia. Jeśli masz instalację bramy, kontynuuj dostarczanie tych szczegółów połączenia, a następnie wybierz pozycję **Utwórz**.

| Właściwość | Wymagany | Opis |
|----------|----------|-------------|
| **Łączenie za pośrednictwem bramy lokalnej** | Tak | Ma zastosowanie, gdy chcesz połączenie lokalne i pokazuje właściwości połączenia lokalnego. |
| **Nazwa połączenia** | Tak | Nazwa połączenia, na przykład "MyLogicApp-DB2-connection" | 
| **Serwer** | Tak | Numer portu dwukropek adresu lub aliasu serwera DB2, na przykład "myDB2server:50000" <p><p>**Uwaga:** Ta wartość jest ciągiem reprezentującym adres lub alias TCP/IP w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP. |
| **baza danych** | Tak | Nazwa bazy danych <p><p>**Uwaga:** Ta wartość jest ciągiem reprezentującym nazwę relacyjnej bazy danych DRDA (RDBNAM): <p>- DB2 dla z/OS akceptuje ciąg 16-bajtowy, w którym baza danych jest znana jako "IBM DB2 for z/OS" lokalizacji. <br>- DB2 dla i akceptuje ciąg 18-bajtowy, gdzie baza danych jest znana jako "IBM DB2 for i" relacyjnej bazy danych. <br>- DB2 dla LUW akceptuje ciąg 8-bajtowy. |
| **Uwierzytelnianie** | Tak | Typ uwierzytelniania połączenia, na przykład "Podstawowe" <p><p>**Uwaga:** Wybierz tę wartość z listy, która zawiera system Basic lub Windows (Kerberos). |
| **Nazwę użytkownika** | Tak | Twoja nazwa użytkownika bazy danych <p><p>**Uwaga:** Ta wartość jest ciągiem, którego długość jest oparta na określonej bazie danych: <p><p>- DB2 dla z/OS akceptuje ciąg 8-bajtowy. <br>- DB2 dla i akceptuje ciąg 10-bajtowy. <br>- DB2 dla Systemu Linux lub UNIX akceptuje ciąg 8-bajtowy. <br>- DB2 dla systemu Windows akceptuje ciąg 30-bajtowy. |
| **Hasło** | Tak | Twoje hasło do bazy danych |
| **Brama** | Tak | Nazwa zainstalowanej lokalnej bramy danych <p><p>**Uwaga:** Wybierz tę wartość z listy, która zawiera wszystkie zainstalowane bramy danych w ramach subskrypcji platformy Azure i grupy zasobów. |
||||

Przykład:

![Szczegóły połączenia lokalnych baz danych](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Wyświetlanie tabel wyjściowych

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

   ![Widok historii uruchamiania](./media/connectors-create-api-db2/run-history.png)

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz tabele.**

   ![Rozwiń akcję](./media/connectors-create-api-db2/expand-action-step.png)

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe zawierają listę tabel.

   ![Wyświetlanie tabel wyjściowych](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Pobierz wiersz

Aby pobrać jeden rekord w tabeli bazy danych DB2, użyj akcji **Pobierz wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2, `SELECT WHERE` `SELECT FROM AREA WHERE AREAID = '99999'`na przykład .

1. Jeśli nigdy wcześniej nie używano akcji DB2 w aplikacji logiki, przejrzyj kroki opisane w [sekcji Dodaj db2 — Pobierz tabele,](#add-db2-action) ale zamiast tego dodaj akcję **Pobierz wiersz,** a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Pobierz wiersz,** oto jak pojawia się przykładowa aplikacja logiki:

   ![Pobierz akcję wiersza](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela zawierająca odpowiedni rekord, na przykład "OBSZAR" w tym przykładzie |
   | **Identyfikator obszaru** | Tak | Identyfikator odpowiedniego rekordu, na przykład "99999" w tym przykładzie |
   ||||

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-row"></a>Wyświetl wiersz danych wyjściowych

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz wiersz.**

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują określony wiersz.

   ![Wyświetl wiersz danych wyjściowych](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Pobierz wiersze

Aby pobrać wszystkie rekordy w tabeli bazy danych DB2, użyj akcji **Pobierz wiersze** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2, `SELECT` `SELECT * FROM AREA`na przykład .

1. Jeśli nigdy wcześniej nie używano akcji DB2 w aplikacji logiki, przejrzyj kroki opisane w [sekcji Dodaj db2 — Pobierz tabele,](#add-db2-action) ale zamiast tego dodaj akcję **Pobierz wiersze,** a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Pobierz wiersze,** oto jak pojawia się przykładowa aplikacja logiki:

   ![Pobierz akcję wierszy](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otwórz listę **nazw tabeli,** a następnie wybierz odpowiednią tabelę, która w tym przykładzie jest "OBSZAR":

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Aby określić filtr lub kwerendę dla wyników, wybierz pozycję **Pokaż opcje zaawansowane**.

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-rows"></a>Wyświetlanie wierszy danych wyjściowych

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz wiersze.**

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują wszystkie rekordy w określonej tabeli.

   ![Wyświetlanie wierszy danych wyjściowych](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Wstaw wiersz

Aby dodać pojedynczy rekord do tabeli bazy danych DB2, użyj akcji **Wstaw wiersza** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2, `INSERT` `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`na przykład .

1. Jeśli nigdy wcześniej nie używano akcji DB2 w aplikacji logiki, przejrzyj kroki opisane w [sekcji Dodaj db2 — Pobierz tabele,](#add-db2-action) ale zamiast tego dodaj akcję **Wstaw wiersza,** a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Wstaw wiersza,** oto jak pojawia się przykładowa aplikacja logiki:

   ![Akcja Wstawianie wiersza](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   W tym przykładzie oto właściwości:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której należy dodać rekord, na przykład "AREA" |
   | **Identyfikator obszaru** | Tak | Identyfikator obszaru do dodania, na przykład "99999" |
   | **Opis obszaru** | Tak | Opis obszaru do dodania, na przykład "Obszar 99999" |
   | **Identyfikator regionu** | Tak | Identyfikator regionu do dodania, na przykład "102" |
   |||| 

   Przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-insert-row-outputs"></a>Wyświetlanie wyjść wierszy wstawiania

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Wstaw wiersz.**

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują rekord dodany do określonej tabeli.

   ![Wyświetlanie danych wyjściowych z wstawionym wierszem](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizuj wiersz

Aby zaktualizować pojedynczy rekord w tabeli bazy danych DB2, użyj akcji **Aktualizuj wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2, `UPDATE` `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`na przykład .

1. Jeśli nigdy wcześniej nie używano akcji DB2 w aplikacji logiki, przejrzyj kroki opisane w [sekcji Dodaj db2 — Pobierz tabele,](#add-db2-action) ale zamiast tego dodaj akcję **Aktualizuj wiersz,** a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Aktualizuj wiersz,** oto jak pojawia się przykładowa aplikacja logiki:

   ![Akcja aktualizacji wiersza](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   W tym przykładzie oto właściwości:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której należy zaktualizować rekord, na przykład "AREA" |
   | **Identyfikator wiersza** | Tak | Identyfikator rekordu do aktualizacji, taki jak "99999" |
   | **Identyfikator obszaru** | Tak | Nowy identyfikator obszaru, taki jak "99999" |
   | **Opis obszaru** | Tak | Nowy opis obszaru, taki jak "Zaktualizowano 99999" |
   | **Identyfikator regionu** | Tak | Nowy identyfikator regionu, na przykład "102" |
   ||||

   Przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-update-row-outputs"></a>Wyświetlanie wyjść wiersza aktualizacji

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Aktualizuj wiersz.**

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują rekord zaktualizowany w określonej tabeli.

   ![Wyświetlanie danych wyjściowych ze zaktualizowanym wierszem](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Usuń wiersz

Aby usunąć pojedynczy rekord z tabeli bazy danych DB2, użyj akcji **Usuń wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2, `DELETE` `DELETE FROM AREA WHERE AREAID = '99999'`na przykład .

1. Jeśli nigdy wcześniej nie używano akcji DB2 w aplikacji logiki, przejrzyj kroki opisane w [sekcji Dodaj db2 — Pobierz tabele,](#add-db2-action) ale zamiast tego dodaj akcję **Usuń wiersz,** a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Usuń wiersz,** oto jak pojawia się przykładowa aplikacja logiki:

   ![Akcja Usuń wiersz](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   W tym przykładzie oto właściwości:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której należy usunąć rekord, na przykład "AREA" |
   | **Identyfikator wiersza** | Tak | Identyfikator rekordu do usunięcia, na przykład "99999" |
   ||||

   Przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-delete-row-outputs"></a>Wyświetlanie wyjść wierszy usuwania

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z uruchomienia.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**w sekcji **Historia przebiegów** wybierz ostatni bieg, który jest pierwszym elementem na liście.

1. W obszarze **Uruchamianie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Usuń wiersz.**

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz opcję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe nie zawierają już rekordu usuniętego z określonej tabeli.

   ![Wyświetlanie danych wyjściowych bez usuniętego wiersza](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/db2/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
