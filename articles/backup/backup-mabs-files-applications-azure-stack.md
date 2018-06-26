---
title: Wykonaj kopię zapasową plików w stosie maszynach wirtualnych platformy Azure
description: Kopia zapasowa Azure umożliwia tworzenie kopii zapasowej i odzyskiwanie plików stosu Azure i aplikacji do środowiska Azure stosu.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 2fb3bad56de781dd81d4c5f82b734c9420c75dee
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751708"
---
# <a name="back-up-files-on-azure-stack"></a>Tworzenie kopii zapasowych Azure stosu
Kopia zapasowa Azure umożliwia ochronę (lub utworzyć kopię zapasową) plików i aplikacji na stosie Azure. Aby utworzyć kopię zapasową plików i aplikacji, należy zainstalować serwer kopii zapasowej Microsoft Azure jako maszynę wirtualną działającą na stosie Azure. Można chronić pliki na dowolnym serwerze stosu Azure w tej samej sieci wirtualnej. Po zainstalowaniu serwera kopii zapasowej Azure, Dodawanie dysku systemu Azure, aby zwiększyć magazynu lokalnego, która jest dostępna dla krótkoterminowej kopii zapasowej danych. Serwer kopii zapasowej systemu Azure korzysta z magazynu Azure w celu przechowywania długoterminowego.

> [!NOTE]
> Jeśli serwer usługi Kopia zapasowa Azure i System Center Data Protection Manager (DPM) są podobne, program DPM nie jest obsługiwane do użycia z programem Azure stosu.
>

