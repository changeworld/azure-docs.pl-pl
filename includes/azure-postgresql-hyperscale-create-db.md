---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183474"
---
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie usługi Azure Database for PostgreSQL — w Hiperskali (Citus)

Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for PostgreSQL:
1. W lewym górnym rogu witryny Azure Portal kliknij polecenie **Utwórz zasób**.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Bazy danych** wybierz pozycję **Azure Database for PostgreSQL**.
3. Dla opcji wdrożenia kliknij **Utwórz** przycisku w obszarze **grupy serwerów na dużą skalę (Citus) — wersja ZAPOZNAWCZA.**
4. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
   - Grupa zasobów: kliknij **Utwórz nową** link poniżej pola tekstowego dla tego pola. Wprowadź nazwę, taką jak **myresourcegroup**.
   - Nazwa grupy serwerów: wprowadź unikatową nazwę dla nowej grupy serwerów będzie również używany dla poddomeny serwera.
   - Nazwa użytkownika Administratora: obecnie musi być wartością **citus**i nie można zmienić.
   - Hasło: musi zawierać co najmniej ośmiu znaków i zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie małe litery, cyfry (0 – 9) i znaki inne niż alfanumeryczne (!, $, #, % itd.)
   - Lokalizacja: Użyj lokalizacji, która jest najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

   > [!IMPORTANT]
   > Hasło administratora serwera określone w tym miejscu jest wymagane do logowania do serwera i jego baz danych. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

5. Kliknij przycisk **Konfigurowanie grupy serwerów**. Pozostaw ustawienia w tej sekcji bez zmian, a następnie kliknij przycisk **Zapisz**.
6. Kliknij przycisk **przeglądu + Utwórz** i następnie **Utwórz** aby aprowizować serwer. Aprowizacja zajmuje kilka minut.
7. Strony nastąpi przekierowanie do monitorowania wdrożenia. Zmiany stanu na żywo z **wdrożenia jest w toku** do **swoje wdrożenie jest ukończone**, kliknij przycisk **dane wyjściowe** element menu po lewej stronie.
8. Strony danych wyjściowych będzie zawierał nazwę hosta koordynatora za pomocą przycisku obok niej, aby skopiować wartość do Schowka. Zarejestruj te informacje w celu późniejszego użycia.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) używa usługi zapory na poziomie serwera. Domyślnie Zapora uniemożliwia wszystkich zewnętrznym aplikacjom i narzędziom łączenie się z węzłem koordynatora i wszelkimi bazami danych wewnątrz. Firma Microsoft należy dodać regułę otwierającą zaporę dla określonego zakresu adresów IP.

1. Z **dane wyjściowe** sekcji, w której został skopiowany wcześniej z nazwą hosta węzła koordynatora, kliknij przycisk Wstecz do **Przegląd** elementu menu.

2. Znajdź nazwę wdrożenia grupy serwerów, a następnie kliknij go. (Nazwa grupy serwera będzie *nie* sufiksu. Elementy z nazwy kończące się na, na przykład, "-c", "-w0", lub "-w1" grupy serwerów nie są.)

3. Kliknij przycisk **zapory** w obszarze **zabezpieczeń** w menu po lewej stronie.

4. Kliknij łącze **+ Dodaj regułę zapory dla bieżącego adresu IP klienta**.

5. Na koniec kliknij **Zapisz** przycisku.

   > [!NOTE]
   > Serwer Azure PostgreSQL komunikuje się przez port 5432. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być zablokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Połączenia z bazą danych za pomocą narzędzia psql

Podczas tworzenia usługi Azure Database for postgresql w warstwie serwera domyślna baza danych o nazwie **citus** zostanie utworzony. Aby połączyć z serwerem bazy danych, należy parametry połączenia i hasło administratora.

1. Uzyskaj parametry połączenia. Na stronie grupy serwera kliknij **parametry połączenia** elementu menu. (Jest w obszarze **ustawienia**.) Znajdź parametry oznaczone jako  **C++ (libpq)** . Klucz będzie mieć postać:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Skopiuj ciąg. Należy zastąpić "{Twoja\_hasło}" z hasło administracyjne, którą wybrano wcześniej. System nie przechowuje hasło w postaci zwykłego tekstu i dlatego nie można wyświetlić go dla Ciebie w parametrach połączenia.

2. Otwórz okno terminalu na komputerze lokalnym.

3. W wierszu polecenia nawiązać połączenie z usługi Azure Database for postgresql w warstwie serwera za pomocą [psql](https://www.postgresql.org/docs/current/app-psql.html) narzędzia. Przekazywanie parametrów połączenia w cudzysłowie, upewniając się, że zawiera hasło:
   ```bash
   psql "{connection_string}"
   ```

   Na przykład poniższe polecenie nawiązuje połączenie do węzła koordynatora, grupy serwerów **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
