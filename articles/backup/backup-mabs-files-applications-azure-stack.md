---
title: Tworzenie kopii zapasowej plików w usłudze Azure Stack maszyn wirtualnych
description: Usługa Azure Backup umożliwia tworzenie kopii zapasowej i odzyskiwanie plików usługi Azure Stack i aplikacji do środowiska usługi Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848762"
---
# <a name="back-up-files-on-azure-stack"></a>Tworzenie kopii zapasowej plików w usłudze Azure Stack
Usługi Azure Backup umożliwia ochronę (lub utworzyć kopię zapasową) plików i aplikacji w usłudze Azure Stack. Aby utworzyć kopię zapasową plików i aplikacji, zainstaluj serwer usługi Microsoft Azure Backup jako maszynę wirtualną działającą w usłudze Azure Stack. Można chronić pliki na każdym serwerze usługi Azure Stack w tej samej sieci wirtualnej. Po zainstalowaniu serwera usługi Azure Backup, Dodaj dysków platformy Azure w celu zwiększenia magazynu lokalnego, która jest dostępna dla krótkoterminowej kopii zapasowej danych. Usługa Azure Backup Server używa usługi Azure storage do długoterminowego przechowywania danych.

> [!NOTE]
> Chociaż usługi Azure Backup Server i System Center Data Protection Manager (DPM) są podobne, program DPM nie jest obsługiwane do użytku z usługą Azure Stack.
>

