---
title: Tworzenie kopii zapasowych i przywracanie — witryna Azure portal — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób przywracania serwera w usłudze Azure Database dla postgreSQL — pojedynczy serwer przy użyciu witryny Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765634"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Jak wykonać kopię zapasową i przywrócić serwer w usłudze Azure Database for PostgreSQL — pojedynczy serwer przy użyciu witryny Azure portal

## <a name="backup-happens-automatically"></a>Tworzenie kopii zapasowej odbywa się automatycznie
Usługa Azure Database for PostgreSQL serwerów są okresowo archiwizowane, aby włączyć funkcje przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie, na nowym serwerze.

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Wybór między konfigurowaniem serwera dla lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera, w oknie **Warstwa cenowa.**

> [!NOTE]
> Po utworzeniu serwera nie można przełączyć tego rodzaju nadmiarowości, która jest nadmiarowa geograficznie i lokalnie nadmiarowa.
>

Podczas tworzenia serwera za pośrednictwem witryny Azure portal, w oknie **warstwy cenowej** jest, gdzie można wybrać **lokalnie nadmiarowe** lub **geograficznie nadmiarowe** kopie zapasowe dla serwera. W tym oknie jest również wybranie **okresu przechowywania kopii zapasowych** — jak długo (w liczbie dni) mają być przechowywane kopie zapasowe serwera.

   ![Warstwa cenowa — wybierz nadmiarowość kopii zapasowych](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [usługę Azure Database for PostgreSQL server Quickstart](quickstart-create-server-database-portal.md).

Okres przechowywania kopii zapasowej serwera można zmienić za pomocą następujących kroków:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
2. Wybierz serwer usługi Azure Database for PostgreSQL. Ta akcja powoduje otwarcie strony **Przegląd.**
3. Wybierz **warstwę cenową** z menu w obszarze **USTAWIENIA**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** na preferencje między 7 a 35 dni.
Na poniższym zrzucie ekranu został on zwiększony do 34 dni.
![Wydłużył się okres przechowywania kopii zapasowych](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij **przycisk OK,** aby potwierdzić zmianę.

Okres przechowywania kopii zapasowej określa, jak daleko wstecz w czasie można pobrać przywracanie punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie w czasie jest opisane w dalszej części poniżej. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Usługa Azure Database for PostgreSQL umożliwia przywrócenie serwera do punktu w czasie i do nowej kopii serwera. Za pomocą tego nowego serwera można odzyskać dane lub wskazać, że aplikacje klienckie wskażą ten nowy serwer.

Na przykład jeśli tabela została przypadkowo upuszczona w południe dzisiaj, można przywrócić do czasu tuż przed południem i pobrać brakujące tabeli i dane z tej nowej kopii serwera. Przywracanie w czasie znajduje się na poziomie serwera, a nie na poziomie bazy danych.

Następujące kroki przywracają przykładowy serwer do punktu w czasie:
1. W witrynie Azure portal wybierz usługę Azure Database dla serwera PostgreSQL. 

2. Na pasku narzędzi strony **Przegląd** serwera wybierz pozycję **Przywróć**.

   ![Usługa Azure Database for PostgreSQL — omówienie — przycisk Przywróć](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz Przywracanie wymaganymi informacjami:

   ![Usługa Azure Database for PostgreSQL — informacje o przywracanie](./media/howto-restore-server-portal/3-restore.png)
   - **Punkt przywracania:** Wybierz punkt w czasie, do którego chcesz przywrócić.
   - **Serwer docelowy**: Podaj nazwę nowego serwera.
   - **Lokalizacja:** Nie można wybrać regionu. Domyślnie jest taki sam jak serwer źródłowy.
   - **Warstwa cenowa:** Nie można zmienić tych parametrów podczas przywracania punktu w czasie. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij **przycisk OK,** aby przywrócić serwer, aby przywrócić go do punktu w czasie. 

5. Po zakończeniu przywracania zlokalizuj nowy serwer, który zostanie utworzony w celu sprawdzenia, czy dane zostały przywrócone zgodnie z oczekiwaniami.

Nowy serwer utworzony przez przywracanie punktu w czasie ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w wybranym punkcie w czasie. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma reguł zapory ani punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera.


## <a name="geo-restore"></a>Przywracanie geograficzne

Jeśli serwer został skonfigurowany do geograficznie nadmiarowych kopii zapasowych, można utworzyć nowy serwer z kopii zapasowej tego istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który jest dostępny w usłudze Azure Database for PostgreSQL.  

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu. Wybierz **bazy danych** > **usługi Azure Database dla postgreSQL**.

   ![Opcja „Azure Database for PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. W menu rozwijanym **Wybierz źródło** formularza wybierz pozycję **Kopia zapasowa**. Ta akcja ładuje listę serwerów, na których włączono geograficznie nadmiarowe kopie zapasowe. Wybierz jedną z tych kopii zapasowych, aby być źródłem nowego serwera.
   ![Wybierz źródło: Kopia zapasowa i lista geograficznie nadmiarowych kopii zapasowych](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Po utworzeniu serwera może nie być natychmiast dostępny do przywracania geograficznego. Może upłynąć kilka godzin, aby wypełnić niezbędne metadane.
   >

3. Wypełnij resztę formularza swoimi preferencjami. Można wybrać dowolną **lokalizację**. Po wybraniu lokalizacji można wybrać **warstwę cenową**. Domyślnie wyświetlane są parametry istniejącego serwera, z którego przywracasz. Możesz kliknąć przycisk **OK** bez wprowadzania zmian, aby odziedziczyć te ustawienia. Można też zmienić **generowanie obliczeń** (jeśli jest dostępne w wybranym regionie), liczbę **kopnów wirtualnych,** **okres przechowywania kopii zapasowych**i **opcję nadmiarowości kopii zapasowych.** Zmiana **warstwy cenowej** (podstawowy, ogólnego przeznaczenia lub zoptymalizowany pod kątem pamięci) lub rozmiar **magazynu** podczas przywracania nie jest obsługiwana.


Nowy serwer utworzony przez przywracanie geograficzne ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w momencie inicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma reguł zapory ani punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych](concepts-backup.md)usługi .
- Dowiedz się więcej o opcjach [ciągłości biznesowej.](concepts-business-continuity.md)
