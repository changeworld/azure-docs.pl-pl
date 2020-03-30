---
title: Tworzenie kopii zapasowych i przywracanie — witryna Azure portal — usługa Azure Database for MySQL
description: W tym artykule opisano sposób przywracania serwera w usłudze Azure Database dla mysql przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 619dc05d709f41941d16764bf32b49a0d2a11958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372986"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak wykonać kopię zapasową i przywrócić serwer w usłudze Azure Database dla MySQL przy użyciu witryny Azure portal

## <a name="backup-happens-automatically"></a>Tworzenie kopii zapasowej odbywa się automatycznie
Usługa Azure Database for MySQL serwerów są okresowo archiwizowane, aby włączyć funkcje przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie, na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera i bazy danych MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Wybór między konfigurowaniem serwera dla lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera, w oknie **Warstwa cenowa.**

> [!NOTE]
> Po utworzeniu serwera nie można przełączyć tego rodzaju nadmiarowości, która jest nadmiarowa geograficznie i lokalnie nadmiarowa.
>

Podczas tworzenia serwera za pośrednictwem witryny Azure portal, w oknie **warstwy cenowej** jest, gdzie można wybrać **lokalnie nadmiarowe** lub **geograficznie nadmiarowe** kopie zapasowe dla serwera. W tym oknie jest również wybranie **okresu przechowywania kopii zapasowych** — jak długo (w liczbie dni) mają być przechowywane kopie zapasowe serwera.

   ![Warstwa cenowa — wybierz nadmiarowość kopii zapasowych](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [szybki start serwera Usługi Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

Okres przechowywania kopii zapasowej można zmienić na serwerze, wykonując następujące kroki:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
2. Wybierz swoją usługę Azure Database dla serwera MySQL. Ta akcja powoduje otwarcie strony **Przegląd.**
3. Wybierz **warstwę cenową** z menu w obszarze **USTAWIENIA**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** na preferencje między 7 a 35 dni.
Na poniższym zrzucie ekranu został on zwiększony do 34 dni.
![Wydłużył się okres przechowywania kopii zapasowych](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij **przycisk OK,** aby potwierdzić zmianę.

Okres przechowywania kopii zapasowej określa, jak daleko wstecz w czasie można pobrać przywracanie punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie w czasie jest opisane w dalszej części poniżej. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Usługa Azure Database for MySQL umożliwia przywrócenie serwera do punktu w czasie i do nowej kopii serwera. Za pomocą tego nowego serwera można odzyskać dane lub wskazać, że aplikacje klienckie wskażą ten nowy serwer.

Na przykład jeśli tabela została przypadkowo upuszczona w południe dzisiaj, można przywrócić do czasu tuż przed południem i pobrać brakujące tabeli i dane z tej nowej kopii serwera. Przywracanie w czasie znajduje się na poziomie serwera, a nie na poziomie bazy danych.

Następujące kroki przywracają przykładowy serwer do punktu w czasie:
1. W witrynie Azure portal wybierz swoją bazę danych platformy Azure dla serwera MySQL. 

2. Na pasku narzędzi strony **Przegląd** serwera wybierz pozycję **Przywróć**.

   ![Usługa Azure Database for MySQL — omówienie — przycisk Przywróć](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz Przywracanie wymaganymi informacjami:

   ![Usługa Azure Database for MySQL — informacje o przywracanie](./media/howto-restore-server-portal/3-restore.png)
   - **Punkt przywracania:** Wybierz punkt w czasie, do którego chcesz przywrócić.
   - **Serwer docelowy**: Podaj nazwę nowego serwera.
   - **Lokalizacja:** Nie można wybrać regionu. Domyślnie jest taki sam jak serwer źródłowy.
   - **Warstwa cenowa:** Nie można zmienić tych parametrów podczas przywracania punktu w czasie. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij **przycisk OK,** aby przywrócić serwer, aby przywrócić go do punktu w czasie. 

5. Po zakończeniu przywracania zlokalizuj nowy serwer, który zostanie utworzony w celu sprawdzenia, czy dane zostały przywrócone zgodnie z oczekiwaniami.

Nowy serwer utworzony przez przywracanie punktu w czasie ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w wybranym punkcie w czasie. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera są przywracane.

## <a name="geo-restore"></a>Przywracanie geograficzne
Jeśli serwer został skonfigurowany do geograficznie nadmiarowych kopii zapasowych, można utworzyć nowy serwer z kopii zapasowej tego istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który jest dostępny w usłudze Azure Database for MySQL.  

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu. Wybierz **baz danych** > **usługi Azure Database dla mysql**.

   ![Opcja "Usługa Azure Database for MySQL"](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. W menu rozwijanym **Wybierz źródło** formularza wybierz pozycję **Kopia zapasowa**. Ta akcja ładuje listę serwerów, na których włączono geograficznie nadmiarowe kopie zapasowe. Wybierz jedną z tych kopii zapasowych, aby być źródłem nowego serwera.
   ![Wybierz źródło: Kopia zapasowa i lista geograficznie nadmiarowych kopii zapasowych](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Po utworzeniu serwera może nie być natychmiast dostępny do przywracania geograficznego. Może upłynąć kilka godzin, aby wypełnić niezbędne metadane.
   >

3. Wypełnij resztę formularza swoimi preferencjami. Można wybrać dowolną **lokalizację**. Po wybraniu lokalizacji można wybrać **warstwę cenową**. Domyślnie wyświetlane są parametry istniejącego serwera, z którego przywracasz. Możesz kliknąć przycisk **OK** bez wprowadzania zmian, aby odziedziczyć te ustawienia. Można też zmienić **generowanie obliczeń** (jeśli jest dostępne w wybranym regionie), liczbę **kopnów wirtualnych,** **okres przechowywania kopii zapasowych**i **opcję nadmiarowości kopii zapasowych.** Zmiana **warstwy cenowej** (podstawowy, ogólnego przeznaczenia lub zoptymalizowany pod kątem pamięci) lub rozmiar **magazynu** podczas przywracania nie jest obsługiwana.

Nowy serwer utworzony przez przywracanie geograficzne ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w momencie inicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera są przywracane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych](concepts-backup.md) usługi
- Dowiedz się więcej o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)
