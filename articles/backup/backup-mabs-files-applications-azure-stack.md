---
title: Łącze kopie zapasowe plików na maszynach wirtualnych usługi Azure Stack
description: Użyj usługi Azure Backup, aby wykonać kopię zapasową i odzyskać pliki i aplikacje usługi Azure Stack w środowisku usługi Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172332"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Łącze kopie zapasowe plików i aplikacji w usłudze Azure Stack

Usługi Azure Backup można używać do ochrony (lub tworzenia kopii zapasowych) plików i aplikacji w usłudze Azure Stack. Aby utworzyć kopię zapasową plików i aplikacji, należy zainstalować serwer kopii zapasowej platformy Microsoft Azure jako maszynę wirtualną uruchomiającą w usłudze Azure Stack. Pliki można chronić na dowolnym serwerze usługi Azure Stack w tej samej sieci wirtualnej. Po zainstalowaniu usługi Azure Backup Server dodaj dyski platformy Azure, aby zwiększyć magazyn lokalny dostępny dla krótkoterminowych danych kopii zapasowej. Usługa Azure Backup Server używa magazynu platformy Azure do długoterminowego przechowywania.

> [!NOTE]
> Chociaż usługa Azure Backup Server and System Center Data Protection Manager (DPM) są podobne, program DPM nie jest obsługiwany do użytku z usługą Azure Stack.
>

