---
title: Nawiązać połączenia z bazą danych IBM Informix - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie zasobami za pomocą interfejsów API REST programu IBM Informix i usługi Azure Logic Apps
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
ms.openlocfilehash: d0008c19ed96f731f7b57c5d8aa41cd9f128bc20
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296040"
---
# <a name="get-started-with-the-informix-connector"></a>Rozpoczynanie pracy z łącznikiem programu Informix
Łącznik usługi Microsoft dla programu Informix łączy Logic Apps zasobów przechowywanych w bazie danych programu IBM Informix. Łącznik programu Informix obejmuje klienta Microsoft do komunikowania się z komputerami zdalnymi serwerem Informix w sieci TCP/IP. Obejmuje chmury baz danych, takich jak IBM Informix dla systemu Windows Azure wirtualizacji i lokalnych baz danych za pomocą bramy danych lokalnych. Zobacz [obsługiwane listy](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix platform i wersji (w tym temacie).

Łącznik obsługuje następujące operacje bazy danych:

* Listy tabel bazy danych
* Odczyt przy użyciu wybierz jeden wiersz.
* Wszystkie wiersze, używając wybierz do odczytu
* Dodaj jeden wiersz za pomocą polecenia Wstaw
* Instrukcja ALTER jeden wiersz za pomocą aktualizacji
* Usuń jeden wiersz używanie opcji usuwania

W tym temacie przedstawiono sposób korzystania z łącznika w aplikacji logiki, aby operacje bazy danych procesu.

Aby dowiedzieć się więcej na temat aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostępne akcje
Ten łącznik obsługuje następujące akcje logikę w aplikacji:

* Getables
* GetRow
* Funkcja GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Wyświetl tabele
Tworzenie aplikacji logiki do żadnej operacji składa się z wielu czynności wykonywanych za pośrednictwem portalu Microsoft Azure.

W aplikacji logiki akcja zostanie dodana do listy tabel w bazie danych programu Informix. Ta akcja powoduje, że łącznik do przetwarzania zestawienie schematu Informix, takich jak `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixgetTables`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**.  
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Get tabele (wersja zapoznawcza)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. W **Informix - Get tabel** okienko konfiguracji, wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem bramy danych lokalnych**. Należy zauważyć, że zmianie ustawienia z chmury do środowiska lokalnego.
   
   * Wpisz wartość **serwera**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `ibmserver01:9089`.
   * Wpisz wartość **bazy danych**. Na przykład wpisz `nwind`.
   * Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
   * Wpisz wartość **Username**. Na przykład wpisz `informix`.
   * Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
   * Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
7. Wybierz **Utwórz**, a następnie wybierz **zapisać**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. W **InformixgetTables** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
9. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_tables**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe, które powinna zawierać listę tabel.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Tworzenie połączeń
Ten łącznik obsługuje połączenia z bazą danych na lokalnym i w chmurze przy użyciu następujących właściwości połączenia. 

| Właściwość | Opis |
| --- | --- |
| serwer |Wymagany. Akceptuje wartości ciągu reprezentujący adres TCP/IP lub alias w formacie IPv4 lub IPv6, a następnie (-średnikami) przez numer portu TCP/IP. |
| baza danych |Wymagany. Akceptuje wartości ciągu reprezentujący DRDA relacyjne nazwę bazy danych (RDBNAM). Informix akceptuje ciąg 128-bajtowych (baza danych jest znany jako nazwa bazy danych programu IBM Informix (dbname)). |
| uwierzytelnianie |Opcjonalny. Akceptuje wartości elementu listy, Basic lub systemu Windows (kerberos). |
| nazwa użytkownika |Wymagany. Akceptuje wartości ciągu. |
| hasło |Wymagany. Akceptuje wartości ciągu. |
| brama |Wymagany. Akceptuje wartości elementu listy, reprezentujący bramy danych lokalnych zdefiniowany do aplikacji logiki w ramach grupy magazynów. |

## <a name="create-the-on-premises-gateway-connection"></a>Utwórz połączenie bramy lokalnej
Tego łącznika można uzyskać dostępu do bazy danych programu Informix lokalnej za pomocą bramy danych lokalnych. Zobacz Tematy bramy, aby uzyskać więcej informacji. 

1. W **bram** okienko konfiguracji, wybierz opcję **wyboru** umożliwiające **Połącz za pośrednictwem bramy**. Informacje o ustawieniach zmiany z chmury do środowiska lokalnego.
2. Wpisz wartość **serwera**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `ibmserver01:9089`.
3. Wpisz wartość **bazy danych**. Na przykład wpisz `nwind`.
4. Wybierz wartość dla **uwierzytelniania**. Na przykład wybierz **podstawowe**.
5. Wpisz wartość **Username**. Na przykład wpisz `informix`.
6. Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
7. Wybierz wartość dla **bramy**. Na przykład wybierz **datagateway01**.
8. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Utwórz połączenie z chmury
Ten łącznik mogą uzyskiwać dostęp do bazy danych programu Informix chmurę. 

1. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**. 
2. Wpisz wartość **nazwa połączenia**. Na przykład wpisz `hisdemo2`.
3. Wpisz wartość **nazwa serwera programu Informix**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `hisdemo2.cloudapp.net:9089`.
4. Wpisz wartość **Nazwa bazy danych programu Informix**. Na przykład wpisz `nwind`.
5. Wpisz wartość **Username**. Na przykład wpisz `informix`.
6. Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
7. Wybierz **Utwórz** aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Pobierz wszystkie wiersze, używając wybierz
Można utworzyć działania aplikacji logiki, aby pobrać wszystkie wiersze w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcję Informix SELECT, takich jak `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa** (np. "**InformixgetRows**"), **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Get wierszy (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**.
7. W **połączeń** okienko konfiguracji, wybierz opcję **Utwórz nowy**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. W **bram** okienko konfiguracji, pozostaw **wyboru** wyłączone (które nie były kliknięte) **Połącz za pośrednictwem bramy**.
   
   * Wpisz wartość **nazwa połączenia**. Na przykład wpisz `HISDEMO2`.
   * Wpisz wartość **nazwa serwera programu Informix**, w postaci adresu lub alias numer portu dwukropkiem. Na przykład wpisz `HISDEMO2.cloudapp.net:9089`.
   * Wpisz wartość **Nazwa bazy danych programu Informix**. Na przykład wpisz `NWIND`.
   * Wpisz wartość **Username**. Na przykład wpisz `informix`.
   * Wpisz wartość **hasło**. Na przykład wpisz `Password1`.
9. Wybierz **Utwórz** aby kontynuować.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
11. Opcjonalnie wybierz **Pokaż zaawansowane opcje** umożliwia określenie opcji zapytania.
12. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. W **InformixgetRows** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
14. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe, który powinien zawierać listę wierszy.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Dodaj jeden wiersz za pomocą polecenia Wstaw
Można utworzyć akcji aplikacji logiki, aby dodać jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix INSERT, takich jak `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixinsertRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Wstaw wiersz (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** okienko konfiguracji, wybierz połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**, typ `Area 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. W **InformixinsertRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą nowego wiersza.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Pobieranie przy użyciu wybierz jeden wiersz.
Można utworzyć działania aplikacji logiki w celu pobrania jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix wybierz gdzie, takich jak `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixgetRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Get wierszy (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** konfiguracje okienku zaznacz, aby wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**. 
10. Opcjonalnie wybierz **Pokaż zaawansowane opcje** umożliwia określenie opcji zapytania.
11. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. W **InformixgetRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
13. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą wiersza.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Zmień jeden wiersz za pomocą aktualizacji
Można utworzyć działania aplikacji logiki, aby zmienić jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania instrukcji Informix UPDATE, takich jak `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixupdateRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix — aktualizacja wiersza (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** konfiguracje okienku zaznacz, aby wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**, typ `Updated 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. W **InformixupdateRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą nowego wiersza.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Usuń jeden wiersz używanie opcji usuwania
Można utworzyć działania aplikacji logiki, aby usunąć jeden wiersz w tabeli programu Informix. Ta akcja powoduje, że łącznik do przetwarzania zestawienie Informix usunąć, takich jak `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. W **Azure start tablicy**, wybierz pozycję **+** (znak plus) **sieci Web i mobilność**, a następnie **aplikacji logiki**.
2. Wprowadź **nazwa**, takich jak `InformixdeleteRow`, **subskrypcji**, **grupy zasobów**, **lokalizacji**, i **planu usługi App Service**. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodaj trigger i action
1. W **projektanta aplikacji logiki**, wybierz pozycję **puste LogicApp** w **szablony** listy.
2. W **wyzwalaczy** listy, wybierz **cyklu**. 
3. W **cyklu** wyzwalacza, wybierz opcję **Edytuj**, wybierz pozycję **częstotliwość** listy rozwijanej wybierz **dzień**, a następnie wybierz **interwał** na typ **7**. 
4. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.
5. W **akcje** listy, wpisz **informix** w **Wyszukaj więcej akcji** pole edycji, a następnie wybierz **Informix - Usuń wiersz (wersja zapoznawcza)**.
6. W **Pobierz wiersze (wersja zapoznawcza)** akcji wybierz **zmienić połączenie**. 
7. W **połączeń** okienko konfiguracje, wybrać istniejące połączenie. Na przykład wybierz **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. W **nazwy tabeli** listy, wybierz **Strzałka w dół**, a następnie wybierz **obszaru**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdką). Na przykład wpisz `99999` dla **AREAID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. W **InformixdeleteRow** bloku, w **wszystkie elementy** w obszarze **Podsumowanie**, wybierz element w pierwszej listy (Uruchom ostatniego).
12. W **Uruchom aplikację logiki** bloku, wybierz opcję **Uruchom szczegóły**. W ramach **akcji** listy, wybierz **Get_rows**. Zobacz wartość **stan**, które powinny być **zakończyło się pomyślnie**. Wybierz **dane wejściowe, łącza** do wyświetlania danych wejściowych. Wybierz **dane wyjściowe link**i wyświetlić dane wyjściowe; obejmującą usuniętych wierszy.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Obsługiwane platformy Informix i wersje
Ten łącznik obsługuje następujące wersje programu IBM Informix skonfigurowany do obsługi połączeń klienckich rozproszonych relacyjnej bazy danych architektury DRDA ().

* IBM Informix 12.1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/informix/). 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Eksploruj dostępnych łączników w aplikacjach logiki w naszym [listy interfejsów API](apis-list.md).

