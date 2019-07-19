---
title: Rozwiązywanie problemów z Azure Migrate | Microsoft Docs
description: Zawiera omówienie znanych problemów z usługą Azure Migrate oraz wskazówki dotyczące rozwiązywania problemów z typowymi błędami.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302326"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

[Azure Migrate](migrate-services-overview.md) oferuje centrum narzędzi firmy Microsoft do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Ten dokument zawiera pomoc dotyczącą rozwiązywania problemów z Azure Migrate, Azure Migrate: Ocena serwera i Azure Migrate: Migracja serwera.

## <a name="azure-migrate-project-issues"></a>Problemy dotyczące projektu Azure Migrate

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Nie mogę znaleźć mojego istniejącego projektu Azure Migrate.

Istnieją [dwie wersje](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate. W zależności od wersji, w której został utworzony projekt, wykonaj poniższe czynności, aby znaleźć projekt:

1. Jeśli szukasz projektu utworzonego przy użyciu wersji poprzedniej (stare środowisko) Azure Migrate, postępuj zgodnie z poniższymi instrukcjami, aby znaleźć projekt.

    1. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj pozycję **Azure Migrate**.
    2. Na pulpicie nawigacyjnym Azure Migrate zobaczysz transparent informujący o uzyskiwaniu dostępu do starszych projektów. Ten transparent zobaczysz tylko wtedy, gdy projekt został utworzony ze starym doświadczeniem. Kliknij transparent.

    ![Dostęp do istniejących projektów](./media/troubleshooting-general/access-existing-projects.png)

    3. Zobaczysz teraz listę istniejących projektów utworzonych przy użyciu poprzedniej wersji Azure Migrate.

2. Jeśli szukasz projektu utworzonego z bieżącą wersją (nowe środowisko), postępuj zgodnie z poniższymi instrukcjami, aby znaleźć projekt.

    1. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj pozycję **Azure Migrate**.
    2. Na pulpicie nawigacyjnym Azure Migrate przejdź do strony **serwery** w lewym okienku, a następnie wybierz pozycję **Zmień** w prawym górnym rogu:

    ![Przełącz do istniejącego projektu Azure Migrate](./media/troubleshooting-general/switch-project.png)

    3. Wybierz odpowiednią **subskrypcję** i **Migruj projekt**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Nie mogę utworzyć drugiego projektu Azure Migrate.

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć nowy projekt Azure Migrate.

1. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj pozycję **Azure Migrate**.
2. Na pulpicie nawigacyjnym Azure Migrate przejdź do strony **serwery** w lewym okienku, a następnie wybierz pozycję **Zmień** w prawym górnym rogu:

   ![Zmień projekt Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj** , jak pokazano na poniższym zrzucie ekranu:

   ![Tworzenie drugiego projektu Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Usuwanie projektów Azure Migrate i skojarzonych Log Analytics obszaru roboczego

Usunięcie projektu Azure Migrate powoduje usunięcie projektu migracji wraz z metadanymi dotyczącymi odnalezionych maszyn. Jeśli jednak dołączysz obszar roboczy Log Analytics do narzędzia do oceny serwera, nie usunie on automatycznie Log Analytics obszaru roboczego. Dzieje się tak, ponieważ ten sam obszar roboczy Log Analytics może być używany w wielu przypadkach użycia. Jeśli chcesz również usunąć obszar roboczy Log Analytics, musisz to zrobić ręcznie.

1. Przejdź do obszaru roboczego Log Analytics dołączonego do projektu.
     1. Jeśli projekt migracji nie został jeszcze usunięty, można znaleźć link do obszaru roboczego z poziomu strony przegląd oceny serwera w sekcji podstawowe informacje.

     ![LA — obszar roboczy](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Jeśli projekt migracji został już usunięty, w okienku po lewej stronie w Azure Portal wybierz pozycję **grupy zasobów** . Przejdź do grupy zasobów, w której został utworzony obszar roboczy, a następnie przejdź do niej.
2. Postępuj zgodnie z instrukcjami [w tym artykule](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) , aby usunąć obszar roboczy.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Tworzenie projektu migracji nie powiodło się z powodu błędów *żądania muszą zawierać nagłówki tożsamości użytkownika*

Ten problem może wystąpić w przypadku użytkowników, którzy nie mają dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. Gdy użytkownik jest dodawany do dzierżawy usługi Azure AD po raz pierwszy, otrzymuje wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy. Użytkownicy muszą przejść do wiadomości e-mail i zaakceptować zaproszenie do pomyślnego dodania do dzierżawy. Jeśli nie widzisz wiadomości e-mail, skontaktuj się z użytkownikiem, który ma już dostęp do dzierżawy, i poproś o ponowne wysłanie zaproszenia do Ciebie, korzystając z kroków określonych [tutaj](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po otrzymaniu wiadomości e-mail z zaproszeniem musisz otworzyć wiadomość e-mail, a następnie kliknąć link w wiadomości e-mail, aby zaakceptować zaproszenie. Gdy to zrobisz, musisz wylogować się z Azure Portal i zalogować się ponownie, odświeżanie przeglądarki nie będzie działało. Następnie możesz spróbować utworzyć projekt migracji.

## <a name="appliance-issues"></a>Problemy z urządzeniem

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Wdrożenie urządzenia Azure Migrate dla oprogramowania VMware nie powiodło się z powodu błędu: Podany plik manifestu jest nieprawidłowy: Nieprawidłowy wpis manifestu OVF.

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate jest poprawnie pobrany, sprawdzając jego wartość skrótu. Aby zweryfikować wartość skrótu, zapoznaj się z [artykułem](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli problemy nadal występują, a plik OVF jest wdrażany przy użyciu klienta oprogramowania VMware vSphere, spróbuj wdrożyć go za pomocą internetowego klienta programu vSphere. Jeśli to się nie powiedzie, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę wdrożenia go na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi, wykonując następujące czynności:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) przy użyciu klienta sieci Web (https://<*adres IP hosta*>/UI).
   - Przejdź do**spisu zasobów** **domowych** > .
   - Kliknij pozycję **plik** > **Wdróż szablon OVF**. Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>Urządzenie nie może nawiązać połączenia z Internetem

Może się tak zdarzyć, gdy komputer, którego używasz, znajduje się za serwerem proxy. Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy wymaga jednego z nich.
Jeśli używasz dowolnego serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, pamiętaj o dodaniu tych wymaganych adresów URL do listy dozwolonych:

Scenariusz | Lista adresów URL
--- | ---
Ocena serwera dla oprogramowania VMware | [Etapie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Ocena serwera dla funkcji Hyper-V | [Etapie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Migracja serwera dla oprogramowania VMware (bez agentów) | [Etapie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Migracja serwera dla oprogramowania VMware (opartego na agentach) | [Etapie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Migracja serwera dla funkcji Hyper-V | [Etapie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, musisz zaimportować certyfikat serwera proxy na maszynę wirtualną urządzenia. Certyfikat serwera proxy można zaimportować, wykonując kroki opisane [tutaj](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Błąd 802: Błąd synchronizacji daty i godziny

Zegar serwera może nie być zsynchronizowany z bieżącym czasem przez więcej niż pięć minut. Zmień czas zegara na maszynie wirtualnej modułu zbierającego tak, aby pasował do bieżącego czasu w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom polecenie W32tm/TZ.
3. Aby zsynchronizować godzinę, uruchom polecenie w32tm/resync.


### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer błędów

Nie można nawiązać połączenia z programem vCenter Server „Servername.com:9443” z powodu następującego błędu: Brak punktów końcowych nasłuchujących w lokalizacji https://Servername.com:9443/sdk, które mogłyby zaakceptować komunikat.

Sprawdź, czy korzystasz z najnowszej wersji urządzenia zbierającego, a jeśli nie, Uaktualnij urządzenie do [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).

Jeśli problem nadal występuje w najnowszej wersji, może to być spowodowane faktem, że maszyna modułu zbierającego nie może rozpoznać określonej nazwy vCenter Server lub określony port jest nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający podejmie próbę nawiązania połączenia z numerem portu 443.

1. Spróbuj wysłać polecenie ping do Servername.com z komputera modułu zbierającego.
2. Jeśli nie możesz wykonać kroku 1, spróbuj połączyć się z programem vCenter Server za pośrednictwem adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Na koniec sprawdź, czy program vCenter Server działa.

## <a name="discovery-issues"></a>Problemy z odnajdywaniem

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>Po rozpoczęciu odnajdywania nie widzę odnalezionych maszyn wirtualnych w Azure Portal. Kafelki serwer oceny serwera i migracji serwera pokazują stan "odnajdywanie w toku"
Po rozpoczęciu odnajdywania urządzenia poczekaj chwilę na wykrycie wykrytych maszyn na Azure Portal. Odnalezienie oprogramowania VMware trwa około 15 minut, a przez co najmniej 2 minuty na dodany host na potrzeby odnajdywania funkcji Hyper-V. Jeśli nadal widzisz "odnajdywanie w toku" nawet po tym czasie, kliknij przycisk **Odśwież** na karcie **serwery** . Powinno to spowodować wyświetlenie liczby odnalezionych serwerów na kafelkach oceny serwera i migracji serwera.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Używam urządzenia, które stale odnajduje środowisko lokalne, ale maszyny wirtualne, które zostały usunięte w środowisku lokalnym, są nadal wyświetlane w portalu.

Aby odzwierciedlić dane odnajdywania zebrane przez urządzenie w portalu, zajmie to 30 minut. Jeśli nie widzisz aktualnych informacji nawet po 30 minutach, wydaj odświeżanie danych, wykonując następujące czynności:

1. W obszarze serwery > Azure Migrate: Ocena serwera, kliknij przycisk **Przegląd**.
2. W obszarze **Zarządzanie**kliknij pozycję **Agent Health**
3. Kliknij opcję, aby **odświeżyć agenta**. Zostanie wyświetlona poniżej opcja poniżej listy agentów.
4. Poczekaj na zakończenie operacji odświeżania. Sprawdź, czy masz dostęp do aktualnych informacji na temat maszyn wirtualnych.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nie mam najnowszych informacji na lokalnych maszynach wirtualnych w portalu

Aby odzwierciedlić dane odnajdywania zebrane przez urządzenie w portalu, zajmie to 30 minut. Jeśli nie widzisz aktualnych informacji nawet po 30 minutach, wydaj odświeżanie danych, wykonując następujące czynności:

1. W obszarze serwery > Azure Migrate: Ocena serwera, kliknij przycisk **Przegląd**.
2. W obszarze **Zarządzanie**kliknij pozycję **Agent Health**
3. Kliknij opcję, aby **odświeżyć agenta**. Zostanie wyświetlona ta opcja poniżej listy agentów.
4. Poczekaj na zakończenie operacji odświeżania. Na swoich maszynach wirtualnych powinna być teraz wyświetlana aktualna informacja.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nie można nawiązać połączenia z hostami lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu WinRM: 0x803381B9 (Identyfikator błędu: 50004)
Ten błąd występuje, gdy serwer DNS obsługujący urządzenie nie może rozpoznać podanej nazwy klastra lub hosta. Jeśli ten błąd jest wyświetlany w klastrze, spróbuj podać w pełni kwalifikowaną nazwę domeny klastra. 

Ten błąd może również zostać wyświetlony w przypadku hostów w klastrze. W takim przypadku urządzenie może połączyć się z klastrem. Ale klaster zwrócił nazwy hostów, które nie są w pełni kwalifikowanymi nazwami domen. 

Aby rozwiązać ten problem, zaktualizuj plik Hosts na urządzeniu, dodając mapowanie adresów IP i nazw hostów.
1. Otwórz Notatnik jako administrator. Otwórz plik C:\Windows\System32\Drivers\etc\hosts.
2. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym widzisz ten błąd.
3. Zapisz i zamknij plik Hosts.
4. Możesz sprawdzić, czy urządzenie może nawiązać połączenie z hostami przy użyciu aplikacji do zarządzania urządzeniami. Po 30 minutach powinno być możliwe wyświetlenie najnowszych informacji na temat tych hostów na Azure Portal. 


## <a name="assessment-issues"></a>Problemy z oceną

### <a name="azure-readiness-issues"></a>Problemy z gotowością platformy Azure

Problem | Korygowanie
--- | ---
Nieobsługiwany typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zalecamy przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Migracji na serwer Azure Migrate można użyć do przeprowadzenia migracji takich maszyn wirtualnych, ponieważ spowoduje to przekonwertowanie typu rozruchowego maszyny wirtualnej na system BIOS podczas migracji.
Warunkowo obsługiwane systemy operacyjne Windows | System operacyjny przeszedł koniec okresu świadczenia pomocy technicznej i potrzebuje niestandardowej umowy pomocy technicznej (CSA) w celu uzyskania [pomocy technicznej na platformie Azure](https://aka.ms/WSosstatement). Rozważ uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](https://aka.ms/WSosstatement), rozważ uaktualnienie systemu operacyjnego maszyny przed migracją na platformę Azure.
Warunkowo potwierdzony system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md), dlatego należy rozważyć uaktualnienie systemu operacyjnego maszyny przed migracją na platformę Azure.
Niepotwierdzony system operacyjny Linux | Maszyna może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. Rozważ uaktualnienie systemu operacyjnego do [zatwierdzonej wersji Linux](../virtual-machines/linux/endorsed-distros.md) przed przeprowadzeniem migracji na platformę Azure
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server, ze względu na to, że Azure Migrate nie może zidentyfikować gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji maszyny upewnij się, że system operacyjny działający na maszynie jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwana liczba bitów systemu operacyjnego | Maszyny wirtualne z 32-bitową wersją systemu operacyjnego mogą zostać uruchomione na platformie Azure, ale przed migracją do platformy Azure zaleca się uaktualnienie systemu operacyjnego z 32-bitowego do 64-bitowego.
Wymaga subskrypcji programu Visual Studio. | Na maszynie jest uruchomiony system operacyjny Windows, który jest obsługiwany tylko w ramach subskrypcji programu Visual Studio.
Nie znaleziono maszyny wirtualnej wymaganej wydajności magazynu. | Wydajność magazynu (IOPS/przepustowość) wymagana dla maszyny przekracza obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu maszyny.
Nie znaleziono maszyny wirtualnej dla wymaganej wydajności sieci. | Wydajność sieci (WE/wychodzącej) wymagana przez maszynę przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla maszyny.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji. | Użyj innej lokalizacji docelowej przed migracją.
Co najmniej jeden niewłaściwy dysk. | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku jest < 4 TB (jeśli nie, zmniejsz rozmiar dysku przed migracją na platformę Azure). Należy upewnić się, że wydajność (IOPS/przepustowość) wymagana przez poszczególne dyski jest obsługiwana przez [maszyny wirtualne zarządzane](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)przez platformę Azure.   
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją Usuń nieużywane karty sieciowe z maszyny.
Liczba dysków przekracza limit | Usuń nieużywane dyski z maszyny przed migracją.
Rozmiar dysku przekracza limit | Platforma Azure obsługuje dyski o rozmiarze do 4 TB. Zmniejsz dyski do mniejszej niż 4 TB przed migracją.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Dysk niedostępny dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej z wymaganymi rdzeniami i pamięcią | Platforma Azure nie może poprawić odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni maszyny lokalnej.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nie mogę określić Umowa Enterprise (EA) jako oferty platformy Azure we właściwościach oceny?
Azure Migrate: Ocena serwera obecnie nie obsługuje cen opartych na Umowa Enterprise (EA). Obejściem jest użycie "płatność zgodnie z rzeczywistym użyciem" jako oferty platformy Azure i użycie właściwości "Discount" w celu określenia dowolnego rabatu niestandardowego, który otrzymujesz. [Dowiedz się więcej o sposobach dostosowywania oceny](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Dlaczego program sprawdza, czy na maszynach wirtualnych z systemem Linux jest stosowany warunek "gotowe". Czy jest coś, co należy zrobić, aby rozwiązać ten problem?
Istnieje znana przerwa w ocenie serwera, w której nie można wykryć pomocniczej wersji systemu operacyjnego Linux zainstalowanej na lokalnych maszynach wirtualnych (na przykład dla RHEL 6,10, obecnie Ocena serwera wykrywa tylko RHEL 6 jako wersję systemu operacyjnego). Ponieważ platforma Azure poświadcza tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako gotowe do oceny serwera. Możesz ręcznie upewnić się, że system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [dokumentację pomocy technicznej systemu Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) Po zweryfikowaniu zatwierdzonej dystrybucji można zignorować to ostrzeżenie.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Jednostka SKU maszyny wirtualnej zalecana przez ocenę serwera ma więcej rdzeni i większy rozmiar pamięci niż to, co jest przydzielone lokalnie. Dlaczego tak jest?
Zalecenie dotyczące jednostki SKU maszyny wirtualnej w ocenie serwera zależy od właściwości oceny. Można utworzyć dwa rodzaje ocen w ocenie serwera, "oparte na wydajności" i "jako oceny lokalne". W przypadku ocen opartych na wydajności Ocena serwera traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Ponadto w przypadku ustalania rozmiarów na podstawie wydajności czynnik komfortu jest uwzględniany w celu zidentyfikowania efektywnego wykorzystania. W przypadku lokalnego określania wielkości dane wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana w zależności od tego, co jest przydzielono lokalnie.

Załóżmy na przykład, że istnieje lokalna maszyna wirtualna z 4 rdzeniami i 8 GB pamięci z 50% użycia procesora CPU i 50% pamięci, a czynnik komfortu 1,3 został określony w ocenie. Jeśli kryterium ustalania wielkości oceny ma wartość "jako lokalna" jednostka SKU maszyny wirtualnej platformy Azure z 4 rdzeniami i 8 GB pamięci jest zalecana, jednak jeśli kryterium ustalania wielkości jest oparte na wydajności, w oparciu o efektywne wykorzystanie procesora i pamięci (50% z 4 rdzeni * 1,3 = 2,6 rdzenie i 50% 8 GB Pamięć * 1,3 = 5,3 GB pamięci), zalecana jest najtańsza jednostka SKU maszyny wirtualnej czterech rdzeni (najbliższa obsługiwana liczba rdzeni) i rozmiar pamięci wynoszącej 8 GB (najbliższy obsługiwany rozmiar pamięci). [Dowiedz się więcej o tym, jak ocenia serwer przeprowadza zmianę rozmiarów.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Jednostka SKU dysku zalecana przez ocenę serwera ma większy rozmiar niż to, co jest przydzielone lokalnie. Dlaczego tak jest?
Rozmiar dysku w ocenie serwera zależy od dwóch właściwości oceny — kryterium ustalania wielkości i typu magazynu. Jeśli kryterium ustalania wielkości jest "na podstawie wydajności" i typ magazynu jest ustawiony na "automatyczny", wartości IOPS i przepływności dysku są brane pod uwagę w celu zidentyfikowania typu dysku docelowego (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub dysków Premium). Następnie zaleca się użycie jednostki SKU dysku w ramach typu dysku w celu uwzględnienia wymagań dotyczących rozmiaru dysku lokalnego. Jeśli kryterium ustalania rozmiaru jest "oparte na wydajności" i typ magazynu to "Premium", zaleca się, aby jednostka SKU dysku Premium na platformie Azure była oparta na wymaganiach dotyczących liczby operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do określania rozmiarów dysków, gdy kryterium ustalania wielkości ma wartość "jako", a typ magazynu to "HDD w warstwie Standardowa", "SSD w warstwie Standardowa" lub "Premium".

Na przykład jeśli masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, Ocena serwera będzie zalecać typ dysku w warstwie Premium (ze względu na wymagania dotyczące wyższych IOPS), a następnie zalecaną jednostkę SKU, która może obsługiwać wymagana liczba IOPS i rozmiar. Najbliższe dopasowanie w tym przykładzie byłyby P15 (256 GB, 1100 IOPS). Mimo że rozmiar wymagany przez dysk lokalny wynosi 32 GB, Ocena serwera zaleca dysk o większym rozmiarze z powodu dużego wymagania IOPS dysku lokalnego.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Czy koszt licencji systemu operacyjnego maszyny wirtualnej uwzględniony w kosztach obliczeniowych szacowany przez ocenę serwera?
Ocena serwera uwzględnia obecnie tylko koszt licencji systemu operacyjnego dla maszyn z systemem Windows. koszt licencji systemu operacyjnego dla maszyn z systemami Linux nie jest obecnie brany pod uwagę. 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ ma historia wydajności i użycie percentylu na zaleceń dotyczących rozmiaru?
Te właściwości mają zastosowanie tylko w przypadku ustalania wielkości na podstawie wydajności. Ocena serwera ciągle zbiera dane wydajności maszyn lokalnych i używa jej do zaleceń jednostki SKU maszyny wirtualnej i jednostki SKU dysku na platformie Azure. Poniżej przedstawiono sposób zbierania danych o wydajności przez ocenę serwera:
- Urządzenie Azure Migrate ciągle profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund dla maszyn wirtualnych VMware i co 30 sekund dla maszyn wirtualnych funkcji Hyper-V.
- Urządzenie zbiera próbki 20/30 sekund, aby utworzyć jeden punkt danych na każde 10 minut. Aby utworzyć pojedynczy punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20/30-sekundowych i wysyła je na platformę Azure.
- Po utworzeniu oceny w ocenie serwera na podstawie wartości percentylu czas trwania wydajności i historia wydajności zostanie zidentyfikowana wartość reprezentatywne wykorzystanie. Na przykład jeśli historia wydajności ma jeden tydzień, a użycie percentyla to używany 95., Azure Migrate sortuje wszystkie 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera używany 95. percentyl jako wartość reprezentatywną.
Wartość percentylu używany 95. zapewnia ignorowanie wszelkich wartości odstających, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu. Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, należy wybrać 99 percentyl jako wykorzystanie percentylu.

## <a name="dependency-visualization-issues"></a>Problemy wizualizacji zależności

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nie mogę znaleźć funkcji wizualizacji zależności dla projektów Azure Government.

Azure Migrate zależy od Service Map funkcji wizualizacji zależności, ponieważ Service Map jest obecnie niedostępna w Azure Government, ta funkcja nie jest dostępna w Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Zainstalowano Microsoft Monitoring Agent (MMA) i agenta zależności na moich lokalnych maszynach wirtualnych, ale zależności są teraz wyświetlane w portalu Azure Migrate.

Po zainstalowaniu agentów, Azure Migrate zwykle pobiera 15-30 minut w celu wyświetlenia zależności w portalu. Jeśli zaczekasz przez ponad 30 minut, upewnij się, że Agent MMA jest w stanie komunikować się z obszarem roboczym pakietu OMS, wykonując poniższe kroki:

Dla maszyny wirtualnej z systemem Windows:
1. Przejdź do **Panelu sterowania** i uruchom **Microsoft Monitoring Agent**.
2. Przejdź do karty **Azure log Analytics (OMS)** w oknie podręcznym właściwości MMA.
3. Upewnij się, że **stan** obszaru roboczego to zielony.
4. Jeśli stan jest inny niż zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do MMA.
        ![Stan MMA](./media/troubleshooting-general/mma-status.png)

W przypadku maszyny wirtualnej z systemem Linux upewnij się, że polecenia instalacji programu MMA i agenta zależności powiodły się.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

Lista systemów operacyjnych Windows obsługiwana przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Lista systemów operacyjnych Linux obsługiwana przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Jakie systemy operacyjne są obsługiwane przez agenta zależności?

W [tym miejscu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)znajduje się lista systemów operacyjnych Windows obsługiwanych przez agenta zależności.
W [tym miejscu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)znajduje się lista systemów operacyjnych Linux obsługiwanych przez agenta zależności.

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Nie mogę wizualizować zależności w Azure Migrate przez więcej niż godzinę czasu trwania?
Azure Migrate umożliwia wizualizację zależności przez maksymalnie jedną godzinę. Mimo że Azure Migrate umożliwia powrót do konkretnej daty w historii dla maksymalnie ostatniego miesiąca, maksymalny czas trwania wizualizacji zależności wynosi do 1 godziny. Na przykład można użyć funkcji czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla okna o pojedynczej godzinie. Można jednak użyć dzienników Azure Monitor, aby [wykonać zapytanie o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nie mogę wizualizować zależności dla grup z więcej niż 10 maszynami wirtualnymi?
Możesz [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , które mają maksymalnie 10 maszyn wirtualnych, jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy podzielenie grupy w mniejszej liczbie i wizualizację zależności.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Agenci są instalowani i używali wizualizacji zależności do tworzenia grup. Teraz po zakończeniu pracy w trybie failover maszyny pokazują akcję "Zainstaluj agenta" zamiast "Wyświetl zależności"
* Publikuj planowane lub nieplanowane przejście w tryb failover, maszyny lokalne są wyłączone i równoważne maszyny są na platformie Azure. Te maszyny uzyskują inny adres MAC. Mogą uzyskać inny adres IP w zależności od tego, czy użytkownik wybrał opcję zachowania lokalnego adresu IP, czy nie. Jeśli oba adresy MAC i IP różnią się, Azure Migrate nie kojarzy maszyn lokalnych z dowolnymi Service Map danymi zależności i poprosi użytkownika o zainstalowanie agentów zamiast wyświetlania zależności.
* Po testowym przejściu w tryb failover maszyny lokalne pozostają włączone zgodnie z oczekiwaniami. Równoważne komputery na platformie Azure uzyskują różne adresy MAC i mogą uzyskać inny adres IP. Jeśli użytkownik nie zablokuje Azure Monitor ruchu wychodzącego z tych maszyn, Azure Migrate nie będzie kojarzyć maszyn lokalnych z dowolnymi Service Map danymi zależności i prosi użytkownika o zainstalowanie agentów zamiast wyświetlania zależności.

## <a name="collect-azure-portal-logs"></a>Zbieranie dzienników Azure Portal

**Jak mogę zbierać Azure Portal dzienników ruchu sieciowego?**

1. Otwórz przeglądarkę i przejdź [do portalu](https://portal.azure.com)i zaloguj się.
2. Naciśnij klawisz F12, aby uruchomić Narzędzia deweloperskie. W razie konieczności Wyczyść ustawienie **Wyczyść wpisy w nawigacji**.
3. Kliknij kartę **Sieć** , a następnie Rozpocznij przechwytywanie ruchu sieciowego:
   - W programie Chrome wybierz opcję **Zachowaj dziennik**. Nagrywanie powinno być uruchamiane automatycznie. Czerwony okrąg wskazuje na to, że ruch jest przechwytywany. Jeśli nie jest wyświetlany, kliknij czarny okrąg, aby rozpocząć.
   - W programie Microsoft Edge/IE nagrywanie powinno być uruchamiane automatycznie. Jeśli tak nie jest, kliknij zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas rejestrowania, zatrzymywania rejestrowania i zapisywania kopii zapisanego działania:
   - W programie Chrome kliknij prawym przyciskiem myszy i kliknij pozycję **Zapisz jako Har z zawartością**. To kompresuje i eksportuje dzienniki jako plik. HAR.
   - W przeglądarce Microsoft Edge/IE kliknij ikonę **Eksportuj przechwycony ruch** . To kompresuje i eksportuje dziennik.
6. Przejdź do karty **konsoli** , aby sprawdzić ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
   - W programie Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz pozycję **Zapisz jako**, aby wyeksportować i zip log.
   - W programie Microsoft Edge/IE kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystko**.
7. Zamknij Narzędzia deweloperskie.
