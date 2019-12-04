---
title: Łączenie z bazą danych programu IBM Informix
description: Zarządzanie zasobami za pomocą interfejsów API REST programu IBM Informix i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789729"
---
# <a name="get-started-with-the-informix-connector"></a>Wprowadzenie do łącznika programu Informix
Łącznik firmy Microsoft dla programu Informix łączy Logic Apps z zasobami przechowywanymi w bazie danych programu IBM Informix. Łącznik programu Informix zawiera klienta firmy Microsoft do komunikowania się ze zdalnym komputerem serwera Informix w sieci TCP/IP. Obejmuje to bazy danych w chmurze, takie jak IBM Informix dla systemu Windows działające w ramach wirtualizacji platformy Azure, oraz lokalne bazy danych korzystające z lokalnej bramy Data Gateway. Zobacz [listę obsługiwanych](connectors-create-api-informix.md#supported-informix-platforms-and-versions) platform i wersji programu IBM Informix (w tym temacie).

Łącznik obsługuje następujące operacje bazy danych:

* Wyświetl listę tabel bazy danych
* Odczytaj jeden wiersz przy użyciu polecenia SELECT
* Czytaj wszystkie wiersze przy użyciu polecenia SELECT
* Dodaj jeden wiersz przy użyciu instrukcji INSERT
* Zmień jeden wiersz przy użyciu aktualizacji
* Usuń jeden wiersz przy użyciu polecenia DELETE

W tym temacie pokazano, jak za pomocą łącznika w aplikacji logiki przetwarzać operacje bazy danych.

Aby dowiedzieć się więcej na temat Logic Apps, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostępne akcje
Ten łącznik obsługuje następujące akcje aplikacji logiki:

* Getmoże
* GetRow —
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista tabel
Tworzenie aplikacji logiki dla każdej operacji składa się z wielu kroków wykonywanych za pomocą Microsoft Azure Portal.

W ramach aplikacji logiki można dodać akcję do listy tabel w bazie danych programu Informix. Ta akcja instruuje Łącznik do przetworzenia instrukcji schematu programu Informix, takiej jak `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę**, np. `InformixgetTables`, **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**.  
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** , a następnie wybierz pozycję **Informix — Pobierz tabele (wersja zapoznawcza)** .
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. W okienku Konfiguracja programu **Informix — Pobieranie tabel** zaznacz **pole wyboru** , aby włączyć **łączenie za pośrednictwem lokalnej bramy danych**. Zauważ, że ustawienia zmieniają się z chmury do lokalnego.
   
   * Wartość typu dla **serwera**w postaci dwukropek numeru portu lub adresu. Na przykład wpisz `ibmserver01:9089`.
   * Wartość typu dla **bazy danych**. Na przykład wpisz `nwind`.
   * Wybierz wartość opcji **uwierzytelnianie**. Na przykład wybierz pozycję **podstawowa**.
   * Wartość typu dla **username**. Na przykład wpisz `informix`.
   * Wpisz wartość **hasła**. Na przykład wpisz `Password1`.
   * Wybierz wartość dla **bramy**. Na przykład wybierz pozycję **datagateway01**.
7. Wybierz pozycję **Utwórz**, a następnie wybierz pozycję **Zapisz**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. W bloku **InformixgetTables** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
9. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_tables**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać listę tabel.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Tworzenie połączeń
Ten łącznik obsługuje połączenia z bazą danych lokalnie i w chmurze przy użyciu następujących właściwości połączenia. 

| Właściwość | Opis |
| --- | --- |
| serwer |Wymagany. Akceptuje wartość ciągu reprezentującą adres TCP/IP lub alias (w formacie IPv4 lub IPv6), po którym następuje numer portu TCP/IP (rozdzielany średnikami). |
| baza danych |Wymagany. Akceptuje wartość ciągu reprezentującą DRDA relacyjnej bazy danych (RDBNAM). Informix akceptuje 128-bajtowy ciąg (baza danych znana jako nazwa bazy danych programu IBM Informix). |
| uwierzytelnianie |Opcjonalny. Akceptuje wartość elementu listy: Basic lub Windows (Kerberos). |
| nazwa użytkownika |Wymagany. Akceptuje wartość ciągu. |
| hasło |Wymagany. Akceptuje wartość ciągu. |
| Punkt |Wymagany. Akceptuje wartość elementu listy reprezentującą lokalną bramę danych zdefiniowaną do Logic Apps w grupie magazynów. |

## <a name="create-the-on-premises-gateway-connection"></a>Tworzenie połączenia bramy lokalnej
Ten łącznik może uzyskać dostęp do lokalnej bazy danych programu Informix przy użyciu lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz temat bramy. 

1. W okienku Konfiguracja **bram** zaznacz **pole wyboru** , aby włączyć **łączenie za pośrednictwem bramy**. Zobacz zmiany ustawień z chmury do lokalnego.
2. Wartość typu dla **serwera**w postaci dwukropek numeru portu lub adresu. Na przykład wpisz `ibmserver01:9089`.
3. Wartość typu dla **bazy danych**. Na przykład wpisz `nwind`.
4. Wybierz wartość opcji **uwierzytelnianie**. Na przykład wybierz pozycję **podstawowa**.
5. Wartość typu dla **username**. Na przykład wpisz `informix`.
6. Wpisz wartość **hasła**. Na przykład wpisz `Password1`.
7. Wybierz wartość dla **bramy**. Na przykład wybierz pozycję **datagateway01**.
8. Wybierz pozycję **Utwórz** , aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Tworzenie połączenia w chmurze
Ten łącznik może uzyskać dostęp do bazy danych Informix w chmurze. 

1. W okienku Konfiguracja **bram** pozostaw **pole wyboru** wyłączone (Niekliknięte) **Połącz za pośrednictwem bramy**. 
2. Wartość typu dla **nazwy połączenia**. Na przykład wpisz `hisdemo2`.
3. Wartość typu w polu **Nazwa serwera programu Informix**, w postaci dwukropekowego adresu lub aliasu numer portu. Na przykład wpisz `hisdemo2.cloudapp.net:9089`.
4. Wartość typu dla **nazwy bazy danych programu Informix**. Na przykład wpisz `nwind`.
5. Wartość typu dla **username**. Na przykład wpisz `informix`.
6. Wpisz wartość **hasła**. Na przykład wpisz `Password1`.
7. Wybierz pozycję **Utwórz** , aby kontynuować. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Pobierz wszystkie wiersze przy użyciu polecenia SELECT
Można utworzyć akcję aplikacji logiki w celu pobrania wszystkich wierszy w tabeli programu Informix. Ta akcja powoduje, że łącznik przetwarza instrukcję SELECT programu Informix, taką jak `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę** (np. "**InformixgetRows**"), **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**. 
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** , a następnie wybierz pozycję **Informix — Pobierz wiersze (wersja zapoznawcza)** .
6. W akcji **Pobierz wiersze (wersja zapoznawcza)** wybierz pozycję **Zmień połączenie**.
7. W okienku Konfiguracja **połączeń** wybierz pozycję **Utwórz nowy**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. W okienku Konfiguracja **bram** pozostaw **pole wyboru** wyłączone (Niekliknięte) **Połącz za pośrednictwem bramy**.
   
   * Wartość typu dla **nazwy połączenia**. Na przykład wpisz `HISDEMO2`.
   * Wartość typu w polu **Nazwa serwera programu Informix**, w postaci dwukropekowego adresu lub aliasu numer portu. Na przykład wpisz `HISDEMO2.cloudapp.net:9089`.
   * Wartość typu dla **nazwy bazy danych programu Informix**. Na przykład wpisz `NWIND`.
   * Wartość typu dla **username**. Na przykład wpisz `informix`.
   * Wpisz wartość **hasła**. Na przykład wpisz `Password1`.
9. Wybierz pozycję **Utwórz** , aby kontynuować.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Z listy **Nazwa tabeli** wybierz **strzałkę w dół**, a następnie wybierz pozycję **obszar**.
11. Opcjonalnie wybierz pozycję **Pokaż opcje zaawansowane** , aby określić opcje zapytania.
12. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. W bloku **InformixgetRows** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
14. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_rows**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać listę wierszy.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Dodaj jeden wiersz przy użyciu instrukcji INSERT
Można utworzyć akcję aplikacji logiki, aby dodać jeden wiersz w tabeli programu Informix. Ta akcja instruuje łącznik, aby przetworzyć instrukcję wstawiania Informix, taką jak `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę**, np. `InformixinsertRow`, **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**. 
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** , a następnie wybierz pozycję **Informix — Wstaw wiersz (wersja zapoznawcza)** .
6. W akcji **Pobierz wiersze (wersja zapoznawcza)** wybierz pozycję **Zmień połączenie**. 
7. W okienku Konfiguracja **połączeń** wybierz pozycję, aby wybrać połączenie. Na przykład wybierz pozycję **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Z listy **Nazwa tabeli** wybierz **strzałkę w dół**, a następnie wybierz pozycję **obszar**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdkę). Na przykład wpisz `99999` dla **AREAID**, wpisz `Area 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. W bloku **InformixinsertRow** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
12. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_rows**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać nowy wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Pobierz jeden wiersz przy użyciu polecenia SELECT
Można utworzyć akcję aplikacji logiki, aby pobrać jeden wiersz w tabeli programu Informix. Ta akcja instruuje łącznik, aby przetworzyć instrukcję "SELECT WHERE", taką jak `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę**, np. `InformixgetRow`, **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**. 
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** , a następnie wybierz pozycję **Informix — Pobierz wiersze (wersja zapoznawcza)** .
6. W akcji **Pobierz wiersze (wersja zapoznawcza)** wybierz pozycję **Zmień połączenie**. 
7. W okienku konfiguracje **połączeń** Wybierz istniejące połączenie. Na przykład wybierz pozycję **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Z listy **Nazwa tabeli** wybierz **strzałkę w dół**, a następnie wybierz pozycję **obszar**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdkę). Na przykład wpisz `99999` dla **AREAID**. 
10. Opcjonalnie wybierz pozycję **Pokaż opcje zaawansowane** , aby określić opcje zapytania.
11. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. W bloku **InformixgetRow** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
13. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_rows**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Zmień jeden wiersz przy użyciu aktualizacji
Można utworzyć akcję aplikacji logiki, aby zmienić jeden wiersz w tabeli programu Informix. Ta akcja instruuje Łącznik do przetworzenia instrukcji UPDATE programu Informix, takiej jak `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę**, np. `InformixupdateRow`, **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**. 
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** , a następnie wybierz pozycję **Informix — Aktualizuj wiersz (wersja zapoznawcza)** .
6. W akcji **Pobierz wiersze (wersja zapoznawcza)** wybierz pozycję **Zmień połączenie**. 
7. W okienku konfiguracje **połączeń** Wybierz istniejące połączenie. Na przykład wybierz pozycję **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Z listy **Nazwa tabeli** wybierz **strzałkę w dół**, a następnie wybierz pozycję **obszar**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdkę). Na przykład wpisz `99999` dla **AREAID**, wpisz `Updated 99999`i wpisz `102` dla **REGIONID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. W bloku **InformixupdateRow** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
12. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_rows**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać nowy wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Usuń jeden wiersz przy użyciu polecenia DELETE
Można utworzyć akcję aplikacji logiki, aby usunąć jeden wiersz w tabeli programu Informix. Ta akcja instruuje Łącznik do przetworzenia instrukcji DELETE programu Informix, takiej jak `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
1. Na **płycie startowej platformy Azure**wybierz pozycję **+** (znak plus), **Sieć Web + aplikacje mobilne**, a następnie pozycję **aplikacja logiki**.
2. Wprowadź **nazwę**, np. `InformixdeleteRow`, **subskrypcję**, **grupę zasobów**, **lokalizację**i **Plan App Service**. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**.

### <a name="add-a-trigger-and-action"></a>Dodawanie wyzwalacza i akcji
1. W **projektancie Logic Apps**wybierz pozycję **puste LogicApp** na liście **Szablony** .
2. Na liście **wyzwalacze** wybierz pozycję **cykl**. 
3. W wyzwalaczu **cyklu** wybierz pozycję **Edycja**, wybierz pozycję Lista rozwijana **częstotliwość** , aby wybrać pozycję **dzień**, a następnie wybierz pozycję **Interwał** do typu **7**. 
4. Zaznacz pole **+ nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.
5. Na liście **Akcje** wpisz **Informix** w polu **Wyszukaj więcej akcji** Edytuj, a następnie wybierz pozycję **Informix-Usuń wiersz (wersja zapoznawcza)** .
6. W akcji **Pobierz wiersze (wersja zapoznawcza)** wybierz pozycję **Zmień połączenie**. 
7. W okienku konfiguracje **połączeń** Wybierz istniejące połączenie. Na przykład wybierz pozycję **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Z listy **Nazwa tabeli** wybierz **strzałkę w dół**, a następnie wybierz pozycję **obszar**.
9. Wprowadź wartości dla wszystkich wymaganych kolumn (zobacz czerwoną gwiazdkę). Na przykład wpisz `99999` dla **AREAID**. 
10. Wybierz pozycję **Zapisz**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. W bloku **InformixdeleteRow** na liście **wszystkie uruchomienia** w obszarze **Podsumowanie**wybierz element pierwszy na liście (ostatni przebieg).
12. W bloku **przebieg aplikacji logiki** wybierz pozycję **Uruchom szczegóły**. Na liście **Akcja** wybierz pozycję **Get_rows**. Sprawdź wartość **stanu**, która powinna się **powieść**. Wybierz **łącze dane wejściowe** , aby wyświetlić dane wejściowe. Wybierz **łącze dane wyjściowe**i Wyświetl dane wyjściowe. który powinien zawierać usunięty wiersz.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Obsługiwane platformy i wersje programu Informix
Ten łącznik obsługuje następujące wersje programu IBM Informix, gdy jest skonfigurowany do obsługi połączeń klienta rozproszonej relacyjnej bazy danych (DRDA).

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/informix/). 

## <a name="next-steps"></a>Następne kroki
[Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zapoznaj się z innymi dostępnymi łącznikami w Logic Apps na naszej [liście interfejsów API](apis-list.md).

