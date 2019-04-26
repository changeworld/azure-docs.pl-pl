---
title: Łączenie z programem IBM DB2 — Azure Logic Apps
description: Zarządzanie zasobami za pomocą interfejsów API REST programu IBM DB2 i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 7785d1788e8d5e9b432a8189345f293ebf05ef7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314233"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Zarządzanie zasobami programu IBM DB2 za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznika programu IBM DB2 można utworzyć automatycznych zadań i przepływów pracy na podstawie zasobów przechowywanych w bazie danych programu DB2. Przepływów pracy można łączyć się z zasobami w bazie danych, Odczyt i listy tabel bazy danych, dodawanie wierszy, zmień wiersze, usunąć wiersze i nie tylko. Możesz dołączyć akcje w aplikacjach logiki, które uzyskiwanie odpowiedzi z bazy danych i udostępnić dane wyjściowe do innych działań.

W tym artykule pokazano, jak utworzyć aplikację logiki, która wykonuje różnych operacji bazy danych. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Obsługiwane platformy i wersje

Łącznik DB2 obejmuje klienta firmy Microsoft, który komunikuje się z serwerami zdalnymi z bazy danych DB2 w sieci TCP/IP. Ten łącznik służy do uzyskiwania dostępu do baz danych w chmurze takich jak dashDB IBM Bluemix lub IBM DB2 for Windows z systemem Azure wirtualizacji. Można również przejść lokalnych baz danych z bazy danych DB2 po [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-connection.md).

Łącznik IBM DB2 obsługuje te IBM DB2 platformach i wersjach, oraz IBM DB2 zgodnych produktów, takich jak dashDB IBM Bluemix, które obsługują rozproszonej architektury bazy danych relacyjnych (DRDA) SQL Access Manager (SQLAM) w wersji 10 i 11:

| Platforma | Wersja | 
|----------|---------|
| IBM DB2 w przypadku z/OS | 11.1, 10.1 |
| IBM DB2 for mam | 7.3, 7.2, 7.1 |
| IBM DB2 for LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operacje obsługiwane bazy danych

Łącznik IBM DB2 obsługuje te operacje bazy danych, które mapowania na odpowiednich akcji w łączniku programu:

| Operacja bazy danych | Akcja łącznika |
|--------------------|------------------|
| Lista tabel bazy danych | Pobierz tabele |
| Odczyt przy użyciu wybierz jeden wiersz. | Pobierz wiersz |
| Odczyt wszystkich wierszy przy użyciu SELECT | Pobierz wiersze |
| Dodaj jeden wiersz, za pomocą INSERT | Wstaw wiersz |
| Edytuj jeden wiersz, za pomocą aktualizacji | Aktualizuj wiersz |
| Usuń jeden wiersz, przy użyciu DELETE | Usuń wiersz |
|||

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* IBM DB2 bazy danych albo oparte na chmurze lub środowisku lokalnym

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki gdzie mają dostęp do bazy danych DB2. Ten łącznik udostępnia tylko akcje, aby uruchomić aplikację logiki, wybierz oddzielne wyzwalacz, na przykład, **cyklu** wyzwalacza.
Przykłady w tym artykule korzystają **cyklu** wyzwalacza.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Dodawanie akcji bazy danych DB2 — Pobierz tabele

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie już otwarty.

1. W obszarze wyzwalacza wybierz **nowy krok**.

1. W polu wyszukiwania wprowadź "db2" jako filtr. Na przykład w obszarze listy akcji wybierz następującą akcję: **Pobierz tabele (wersja zapoznawcza)**

   ![Wybierz akcję](./media/connectors-create-api-db2/select-db2-action.png)

   Teraz monit o podanie szczegółów połączenia bazy danych DB2.

