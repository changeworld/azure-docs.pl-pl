---
title: Przywracanie serwera w bazie danych systemu Azure dla PostgreSQL
description: W tym artykule opisano sposób przywracania serwera w bazie danych Azure dla PostgreSQL przy użyciu portalu Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: b4fe1151337dc6f5874e6ad102c6e905e90fd963
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412760"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Sposób wykonywania kopii zapasowej i przywracania serwera w bazie danych Azure dla PostgreSQL przy użyciu portalu Azure

## <a name="backup-happens-automatically"></a>Kopia zapasowa jest wykonywana automatycznie
Bazy danych platformy Azure dla serwerów PostgreSQL kopię zapasową okresowo do włączania funkcji przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszych w momencie, na nowym serwerze.

## <a name="set-backup-configuration"></a>Zestaw kopii zapasowych konfiguracji

Należy wybrać opcję konfigurowania serwera na potrzeby kopii zapasowych lokalnie nadmiarowego lub geograficznie nadmiarowy kopii zapasowych na utworzenie serwera, w **warstwy cenowej** okna.

> [!NOTE]
> Po utworzeniu serwera, rodzaj nadmiarowość, który ma ona geograficznie nadmiarowy vs magazyn lokalnie nadmiarowy, nie zostaną włączone.
>

Tworząc serwer za pośrednictwem portalu Azure **warstwy cenowej** okno jest, gdzie albo wybierz **magazyn lokalnie nadmiarowy** lub **geograficznie nadmiarowy** kopie zapasowe serwer. To okno jest również, którym można wybrać **okres przechowywania kopii zapasowej** — czas (w dniach) mają być przechowywane dla kopii zapasowych serwera.

   ![Cenowym — wybierz nadmiarowość kopii zapasowej](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [bazy danych serwera PostgreSQL — Szybki Start Azure](quickstart-create-server-database-portal.md).

Okres przechowywania kopii zapasowych serwera można zmienić, modyfikując następujące czynności:
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Wybierz bazy danych Azure, PostgreSQL serwera. Ta akcja powoduje otwarcie **omówienie** strony.
3. Wybierz **warstwy cenowej** z menu, w obszarze **ustawienia**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** do swoich preferencji od 7 do 35 dni.
Na poniższym zrzucie ekranu ma została zwiększona do 34 dni.
![Zwiększona okres przechowywania kopii zapasowych](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij przycisk **OK** aby potwierdzić zmianę.

Okres przechowywania kopii zapasowych decyduje, jak daleko w czasie, które mogą być pobierane w momencie przywracania, ponieważ jest on oparty na kopie zapasowe dostępne. W momencie przywracania jest dalsze opisane w poniższej sekcji. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Bazy danych platformy Azure dla PostgreSQL umożliwia przywrócenie serwera do punktu w czasie, a do z uprawnieniami do nowego serwera. Użyj tego nowego serwera, aby odzyskać dane lub ma punkt do nowego serwera aplikacji klienta.

Na przykład jeśli przypadkowo tabeli w południe dzisiaj, można przywrócenie na czas bezpośrednio przed południe i pobieranie Brak tabeli i danych z tej kopii nowego serwera. W momencie przywracania na serwerze poziomu, nie jest na poziomie bazy danych.

Poniższe kroki należy przywrócić działanie serwera próbki do punktu w czasie:
1. W portalu Azure wybierz PostgreSQL serwera bazy danych Azure. 

2. Na pasku narzędzi serwera **omówienie** wybierz pozycję **przywrócić**.

   ![Bazy danych platformy Azure dla przycisku PostgreSQL — Przegląd — przywracania](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz przywracania wymagane informacje:

   ![Bazy danych platformy Azure dla PostgreSQL — informacji dotyczące przywracania ](./media/howto-restore-server-portal/3-restore.png)
  - **Punkt przywracania**: Wybierz w momencie mają zostać przywrócone.
  - **Serwer docelowy**: Podaj nazwę dla nowego serwera.
  - **Lokalizacja**: nie można wybrać region. Domyślnie jest taki sam jak serwer źródłowy.
  - **Warstwa cenowa**: nie można zmienić tych parametrów, podczas przywracania punktu w czasie wykonywania. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij przycisk **OK** do przywrócenia serwera, aby wykonać przywracanie do punktu w czasie. 

5. Po zakończeniu przywracania, zlokalizuj nowy serwer, który jest tworzony w celu sprawdzenia, czy dane została przywrócona, zgodnie z oczekiwaniami.

>[!Note]
>Nowy serwer powstały w momencie przywracania ma taką samą nazwę logowania administratora serwera i wybierz hasło, które jest prawidłowa dla istniejącego serwera w punkcie czasu. Należy zmienić hasło z nowego serwera **omówienie** strony.

## <a name="geo-restore"></a>Przywracanie Geo
Skonfigurowanie serwera na potrzeby geograficznie nadmiarowy kopii zapasowych można utworzyć nowego serwera z kopii zapasowej tego istniejącego serwera. W dowolnym regionie, że baza danych Azure dla PostgreSQL jest dostępny, można utworzyć nowego serwera.  

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu. Wybierz pozycję **Bazy danych** > **Azure Database for PostgreSQL**.

   ![Opcja „Azure Database for PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. W formularzu **wybierz źródło** listy rozwijanej wybierz **kopii zapasowej**. Ta akcja spowoduje załadowanie listę serwerów, które mają nadmiarowe kopie zapasowe geograficznie włączone. Wybierz jedną z tych kopii zapasowych jako źródło nowego serwera.
   ![Wybierz źródło: Kopia zapasowa i listę z magazynu geograficznie nadmiarowego kopii zapasowych](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Podczas tworzenia serwera nie może być dostępne od razu do przywracania geo. Może upłynąć kilka godzin metadane potrzebne do wypełnienia.
   >

3. Wypełnij pozostałej części formularza z swoje preferencje. Można wybrać dowolny **lokalizacji**. Po wybraniu lokalizacji, możesz wybrać **warstwy cenowej**. Domyślnie są wyświetlane parametry dla istniejącego serwera, który przywracasz z. Możesz kliknąć **OK** bez wprowadzania żadnych zmian, aby te ustawienia były dziedziczone. Lub zmienić **obliczeniowe generowania** (jeśli dostępna w regionie wybrano), liczba **vCores**, **okres przechowywania kopii zapasowej**, i **kopii zapasowej Opcja nadmiarowość**. Zmiana **warstwy cenowej** (podstawowe, ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci) lub **magazynu** rozmiar podczas przywracania nie jest obsługiwany.

>[!Note]
>Nowy serwer tworzone przez przywrócenie geograficznie ma taką samą nazwę logowania administratora serwera i hasło, które jest prawidłowa dla istniejącego serwera w czasie przywracania została zainicjowana. Hasło można zmienić z nowego serwera **omówienie** strony.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o usłudze [kopii zapasowych](concepts-backup.md).
- Dowiedz się więcej o [ciągłość prowadzenia działalności biznesowej](concepts-business-continuity.md) opcje.
