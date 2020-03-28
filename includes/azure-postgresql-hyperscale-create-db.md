---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973422"
---
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie bazy danych platformy Azure dla postgreSql — hiperskala (Citus)

Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for PostgreSQL:
1. W lewym górnym rogu witryny Azure Portal kliknij polecenie **Utwórz zasób**.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Bazy danych** wybierz pozycję **Azure Database for PostgreSQL**.
3. W przypadku opcji wdrażania kliknij przycisk **Utwórz** w obszarze **Grupa serwerów w hiperskali (Citus).**
4. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
   - Grupa zasobów: kliknij pole **Utwórz nowe** łącze pod polem tekstowym tego pola. Wprowadź nazwę, taką jak **myresourcegroup**.
   - Nazwa grupy serwerów: wprowadź unikatową nazwę dla nowej grupy serwerów, która będzie również używana dla poddomeny serwera.
   - Nazwa użytkownika administratora: obecnie musi być wartością **citus**i nie można jej zmienić.
   - Hasło: musi mieć co najmniej osiem znaków i zawierać znaki z trzech z następujących kategorii – angielskie wielkie litery, angielskie małe litery, cyfry (0-9) i znaki inne niż alfanumeryczne (!, $, #, %itd.)
   - Lokalizacja: użyj najbliższej lokalizacji użytkownikom, aby zapewnić im najszybszy dostęp do danych.

   > [!IMPORTANT]
   > Hasło administratora serwera, które określisz w tym miejscu, jest wymagane do zalogowania się do serwera i jego baz danych. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

5. Kliknij **pozycję Konfiguruj grupę serwerów**. Pozostaw ustawienia w tej sekcji bez zmian i kliknij przycisk **Zapisz**.
6. Kliknij **przycisk Dalej : >** sieci u dołu ekranu.

7. Na karcie **Sieć** kliknij przycisk opcji **Publiczny punkt końcowy.**
   ![Wybrany publiczny punkt końcowy](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Kliknij łącze **+ Dodaj bieżący adres IP klienta**.
   ![Dodano adres IP klienta](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Serwer Azure PostgreSQL komunikuje się przez port 5432. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być zablokowany przez zaporę sieciową. Jeśli tak, nie można połączyć się z klastrem w hiperskali (Citus), chyba że dział IT otworzy port 5432.
   >

9. Kliknij **pozycję Przejrzyj + utwórz,** a następnie **utwórz,** aby aprowizować serwer. Aprowizacja zajmuje kilka minut.
10. Strona zostanie przekierowana do monitorowania wdrożenia. Po zmianie stanu na żywo z **wdrożenia jest w toku** do wdrożenia jest **zakończona,** kliknij element menu **Dane po** lewej stronie strony.
11. Strona danych wyjściowych będzie zawierać hostnazy koordynatora z przyciskiem obok, aby skopiować wartość do schowka. Zapisz te informacje do późniejszego użycia.

## <a name="connect-to-the-database-using-psql"></a>Łączenie się z bazą danych za pomocą psql

Podczas tworzenia usługi Azure Database dla serwera PostgreSQL tworzona jest domyślna baza danych o nazwie **citus.** Aby połączyć się z serwerem bazy danych, potrzebny jest ciąg połączenia i hasło administratora.

1. Uzyskaj parametry połączenia. Na stronie grupy serwera kliknij pozycję menu **Parametry połączenia.** (Jest w **ustawieniach**.) Znajdź ciąg oznaczony **psql**. Będzie to formularz:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Skopiuj ciąg. Musisz zastąpić "{twoje\_hasło}" wybranym wcześniej hasłem administracyjnym. System nie przechowuje hasła w postaci zwykłego tekstu, więc nie może go wyświetlić w ciągu połączenia.

2. Otwórz okno terminala na komputerze lokalnym.

3. W wierszu polecenia połącz się z usługą Azure Database dla serwera PostgreSQL za pomocą narzędzia [psql.](https://www.postgresql.org/docs/current/app-psql.html) Przekaż swój ciąg połączenia w cudzysłowie, upewnia się, że zawiera hasło:
   ```bash
   psql "host=..."
   ```

   Na przykład następujące polecenie łączy się z węzłem koordynatora **mydemoserver grupy serwerów serwerów serwerów serwerów serwerów serwerów serwerów serwerów serwerów:**

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
