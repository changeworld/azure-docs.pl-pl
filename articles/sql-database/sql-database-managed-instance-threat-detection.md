---
title: Wykrywanie zagrożeń — wystąpienie zarządzane usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych w wystąpieniu zarządzanym.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: ronmat
ms.reviewer: vanto
ms.openlocfilehash: af93a1c0f1e132b04d3bf5c5511f5db1f29ba30c
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498644"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Usługa Azure SQL Database Managed wykrywania zagrożeń wystąpienia

Funkcja wykrywania zagrożeń SQL wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych w bazie danych wystąpienia zarządzanego Azure SQL (wersja zapoznawcza).

## <a name="overview"></a>Przegląd

Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa do wystąpienia zarządzanego. Wykrywanie zagrożeń jest teraz dostępna w wersji zapoznawczej dla wystąpienia zarządzanego.

Funkcja wykrywania zagrożeń stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń na nietypowe działania bazy danych. Wykrywanie zagrożeń upraszcza reagowanie na potencjalne zagrożenia dla wystąpienia zarządzanego bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa. Badanie pełne środowisko pracy zalecane jest włączanie Azure zarządzane wystąpienia inspekcji, która zapisuje zdarzenia bazy danych inspekcji logowania na koncie magazynu platformy Azure. 

Funkcja wykrywania zagrożeń SQL integruje alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), a opłaty za każde chronione wystąpienie zarządzane są naliczane w tej samej cenie, co Azure Security Center w warstwie standardowa na 15 USD/węzeł/miesiąc, gdzie każdego chronionego wystąpienia zarządzanego jest liczony jako jeden węzeł.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Konfigurowanie wykrywania zagrożeń wystąpienia zarządzanego w witrynie Azure portal
1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji wystąpienia zarządzanego, który chcesz chronić. W **ustawienia** wybierz opcję **wykrywania zagrożeń**. 
3. Na stronie konfiguracji wykrywania zagrożeń 
   - Włącz **ON** wykrywanie zagrożeń.
   - Konfigurowanie **listę adresów e-mail** otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konta usługi Azure storage** gdzie nietypowe zagrożeń rekordów inspekcji są zapisywane. 
4.  Kliknij przycisk **Zapisz** można zapisać zasad wykrywania nowych lub zaktualizowanych przed zagrożeniami.

   ![Wykrywanie zagrożeń](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Zapoznaj się z nietypowych działań wystąpienie zarządzane po wykryciu podejrzanego zdarzenia

1. Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowych działań bazy danych. 

   Wiadomość e-mail zawiera informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń tym o naturze nietypowych działań, nazwę bazy danych, nazwę serwera i czas zdarzenia. Ponadto zawiera informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń dla wystąpienia zarządzanego.

   ![wykrywanie zagrożeń — wiadomość e-mail](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Kliknij przycisk **przeglądania ostatnich alertów SQL** linku w wiadomości e-mail do uruchomienia witryny Azure portal i wyświetlenie strony alerty Centrum zabezpieczeń Azure, który zawiera omówienie active wykryto zagrożeń SQL na wystąpieniu zarządzanym bazy danych.

   ![aktywne zagrożenia](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Kliknij określony alert, aby uzyskać dodatkowe informacje i akcje dla tego typu zagrożenia badanie i korygowanie przez przyszłymi zagrożeniami.

   Na przykład wstrzykiwanie kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie. Wstrzyknięcie kodu SQL jest używany do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych. Dla alertów wstrzyknięcie kodu SQL szczegóły alertu zawierają narażone instrukcji SQL, które zostało wykorzystane.

   ![wstrzyknięcie kodu SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Zarządzane alerty wykrywania zagrożeń wystąpienia 

Wykrywanie zagrożeń dla wystąpienia zarządzanego wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych i może wywołać następujące alerty:
- **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. Może to wskazywać lukę w zabezpieczeniach umożliwiającą ataki przez iniekcję SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:
 - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
 - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL przy użyciu podatnego kodu aplikacji lub procedur składowanych.
- **Dostęp z nietypowej lokalizacji**: ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do wystąpienia zarządzanego, gdy ktoś zalogował się do wystąpienia zarządzanego z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (Nowa aplikacja lub operacji konserwacji dewelopera). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca i tak dalej).
- **Dostęp z centrum danych platformy Azure nietypowe**: ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do wystąpienia zarządzanego, gdy ktoś zalogował się do wystąpienia zarządzanego z centrum danych platformy Azure, które nie występowało uzyskiwania dostępu do tego zarządzanego Wystąpienie w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (Nowa aplikacja w Azure, usługa Power BI, Edytor zapytań SQL Azure i tak dalej). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do serwera wystąpienia zarządzanego, gdy ktoś zalogował się do wystąpienia zarządzanego za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (działanie konserwacji nowemu deweloperowi aplikacji). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Dowiedz się więcej o [inspekcji wystąpienia zarządzanego](https://go.microsoft.com/fwlink/?linkid=869430) 
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