W tym artykule nie obejmuje instalowania usługi Azure Backup Server w środowisku usługi Azure Stack. Aby zainstalować usługę Azure Backup Server w usłudze Azure Stack, zobacz artykuł [Instalowanie serwera kopii zapasowych platformy Azure](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Łącze kopie zapasowe plików i folderów na maszynach wirtualnych usługi Azure Stack na platformie Azure

Aby skonfigurować usługę Azure Backup Server do ochrony plików na maszynach wirtualnych usługi Azure Stack, otwórz konsolę serwera kopii zapasowych platformy Azure. Użyjesz konsoli do konfigurowania grup ochrony i ochrony danych na maszynach wirtualnych.

1. W konsoli serwera kopii zapasowych platformy Azure kliknij pozycję **Ochrona,** a na pasku narzędzi kliknij pozycję **Nowy,** aby otworzyć **kreatora Tworzenie nowej grupy ochrony.**

   ![Konfigurowanie ochrony w konsoli serwera kopii zapasowych platformy Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Otwarcie kreatora może potrwać kilka sekund. Po otwarciu kreatora kliknij przycisk **Dalej,** aby przejść do ekranu **Wybierz typ grupy ochrony.**

   ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery** i kliknij przycisk **Dalej**.

    ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Zostanie otwarty ekran **Wybierz członków grupy.**

    ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na ekranie Wybierz członków **+** **grupy** kliknij, aby rozwinąć listę podelementów. W przypadku wszystkich elementów, które chcesz chronić, zaznacz to pole wyboru. Po wybraniu wszystkich elementów kliknij przycisk **Dalej**.

    ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Firma Microsoft zaleca umieszczenie wszystkich danych, które będą współużytkować zasady ochrony, w jednej grupie ochrony. Aby uzyskać pełne informacje dotyczące planowania i wdrażania grup ochrony, zobacz artykuł Programu DPM programu System Center, [Wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na ekranie **Wybierz metodę ochrony danych** wpisz nazwę grupy ochrony. Zaznacz pole wyboru, aby **chronić krótkoterminowo za pomocą:** i **chcę ochrony online**. Kliknij przycisk **alej**.

    ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Aby wybrać **opcję Chcę ochrony online,** należy najpierw wybrać **chcę krótkoterminowej ochrony za pomocą:** Dysk. Usługa Azure Backup Server nie chroni do taśmy, więc dysk jest jedynym wyborem dla ochrony krótkoterminowej.

5. Na ekranie **Określanie celów krótkoterminowych** wybierz, jak długo należy przechowywać punkty odzyskiwania zapisane na dysku i kiedy zapisywać przyrostowe kopie zapasowe. Kliknij przycisk **alej**.

    > [!IMPORTANT]
    > Nie **należy** przechowywać danych odzyskiwania operacyjnego (kopii zapasowej) na dyskach dołączonych do serwera kopii zapasowych platformy Azure przez okres dłużej niż pięć dni.
    >

    ![Otworzy się kreator nowych grup ochrony](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Zamiast wybierać interwał dla przyrostowych kopii zapasowych, aby uruchomić ekspresową pełną kopię zapasową tuż przed każdym **zaplanowanym punktem**odzyskiwania, kliknij przycisk Tuż przed punktem odzyskiwania . Jeśli chronisz obciążenia aplikacji, usługa Azure Backup Server tworzy punkty odzyskiwania zgodnie z harmonogramem częstotliwości synchronizacji (pod warunkiem, że aplikacja obsługuje przyrostowe kopie zapasowe). Jeśli aplikacja nie obsługuje przyrostowych kopii zapasowych, usługa Azure Backup Server uruchamia ekspresową pełną kopię zapasową.

    W polu **Punkty odzyskiwania plików**określ, kiedy mają być utworzone punkty odzyskiwania. Kliknij **przycisk Modyfikuj,** aby ustawić godziny i dni tygodnia podczas tworzenia punktów odzyskiwania.

6. Na ekranie **Przejrzyj alokację dysku** przejrzyj miejsce na dysku puli magazynów przydzielone dla grupy ochrony.

    **Całkowity rozmiar danych** to rozmiar danych, które chcesz utworzyć kopię zapasową, a miejsce na dysku, które **ma zostać zainicjowane** na serwerze Azure Backup Server, jest zalecanym miejscem dla grupy ochrony. Usługa Azure Backup Server wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Opcje wielkości kopii zapasowej można jednak edytować w obszarze Szczegóły przydziału dysku. W przypadku obciążeń wybierz z menu rozwijanego preferowany magazyn. Edycja zmienia wartości pozycji Całkowita ilość miejsca dla magazynu i Wolne miejsce w magazynie w okienku Dostępny magazyn dyskowy. Niedoprowizowana przestrzeń to ilość magazynu, którą serwer Azure Backup Server sugeruje dodanie do woluminu, aby bezproblemowo kontynuować tworzenie kopii zapasowych w przyszłości.

7. W **obszarze Wybierz metodę tworzenia repliki**wybierz sposób obsługi początkowej pełnej replikacji danych. Jeśli zdecydujesz się replikować za pośrednictwem sieci, platforma Azure zaleca wybranie godziny poza szczytem. W przypadku dużych ilości danych lub mniej niż optymalne warunki sieciowe należy rozważyć replikację danych przy użyciu nośników wymiennych.

8. W polu **Wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Włącz sprawdzanie spójności, aby uruchomić tylko wtedy, gdy replikacja danych staje się niespójna lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, uruchom ręczne sprawdzanie w dowolnym momencie przez:
    * W obszarze **Ochrona** konsoli serwera kopii zapasowych platformy Azure kliknij prawym przyciskiem myszy grupę ochrony i wybierz polecenie **Sprawdź spójność**.

9. Jeśli zdecydujesz się wykonać kopii zapasowej na platformie Azure, na stronie **Określ dane ochrony online** upewnij się, że są wybrane obciążenia, których chcesz wykonać na platformie Azure.

10. W **obszarze Określ harmonogram tworzenia kopii zapasowych online**określ, kiedy powinny wystąpić przyrostowe kopie zapasowe na platformie Azure.

    Wykonywanie kopii zapasowych można zaplanować na każdy dzień/tydzień/miesiąc/rok, natomiast godziny/daty pozwalają ustalić konkretny czas uruchomienia wykonywania kopii zapasowych. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Za każdym razem, gdy uruchamia się zadanie tworzenia kopii zapasowej, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii kopii zapasowych danych przechowywanych na dysku serwera kopii zapasowej platformy Azure.

11. W **obszarze Określ zasady przechowywania online**określ sposób przechowywania punktów odzyskiwania utworzonych na podstawie kopii zapasowych dziennych/tygodniowych/miesięcznych/5-ych na platformie Azure.

12. W **obszarze Wybierz replikację online**określ sposób początkowej pełnej replikacji danych.

13. W **obszarze Podsumowanie**przejrzyj ustawienia. Po **kliknięciu przycisku Utwórz grupę**nastąpi początkowa replikacja danych. Po zakończeniu replikacji danych na stronie **Stan** stan grupy ochrony jest wyświetlany jako **OK**. Początkowe zadanie tworzenia kopii zapasowej odbywa się zgodnie z ustawieniami grupy ochrony.

## <a name="recover-file-data"></a>Odzyskiwanie danych pliku

Użyj konsoli usługi Azure Backup Server, aby odzyskać dane na maszynie wirtualnej.

1. W konsoli serwera kopii zapasowych platformy Azure na pasku nawigacyjnym kliknij pozycję **Odzyskiwanie** i wyszukaj dane, które chcesz odzyskać. Wybierz dane w okienku wyników.

2. W kalendarzu w sekcji punkty odzyskiwania daty pogrubione wskazują, że punkty odzyskiwania są dostępne. Wybierz datę odzyskania.

3. W okienku **elementu do odzyskania** wybierz element, który chcesz odzyskać.

4. W okienku **Akcje** kliknij polecenie **Odzyskaj**, aby otworzyć Kreatora odzyskiwania.

5. Dane można odzyskać w następujący sposób:

    * **Odzyskaj do oryginalnej lokalizacji** — jeśli komputer kliencki jest połączony za pośrednictwem sieci VPN, ta opcja nie działa. Zamiast tego użyj alternatywnej lokalizacji, a następnie skopiuj dane z tej lokalizacji.
    * **Odzyskiwanie do alternatywnej lokalizacji**

6. Określ opcje odzyskiwania:

    * W obszarze **Istniejące zachowanie odzyskiwania wersji**wybierz pozycję **Utwórz kopię,** **Pomiń**lub **Zastąrz .** Zastąpienie jest dostępne tylko podczas odzyskiwania do oryginalnej lokalizacji.
    * W obszarze **Przywracanie zabezpieczeń**wybierz pozycję **Zastosuj ustawienia komputera docelowego** lub **Zastosuj ustawienia zabezpieczeń wersji punktu odzyskiwania**.
    * W przypadku **ograniczania użycia przepustowości sieci**kliknij pozycję **Modyfikuj,** aby włączyć ograniczanie przepustowości sieci.
    * **Powiadomienie** Kliknij **pozycję Wyślij wiadomość e-mail po zakończeniu odzyskiwania**i określ adresatów, którzy otrzymają powiadomienie. Kolejne adresy e-mail oddziel przecinkami.
    * Po dokonaniu wyboru kliknij przycisk **Dalej**

7. Przejrzyj ustawienia odzyskiwania i kliknij polecenie **Odzyskaj**.

    >[!Note]
    >Gdy zadanie odzyskiwania jest w toku, wszystkie zadania synchronizacji dla wybranych elementów odzyskiwania są anulowane.

Jeśli używasz nowoczesnego magazynu kopii zapasowych (MBS), odzyskiwanie użytkownika końcowego serwera plików (EUR) nie jest obsługiwane. Serwer plików EUR ma zależność od usługi kopiowania woluminów w tle (VSS), której nie używa nowoczesny magazyn kopii zapasowych. Jeśli eur jest włączony, należy wykonać następujące kroki w celu odzyskania danych:

1. Przejdź do chronionych plików, a następnie kliknij prawym przyciskiem myszy nazwę pliku i wybierz polecenie **Właściwości**.

2. W menu **Właściwości** kliknij polecenie **Poprzednie wersje** i wybierz wersję, którą chcesz odzyskać.

## <a name="view-azure-backup-server-with-a-vault"></a>Wyświetlanie serwera kopii zapasowych platformy Azure za pomocą przechowalni

Aby wyświetlić jednostki usługi Azure Backup Server w witrynie Azure portal, można wykonać następujące kroki:

1. Otwórz magazyn usług odzyskiwania.
2. Kliknij pozycję Infrastruktura kopii zapasowych.
3. Wyświetl serwery zarządzania kopiami zapasowymi.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat korzystania z usługi Azure Backup Server w celu ochrony innych obciążeń, zobacz jeden z następujących artykułów:

* [Dziele zapasowe farmy programu SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Tworzenie kopii zapasowej serwera SQL](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
