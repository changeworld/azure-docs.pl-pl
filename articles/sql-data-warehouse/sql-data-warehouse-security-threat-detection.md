---
title: Wykrywanie zagrożeń — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Konfigurowanie wykrywania zagrożeń i eksplorować podejrzane zdarzenia w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3204c2705ba7b83c4fe22ab6bdd15c11eeeeda5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421131"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Wykrywanie zagrożeń w usłudze Azure SQL Data Warehouse
Konfigurowanie wykrywania zagrożeń i eksplorować podejrzane zdarzenia w usłudze Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>Co to jest wykrywanie zagrożeń
Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. 

Funkcja wykrywania zagrożeń stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie na bieżąco, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy mogą eksplorować podejrzane zdarzenia przy użyciu [Azure SQL Data Warehouse Auditing](sql-data-warehouse-auditing-overview.md) do określenia, czy są one skutkiem próby uzyskania dostępu, naruszenia zabezpieczeń lub wykorzystania danych w magazynie danych.
Wykrywanie zagrożeń upraszcza reagowanie na potencjalne zagrożenia w magazynie danych, bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa.

Na przykład funkcja wykrywania zagrożeń wykrywa niektóre nietypowe działania bazy danych, wskazują na potencjalne próby iniekcji kodu SQL. Iniekcja kodu SQL jest jednym z typowych problemów z zabezpieczeniami aplikacji internetowych w Internecie. Metody tej używa się do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji, aby przeprowadzać iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych.

## <a name="set-up-threat-detection-for-your-database"></a>Konfigurowanie wykrywania zagrożeń dla bazy danych
1. Uruchamianie witryny Azure Portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do bloku konfiguracji usługi SQL Data Warehouse, którą chcesz monitorować. W bloku Ustawienia wybierz opcję **Inspekcja i wykrywanie zagrożeń**.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. W **Inspekcja i wykrywanie zagrożeń** konfiguracji bloku Włącz **ON** inspekcji, co spowoduje wyświetlenie ustawień wykrywania zagrożeń.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Włącz **ON** wykrywanie zagrożeń.
5. Skonfiguruj listę wiadomości e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu danych nietypowe działania związane z magazynu.
6. Kliknij przycisk **Zapisz** w **inspekcji i wykrywania zagrożeń** blok konfiguracji, aby zapisać nowe lub zaktualizowane funkcję inspekcji i zasady wykrywania zagrożeń.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Zapoznaj się z działania magazynu danych nietypowe w przypadku wykrycia podejrzanych zdarzeń
1. Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowych działań bazy danych. <br/>
   Wiadomość e-mail będzie zapewniać informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń, w tym o naturze nietypowych działań, nazwie bazy danych, nazwie serwera oraz czasie trwania zdarzenia. Ponadto będzie zapewniać informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń dla bazy danych.<br/>
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. W wiadomości e-mail, kliknij pozycję **dziennik inspekcji usługi Azure SQL** link, który spowoduje uruchomienie witryny Azure portal i wyświetlenie odpowiednich rekordów inspekcji zbliżonym do czasu wystąpienia podejrzanego zdarzenia.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Kliknij rekordy inspekcji, aby wyświetlić więcej szczegółów o podejrzanych działaniach bazy danych np. instrukcji SQL błędu przyczyny oraz adresie IP klienta.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. W bloku Rekordy inspekcji kliknij opcję **Otwórz w programie Excel**, aby otworzyć wstępnie skonfigurowany szablon programu Excel i zaimportować oraz uruchomić dogłębną analizę dziennika inspekcji dla przybliżonego czasu wystąpienia podejrzanego zdarzenia.<br/>
   **Uwaga:** W programie Excel 2010 lub nowszej Power Query i **szybkiego łączenia** ustawienie jest wymagane
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Aby skonfigurować ustawienie **Szybkie łączenie** — na karcie wstążki **POWER QUERY** wybierz pozycję **Opcje**, aby wyświetlić okno dialogowe Opcje. Wybierz sekcję prywatności i wybierz drugą opcję — „Ignorowanie poziomów prywatności i potencjalne poprawianie wydajności”:
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Aby załadować dzienniki inspekcji SQL, upewnij się, że parametry na karcie ustawień są skonfigurowane prawidłowo, a następnie wybierz wstążkę „Dane” i kliknij przycisk „Odśwież wszystko”.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Wyniki zostaną wyświetlone w arkuszu **Dzienniki inspekcji SQL**, co umożliwia uruchomienie dogłębnej analizy wykrytych nietypowych działań, a także ograniczenie wpływu zdarzenia dotyczącego zabezpieczeń na Twoją aplikację.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o zabezpieczeniach, zobacz [bezpieczny magazyn danych](sql-data-warehouse-overview-manage-security.md).
