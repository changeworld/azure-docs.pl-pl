---
title: Przywracanie serwera w bazie danych systemu Azure dla programu MySQL
description: W tym artykule opisano sposób przywracania serwera w bazie danych Azure dla programu MySQL przy użyciu portalu Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5d6118a47e10763373c9376ca08d328cf22ab3c8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak wykonywanie kopii zapasowych i przywracania serwera w bazie danych Azure dla programu MySQL przy użyciu portalu Azure

## <a name="backup-happens-automatically"></a>Kopia zapasowa jest wykonywana automatycznie
Bazy danych platformy Azure dla programu MySQL są kopie zapasowe serwerów okresowo do włączania funkcji przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszych w momencie, na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [Azure bazy danych MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Zestaw kopii zapasowych konfiguracji

Należy wybrać opcję konfigurowania serwera na potrzeby kopii zapasowych lokalnie nadmiarowego lub geograficznie nadmiarowy kopii zapasowych na utworzenie serwera, w **warstwy cenowej** okna.

> [!NOTE]
> Po utworzeniu serwera, rodzaj nadmiarowość, który ma ona geograficznie nadmiarowy vs magazyn lokalnie nadmiarowy, nie zostaną włączone.
>

Tworząc serwer za pośrednictwem portalu Azure **warstwy cenowej** okno jest, gdzie albo wybierz **magazyn lokalnie nadmiarowy** lub **geograficznie nadmiarowy** kopie zapasowe serwer. To okno jest również, którym można wybrać **okres przechowywania kopii zapasowej** — czas (w dniach) mają być przechowywane dla kopii zapasowych serwera.

   ![Cenowym — wybierz nadmiarowość kopii zapasowej](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [bazy danych serwera MySQL — Szybki Start Azure](quickstart-create-mysql-server-database-using-azure-portal.md).

Okres przechowywania kopii zapasowych można zmienić na serwerze przez następujące kroki:
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Wybierz bazy danych Azure, aby serwer MySQL. Ta akcja powoduje otwarcie **omówienie** strony.
3. Wybierz **warstwy cenowej** z menu, w obszarze **ustawienia**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** do swoich preferencji od 7 do 35 dni.
Na poniższym zrzucie ekranu ma została zwiększona do 34 dni.
![Zwiększona okres przechowywania kopii zapasowych](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij przycisk **OK** aby potwierdzić zmianę.

Okres przechowywania kopii zapasowych decyduje, jak daleko w czasie, które mogą być pobierane w momencie przywracania, ponieważ jest on oparty na kopie zapasowe dostępne. W momencie przywracania jest dalsze opisane w poniższej sekcji. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Bazy danych platformy Azure dla programu MySQL umożliwia przywrócenie serwera do punktu w czasie, a do z uprawnieniami do nowego serwera. Użyj tego nowego serwera, aby odzyskać dane lub ma punkt do nowego serwera aplikacji klienta.

Na przykład jeśli przypadkowo tabeli w południe dzisiaj, można przywrócenie na czas bezpośrednio przed południe i pobieranie Brak tabeli i danych z tej kopii nowego serwera. W momencie przywracania na serwerze poziomu, nie jest na poziomie bazy danych.

Poniższe kroki należy przywrócić działanie serwera próbki do punktu w czasie:
1. W portalu Azure wybierz bazy danych Azure, aby serwer MySQL. 

2. Na pasku narzędzi serwera **omówienie** wybierz pozycję **przywrócić**.

   ![Bazy danych platformy Azure dla przycisku MySQL — Przegląd — przywracania](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz przywracania wymagane informacje:

   ![Bazy danych platformy Azure dla programu MySQL - informacji dotyczące przywracania ](./media/howto-restore-server-portal/3-restore.png)
  - **Punkt przywracania**: Wybierz w momencie mają zostać przywrócone.
  - **Serwer docelowy**: Podaj nazwę dla nowego serwera.
  - **Lokalizacja**: nie można wybrać region. Domyślnie jest taki sam jak serwer źródłowy.
  - **Warstwa cenowa**: nie można zmienić tych parametrów, podczas przywracania punktu w czasie wykonywania. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij przycisk **OK** do przywrócenia serwera, aby wykonać przywracanie do punktu w czasie. 

5. Po zakończeniu przywracania, zlokalizuj nowy serwer, który jest tworzony w celu sprawdzenia, czy dane została przywrócona, zgodnie z oczekiwaniami.

>[!Note]
>Należy pamiętać, nowy serwer powstały w momencie przywracania ma taką samą nazwę logowania administratora serwera i wybierz hasło, które jest prawidłowa dla istniejącego serwera w punkcie czasu. Należy zmienić hasło z nowego serwera **omówienie** strony.

## <a name="geo-restore"></a>Przywracanie Geo
Skonfigurowanie serwera na potrzeby geograficznie nadmiarowy kopii zapasowych można utworzyć nowego serwera z kopii zapasowej tego istniejącego serwera. W dowolnym regionie, że baza danych Azure dla programu MySQL jest dostępny, można utworzyć nowego serwera.  

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu. Wybierz pozycję **Bazy danych** > **Azure Database for MySQL**.

   ![Opcja "Azure bazy danych MySQL"](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. W formularzu **wybierz źródło** listy rozwijanej wybierz **kopii zapasowej**. Ta akcja spowoduje załadowanie listę serwerów, które mają nadmiarowe kopie zapasowe geograficznie włączone. Wybierz jedną z tych kopii zapasowych jako źródło nowego serwera.
   ![Wybierz źródło: Kopia zapasowa i listę z magazynu geograficznie nadmiarowego kopii zapasowych](./media/howto-restore-server-portal/2-georestore.png)

3. Wypełnij pozostałej części formularza z swoje preferencje. Można wybrać dowolny **lokalizacji**. Po wybraniu lokalizacji, możesz wybrać **warstwy cenowej**. Domyślnie są wyświetlane parametry dla istniejącego serwera, który przywracasz z. Możesz kliknąć **OK** bez wprowadzania żadnych zmian, aby te ustawienia były dziedziczone. Lub zmienić **obliczeniowe generowania** (jeśli dostępna w regionie wybrano), liczba **vCores**, **okres przechowywania kopii zapasowej**, i **kopii zapasowej Opcja nadmiarowość**. Zmiana **warstwy cenowej** (podstawowe, ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci) lub **magazynu** rozmiar podczas przywracania nie jest obsługiwany.

>[!Note]
>Nowy serwer tworzone przez przywrócenie geograficznie ma taką samą nazwę logowania administratora serwera i hasło, które jest prawidłowa dla istniejącego serwera w czasie przywracania została zainicjowana. Hasło można zmienić z nowego serwera **omówienie** strony.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o usłudze [kopii zapasowych](concepts-backup.md).
- Dowiedz się więcej o [ciągłość prowadzenia działalności biznesowej](concepts-business-continuity.md) opcje.
