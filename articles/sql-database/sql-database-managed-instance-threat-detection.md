---
title: Wykrywanie - zagrożeń baza danych Azure SQL zarządzane wystąpienia | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 14ef907717045e2e0cf297694d92468a65e57e4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650081"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Wykrywanie zagrożeń wystąpienia zarządzane baza danych Azure SQL

SQL wykrywanie zagrożeń wykrywa nietypowych działań wskazujący nietypowe i potencjalnie szkodliwe próbuje uzyskać dostęp lub wykorzystać baz danych w usłudze Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza).

## <a name="overview"></a>Przegląd

Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują potencjalne zagrożenia bezpieczeństwa zarządzane wystąpienia. Wykrywanie zagrożeń jest teraz zarządzane wystąpienia w wersji zapoznawczej.

Wykrywanie zagrożeń udostępnia nową warstwę zabezpieczeń, co umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, podając alerty zabezpieczeń na nietypowe działania bazy danych. Wykrywanie zagrożeń upraszcza potencjalne zagrożenia do wystąpienia zarządzane bez konieczności ekspertów zabezpieczeń lub zarządzać zabezpieczeniami zaawansowanymi, monitorowanie systemów. Środowisko pełnego postępowania zaleca się włączania Azure zarządzane wystąpienia inspekcji, która zapisuje zdarzenia bazy danych inspekcji logowania na koncie magazynu Azure. 

Wykrywanie zagrożeń SQL integruje alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/), i jest on rozliczany każdego chronionego wystąpienia zarządzanych w tej samej cenie warstwy standardowa Centrum zabezpieczeń Azure, w $15 węzła/miesięcznie, gdzie każdy chronione wystąpienia zarządzane jest liczone jako jeden węzeł.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Skonfiguruj wykrywanie zagrożeń dla swojego wystąpienia zarządzane w portalu Azure
1. Uruchamianie portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji wystąpienia zarządzane mają być chronione. W **ustawienia** wybierz pozycję **wykrywanie zagrożeń**. 
3. Na stronie Konfiguracja wykrywanie zagrożeń 
   - Włącz **ON** wykrywanie zagrożeń.
   - Skonfiguruj **lista wiadomości e-mail** otrzymywać alerty zabezpieczeń po wykryciu nietypowe działania bazy danych.
   - Wybierz **konto magazynu Azure** gdzie nietypowych zagrożeń rekordów inspekcji są zapisywane. 
4.  Kliknij przycisk **zapisać** zapisać zasady wykrywania zagrożeń nowe lub zaktualizowane.

   ![Wykrywanie zagrożeń](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Eksploruj nietypowych działań wystąpienia zarządzane w przypadku wykrycia podejrzanych zdarzeń

1. Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowe działania bazy danych. 

   Wiadomości e-mail zawiera informacje dotyczące zdarzeń podejrzane zabezpieczeń, w tym charakter nietypowych działań, nazwa bazy danych, nazwę serwera i czas trwania zdarzenia. Ponadto zawiera informacje na temat przyczyny i zalecane akcje do sprawdzania i ograniczyć potencjalne zagrożenie dla wystąpienia zarządzane.

   ![wykrywanie zagrożeń — wiadomości e-mail](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Kliknij przycisk **przeglądania ostatnich alertów SQL** link w wiadomości e-mail do uruchomienia portalu Azure i pokazać na stronie alerty Centrum zabezpieczeń Azure, które zawiera omówienie active zagrożeń SQL wykryte w bazie danych wystąpienia zarządzane.

   ![aktywne zagrożeń](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Kliknij określony alert, aby uzyskać dodatkowe szczegóły i akcje badanie tego zagrożenia i korygowania przyszłych zagrożeń.

   Na przykład iniekcja kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie. Iniekcja kodu SQL służy do przeprowadzania ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych. Dla alertów iniekcja kodu SQL szczegóły alertu zawierają narażone instrukcji SQL, która została wykorzystana.

   ![iniekcja kodu SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Zarządzane alertów wykrywania zagrożeń wystąpienia 

Wykrywanie zagrożeń dla wystąpienia zarządzane wykrywa nietypowe działania, które wskazują nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystać baz danych i mogą wyzwalać następujące alerty:
- **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. Może to wskazywać lukę w zabezpieczeniach umożliwiającą ataki przez iniekcję SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:
 - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
 - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, czy osoba atakująca podejmuje próbę wprowadzenia złośliwego instrukcji SQL przy użyciu kodu usterce aplikacji lub procedur składowanych.
- **Dostęp z nietypowych lokalizacji**: ten alert jest wyzwalany, gdy zmiany w wzorca dostępu do wystąpienia zarządzane, gdy ktoś zalogował się na do wystąpienia zarządzane z nietypowych lokalizacji geograficznych. W niektórych przypadkach alert wykrywa uzasadnionych akcji (Nowa aplikacja lub dewelopera operacji konserwacji). W pozostałych przypadkach alert wykryje złośliwe działania (byłych pracowników, atakujący zewnętrznych itd.).
- **Dostęp z centrum danych Azure nietypowe**: ten alert jest wyzwalane, gdy nastąpiła zmiana we wzorcu dostępu do wystąpienia zarządzane, gdy ktoś zalogował się na do wystąpienia zarządzane z centrum danych Azure, która nie była widoczna podczas uzyskiwania dostępu do tego zarządzane Wystąpienie w ostatnim okresie. W niektórych przypadkach alert wykrywa uzasadnionych akcji (nowej aplikacji platformy Azure, usługa Power BI, Edytor zapytań SQL Azure i tak dalej). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanym podmiotu**: ten alert jest wyzwalany, gdy zmiany w wzorca dostępu do serwera zarządzanego wystąpienia, gdy ktoś zalogował się na do wystąpienia zarządzane przy użyciu nietypowe podmiot zabezpieczeń (użytkownika SQL). W niektórych przypadkach alert wykrywa uzasadnionych akcji (operacji konserwacji nowe Deweloper aplikacji). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W pozostałych przypadkach alert wykrywa ataków siłowych.

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat wystąpienia zarządzane, zobacz [co to jest wystąpieniem zarządzane](sql-database-managed-instance.md)
- Dowiedz się więcej o [zarządzane wystąpienia inspekcji](https://go.microsoft.com/fwlink/?linkid=869430) 
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
