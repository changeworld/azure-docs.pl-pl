---
title: Wykrywanie - Azure SQL Data Warehouse zagrożeń | Dokumentacja firmy Microsoft
description: Skonfiguruj wykrywanie zagrożeń i Eksploruj podejrzane zdarzenia w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 8dc1ef0432536c6bfd4fe069406cd057ca069ea2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Wykrywanie zagrożeń w magazynie danych SQL Azure
Skonfiguruj wykrywanie zagrożeń i Eksploruj podejrzane zdarzenia w usłudze Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>Co to jest wykrywanie zagrożeń
Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych, które wskazują możliwe zagrożenia bezpieczeństwa bazy danych. 

Funkcja wykrywania zagrożeń stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie na bieżąco, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy można eksplorować podejrzane zdarzenia przy użyciu [inspekcję magazynu danych SQL Azure](sql-data-warehouse-auditing-overview.md) ustalenie, jeśli są one wynikiem próby dostępu, naruszenia lub wykorzystania danych w magazynie danych.
Wykrywanie zagrożeń upraszcza potencjalne zagrożenia w magazynie danych, bez konieczności ekspertów zabezpieczeń lub zarządzać zabezpieczeniami zaawansowanymi, monitorowanie systemów.

Na przykład funkcja wykrywania zagrożeń wykrywa niektóre nietypowe działania bazy danych, wskazują na potencjalne próby iniekcji kodu SQL. Iniekcja kodu SQL jest jednym z typowych problemów z zabezpieczeniami aplikacji internetowych w Internecie. Metody tej używa się do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji, aby przeprowadzać iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych.

## <a name="set-up-threat-detection-for-your-database"></a>Skonfiguruj wykrywanie zagrożeń dla bazy danych
1. Uruchamianie portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do bloku konfiguracji usługi SQL Data Warehouse, który chcesz monitorować. W bloku Ustawienia wybierz opcję **Inspekcja i wykrywanie zagrożeń**.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. W **Inspekcja i wykrywanie zagrożeń** Włącz bloku konfiguracji **ON** inspekcji, które będą wyświetlane ustawienia wykrywania zagrożeń.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Włącz **ON** wykrywanie zagrożeń.
5. Skonfiguruj listę wiadomości e-mail, które będą wysyłane alerty zabezpieczeń po wykryciu nietypowych danych magazynu działań.
6. Kliknij przycisk **zapisać** w **Inspekcja i wykrywanie zagrożeń** bloku konfiguracji, aby zapisać nowe lub zaktualizowane inspekcji i zasady wykrywania zagrożeń.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Eksplorowanie danych nietypowych działań magazynu po wykryciu podejrzanych zdarzeń
1. Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowe działania bazy danych. <br/>
   Wiadomość e-mail będzie zapewniać informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń, w tym o naturze nietypowych działań, nazwie bazy danych, nazwie serwera oraz czasie trwania zdarzenia. Ponadto będzie zapewniać informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń dla bazy danych.<br/>
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. W wiadomości e-mail, kliknij polecenie **dziennik inspekcji SQL Azure** łącza, co spowoduje uruchomienie portalu Azure i wyświetlić odpowiednie rekordy inspekcji w czasie zbliżonym do podejrzane zdarzenia.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Polecenie rekordów inspekcji, aby wyświetlić więcej szczegółów na działania podejrzane bazy danych, takie jak instrukcji SQL, Niepowodzenie IP Przyczyna i klienta.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. W bloku Rekordy inspekcji kliknij opcję **Otwórz w programie Excel**, aby otworzyć wstępnie skonfigurowany szablon programu Excel i zaimportować oraz uruchomić dogłębną analizę dziennika inspekcji dla przybliżonego czasu wystąpienia podejrzanego zdarzenia.<br/>
   **Uwaga:** programu Excel 2010 lub nowszej, zasilanie kwerendy i **szybkie łączenie** ustawienie jest wymagane
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Aby skonfigurować ustawienie **Szybkie łączenie** — na karcie wstążki **POWER QUERY** wybierz pozycję **Opcje**, aby wyświetlić okno dialogowe Opcje. Wybierz sekcję prywatności i wybierz drugą opcję — „Ignorowanie poziomów prywatności i potencjalne poprawianie wydajności”:
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Aby załadować dzienniki inspekcji SQL, upewnij się, że parametry na karcie ustawień są skonfigurowane prawidłowo, a następnie wybierz wstążkę „Dane” i kliknij przycisk „Odśwież wszystko”.
   
    ![Okienko nawigacji](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Wyniki zostaną wyświetlone w arkuszu **Dzienniki inspekcji SQL**, co umożliwia uruchomienie dogłębnej analizy wykrytych nietypowych działań, a także ograniczenie wpływu zdarzenia dotyczącego zabezpieczeń na Twoją aplikację.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o zabezpieczeniach, zobacz [bezpiecznego magazynu danych](sql-data-warehouse-overview-manage-security.md).
