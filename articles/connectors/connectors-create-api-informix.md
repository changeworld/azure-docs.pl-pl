---
title: Nawiązać połączenie z bazą danych IBM Informix — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie zasobami za pomocą interfejsów API REST programu IBM Informix i Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691005"
---
# <a name="get-started-with-the-informix-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Informix
Łącznik usługi Microsoft dla programu Informix łączy Logic Apps do zasobów przechowywanych w bazie danych programu IBM Informix. Łącznik programu Informix obejmuje klienta Microsoft do komunikowania się z komputerami zdalnymi serwerem Informix w sieci TCP/IP. Obejmuje baz danych w chmurze, takich jak IBM Informix for Windows z systemem Azure wirtualizacji i baz danych przy użyciu lokalnej bramy danych lokalnych. Zobacz [obsługiwane listy](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix platform i wersji (w tym temacie).

Łącznik obsługuje następujące operacje bazy danych:

* Lista tabel bazy danych
* Odczyt przy użyciu wybierz jeden wiersz.
* Odczyt wszystkich wierszy przy użyciu SELECT
* Dodaj jeden wiersz, za pomocą INSERT
* Instrukcja ALTER jeden wiersz, za pomocą aktualizacji
* Usuń jeden wiersz, przy użyciu DELETE

W tym temacie przedstawiono sposób korzystania z łącznika w aplikacji logiki do obsługi operacji bazy danych procesu.

Aby dowiedzieć się więcej o usłudze Logic Apps, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostępne akcje
Ten łącznik obsługuje następujące akcje aplikacji Logic Apps:

* Getables
* Getrow —
* GetRows
* InsertRow —
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Wyświetl tabele
Tworzenie aplikacji logiki dla każdej operacji składa się z wielu kroków wykonywanych za pośrednictwem portalu Microsoft Azure.

W aplikacji logiki możesz dodać do listy tabel w bazie danych programu Informix akcję. Ta akcja powoduje, że łącznik do przetwarzania instrukcji schematu Informix, takich jak `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixgetTables`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **usługi App Service Planowanie**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**.  
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Get tabel (wersja zapoznawcza)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. W **Informix - Get tabel** konfiguracji wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem lokalnej bramy danych**. Zwróć uwagę, zmianie ustawienia z chmury do środowiska lokalnego.
   
   * Wpisz wartość dla **serwera**, w postaci adres lub alias numeru portu średnikami. Na przykład wpisz `ibmserver01:9089`.
   * Wpisz wartość dla **bazy danych**. Na przykład wpisz `nwind`.
   * Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
   * Wpisz wartość dla **Username**. Na przykład wpisz `informix`.
   * Wpisz wartość dla **hasło**. Na przykład wpisz `Password1`.
   * Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
7. Wybierz **Utwórz**, a następnie wybierz pozycję **Zapisz**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. W **InformixgetTables** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
9. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_tables**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, który powinien zawierać listę tabel.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Tworzenie połączeń
Ten łącznik obsługuje połączenia do bazy danych w środowisku lokalnym i w chmurze przy użyciu następujących właściwości połączenia. 

| Właściwość | Opis |
| --- | --- |
| serwer |Wymagany. Akceptuje wartości ciągu reprezentujący adresu TCP/IP lub alias w formacie IPv4 lub IPv6, a następnie (-średnikami) przez numer portu TCP/IP. |
| baza danych |Wymagany. Akceptuje wartości ciągu reprezentujący DRDA relacyjnej bazy danych nazwa (RDBNAM). Informix akceptuje ciąg 128 bajtów (baza danych jest znany jako nazwa bazy danych programu IBM Informix (dbname)). |
| uwierzytelnianie |Opcjonalny. Akceptuje wartości elementu listy, podstawowe lub Windows (kerberos). |
| nazwa użytkownika |Wymagany. Przyjmuje wartość ciągu. |
| password |Wymagany. Przyjmuje wartość ciągu. |
| brama |Wymagany. Akceptuje wartości elementu listy, reprezentujący lokalnej bramy danych zdefiniowana z usługi Logic Apps w ramach grupy magazynów. |

## <a name="create-the-on-premises-gateway-connection"></a>Utwórz połączenie bramy lokalnej
Ten łącznik może uzyskać dostęp do bazy danych programu Informix lokalnie przy użyciu lokalnej bramy danych. Zobacz Tematy bramy, aby uzyskać więcej informacji. 

1. W **bram** konfiguracji wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem bramy**. Informacje o ustawieniach zmiany z chmury do środowiska lokalnego.
2. Wpisz wartość dla **serwera**, w postaci adres lub alias numeru portu średnikami. Na przykład wpisz `ibmserver01:9089`.
3. Wpisz wartość dla **bazy danych**. Na przykład wpisz `nwind`.
4. Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
5. Wpisz wartość dla **Username**. Na przykład wpisz `informix`.
6. Wpisz wartość dla **hasło**. Na przykład wpisz `Password1`.
7. Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
8. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Utwórz połączenie chmury
Ten łącznik mają dostęp do chmury bazy danych programu Informix. 

1. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**. 
2. Wpisz wartość dla **nazwa połączenia**. Na przykład wpisz `hisdemo2`.
3. Wpisz wartość dla **nazwa serwera programu Informix**, w postaci adres lub alias numeru portu średnikami. Na przykład wpisz `hisdemo2.cloudapp.net:9089`.
4. Wpisz wartość dla **Nazwa bazy danych programu Informix**. Na przykład wpisz `nwind`.
5. Wpisz wartość dla **Username**. Na przykład wpisz `informix`.
6. Wpisz wartość dla **hasło**. Na przykład wpisz `Password1`.
7. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Pobierz wszystkie wiersze, używając SELECT
Można utworzyć akcji aplikacji logiki, aby pobrać wszystkie wiersze w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix SELECT, takich jak `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa** (np. "**InformixgetRows**"), **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — Pobierz wiersze (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **Zmień połączenie**.
7. W **połączeń** konfiguracji wybierz opcję **Utwórz nową**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**.
   
   * Wpisz wartość dla **nazwa połączenia**. Na przykład wpisz `HISDEMO2`.
   * Wpisz wartość dla **nazwa serwera programu Informix**, w postaci adres lub alias numeru portu średnikami. Na przykład wpisz `HISDEMO2.cloudapp.net:9089`.
   * Wpisz wartość dla **Nazwa bazy danych programu Informix**. Na przykład wpisz `NWIND`.
   * Wpisz wartość dla **Username**. Na przykład wpisz `informix`.
   * Wpisz wartość dla **hasło**. Na przykład wpisz `Password1`.
9. Wybierz **Utwórz** aby kontynuować.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. W **nazwy tabeli** listy wybierz **Strzałka w dół**, a następnie wybierz pozycję **obszaru**.
11. Opcjonalnie można zaznaczyć **Pokaż opcje zaawansowane** umożliwia określenie opcji zapytania.
12. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. W **InformixgetRows** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
14. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_rows**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, który powinien zawierać listę wierszy.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Dodaj jeden wiersz, za pomocą INSERT
Można utworzyć akcji aplikacji logiki, aby dodać jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix INSERT, takich jak `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixinsertRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **usługi App Service Planowanie**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — Wstaw wiersz (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **Zmień połączenie**. 
7. W **połączeń** konfiguracji wybierz, aby wybrać połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy wybierz **Strzałka w dół**, a następnie wybierz pozycję **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (patrz czerwona gwiazdka). Na przykład wpisz `99999` dla **AREAID**, typ `Area 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. W **InformixinsertRow** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
12. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_rows**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, które należy uwzględnić nowy wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Pobieranie przy użyciu wybierz jeden wiersz.
Można utworzyć akcji aplikacji logiki, aby pobrać jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik aby przetworzyć wybierz Informix gdzie instrukcji, takich jak `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixgetRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **usługi App Service Planowanie**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — Pobierz wiersze (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **Zmień połączenie**. 
7. W **połączeń** konfiguracji wybierz, aby wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy wybierz **Strzałka w dół**, a następnie wybierz pozycję **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (patrz czerwona gwiazdka). Na przykład wpisz `99999` dla **AREAID**. 
10. Opcjonalnie można zaznaczyć **Pokaż opcje zaawansowane** umożliwia określenie opcji zapytania.
11. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. W **InformixgetRow** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
13. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_rows**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, który powinien zawierać wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Jeden wiersz zmiany za pomocą aktualizacji
Można utworzyć akcji aplikacji logiki, aby zmienić jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix UPDATE, takich jak `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixupdateRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **usługi App Service Planowanie**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — Aktualizuj wiersz (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **Zmień połączenie**. 
7. W **połączeń** konfiguracji wybierz, aby wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy wybierz **Strzałka w dół**, a następnie wybierz pozycję **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (patrz czerwona gwiazdka). Na przykład wpisz `99999` dla **AREAID**, typ `Updated 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. W **InformixupdateRow** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
12. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_rows**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, które należy uwzględnić nowy wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Usuń jeden wiersz, przy użyciu DELETE
Można utworzyć akcji aplikacji logiki, aby usunąć jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji usuwania Informix, takich jak `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz opcję **+** (znak plus), **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixdeleteRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **usługi App Service Planowanie**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj wyzwalacz i Akcja
1. W **Projektant aplikacji logiki**, wybierz opcję **pustą aplikację LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz opcję **częstotliwość** listy rozwijanej do wybierz **dzień**, a następnie wybierz  **Interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — Usuń wiersz (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **Zmień połączenie**. 
7. W **połączeń** okienko konfiguracji, wybierz istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy wybierz **Strzałka w dół**, a następnie wybierz pozycję **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (patrz czerwona gwiazdka). Na przykład wpisz `99999` dla **AREAID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. W **InformixdeleteRow** bloku, w ramach **wszystkie przebiegi** w obszarze **Podsumowanie**, wybierz element, który w pierwszej z listy (ostatniego uruchomienia).
12. W **przebiegu aplikacji logiki** bloku wybierz **szczegóły przebiegu**. W ramach **akcji** listy wybierz **Get_rows**. Zobacz wartość **stan**, powinien być **Powodzenie**. Wybierz **link danych wejściowych** Aby wyświetlić dane wejściowe. Wybierz **link danych wyjściowych**i wyświetlić dane wyjściowe, który powinien zawierać wiersz usunięty.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Obsługiwane Informix platformy i wersje
Ten łącznik obsługuje następujące wersje programu IBM Informix, gdy skonfigurowane do obsługi połączeń klienckich rozproszonej architektury bazy danych relacyjnych (DRDA).

* IBM Informix 12.1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/informix/). 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zapoznaj się z innych dostępnych łączników w usłudze Logic Apps w naszym [listy interfejsów API](apis-list.md).