W tym artykule nie opisano instalowanie serwera usługi Kopia zapasowa Azure w środowisku Azure stosu. Aby zainstalować serwer kopii zapasowej Azure na stosie Azure, zapoznaj się z artykułem [Instalowanie serwera kopii zapasowej Azure](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Kopie zapasowe plików i folderów na maszynach wirtualnych Azure stosu na platformie Azure

Aby skonfigurować serwer kopii zapasowej Azure do ochrony plików na maszynach wirtualnych Azure VMs stosu, otwórz konsolę serwer kopii zapasowej Azure. Użyjesz konsoli do konfigurowania grup ochrony i ochrony danych na maszynach wirtualnych.

1. W konsoli serwera usługi Kopia zapasowa Azure kliknij **ochrony** i na pasku narzędzi, kliknij przycisk **nowy** otworzyć **tworzenia nowej grupy ochrony** kreatora.

   ![Konfigurowanie ochrony w konsoli serwera usługi Kopia zapasowa Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Może potrwać kilka sekund otworzyć kreatora. Po otwarciu kreatora, kliknij przycisk **dalej** aby przejść do **wybierz typ grupy ochrony** ekranu.

   ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na **wybierz typ grupy ochrony** ekranu, wybierz **serwerów** i kliknij przycisk **dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **Wybierz członków grupy** ekranu zostanie otwarta. 

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. W **Wybierz członków grupy** kliknij **+** rozwiń listę elementów podrzędnych. Dla wszystkich elementów, które chcesz chronić zaznacz pole wyboru. Po wybraniu wszystkich elementów kliknij **dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Firma Microsoft zaleca umieszczanie wszystkie dane, które do grupy ochrony co udostępni zasadę ochrony. Aby uzyskać pełne informacje o planowaniu i wdrażaniu grup ochrony, zapoznaj się z artykułem programu System Center DPM [wdrażanie grup ochrony](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. W **wybierz metodę ochrony danych** ekranu, wpisz nazwę grupy ochrony. Zaznacz pole wyboru **chcę uzyskać krótkoterminową ochronę za pomocą:** i **chcę uzyskać ochronę online**. Kliknij przycisk **Dalej**.

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Aby wybrać **chcę uzyskać ochronę online**, musisz najpierw wybrać **chcę uzyskać krótkoterminową ochronę za pomocą:** dysku. Serwer kopii zapasowej systemu Azure nie chronić na taśmie, więc dysk jest jedyną dostępną dla ochrony krótkoterminowej.

5. W **Określ cele krótkoterminowe** ekranu, czas przechowywania punktów odzyskiwania, zapisywane na dysku i kiedy należy zapisać przyrostowych kopii zapasowych. Kliknij przycisk **Dalej**.

    > [!IMPORTANT]
    > Należy **nie** zachowania danych operacyjnych dotyczących odzyskiwania (kopia zapasowa) na dyskach dołączone do serwera kopii zapasowej Azure przez więcej niż pięć dni.
    >

    ![Otwiera Kreatora nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Zamiast określać interwał tworzenia przyrostowych kopii zapasowych, aby uruchomienie ekspresowej pełnej kopii zapasowej bezpośrednio przed każdym zaplanowanym punktem odzyskiwania, kliknij przycisk **zaraz przed punktem odzyskiwania**. Jeśli chronisz obciążenia aplikacji, serwer kopii zapasowej Azure tworzy punkty odzyskiwania na harmonogram częstotliwości synchronizacji (zakładając, że aplikacja obsługuje przyrostowe kopie zapasowe). Jeśli aplikacja nie obsługuje przyrostowych kopii zapasowych, Utwórz kopię zapasową serwera Azure uruchamia ekspresową pełną kopią zapasową.

    Aby uzyskać **punktów odzyskiwania plików**, określ, kiedy należy utworzyć punktów odzyskiwania. Kliknij przycisk **Modyfikuj** można ustawić godziny i dni tygodnia, kiedy są tworzone punkty odzyskiwania.

6. W **Przejrzyj przydział dysku** ekranu, przejrzyj miejsce puli magazynu przydzielone do grupy ochrony.

    **Całkowity rozmiar danych** rozmiar danych, aby utworzyć kopię zapasową i **miejsca na dysku do obsługi administracyjnej** na serwerze kopii zapasowej Azure jest zalecane miejsce dla grupy ochrony. Serwer kopii zapasowej systemu Azure wybiera idealne wolumin kopii zapasowej, na podstawie ustawień. Można jednak edytować opcje tworzenia kopii zapasowej woluminu informacje dotyczące alokacji dysku. W przypadku obciążeń wybierz preferowany magazynu w menu rozwijanym. Zmiany wprowadzone zmiany wartości dla całkowita ilość miejsca i wolnego miejsca, w okienku dostępny magazyn na dysku. Ilość miejsca w magazynie, które sugeruje serwer kopii zapasowej Azure należy dodać do woluminu, aby kontynuować tworzenie kopii zapasowych w przyszłości sprawnie jest underprovisioned miejsca.

7. W **wybierz metodę tworzenia repliki**, wybierz sposób obsługi replikacji początkowej danych. Jeśli zdecydujesz się replikacji za pośrednictwem sieci, Azure zaleca wybierz godzinę poza godzinami szczytu. Dla dużych ilości danych lub nieoptymalnych siecią należy wziąć pod uwagę replikację danych za pomocą nośnika wymiennego.

8. W **wybierz opcje sprawdzania spójności**, wybierz sposób automatyzacji sprawdzania spójności. Włącz sprawdzanie spójności uruchomić tylko wtedy, gdy replikacja danych stanie się niespójna, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, należy uruchomić sprawdzanie ręczne, w dowolnym momencie przez:
    * W **ochrony** konsoli serwera usługi Kopia zapasowa Azure, kliknij prawym przyciskiem myszy grupę ochrony i wybierz **Przeprowadź Sprawdzanie spójności**.

9. Jeśli chcesz utworzyć kopię zapasową na platformie Azure, na **Określ dane chronione w trybie online** strony upewnij się, że wybrano obciążeń, aby utworzyć kopię zapasową na platformie Azure.

10. W **harmonogram tworzenia kopii zapasowych online określ**, określ, kiedy powinny występować przyrostowych kopii zapasowych na platformie Azure. 

    Można zaplanować uruchamianie co dzień/tydzień/miesiąc/rok i godzina i Data, w którym należy uruchomić wykonywania kopii zapasowych. Tworzenie kopii zapasowych może wystąpić maksymalnie dwa razy dziennie. Każdym uruchomieniu zadania tworzenia kopii zapasowej danych punkt odzyskiwania jest tworzony na platformie Azure z kopii kopii zapasowej danych przechowywanych na dysku serwera kopii zapasowej Azure.

11. W **Określ zasady przechowywania online**, określ, jak punkty odzyskiwania utworzone na podstawie kopii zapasowych codziennie/co tydzień/co miesiąc/corocznej są przechowywane na platformie Azure.

12. W **wybierz replikacji online**, określ, jak następuje pełna Replikacja początkowa danych. 

13. Na **Podsumowanie**, przejrzyj ustawienia. Po kliknięciu **Utwórz grupę**, Replikacja początkowa danych. Podczas replikacji danych zakończeniu na **stan** strony, stan grupy ochrony jest pokazywana jako **OK**. Początkowe zadanie tworzenia kopii zapasowej odbywa się zgodnie z ochrony grupy ustawień.

Pytania wymagające odpowiedzi: jak można rozwinąć magazynu danych na dysku do przechowywania krótkoterminowego dysku stosu Azure. Co to są wskazówki, które muszą zostać wywołane limit wyjaśniający krótkoterminowy Magazyn dyskowy?

## <a name="recover-file-data"></a>Odzyskiwanie danych plików

Aby odzyskać dane z maszyną wirtualną za pomocą konsoli serwera usługi Kopia zapasowa Azure.

1. W konsoli programu Azure Utwórz kopię zapasową serwera, na pasku nawigacyjnym kliknij **odzyskiwania** i przeglądania danych do odzyskania. Wybierz dane w okienku wyników.

2. W kalendarzu w sekcji punktów odzyskiwania dat wytłuszczonym drukiem wskazują, że są dostępne punkty odzyskiwania. Wybierz datę, aby odzyskać punktu odzyskiwania.

3. W **elementy możliwe do odzyskania** okienku, wybierz element, który chcesz odzyskać.

4. W **akcje** okienku, kliknij przycisk **odzyskać** aby otworzyć Kreatora odzyskiwania.

5. Dane można odzyskać w następujący sposób:

    * **Odzyskaj do oryginalnej lokalizacji** — Jeśli komputer kliencki jest połączony za pośrednictwem połączenia VPN, ta opcja nie działa. Zamiast tego użyć alternatywnej lokalizacji, a następnie skopiuj dane z tej lokalizacji.
    * **Odzyskaj do lokalizacji alternatywnej**

6. Określ opcje odzyskiwania:

    * Dla **zachowanie funkcji odzyskiwania wersji istniejące**, wybierz pozycję **utworzyć kopię**, **Pomiń**, lub **Zastąp**. Zastąp jest dostępna tylko wtedy, gdy odzyskiwanie do oryginalnej lokalizacji.
    * Aby uzyskać **Przywróć zabezpieczenia**, wybierz **Zastosuj ustawienia komputera docelowego** lub **Zastosuj ustawienia bezpieczeństwa wersji punktu odzyskiwania**.
    * Dla **przepustowości**, kliknij przycisk **Modyfikuj** umożliwiające ograniczenie przepustowości sieci.
    * **Powiadomienie** kliknij **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**, i określ adresatów, którzy będą otrzymywać powiadomienia. Kolejne adresy e-mail oddziel przecinkami.
    * Po dokonaniu wyboru, kliknij przycisk **dalej**

7. Przejrzyj ustawienia odzyskiwania, a następnie kliknij przycisk **odzyskać**. 

    > [!Note] 
    > Gdy zadanie odzyskiwania jest w toku, zostaną anulowane wszystkie zadania synchronizacji dla wybrane odzyskiwanie elementy.
    >

Jeśli używasz nowoczesnych magazynu kopii zapasowej (MB), serwer plików odzyskiwania przez użytkownika końcowego (EUR) nie jest obsługiwana. EUR serwera plików ma zależność na woluminów w tle kopii Service (VSS), które nowoczesne magazynu kopii zapasowej nie są używane. Jeśli włączono EUR, wykonaj następujące kroki, aby odzyskać dane:

1. Przejdź do chronionych plików i kliknij prawym przyciskiem myszy nazwę pliku i wybierz **właściwości**.

2. Na **właściwości** menu, kliknij przycisk **poprzednie wersje** i wybierz wersję, w której chcesz odzyskać.

## <a name="view-azure-backup-server-with-a-vault"></a>Serwer kopii zapasowej Azure widoku w magazynie
Aby wyświetlić jednostek serwer kopii zapasowej Azure w portalu Azure, należy wykonać następujące czynności:
1. Otwórz magazyn usług odzyskiwania.
2. Kliknij przycisk infrastruktura kopii zapasowej.
3. Widoku serwerów zarządzania kopiami zapasowymi.

## <a name="see-also"></a>Zobacz także
Uzyskać na ochrona innych obciążeń za pomocą serwera usługi Kopia zapasowa Azure zobacz następujące artykuły:
- [Tworzenie kopii zapasowej farmy programu SharePoint](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Tworzenie kopii zapasowych programu SQL server](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
