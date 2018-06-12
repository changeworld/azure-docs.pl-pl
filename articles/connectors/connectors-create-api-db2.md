---
title: Połącz z bazą danych DB2 — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie zasobami za pomocą interfejsów API REST bazy danych DB2 i usługi Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, estfan
ms.suite: integration
tags: connectors
ms.openlocfilehash: 507bc48b6b775d6a6fb5f855210d33520e187a74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295095"
---
# <a name="get-started-with-the-db2-connector"></a>Rozpoczynanie pracy z łącznik DB2
Łącznik usługi Microsoft dla bazy danych DB2 nawiązywanie zasobów przechowywanych w bazie danych programu IBM DB2 Logic Apps. Ten łącznik obejmuje klienta Microsoft do komunikowania się ze zdalnymi komputerami serwera bazy danych DB2 w sieci TCP/IP. Obejmuje chmury baz danych, takich jak dashDB IBM Bluemix lub IBM DB2 dla systemu Windows Azure wirtualizacji i lokalnych baz danych za pomocą bramy danych lokalnych. Zobacz [obsługiwane listy](connectors-create-api-db2.md#supported-db2-platforms-and-versions) IBM DB2 platform i wersji (w tym temacie).

Łącznik DB2 obsługuje następujące operacje bazy danych:

* Listy tabel bazy danych
* Odczyt przy użyciu wybierz jeden wiersz.
* Wszystkie wiersze, używając wybierz do odczytu
* Dodaj jeden wiersz za pomocą polecenia Wstaw
* Instrukcja ALTER jeden wiersz za pomocą aktualizacji
* Usuń jeden wiersz używanie opcji usuwania

W tym temacie przedstawiono sposób korzystania z łącznika w aplikacji logiki, aby operacje bazy danych procesu.

Aby dowiedzieć się więcej na temat aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostępne akcje
Łącznik DB2 obsługuje następujące akcje logikę w aplikacji:

* GetTables
* GetRow
* Funkcja GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Wyświetl tabele
Tworzenie aplikacji logiki do żadnej operacji składa się z wielu czynności wykonywanych za pośrednictwem portalu Microsoft Azure.

W aplikacji logiki akcja zostanie dodana do listy tabel w bazie danych DB2. Akcja powoduje, że łącznik do przetwarzania zestawienie schematu bazy danych DB2, takich jak `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `Db2getTables`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie ustaw **interwał** na typ **7**.  
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz `db2` w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — tabele Get (wersja zapoznawcza)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. W **bazy danych DB2 — tabele Get** okienko konfiguracji, wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem bramy danych lokalnych**. Należy zauważyć, że zmianie ustawienia z chmury do środowiska lokalnego.
   
   * Wpisz wartość **serwera**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `ibmserver01:50000`.
   * Wpisz wartość **bazy danych**. Na przykład wpisz `nwind`.
   * Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
   * Wpisz wartość **Username**. Na przykład wpisz `db2admin`.
   * Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
   * Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
7. Wybierz **Utwórz**, a następnie wybierz **zapisać**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. W **Db2getTables** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
9. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_tables**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe, które powinna zawierać listę tabel.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Tworzenie połączeń
Ten łącznik obsługuje połączenia z lokalnym baz danych i w chmurze przy użyciu następujących właściwości połączenia. 

| Właściwość | Opis |
| --- | --- |
| serwer |Wymagany. Przyjmuje wartość ciągu, która reprezentuje adres TCP/IP lub alias w formacie IPv4 lub IPv6, a następnie (rozdzielany średnikami) przez numer portu TCP/IP. |
| baza danych |Wymagany. Przyjmuje wartość ciągu, która reprezentuje DRDA relacyjne nazwę bazy danych (RDBNAM). Bazy danych DB2 dla z/OS akceptuje ciąg 16-bajtowych (baza danych jest znany jako IBM DB2 dla lokalizacji z/OS). Bazy danych DB2 dla i5/OS akceptuje ciąg 18-bajtowych (baza danych jest nazywany IBM DB2 dla i relacyjnej bazy danych). Bazy danych DB2 dla LUW akceptuje ciąg 8-bajtowych. |
| uwierzytelnianie |Opcjonalny. Akceptuje wartości elementu listy, Basic lub systemu Windows (kerberos). |
| nazwa użytkownika |Wymagany. Akceptuje wartości ciągu. Bazy danych DB2 dla z/OS akceptuje ciąg 8-bajtowych. Bazy danych DB2 dla i akceptuje ciąg 10-bajtowych. Bazy danych DB2 dla systemu Linux lub UNIX akceptuje ciąg 8-bajtowych. Bazy danych DB2 dla systemu Windows akceptuje ciąg 30-bajtowych. |
| hasło |Wymagany. Akceptuje wartości ciągu. |
| brama |Wymagany. Akceptuje wartości elementu listy, reprezentujący bramy danych lokalnych zdefiniowany do aplikacji logiki w ramach grupy magazynów. |

## <a name="create-the-on-premises-gateway-connection"></a>Utwórz połączenie bramy lokalnej
Ten łącznik można uzyskać dostępu do bazy danych DB2 lokalnie przy użyciu bramy lokalnej. Zobacz Tematy bramy, aby uzyskać więcej informacji. 

1. W **bram** okienko konfiguracji, wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem bramy**. Należy zauważyć, że zmianie ustawienia z chmury do środowiska lokalnego.
2. Wpisz wartość **serwera**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `ibmserver01:50000`.
3. Wpisz wartość **bazy danych**. Na przykład wpisz `nwind`.
4. Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
5. Wpisz wartość **Username**. Na przykład wpisz `db2admin`.
6. Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
7. Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
8. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Utwórz połączenie z chmury
Ten łącznik można uzyskać dostępu do bazy danych DB2 chmury. 

1. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**. 
2. Wpisz wartość **nazwa połączenia**. Na przykład wpisz `hisdemo2`.
3. Wpisz wartość **nazwę serwera bazy danych DB2**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `hisdemo2.cloudapp.net:50000`.
4. Wpisz wartość **Nazwa bazy danych DB2**. Na przykład wpisz `nwind`.
5. Wpisz wartość **Username**. Na przykład wpisz `db2admin`.
6. Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
7. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Pobierz wszystkie wiersze, używając wybierz
Można określić akcję aplikacji logiki, aby pobrać wszystkie wiersze w tabeli bazy danych DB2. To powoduje, że łącznik do przetwarzania zestawienie wybierz bazy danych DB2, takich jak `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `Db2getRows`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz `db2` w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — Get wierszy (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**.
7. W **połączeń** okienko konfiguracji, wybierz opcję **Utwórz nowy**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**.
   
   * Wpisz wartość **nazwa połączenia**. Na przykład wpisz `HISDEMO2`.
   * Wpisz wartość **nazwę serwera bazy danych DB2**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `HISDEMO2.cloudapp.net:50000`.
   * Wpisz wartość **Nazwa bazy danych DB2**. Na przykład wpisz `NWIND`.
   * Wpisz wartość **Username**. Na przykład wpisz `db2admin`.
   * Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
9. Wybierz **Utwórz** aby kontynuować.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
11. Opcjonalnie wybierz **Pokaż zaawansowane opcje** umożliwia określenie opcji zapytania.
12. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. W **Db2getRows** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
14. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe, który powinien zawierać listę wierszy.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Dodaj jeden wiersz za pomocą polecenia Wstaw
Można określić akcję aplikacji logiki, aby dodać jeden wiersz w tabeli bazy danych DB2. Ta akcja powoduje, że łącznik do przetwarzania instrukcji INSERT bazy danych DB2, takich jak `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `Db2insertRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **bazy danych db2** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — Wstaw wiersz (wersja zapoznawcza)**.
6. W **bazy danych DB2 — Wstaw wiersz (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** okienko konfiguracji, wybierz połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**, typ `Area 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. W **Db2insertRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą nowego wiersza.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Pobieranie przy użyciu wybierz jeden wiersz.
Można określić akcję aplikacji logiki można pobrać jeden wiersz w tabeli bazy danych DB2. Ta akcja powoduje, że łącznik do przetwarzania instrukcji wybierz bazy danych DB2 gdzie, takich jak `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa** (np. "**Db2getRow**"), **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy. 
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **bazy danych db2** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — Get wierszy (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** okienko konfiguracje, wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**. 
10. Opcjonalnie wybierz **Pokaż zaawansowane opcje** umożliwia określenie opcji zapytania.
11. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. W **Db2getRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
13. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą wiersza.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Zmień jeden wiersz za pomocą aktualizacji
Można określić akcję aplikacji logiki, aby zmienić jeden wiersz w tabeli bazy danych DB2. Ta akcja powoduje, że łącznik do przetwarzania instrukcji aktualizacji bazy danych DB2, takich jak `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `Db2updateRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **bazy danych db2** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — aktualizacja wiersza (wersja zapoznawcza)**.
6. W **bazy danych DB2 — aktualizacja wiersza (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** konfiguracje okienku zaznacz, aby wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**, typ `Updated 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. W **Db2updateRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą nowego wiersza.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Usuń jeden wiersz używanie opcji usuwania
Można określić akcję aplikacji logiki, aby usunąć jeden wiersz w tabeli bazy danych DB2. Ta akcja powoduje, że łącznik do przetwarzania zestawienie usunąć bazy danych DB2, takich jak `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `Db2deleteRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy. 
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wybierz **bazy danych db2** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **bazy danych DB2 — Usuń wiersz (wersja zapoznawcza)**.
6. W **bazy danych DB2 — Usuń wiersz (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** okienko konfiguracje, wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. W **Db2deleteRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą usuniętych wierszy.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Obsługiwane platformy bazy danych DB2 i wersje
Ten łącznik obsługuje następujące platformy IBM DB2 i wersji, jak również produkty zgodne IBM DB2 (np. IBM Bluemix dashDB), które obsługują SQL Menedżera dostępu (SQLAM) rozproszonych relacyjnej bazy danych architektury DRDA () w wersji 10 lub 11:

* IBM DB2 dla 11.1 z/OS
* IBM DB2 dla z/OS 10.1
* IBM DB2 dla i 7.3
* IBM DB2 dla i 7.2
* IBM DB2 dla i 7.1
* IBM DB2 LUW 11
* IBM DB2 dla LUW 10.5

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/db2/). 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Eksploruj dostępnych łączników w aplikacjach logiki w naszym [listy interfejsów API](apis-list.md).

