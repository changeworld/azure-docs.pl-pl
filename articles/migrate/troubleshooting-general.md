---
title: Rozwiązywanie problemów z Azure Migrate | Microsoft Docs
description: Zawiera omówienie znanych problemów z usługą Azure Migrate oraz wskazówki dotyczące rozwiązywania problemów z typowymi błędami.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372583"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

[Azure Migrate](migrate-services-overview.md) oferuje centrum narzędzi firmy Microsoft do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Ten dokument zawiera pomoc dotyczącą rozwiązywania problemów z Azure Migrate, Azure Migrate: Ocena serwera i Azure Migrate: Server Migration.

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

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Które usługi Azure lokalizacje geograficzne są obsługiwane przez Azure Migrate?

Listę dla programu VMware można znaleźć [tutaj](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i dla [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

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

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate jest poprawnie pobrany, sprawdzając jego wartość skrótu. Aby zweryfikować wartość skrótu, zapoznaj się z [artykułem](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
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


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Nie można pomyślnie zarejestrować urządzenia w projekcie Azure Migrate (Identyfikator błędu: 60052)

Ten błąd jest spowodowany brakiem wystarczających uprawnień na koncie platformy Azure używanym do zarejestrowania urządzenia. Upewnij się, że konto użytkownika platformy Azure użyte do zarejestrowania urządzenia ma co najmniej dostęp współautora do subskrypcji. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o wymaganych rolach i uprawnieniach platformy Azure.

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Nie można pomyślnie zarejestrować urządzenia w projekcie Azure Migrate (Identyfikator błędu: 60039)

Nie znaleziono projektu Azure Migrate wybranego przez użytkownika w celu zarejestrowania urządzenia, co spowodowało niepowodzenie rejestracji. Przejdź do Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów. Jeśli projekt nie istnieje, Utwórz nowy projekt Azure Migrate w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) na temat tworzenia nowego projektu Azure Migrate.

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Operacja zarządzania magazynem kluczy platformy Azure nie powiodła się (Identyfikator błędu: 60030, 60031)

Upewnij się, że konto użytkownika platformy Azure użyte do zarejestrowania urządzenia ma co najmniej dostęp współautora do subskrypcji. Sprawdź również, czy konto ma dostęp do Key Vault określonej w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o wymaganych rolach i uprawnieniach platformy Azure.

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>Nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla danej listy hostów lub klastrów (Identyfikator błędu: 60028)

Nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o maszynie wirtualnej; pozostałe hosty, które zostały dodane, zostały pomyślnie dodane. Dodaj ponownie hosty z powodu błędu przy użyciu opcji **Dodaj hosta** . Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, i spróbuj  ponownie wykonać odnajdywanie.

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>Operacja Azure Active Directory (AAD) nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi AAD (Identyfikator błędu: 60025)

Konto użytkownika platformy Azure użyte do zarejestrowania urządzenia nie ma dostępu do aplikacji usługi AAD określonej w komunikacie o błędzie. Sprawdź, czy jesteś właścicielem aplikacji usługi AAD. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o uprawnieniach aplikacji usługi AAD.


## <a name="discovery-issues"></a>Problemy z odnajdywaniem

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Po rozpoczęciu odnajdywania nie widzę odnalezionych maszyn wirtualnych w Azure Portal. Kafelki dotyczące oceny serwera i migracji serwera pokazują stan "odnajdywanie w toku"
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
Warunkowo wspierany system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md), dlatego należy rozważyć uaktualnienie systemu operacyjnego maszyny przed migracją na platformę Azure.
Niewspierany system operacyjny Linux | Maszyna może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. Rozważ uaktualnienie systemu operacyjnego do [zatwierdzonej wersji Linux](../virtual-machines/linux/endorsed-distros.md) przed przeprowadzeniem migracji na platformę Azure
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server, ze względu na to, że Azure Migrate nie może zidentyfikować gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji maszyny upewnij się, że system operacyjny działający na maszynie jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwany typ systemu operacyjnego (liczba bitów) | Maszyny wirtualne z 32-bitową wersją systemu operacyjnego mogą zostać uruchomione na platformie Azure, ale przed migracją do platformy Azure zaleca się uaktualnienie systemu operacyjnego z 32-bitowego do 64-bitowego.
Wymaga subskrypcji programu Visual Studio. | Na maszynie jest uruchomiony system operacyjny Windows, który jest obsługiwany tylko w ramach subskrypcji programu Visual Studio.
Nie znaleziono maszyny wirtualnej wymaganej wydajności magazynu. | Wydajność magazynu (IOPS/przepustowość) wymagana dla maszyny przekracza obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu maszyny.
Nie znaleziono maszyny wirtualnej dla wymaganej wydajności sieci. | Wydajność sieci (WE/wychodzącej) wymagana przez maszynę przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla maszyny.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji. | Użyj innej lokalizacji docelowej przed migracją.
Co najmniej jeden niewłaściwy dysk. | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku jest < 4 TB (jeśli nie, zmniejsz rozmiar dysku przed migracją na platformę Azure). Należy upewnić się, że wydajność (IOPS/przepustowość) wymagana przez poszczególne dyski jest obsługiwana przez [maszyny wirtualne zarządzane](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)przez platformę Azure.   
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją Usuń nieużywane karty sieciowe z maszyny.
Liczba dysków przekracza limit | Usuń nieużywane dyski z maszyny przed migracją.
Rozmiar dysku przekracza limit | Platforma Azure obsługuje dyski o rozmiarze do 4 TB. Zmniejsz dyski do mniejszej niż 4 TB przed migracją.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Brak dostępnych dysków dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej o żądanej liczbie rdzeni i pamięci | Platforma Azure nie może poprawić odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni maszyny lokalnej.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nie mogę określić Umowa Enterprise (EA) jako oferty platformy Azure we właściwościach oceny?
Azure Migrate: Server Assessment aktualnie nie obsługuje cennika opartego na umowach Enterprise Agreement (EA). Obejście polega na skorzystaniu z oferty platformy Azure „Płatność zgodnie z rzeczywistym użyciem” i użyciu właściwości „Discount” w celu określenia wszelkich otrzymanych rabatów niestandardowych. [Dowiedz się więcej o tym, w jaki sposób można dostosować ocenę](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Dlaczego program sprawdza, czy na maszynach wirtualnych z systemem Linux jest stosowany warunek "gotowe". Czy jest coś, co należy zrobić, aby rozwiązać ten problem?
W narzędziu Server Assessment istnieje znana luka polegająca na tym, że nie potrafi ono wykryć wersji pomocniczej systemu operacyjnego Linux zainstalowanego na lokalnych maszynach wirtualnych (na przykład w przypadku systemu RHEL 6.10 narzędzie Server Assessment obecnie wykrywa tylko RHEL 6 jako wersję systemu operacyjnego). Platforma Azure obsługuje tylko określone wersje systemu Linux, dlatego maszyny wirtualne z systemem Linux są obecnie oznaczane jako warunkowo gotowe w narzędziu Server Assessment. Możesz ręcznie upewnić się, że system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [dokumentację pomocy technicznej systemu Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) Kiedy sprawdzisz zatwierdzoną dystrybucję, możesz zignorować to ostrzeżenie.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Jednostka SKU maszyny wirtualnej zalecana przez ocenę serwera ma więcej rdzeni i większy rozmiar pamięci niż to, co jest przydzielone lokalnie. Dlaczego tak jest?
Zalecenie dotyczące jednostki SKU maszyny wirtualnej w narzędziu Server Assessment zależy od właściwości oceny. W narzędziu Server Assessment można utworzyć dwa rodzaje oceny: „Na podstawie wydajności” i „Jako lokalne”. W przypadku ocen opartych na wydajności Ocena serwera traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Ponadto w przypadku ustalania rozmiaru na podstawie wydajności brany jest pod uwagę współczynnik komfortu w celu określenia efektywnego użycia. W przypadku ustalania rozmiaru dla kryterium „Jako lokalne” dane dotyczące wydajności nie są brane pod uwagę i docelowa jednostka SKU jest zalecana na podstawie zasobów przydzielonych w środowisku lokalnym.

Załóżmy na przykład, że istnieje lokalna maszyna wirtualna z 4 rdzeniami i 8 GB pamięci z 50% użycia procesora CPU i 50% pamięci, a czynnik komfortu 1,3 został określony w ocenie. Jeśli kryterium ustalania wielkości oceny ma wartość "jako lokalna" jednostka SKU maszyny wirtualnej platformy Azure z 4 rdzeniami i 8 GB pamięci jest zalecana, jednak jeśli kryterium ustalania wielkości jest oparte na wydajności, w oparciu o efektywne wykorzystanie procesora i pamięci (50% z 4 rdzeni * 1,3 = 2,6 rdzenie i 50% 8 GB Pamięć * 1,3 = 5,3 GB pamięci), zalecana jest najtańsza jednostka SKU maszyny wirtualnej czterech rdzeni (najbliższa obsługiwana liczba rdzeni) i rozmiar pamięci wynoszącej 8 GB (najbliższy obsługiwany rozmiar pamięci). [Dowiedz się więcej o tym, jak narzędzie Server Assessment ustala rozmiar.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Jednostka SKU dysku zalecana przez ocenę serwera ma większy rozmiar niż to, co jest przydzielone lokalnie. Dlaczego tak jest?
Ustalanie rozmiaru dysków w narzędziu Server Assessment zależy od dwóch właściwości oceny — kryterium ustalania rozmiaru i typu magazynu. Jeśli kryterium ustalania wielkości jest "na podstawie wydajności" i typ magazynu jest ustawiony na "automatyczny", wartości IOPS i przepływności dysku są brane pod uwagę w celu zidentyfikowania typu dysku docelowego (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub dysków Premium). Następnie zalecana jest jednostka SKU dysku w obrębie danego typu dysku z uwzględnieniem wymagań dotyczących rozmiaru dysku lokalnego. Jeśli kryterium ustalania rozmiaru jest "oparte na wydajności" i typ magazynu to "Premium", zaleca się, aby jednostka SKU dysku Premium na platformie Azure była oparta na wymaganiach dotyczących liczby operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do ustalania rozmiaru dysków, gdy kryterium jest ustalanie rozmiaru jako lokalnego, a typ magazynu to HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium.

Na przykład jeśli masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, Ocena serwera będzie zalecać typ dysku w warstwie Premium (ze względu na wymagania dotyczące wyższych IOPS), a następnie zalecaną jednostkę SKU, która może obsługiwać wymagana liczba IOPS i rozmiar. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). W związku z tym, nawet jeśli rozmiar wymagany przez dysk lokalny wynosił 32 GB, narzędzie Server Assessment zaleciło dysk o większym rozmiarze z powodu wymogu dużej liczby operacji we/wy na sekundę dla dysku lokalnego.

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Dlaczego moje raporty oceny mówią "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing" dla niektórych maszyn wirtualnych?
Powyższe problemy są wymienione, gdy urządzenie Azure Migrate nie może zbierać danych wydajności dla lokalnych maszyn wirtualnych. Taka sytuacja może wystąpić, jeśli maszyny wirtualne są wyłączone w czasie trwania, dla którego tworzysz ocenę (ostatni dzień/jeden tydzień/miesiąc), ponieważ urządzenie nie może zebrać danych wydajności dla maszyny wirtualnej, gdy jest ona wyłączona. Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy na tych maszynach wirtualnych jest włączona pamięć dynamiczna. Istnieje znany problem, który obecnie jest spowodowany tym, że urządzenie Azure Migrate nie może zbierać informacji o wykorzystaniu pamięci dla maszyn wirtualnych, na których nie jest włączona pamięć dynamiczna. Należy zauważyć, że problem dotyczy tylko maszyn wirtualnych funkcji Hyper-V, a nie maszyn wirtualnych VMware. Jeśli brakuje któregokolwiek z liczników wydajności, Azure Migrate: Ocena serwera powraca do przyznanych rdzeni/pamięci i odpowiednio zaleca rozmiar maszyny wirtualnej.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Czy koszt licencji systemu operacyjnego maszyny wirtualnej uwzględniony w kosztach obliczeniowych szacowany przez ocenę serwera?
Obecnie narzędzie Server Assessment uwzględnia tylko koszt licencji systemu operacyjnego dla maszyn z systemem Windows, natomiast koszt licencji systemu operacyjnego dla maszyn z systemem Linux nie jest obecnie uwzględniany.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ ma historia wydajności i użycie percentylu na zaleceń dotyczących rozmiaru?
Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności. Narzędzie Server Assessment nieprzerwanie zbiera dane wydajności maszyn lokalnych i używa ich do określania zalecanych jednostek SKU maszyn wirtualnych i jednostek SKU dysków na platformie Azure. Poniżej przedstawiono, jak dane wydajności są zbierane przez narzędzie Server Assessment:
- Urządzenie Azure Migrate ciągle profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund dla maszyn wirtualnych VMware i co 30 sekund dla maszyn wirtualnych funkcji Hyper-V.
- Urządzenie podsumowuje 20/30-sekundowe próbki i tworzy jeden punkt danych co 10 minut. Aby utworzyć jeden punkt danych, urządzenie wybiera szczytową wartość z wszystkich 20/30-sekundowych próbek i wysyła ją do platformy Azure.
- Podczas tworzenia oceny w narzędziu Server Assessment (na podstawie czasu trwania wydajności i wartości percentylu historii wydajności) określana jest reprezentatywna wartość użycia. Na przykład jeśli historia wydajności ma jeden tydzień, a użycie percentyla to używany 95., Azure Migrate sortuje wszystkie 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera używany 95. percentyl jako wartość reprezentatywną.
Wartość 95. percentylu zapewnia, że zostaną zignorowane wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99. percentylu. Jeśli chcesz wybrać szczytowe użycie w danym okresie i nie pomijać żadnych elementów odstających, jako użycie percentyla wybierz 99. percentyl.

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
