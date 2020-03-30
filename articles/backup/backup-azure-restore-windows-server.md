---
title: Przywracanie danych na platformie Azure na serwerze windows
description: W tym artykule dowiesz się, jak przywrócić dane przechowywane na platformie Azure na serwerze Windows lub komputerze z systemem Windows za pomocą agenta usług microsoft azure recovery services (MARS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 25ca8eecaeb615f071340188a23fae7978ddb75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409817"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Przywracanie plików do systemu Windows przy użyciu modelu wdrażania usługi Azure Resource Manager

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, należy użyć Kreatora odzyskiwania danych w agencie usług odzyskiwania platformy Microsoft Azure (MARS). Możesz:

* Przywróć dane na tym samym komputerze, z którego pobrano kopie zapasowe.
* Przywracanie danych do alternatywnej maszyny.

Użyj funkcji przywracania błyskawicznego, aby zainstalować zapisywalną migawkę punktu odzyskiwania jako wolumin odzyskiwania. Następnie można eksplorować wolumin odzyskiwania i kopiować pliki na komputer lokalny, a tym samym selektywnie przywracać pliki.

> [!NOTE]
> [Aktualizacja usługi Azure Backup ze stycznia 2017 r.](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) jest wymagana, jeśli chcesz przywrócić dane za pomocą funkcji natychmiastowego przywracania. Ponadto dane kopii zapasowej muszą być chronione w magazynach w ustawieniach regionalnych wymienionych w artykule pomocy technicznej. Aby uzyskać najnowszą listę ustawień regionalnych obsługujących natychmiastowe przywracanie, zapoznaj się z [aktualizacją usługi Azure Backup ze stycznia 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) r.
>

Użyj natychmiastowego przywracania z magazynami usług odzyskiwania w witrynie Azure portal. Jeśli dane zostały zapisane w magazynach kopii zapasowych, zostały one przekonwertowane na magazyny usług odzyskiwania. Jeśli chcesz użyć narzędzia Natychmiastowe przywracanie, pobierz aktualizację MARS i postępuj zgodnie z procedurami, które wspominają o przywracaniu błyskawicznym.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Odzyskiwanie danych na tym samym komputerze za pomocą funkcji natychmiastowego przywracania

Jeśli przypadkowo usunięto plik i chcesz przywrócić go na tym samym komputerze (z którego została pobrana kopia zapasowa), poniższe kroki pomogą ci odzyskać dane.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie została zainstalowana przystawka, wyszukaj komputer lub serwer w **poszukiwaniu kopii zapasowej platformy Microsoft Azure**.

    Aplikacja klasyczna powinna pojawić się w wynikach wyszukiwania.

2. Wybierz **pozycję Odzyskaj dane,** aby uruchomić kreatora.

    ![Zrzut ekranu przedstawiający usługę Azure Backup z wyróżnioną pojednawką pozycję Odzyskaj dane](./media/backup-azure-restore-windows-server/recover.png)

3. Na stronie **Wprowadzenie,** aby przywrócić dane na tym samym serwerze lub komputerze, wybierz pozycję **Ten serwer`<server name>`( )** > **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Wprowadzenie do kreatora odzyskiwania danych](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **Pojedyncze pliki i foldery** > **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Kreatora odzyskiwania danych Wybierz tryb odzyskiwania](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Opcja przywracania poszczególnych plików i folderów wymaga programu .NET Framework 4.5.2 lub nowszego. Jeśli opcja **Pojedyncze pliki i foldery** nie jest widoczna, należy uaktualnić program .NET Framework do wersji 4.5.2 lub nowszej i spróbować ponownie.
 
   > [!TIP]
   > Opcja **Poszczególne pliki i foldery** umożliwia szybki dostęp do danych punktu odzyskiwania. Nadaje się do odzyskiwania pojedynczych plików, o rozmiarach o łącznej wartości nie większej niż 80 GB i oferuje szybkość transferu lub kopiowania do 6 MB/s podczas odzyskiwania. Opcja **Wolumin** odzyskuje wszystkie kopie zapasowe danych w określonym woluminie. Ta opcja zapewnia większą szybkość transferu (do 60 MB/s), co jest idealne do odzyskiwania dużych ilości danych lub całych woluminów.

5. Na stronie **Wybierz wolumin i datę** wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty dostępnych jest wiele punktów odzyskiwania, wybierz określony punkt odzyskiwania z menu rozwijanego **Czas.**

    ![Zrzut ekranu przedstawiający stronę Wybieranie woluminu i daty kreatora odzyskiwania danych](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po wybraniu punktu odzyskiwania do przywrócenia wybierz pozycję **Zamontuj**.

    Usługa Azure Backup umożliwia zainstalowanie lokalnego punktu odzyskiwania i używa go jako woluminu odzyskiwania.

7. Na stronie **Przeglądaj i odzyskiwać pliki** wybierz pozycję **Przeglądaj,** aby otworzyć Eksploratora Windows, a następnie znajdź żądane pliki i foldery.

    ![Screenshot of Recover Data Wizard Browse and Recover Files page](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. W Eksploratorze Windows skopiuj pliki i foldery, które chcesz przywrócić, i wklej je do dowolnej lokalizacji lokalnej na serwerze lub komputerze. Można otworzyć lub przesłać strumieniowo pliki bezpośrednio z woluminu odzyskiwania i sprawdzić, czy są odzyskiwanie poprawnych wersji.

    ![Zrzut ekranu przedstawiający Eksploratora Windows z wyróżnioną połączeń Kopiowania](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po zakończeniu wybierz pozycję **Odinstaluj** . **Unmount** Następnie wybierz pozycję **Tak,** aby potwierdzić, że chcesz odinstalować wolumin.

    ![Screenshot of Recover Data Wizard Browse and Recover Files page](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz **opcji Odłącz,** wolumin odzyskiwania pozostanie zamontowany przez 6 godzin od momentu zamontowania. Jednak czas instalacji jest wydłużony do maksymalnie 24 godzin w przypadku trwającej kopii pliku. Żadne operacje tworzenia kopii zapasowej nie będą uruchamiane podczas montażu woluminu. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowywaniu woluminu odzyskiwania.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Przywracanie danych na komputerze alternatywnym za pomocą funkcji natychmiastowego przywracania

Jeśli cały serwer zostanie utracony, nadal można odzyskać dane z usługi Azure Backup na innym komputerze. Poniższe kroki ilustrują przepływ pracy.

Te kroki obejmują następującą terminologię:

* *Maszyna źródłona* — oryginalna maszyna, z której została pobrana kopia zapasowa i która jest obecnie niedostępna.
* *Maszyna docelowa* — maszyna, do której dane są odzyskiwane.
* *Przykładowy magazyn* — magazyn usług odzyskiwania, do którego jest rejestrowana maszyna źródłowa i maszyna docelowa.

> [!NOTE]
> Nie można przywrócić kopii zapasowych na komputerze docelowym z uruchomioną wcześniejszą wersją systemu operacyjnego. Na przykład kopię zapasową pobraną z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 7 (lub nowszym). Kopii zapasowej pobranej z komputera z systemem Windows 8 nie można przywrócić na komputerze z systemem Windows 7.
>
>

1. Otwórz przystawkę **Kopia zapasowa platformy Microsoft Azure** na komputerze docelowym.

2. Upewnij się, że komputer docelowy i komputer źródłowy są zarejestrowane w tym samym magazynie usług odzyskiwania.

3. Wybierz **pozycję Odzyskaj dane,** aby otworzyć **Kreatora odzyskiwania danych**.

    ![Zrzut ekranu przedstawiający usługę Azure Backup z wyróżnioną pojednawką pozycję Odzyskaj dane](./media/backup-azure-restore-windows-server/recover.png)

4. Na stronie **Wprowadzenie** wybierz pozycję **Inny serwer**.

    ![Zrzut ekranu przedstawiający stronę Wprowadzenie do kreatora odzyskiwania danych](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń przechowalni odpowiadający przechowalni próbkowania, a następnie wybierz przycisk **Dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), pobierz nowy plik poświadczeń magazynu z przykładowego magazynu w witrynie Azure portal. Po podasz prawidłowe poświadczenia magazynu pojawi się nazwa odpowiedniego magazynu kopii zapasowych.

6. Na stronie **Wybierz serwer kopii zapasowej** wybierz komputer źródłowy z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz **przycisk Dalej**.

    ![Zrzut ekranu przedstawiający stronę Kreatora odzyskiwania danych Wybierz serwer kopii zapasowej](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **Pojedyncze pliki i foldery** > **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Kreatora odzyskiwania danych Wybierz tryb odzyskiwania](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na stronie **Wybierz wolumin i datę** wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty dostępnych jest wiele punktów odzyskiwania, wybierz określony punkt odzyskiwania z menu rozwijanego **Czas.**

    ![Zrzut ekranu przedstawiający stronę Wybieranie woluminu i daty kreatora odzyskiwania danych](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Wybierz **pozycję Zamontuj,** aby lokalnie zainstalować punkt odzyskiwania jako wolumin odzyskiwania na komputerze docelowym.

10. Na stronie **Przeglądaj i odzyskiwać pliki** wybierz pozycję **Przeglądaj,** aby otworzyć Eksploratora Windows, a następnie znajdź żądane pliki i foldery.

    ![Zrzut ekranu przedstawiający stronę Kreatora odzyskiwania danych Przeglądaj i odzyskiwać pliki](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksploratorze Windows skopiuj pliki i foldery z woluminu odzyskiwania i wklej je do lokalizacji komputera docelowego. Można otworzyć lub przesłać strumieniowo pliki bezpośrednio z woluminu odzyskiwania i sprawdzić, czy poprawne wersje są odzyskiwane.

    ![Zrzut ekranu przedstawiający Eksploratora Windows z wyróżnioną połączeń Kopiowania](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu wybierz pozycję **Odinstaluj** . **Unmount** Następnie wybierz pozycję **Tak,** aby potwierdzić, że chcesz odinstalować wolumin.

    ![Zrzut ekranu przedstawiający stronę Kreatora odzyskiwania danych Przeglądaj i odzyskiwać pliki](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz **opcji Odłącz,** wolumin odzyskiwania pozostanie zamontowany przez 6 godzin od momentu zamontowania. Jednak czas instalacji jest wydłużony do maksymalnie 24 godzin w przypadku trwającej kopii pliku. Żadne operacje tworzenia kopii zapasowej nie będą uruchamiane podczas montażu woluminu. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowywaniu woluminu odzyskiwania.
    >

## <a name="next-steps"></a>Następne kroki

* Po odzyskaniu plików i folderów można [zarządzać kopiami zapasowymi](backup-azure-manage-windows-server.md).

* Znajdź [Typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md).