1. Wykonaj kroki tworzenia połączenia dla [baz danych w chmurze](#cloud-connection) lub [lokalnych baz danych](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Łączenie chmurą bazy danych DB2

Aby skonfigurować połączenie, podaj następujące szczegóły połączenia po wyświetleniu monitu wybierz pozycję **Utwórz**, a następnie zapisz aplikację logiki:

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Nawiązywanie połączenia za pośrednictwem bramy lokalnej** | Nie | Ma zastosowanie tylko w przypadku połączeń lokalnych. |
| **Nazwa połączenia** | Yes | Nazwę połączenia, na przykład "MyLogicApp — bazy danych DB2 — połączenie" |
| **Serwer** | Yes | Numer portu dwukropek adres lub alias dla serwera bazy danych DB2, na przykład "myDB2server.cloudapp.net:50000" <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje adresu TCP/IP lub alias, albo w formacie IPv4 lub IPv6, po której następują dwukropek i numer portu TCP/IP. |
| **Baza danych** | Yes | Nazwa bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje DRDA relacyjnej bazy danych nazwa (RDBNAM): <p>— Bazy danych DB2 w przypadku z/OS akceptuje ciąg 16-bajtowy, w których bazy danych jest znany jako lokalizację "IBM DB2 z/OS". <br>— Bazy danych DB2 dla i akceptuje ciąg 18-bajtową, w którym baza danych jest znany jako "IBM DB2 for mam" relacyjnej bazy danych. <br>— Bazy danych DB2 dla LUW akceptuje ciąg 8-bajtowych. |
| **Nazwa użytkownika** | Yes | Swoją nazwę użytkownika dla bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, którego długość zależy od konkretnej bazy danych: <p><p>— Bazy danych DB2 w przypadku z/OS akceptuje ciąg 8-bajtowych. <br>— Bazy danych DB2 dla i akceptuje ciąg 10 bajtów. <br>— Bazy danych DB2 dla systemu Linux lub UNIX akceptuje ciąg 8-bajtowych. <br>— Bazy danych DB2 dla Windows akceptuje ciąg 30-bajtowy. |
| **Hasło** | Yes | Hasło dla bazy danych |
||||

Na przykład:

![Szczegóły połączenia dla baz danych w chmurze](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Nawiązać połączenie z lokalnej bazy danych DB2

Przed utworzeniem połączenia, musi już mieć zainstalowanej usługi bramy danych lokalnych. W przeciwnym razie nie zakończeniu konfigurowania połączenia. Jeśli masz instalację bramy, kontynuuj podanie tych szczegółów połączenia, a następnie wybierz **Utwórz**.

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Nawiązywanie połączenia za pośrednictwem bramy lokalnej** | Yes | Ma zastosowanie, gdy chcesz, aby połączenie lokalne i pokazuje właściwości połączenia lokalnego. |
| **Nazwa połączenia** | Yes | Nazwę połączenia, na przykład "MyLogicApp — bazy danych DB2 — połączenie" | 
| **Serwer** | Yes | Numer portu dwukropek adres lub alias dla serwera bazy danych DB2, na przykład "myDB2server:50000" <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje adresu TCP/IP lub alias, albo w formacie IPv4 lub IPv6, po której następują dwukropek i numer portu TCP/IP. |
| **Baza danych** | Yes | Nazwa bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje DRDA relacyjnej bazy danych nazwa (RDBNAM): <p>— Bazy danych DB2 w przypadku z/OS akceptuje ciąg 16-bajtowy, w których bazy danych jest znany jako lokalizację "IBM DB2 z/OS". <br>— Bazy danych DB2 dla i akceptuje ciąg 18-bajtową, w którym baza danych jest znany jako "IBM DB2 for mam" relacyjnej bazy danych. <br>— Bazy danych DB2 dla LUW akceptuje ciąg 8-bajtowych. |
| **Uwierzytelnianie** | Yes | Typ uwierzytelniania dla połączenia, na przykład "Basic" <p><p>**Uwaga**: Wybierz tę wartość z listy, która zawiera podstawowe lub Windows (Kerberos). |
| **Nazwa użytkownika** | Yes | Swoją nazwę użytkownika dla bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, którego długość zależy od konkretnej bazy danych: <p><p>— Bazy danych DB2 w przypadku z/OS akceptuje ciąg 8-bajtowych. <br>— Bazy danych DB2 dla i akceptuje ciąg 10 bajtów. <br>— Bazy danych DB2 dla systemu Linux lub UNIX akceptuje ciąg 8-bajtowych. <br>— Bazy danych DB2 dla Windows akceptuje ciąg 30-bajtowy. |
| **Hasło** | Yes | Hasło dla bazy danych |
| **Brama** | Yes | Nazwa zainstalowane lokalne bramy danych <p><p>**Uwaga**: Wybierz tę wartość z listy, która zawiera wszystkie bramy danych zainstalowanych w ramach subskrypcji platformy Azure i grupę zasobów. |
||||

Na przykład:

![Szczegóły połączenia dla lokalnych baz danych](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Wyświetl tabele danych wyjściowych

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

   ![Widok historii uruchamiania](./media/connectors-create-api-db2/run-history.png)

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Pobierz tabele** akcji.

   ![Rozwiń akcję](./media/connectors-create-api-db2/expand-action-step.png)

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe zawierają listę tabel.

   ![Wyświetl tabele danych wyjściowych](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Pobierz wiersz

Aby pobrać do jednego rekordu w tabeli bazy danych DB2, należy użyć **Pobierz wiersz** akcji w aplikacji logiki. Ta akcja uruchamia DB2 `SELECT WHERE` instrukcji, na przykład `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Jeśli nie znasz Akcje bazy danych DB2 w aplikacji logiki, przejrzyj kroki w [Akcja Dodaj bazy danych DB2 — tabele Get](#add-db2-action) sekcji, ale Dodaj **Pobierz wiersz** akcji zamiast tego, a następnie wróć tutaj, aby kontynuować.

   Po dodaniu **Pobierz wiersz** akcji, Oto jak pojawia się Twoja Przykładowa aplikacja logiki:

   ![Pobierz wiersz akcji](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli, akcji zawiera odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Yes | Tabeli, która ma rekord ma, takie jak "Obszar" w tym przykładzie |
   | **Identyfikator obszaru** | Yes | Identyfikator rekordu ma, takie jak "99999", w tym przykładzie |
   ||||

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-row"></a>Wyświetl dane wyjściowe wiersza

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Pobierz wiersz** akcji.

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują usługi określony wiersz.

   ![Wyświetl dane wyjściowe wiersza](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Pobierz wiersze

Aby pobrać wszystkie rekordy w tabeli bazy danych DB2, należy użyć **Pobierz wiersze** akcji w aplikacji logiki. Ta akcja uruchamia DB2 `SELECT` instrukcji, na przykład `SELECT * FROM AREA`.

1. Jeśli nie znasz Akcje bazy danych DB2 w aplikacji logiki, przejrzyj kroki w [Akcja Dodaj bazy danych DB2 — tabele Get](#add-db2-action) sekcji, ale Dodaj **Pobierz wiersze** akcji zamiast tego, a następnie wróć tutaj, aby kontynuować.

   Po dodaniu **Pobierz wiersze** akcji, Oto jak pojawia się Twoja Przykładowa aplikacja logiki:

   ![Pobierz wiersze akcji](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otwórz **nazwy tabeli** listy, a następnie wybierz tabelę, czyli "Obszar" w tym przykładzie:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Aby określić filtr lub zapytanie, aby uzyskać wyniki, wybierz **Pokaż opcje zaawansowane**.

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-rows"></a>Wyświetl dane wyjściowe wierszach

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Pobierz wiersze** akcji.

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują wszystkie rekordy w określonej tabeli.

   ![Wyświetl dane wyjściowe wierszach](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Wstaw wiersz

Aby dodać pojedynczego rekordu do tabeli bazy danych DB2, użyj **Wstaw wiersz** akcji w aplikacji logiki. Ta akcja uruchamia DB2 `INSERT` instrukcji, na przykład `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Jeśli nie znasz Akcje bazy danych DB2 w aplikacji logiki, przejrzyj kroki w [Akcja Dodaj bazy danych DB2 — tabele Get](#add-db2-action) sekcji, ale Dodaj **Wstaw wiersz** akcji zamiast tego, a następnie wróć tutaj, aby kontynuować.

   Po dodaniu **Wstaw wiersz** akcji, Oto jak pojawia się Twoja Przykładowa aplikacja logiki:

   ![Wstaw wiersz akcji](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli, akcji zawiera odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na przykład poniżej przedstawiono właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Yes | Tabela gdzie dodać rekord, takie jak "Obszar" |
   | **Identyfikator obszaru** | Yes | Identyfikator dla obszaru, aby dodać, takie jak "99999" |
   | **Opis elementu obszaru** | Yes | Opis dla obszaru dodać, takie jak "Obszaru 99999" |
   | **Identyfikator regionu** | Yes | Identyfikator w regionie, aby dodać, takie jak "102" |
   |||| 

   Na przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

### <a name="view-insert-row-outputs"></a>Wyświetl Wstaw wiersz danych wyjściowych

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Wstaw wiersz** akcji.

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe zawierają rekord, który zostanie dodany do określonej tabeli.

   ![Wyświetlanie danych wyjściowych z wstawionego wiersza](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizuj wiersz

Aby zaktualizować pojedynczy rekord w tabeli bazy danych DB2, użyj **Aktualizuj wiersz** akcji w aplikacji logiki. Ta akcja uruchamia DB2 `UPDATE` instrukcji, na przykład `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Jeśli nie znasz Akcje bazy danych DB2 w aplikacji logiki, przejrzyj kroki w [Akcja Dodaj bazy danych DB2 — tabele Get](#add-db2-action) sekcji, ale Dodaj **Aktualizuj wiersz** akcji zamiast tego, a następnie wróć tutaj, aby kontynuować.

   Po dodaniu **Aktualizuj wiersz** akcji, Oto jak pojawia się Twoja Przykładowa aplikacja logiki:

   ![Zaktualizuj wiersz akcji](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli, akcji zawiera odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na przykład poniżej przedstawiono właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Yes | Tabela zaktualizować rekord, takie jak "Obszar" |
   | **Identyfikator wiersza** | Yes | Identyfikator rekordu do zaktualizowania, takie jak "99999" |
   | **Identyfikator obszaru** | Yes | Nowy identyfikator obszaru, takie jak "99999" |
   | **Opis elementu obszaru** | Yes | Nowy opis obszaru, na przykład "Zaktualizowano 99999" |
   | **Identyfikator regionu** | Yes | Nowy identyfikator regionu, takich jak "102" |
   ||||

   Na przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

### <a name="view-update-row-outputs"></a>Wyświetla widok Aktualizuj wiersz

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Aktualizuj wiersz** akcji.

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe zawierają rekord, który zostanie zaktualizowany w określonej tabeli.

   ![Wyświetlanie danych wyjściowych z zaktualizowany wiersz](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Usuń wiersz

Aby usunąć pojedynczy rekord z tabeli bazy danych DB2, użyj **Usuń wiersz** akcji w aplikacji logiki. Ta akcja uruchamia DB2 `DELETE` instrukcji, na przykład `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Jeśli nie znasz Akcje bazy danych DB2 w aplikacji logiki, przejrzyj kroki w [Akcja Dodaj bazy danych DB2 — tabele Get](#add-db2-action) sekcji, ale Dodaj **Usuń wiersz** akcji zamiast tego, a następnie wróć tutaj, aby kontynuować.

   Po dodaniu **Usuń wiersz** akcji, Oto jak pojawia się Twoja Przykładowa aplikacja logiki:

   ![Usuń akcję wiersza](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Określ wartości dla wszystkich wymaganych właściwości (*). Po wybraniu tabeli, akcji zawiera odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na przykład poniżej przedstawiono właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Yes | Tabela miejsca usunąć rekord, takie jak "Obszar" |
   | **Identyfikator wiersza** | Yes | Identyfikator rekordu do usunięcia, takie jak "99999" |
   ||||

   Na przykład:

   ![Wybieranie tabeli](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

### <a name="view-delete-row-outputs"></a>Wyświetl Usuń wiersz danych wyjściowych

Aby ręcznie uruchomić swoją aplikację logiki, na pasku narzędzi Projektanta wybierz **Uruchom**. Po zakończeniu aplikacja logiki możesz wyświetlić dane wyjściowe przebiegu.

1. W menu aplikacji logiki, wybierz **Przegląd**.

1. W obszarze **Podsumowanie**w **Historia przebiegów** wybierz ostatniego przebiegu, który jest pierwszy element na liście.

1. W obszarze **przebiegu aplikacji logiki**, można teraz sprawdzić stan danych wejściowych, a dane wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń **Usuń wiersz** akcji.

1. Aby wyświetlić dane wejściowe, wybierz **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe zawierają już rekord, który został usunięty z określonej tabeli.

   ![Wyświetlanie danych wyjściowych bez usunięty wiersz](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/db2/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
