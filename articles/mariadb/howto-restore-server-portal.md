---
title: Jak przywrócić serwer w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano sposób przywracania serwera w usłudze Azure Database dla serwera MariaDB przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 23d683fea494ad0509af359d6e49519f2bc6aa99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746642"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak utworzyć kopię zapasową i przywrócić serwer w usłudze Azure Database dla serwera MariaDB przy użyciu witryny Azure portal

## <a name="backup-happens-automatically"></a>Kopia zapasowa odbywa się automatycznie
Azure Database dla serwera MariaDB są kopie zapasowe serwerów okresowo do włączania funkcji przywracania. Za pomocą tej funkcji można przywrócić serwera i jego baz danych do wcześniejszych punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database dla serwera MariaDB i bazy danych](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Ustaw konfigurację tworzenia kopii zapasowych

Upewnij się wyborem między skonfigurowaniem serwera na potrzeby kopii zapasowych lokalnie nadmiarowy lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera w **warstwy cenowej** okna.

> [!NOTE]
> Po utworzeniu serwera typu nadmiarowości, który ma, nie mogą być przełączane lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy vs.
>

Podczas tworzenia serwera w witrynie Azure portal, **warstwy cenowej** okno jest wybierasz opcję **lokalnie nadmiarowy** lub **magazyn geograficznie nadmiarowy** kopii zapasowych serwer. To okno jest również wybierania **okres przechowywania kopii zapasowej** — jak długo (w dniach) mają być przechowywane dla kopii zapasowych serwera.

   ![Cennik warstwy — wybierz opcję nadmiarowości kopii zapasowej](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [— Azure Database dla serwera MariaDB przewodnika Szybki Start](quickstart-create-mariadb-server-database-using-azure-portal.md).

Okres przechowywania kopii zapasowej można zmienić na serwerze przez następujące kroki:
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz usługi Azure Database dla serwera MariaDB. Ta akcja powoduje otwarcie **Przegląd** strony.

3. Wybierz **warstwy cenowej** z menu, w obszarze **ustawienia**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** zgodnie z preferencjami od 7 do 35 dni.
Na poniższym zrzucie ekranu ma został zwiększony do 35 dni.
![Zwiększono okres przechowywania kopii zapasowej](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij przycisk **OK** aby potwierdzić zmiany.

Okres przechowywania kopii zapasowej decyduje jak daleko w czasie, które mogą być pobierane przywracania w momencie, ponieważ jest on oparty na dostępnych kopii zapasowych. W momencie przywracania jest dokładniejszym opisem zawartym w poniższej sekcji. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Azure Database dla serwera MariaDB umożliwia przywrócenie serwera do punktu w czasie, a do z nową kopią serwera. Możesz użyć tego nowego serwera, aby odzyskać dane lub ma swoje aplikacje klienckie, które wskazują ten nowy serwer.

Na przykład jeśli tabela została przypadkowo porzucony w południe obecnie można przywrócenie na czas, po prostu przed południem i pobrać brakujące tabelę i dane z tej nowej kopii serwera. W momencie przywracania na serwerze poziomu, nie jest na poziomie bazy danych.

Poniższa procedura opisuje przywrócenie przykładowego serwera do punktu w czasie:
1. W witrynie Azure portal wybierz usługi Azure Database dla serwera MariaDB. 

2. Na pasku narzędzi serwera **Przegląd** wybierz opcję **przywrócić**.

   ![Usługa Azure Database for przywracania MariaDB — Przegląd — przycisk](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz Przywracanie wymaganymi informacjami:

   ![Azure Database dla serwera MariaDB — informacje o przywracania](./media/howto-restore-server-portal/3-restore.png)
   - **Punkt przywracania**: Wybierz punkt w czasie, który chcesz przywrócić.
   - **Serwer docelowy**: Podaj nazwę dla nowego serwera.
   - **Lokalizacja**: Nie można wybrać region. Domyślnie jest taka sama jak na serwerze źródłowym.
   - **Warstwa cenowa**: Nie można zmienić tych parametrów, podczas przywracania do punktu w czasie wykonywania. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij przycisk **OK** Aby przywrócić serwer do przywracania do punktu w czasie. 

5. Po zakończeniu przywracania zlokalizuj nowy serwer, który jest tworzony w celu sprawdzenia, czy dane zostały przywrócone zgodnie z oczekiwaniami.

>[!Note]
>Należy pamiętać, nowy serwer utworzone przez Przywracanie do punktu w czasie ma taką samą nazwą logowania administratora serwera i wybrać hasło, która była prawidłowa dla istniejącego serwera w punkcie czasu. Można zmienić hasło z nowego serwera **Przegląd** strony.

## <a name="geo-restore"></a>Przywracanie geograficzne
Skonfigurowanie serwera na potrzeby geograficznie nadmiarowych kopii zapasowych można utworzyć nowy serwer z kopii zapasowej tego istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, że usługi Azure Database dla serwera MariaDB jest dostępny.  

1. Wybierz **baz danych** > **Azure Database dla serwera MariaDB**. Możesz również wpisać **MariaDB** w polu wyszukiwania, aby znaleźć tę usługę.

   ![Opcja "Azure bazy danych MariaDB"](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. W formularzu **wybierz źródło** listy rozwijanej wybierz **kopii zapasowej**. Ta akcja spowoduje załadowanie listę serwerów, które geograficznie nadmiarowy tworzenia kopii zapasowych jest włączone. Wybierz jeden z tych kopii zapasowych jako źródło dla nowego serwera.
   ![Wybierz źródło: Lista geograficznie nadmiarowych kopii zapasowych i kopii zapasowych](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Gdy tworzona jest najpierw serwer nie może być natychmiast dostępne dla przywracania geograficznego. Może upłynąć kilka godzin metadane potrzebne do wypełnienia.
   >

3. Wypełnij pozostałej części formularza z preferencjami. Możesz wybrać dowolny **lokalizacji**. Po wybraniu lokalizację, możesz wybrać **warstwy cenowej**. Domyślnie są wyświetlane parametry dla istniejącego serwera, który jest przywracana z. Możesz kliknąć pozycję **OK** bez wprowadzania żadnych zmian, aby te ustawienia były dziedziczone. Lub możesz zmienić **Generowanie obliczeń** (jeśli dostępne w regionie wybrano), liczba **rdzeni wirtualnych**, **okres przechowywania kopii zapasowej**, i **kopii zapasowej Opcja nadmiarowości**. Zmiana **warstwy cenowej** (podstawowa, ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci) lub **magazynu** rozmiar podczas przywracania nie jest obsługiwany.

>[!Note]
>Nowy serwer utworzone przez Przywracanie geograficzne ma tę samą nazwę logowania administratora serwera i hasła, która była prawidłowa dla istniejącego serwera w czasie przywracania została zainicjowana. Hasło można zmienić z nowego serwera **Przegląd** strony.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o usłudze [kopie zapasowe](concepts-backup.md).
- Dowiedz się więcej o [ciągłość prowadzenia działalności biznesowej](concepts-business-continuity.md) opcje.