Ten artykuł nie obejmuje instalacji usługi Azure Backup Server w środowisku usługi Azure Stack. Aby zainstalować usługi Azure Backup Server w usłudze Azure Stack, zapoznaj się z artykułem [instalowania serwera usługi Azure Backup](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Tworzenie kopii zapasowych plików i folderów na maszynach wirtualnych Azure Stack na platformie Azure

Aby skonfigurować usługi Azure Backup Server, aby chronić pliki na maszynach wirtualnych usługi Azure Stack, otwórz konsolę serwera usługi Azure Backup. Konfigurowanie grup ochrony i ochrony danych na maszynach wirtualnych, użyjemy konsoli.

1. Kliknij w konsoli usługi Azure Backup Server **ochrony** i na pasku narzędzi kliknij **New** otworzyć **tworzenia nowej grupy ochrony** kreatora.

   ![Konfigurowanie ochrony w konsoli usługi Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Może potrwać kilka sekund otworzyć kreatora. Po otwarciu kreatora kliknij przycisk **dalej** celu przechodzenia do **wybierz typ grupy ochrony** ekranu.

   ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na **wybierz typ grupy ochrony** ekranu, wybierz **serwerów** i kliknij przycisk **dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **Wybierz członków grupy** zostanie otwarty ekran. 

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. W **Wybierz członków grupy** ekranu, kliknij przycisk **+** rozwiń listę elementów podrzędnych. Dla wszystkich elementów, które mają być chronione zaznacz pole wyboru. Po wybraniu wszystkich elementów kliknij **dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Firma Microsoft zaleca umieszczenie wszystkich danych, które współużytkują zasady ochrony, w grupie ochrony jeden. Aby uzyskać pełne informacje o planowaniu i wdrażaniu grupy ochrony, zobacz artykuł programu System Center DPM [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. W **wybierz metodę ochrony danych** ekranu, wpisz nazwę grupy ochrony. Zaznacz pole wyboru **chcę krótkoterminową ochronę za pomocą:** i **chcę uzyskać ochronę online**. Kliknij przycisk **Dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Aby wybrać **chcę uzyskać ochronę online**, musisz najpierw wybrać **chcę krótkoterminową ochronę za pomocą:** Dysk. Usługa Azure Backup Server nie ochrona na taśmie, dysk jest tylko opcja w przypadku ochrony krótkoterminowej.

5. W **Określ cele krótkoterminowe** ekranu, czas przechowywania punktów odzyskiwania, zapisane na dysku i kiedy należy zapisywać przyrostowe kopie zapasowe. Kliknij przycisk **Dalej**.

    > [!IMPORTANT]
    > Należy **nie** przechowywanie danych odzyskiwania operacyjnego (kopia zapasowa) na dyskach dołączonych do serwera usługi Azure Backup więcej niż pięć dni.
    >

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Zamiast określać interwał tworzenia przyrostowych kopii zapasowych, aby uruchomienie ekspresowej pełnej kopii zapasowej bezpośrednio przed każdym zaplanowanym punktem odzyskiwania, kliknij przycisk **zaraz przed punktem odzyskiwania**. W przypadku ochrony obciążeń aplikacji, serwer usługi Azure Backup tworzy punkty odzyskiwania na harmonogram częstotliwości synchronizacji, (pod warunkiem aplikacja obsługuje przyrostowe kopie zapasowe). Jeśli aplikacja nie obsługuje przyrostowych kopii zapasowych, serwer usługi Azure Backup uruchamia ekspresową pełną kopię zapasową.

    Aby uzyskać **punktów odzyskiwania plików**, określ, kiedy do tworzenia punktów odzyskiwania. Kliknij przycisk **Modyfikuj** można ustawić godziny i dni tygodnia, gdy są tworzone punkty odzyskiwania.

6. W **Przejrzyj przydział dysku** ekranu, przejrzyj miejsce w puli magazynu przydzielone danej grupie ochrony.

    **Całkowity rozmiar danych** jest rozmiar danych, aby utworzyć kopię zapasową i **miejsca na dysku do zainicjowania obsługi administracyjnej** na serwer usługi Azure Backup jest zalecane miejsce dla grupy ochrony. Usługa Azure Backup Server wybierze idealną wielkość kopii zapasowej na podstawie ustawień. Można jednak edytować opcje wielkości kopii zapasowej w szczegółach alokacji dysku. W przypadku obciążeń wybierz preferowany magazyn, w menu rozwijanym. Edycja zmienia wartości Całkowita ilość miejsca i ilość wolnego miejsca, w okienku dostępny Magazyn dyskowy. Miejsce to ilość miejsca w magazynie, który serwer usługi Azure Backup sugeruje dodać do woluminu, aby kontynuować tworzenie kopii zapasowych sprawnie w przyszłości.

7. W **wybierz metodę tworzenia repliki**, wybierz, jak chcesz obsługiwać początkową pełną replikację danych. Jeśli zdecydujesz się replikacja przez sieć, Azure zaleca wybierz godzinę poza godzinami szczytu. Dla dużych ilości danych lub nieoptymalnych siecią warto rozważyć replikowanie danych za pomocą nośnika wymiennego.

8. W **wybierz opcje sprawdzania spójności**, wybierz sposób automatyzacji sprawdzania spójności. Włącz sprawdzanie spójności uruchomić tylko wtedy, gdy replikacja danych stanie się niespójna lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, należy uruchomić sprawdzanie ręczne, w dowolnym momencie przez:
    * W **ochrony** obszarze konsoli serwera usługi Azure Backup, kliknij prawym przyciskiem myszy grupę ochrony i wybierz pozycję **Przeprowadź Sprawdzanie spójności**.

9. Jeśli zdecydujesz się utworzyć kopię zapasową na platformie Azure, na **Określ dane chronione w trybie online** strony, upewnij się, obciążenia, aby utworzyć kopię zapasową na platformie Azure są zaznaczone.

10. W **Określanie harmonogramu tworzenia kopii zapasowych online**, określ, kiedy powinny występować przyrostowe kopie zapasowe na platformie Azure. 

    Można zaplanować tworzenie kopii zapasowych do uruchamiania na co dzień/tydzień/miesiąc/rok i godzina i Data, w którym powinno być ono uruchomione. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Każdym uruchomieniu zadania tworzenia kopii zapasowej, punkt odzyskiwania danych jest tworzony na platformie Azure z kopii kopii zapasowej danych przechowywanych na dysku serwera usługi Azure Backup.

11. W **Określ zasady przechowywania danych online**, określ, jak punktów odzyskiwania tworzonych na podstawie kopii zapasowych co dzień/tydzień/miesiąc/rok są przechowywane na platformie Azure.

12. W **wybierz replikację online**, określ, jak występuje początkowa Pełna replikacja danych. 

13. Na **Podsumowanie**, przejrzyj ustawienia. Po kliknięciu **Utwórz grupę**, następuje Replikacja początkowa danych. Podczas replikacji danych zakończeniu na **stan** strony, stan grupy ochrony jest wyświetlany jako **OK**. Zadania tworzenia początkowej kopii zapasowej odbywa się zgodnie z ochrony ustawień grupy.

## <a name="recover-file-data"></a>Odzyskiwanie danych plików

Za pomocą konsoli usługi Azure Backup Server do odzyskania danych do maszyny wirtualnej.

1. W konsoli usługi Azure Backup Server, na pasku nawigacyjnym kliknij **odzyskiwania** i przeglądanie danych, którą chcesz odzyskać. W okienku wyników wybierz dane.

2. W kalendarzu w sekcji punktów odzyskiwania dat wytłuszczonym drukiem wskazują, że dostępnych punktów odzyskiwania. Wybierz datę, aby odzyskać.

3. W **element możliwy do odzyskania** okienku, wybierz element, który chcesz odzyskać.

4. W **akcje** okienku kliknij **odzyskać** aby otworzyć Kreatora odzyskiwania.

5. Dane można odzyskać w następujący sposób:

    * **Odzyskaj do oryginalnej lokalizacji** — Jeśli komputer kliencki jest połączony za pośrednictwem sieci VPN, ta opcja nie działa. Zamiast tego należy użyć alternatywnej lokalizacji, a następnie skopiować dane z tej lokalizacji.
    * **Odzyskiwanie do lokalizacji alternatywnej**

6. Określ opcje odzyskiwania:

    * Dla **istniejące zachowanie funkcji odzyskiwania wersji**, wybierz opcję **Utwórz kopię**, **Pomiń**, lub **Zastąp**. Zastąpienie jest dostępna tylko w przypadku odzyskiwania do oryginalnej lokalizacji.
    * Aby uzyskać **Przywróć zabezpieczenia**, wybierz **Zastosuj ustawienia komputera docelowego** lub **Zastosuj ustawienia zabezpieczeń wersji punktu odzyskiwania**.
    * Dla **przepustowości**, kliknij przycisk **Modyfikuj** umożliwiające ograniczenie przepustowości sieci.
    * **Powiadomienie** kliknij **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**, i określ adresatów, którzy będą otrzymywać powiadomienia. Kolejne adresy e-mail oddziel przecinkami.
    * Po dokonaniu wyboru, kliknij przycisk **dalej**

7. Przejrzyj ustawienia odzyskiwania, a następnie kliknij przycisk **odzyskać**. 

    > [!Note] 
    > Gdy zadanie odzyskiwania jest w toku, zostaną anulowane wszystkie zadania synchronizacji dla wybrane elementy odzyskiwania.
    >

Jeśli używasz nowoczesnego magazynu kopii zapasowych (MB), serwer plików odzyskiwania przez użytkownika końcowego (EUR) nie jest obsługiwane. EUR serwera plików ma zależność na wolumin kopiowania w tle usługi (VSS), która nie korzysta z Modern Backup Storage. Jeśli EUR jest włączona, umożliwia odzyskiwanie danych następujące czynności:

1. Przejdź do plików chronionych, kliknij prawym przyciskiem myszy nazwę pliku i wybierz pozycję **właściwości**.

2. Na **właściwości** menu, kliknij przycisk **poprzednie wersje** i wybierz wersję, którą chcesz odzyskać.

## <a name="view-azure-backup-server-with-a-vault"></a>Wyświetlanie usługi Azure Backup Server przy użyciu magazynu
Aby wyświetlić jednostki usługi Azure Backup Server w witrynie Azure Portal, należy wykonać następujące czynności:
1. Otwórz magazyn usługi Recovery Services.
2. Kliknij przycisk infrastruktury kopii zapasowych.
3. Wyświetl serwery zarządzania kopiami zapasowymi.

## <a name="see-also"></a>Zobacz także
Aby uzyskać informacje na temat korzystania z usługi Azure Backup Server do ochrony innych obciążeń, zobacz jeden z następujących artykułów:
- [Tworzenie kopii zapasowych farmy programu SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Tworzenie kopii zapasowych programu SQL server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
