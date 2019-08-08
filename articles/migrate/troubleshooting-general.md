---
title: Rozwiązywanie problemów z Azure Migrate | Microsoft Docs
description: Zawiera omówienie znanych problemów z usługą Azure Migrate, a także wskazówki dotyczące rozwiązywania problemów z typowymi błędami.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: fa1e7fcf89ccc06e429831191ba5dfce3cf33797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828315"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

[Azure Migrate](migrate-services-overview.md) oferuje centrum narzędzi do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Ten artykuł pomaga w rozwiązywaniu problemów z Azure Migrate, oceny serwera Azure Migrate i migracji serwera Azure Migrate.

## <a name="azure-migrate-project-issues"></a>Problemy dotyczące projektu Azure Migrate

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Nie mogę znaleźć istniejącego projektu Azure Migrate.

Istnieją [dwie wersje](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate. W zależności od wersji, w której został utworzony projekt, należy użyć jednej z następujących metod:

* Jeśli szukasz projektu utworzonego przy użyciu poprzedniej wersji (stare środowisko) Azure Migrate, wykonaj następujące kroki, aby znaleźć projekt:

    1. Przejdź do [Azure Portal](https://portal.azure.com)i wyszukaj pozycję **Azure Migrate**.
    2. Na pulpicie nawigacyjnym Azure Migrate zobaczysz transparent wskazujący dostęp do starszych projektów. Ten transparent zobaczysz tylko wtedy, gdy projekt został utworzony ze starym doświadczeniem. Wybierz transparent.

       ![Dostęp do istniejących projektów](./media/troubleshooting-general/access-existing-projects.png)

    3. Zobaczysz teraz listę istniejących projektów, które zostały utworzone przy użyciu poprzedniej wersji Azure Migrate.

* Jeśli szukasz projektu utworzonego z bieżącą wersją (nowe środowisko), wykonaj następujące kroki, aby znaleźć projekt:

    1. Przejdź do [Azure Portal](https://portal.azure.com)i wyszukaj pozycję **Azure Migrate**.
    2. Na pulpicie nawigacyjnym Azure Migrate przejdź do strony **serwery** w lewym okienku, a następnie wybierz pozycję **Zmień** w prawym górnym rogu.

       ![Przełącz do istniejącego projektu Azure Migrate](./media/troubleshooting-general/switch-project.png)

    3. Wybierz odpowiednią subskrypcję i projekt Azure Migrate.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Jak mogę utworzyć drugi Azure Migrate projekt?

Wykonaj następujące kroki, aby utworzyć nowy projekt Azure Migrate:

1. Przejdź do [Azure Portal](https://portal.azure.com)i wyszukaj pozycję **Azure Migrate**.
2. Na pulpicie nawigacyjnym Azure Migrate przejdź do strony **serwery** w lewym okienku, a następnie wybierz pozycję **Zmień** w prawym górnym rogu.

   ![Zmień projekt Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.

   ![Tworzenie drugiego projektu Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Które usługi Azure lokalizacje geograficzne Azure Migrate obsługiwać?

Zobacz listy dla oprogramowania [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Jak mogę usunąć projekty Azure Migrate i powiązane Log Analytics obszary robocze?

Po usunięciu projektu Azure Migrate, projekt migracji *i* metadane dotyczące odnalezionych maszyn zostaną usunięte. Jeśli jednak dołączysz obszar roboczy Log Analytics do narzędzia do oceny serwera, obszar roboczy Log Analytics nie zostanie automatycznie usunięty. (Ten sam obszar roboczy Log Analytics może być używany w wielu przypadkach użycia). Jeśli chcesz również usunąć obszar roboczy Log Analytics, musisz wykonać to ręcznie:

1. Przejdź do obszaru roboczego Log Analytics dołączonego do projektu.
     * Jeśli projekt migracji nie został jeszcze usunięty, można znaleźć link do obszaru roboczego z poziomu strony przegląd oceny serwera w sekcji podstawowe informacje.

       ![LA — obszar roboczy](./media/troubleshooting-general/loganalytics-workspace.png)

     * Jeśli projekt migracji został już usunięty, wybierz pozycję **grupy zasobów** w lewym okienku Azure Portal. Przejdź do grupy zasobów, w której został utworzony obszar roboczy, a następnie przejdź do niej.
2. Postępuj zgodnie z instrukcjami w temacie [Usuwanie obszaru roboczego usługi Azure log Analytics przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Tworzenie projektu migracji nie powiodło się z powodu błędu "żądania muszą zawierać nagłówki tożsamości użytkownika".

Ten problem może wystąpić w przypadku użytkowników, którzy nie mają dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. Gdy użytkownik zostanie dodany do dzierżawy usługi Azure AD po raz pierwszy, użytkownik otrzyma wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy. Użytkownicy muszą zaakceptować zaproszenie, aby pomyślnie dodać je do dzierżawy. Jeśli nie widzisz wiadomości e-mail, skontaktuj się z użytkownikiem, który ma już dostęp do dzierżawy, i poproś o ponowne wysłanie zaproszenia do Ciebie, wykonując czynności opisane w artykule [ponowne wysyłanie zaproszeń do użytkowników-Gości](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po otrzymaniu wiadomości e-mail z zaproszeniem musisz otworzyć wiadomość e-mail i wybrać link, aby zaakceptować zaproszenie. Następnie musisz wylogować się z Azure Portal i zalogować się ponownie. (Odświeżanie przeglądarki nie będzie działało). Następnie można rozpocząć tworzenie projektu migracji.

## <a name="appliance-issues"></a>Problemy z urządzeniem

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>Wdrożenie urządzenia Azure Migrate dla oprogramowania VMware nie powiodło się z "podany plik manifestu jest nieprawidłowy: Nieprawidłowy wpis manifestu OVF.

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate został poprawnie pobrany, sprawdzając jego wartość skrótu. Aby uzyskać wskazówki, zobacz [Przygotowywanie maszyn wirtualnych VMware do oceny i migracji na platformę Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli wdrożenie nadal kończy się niepowodzeniem i jeśli używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci Web vSphere. Jeśli wdrożenie nadal kończy się niepowodzeniem, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę wdrożenia go na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi, wykonując następujące czynności:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) za pomocą klienta sieci Web (https://<*adres IP hosta*>/UI).
   - Przejdź do**spisu zasobów** **domowych** > .
   - Wybierz pozycję **plik** > **Wdróż OVF szablon**. Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

### <a name="the-appliance-cant-connect-to-the-internet"></a>Urządzenie nie może połączyć się z Internetem.

Takie zachowanie może wystąpić, gdy komputer, którego używasz, znajduje się za serwerem proxy. Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy ich potrzebuje.
Jeśli używasz dowolnego serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, pamiętaj o dodaniu tych wymaganych adresów URL do listy dozwolonych:

- [Ocena serwera dla oprogramowania VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Ocena serwera dla funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Migracja serwera dla oprogramowania VMware (bez agentów)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Migracja serwera dla oprogramowania VMware (opartego na agentach)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Migracja serwera dla funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, należy zaimportować certyfikat serwera proxy na maszynę wirtualną urządzenia. Certyfikat serwera proxy można zaimportować, wykonując kroki opisane w [Azure Migrate urządzeniu](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Błąd 802: Błąd synchronizacji daty i godziny.

Zegar serwera może nie być zsynchronizowany z bieżącym czasem przez więcej niż pięć minut. Zmień czas zegara na maszynie wirtualnej modułu zbierającego tak, aby pasował do bieżącego czasu w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom polecenie **W32tm/TZ**.
3. Aby zsynchronizować godzinę, uruchom polecenie **w32tm/resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Nie można nawiązać połączenia. Błąd: UnableToConnectToServer.

Być może nie można nawiązać połączenia z *serwerem vCenter Server ServerName*. com: 9443. Szczegóły błędu wskazują, że nie ma punktu końcowego nasłuchu na*serwerze https://servername*. com: 9443/SDK, który może akceptować komunikat.

W takiej sytuacji należy sprawdzić, czy jest uruchomiona Najnowsza wersja urządzenia modułu zbierającego. Jeśli nie, Uaktualnij urządzenie do [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).

Jeśli problem nadal występuje w najnowszej wersji, maszyna modułu zbierającego może nie być w stanie rozpoznać określonej nazwy vCenter Server lub określony port może być nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający podejmie próbę nawiązania połączenia z numerem portu 443.

1. Wyślij polecenie ping *servername*. com z komputera modułu zbierającego.
2. Jeśli krok 1 zakończy się niepowodzeniem, spróbuj połączyć się z serwerem vCenter przy użyciu adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Sprawdź, czy serwer vCenter jest uruchomiony.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Urządzenie może nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate (Identyfikator błędu: 60052).

Ten błąd występuje, gdy konto platformy Azure użyte do zarejestrowania urządzenia ma niewystarczające uprawnienia. Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o wymaganych rolach i uprawnieniach platformy Azure.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Urządzenie może nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate (Identyfikator błędu: 60039).

Projekt Azure Migrate wybrany do zarejestrowania urządzenia może nie zostać znaleziony. W takiej sytuacji Rejestracja nie powiedzie się. Przejdź do Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów. Jeśli projekt nie istnieje, Utwórz nowy projekt Azure Migrate w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) na temat tworzenia nowego projektu Azure Migrate.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Nie można wykonać operacji zarządzania Azure Key Vault (Identyfikator błędu: 60030, 60031).

Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji. Upewnij się również, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o wymaganych rolach i uprawnieniach platformy Azure.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów (Identyfikator błędu: 60028).

Nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o maszynie wirtualnej. Pozostałe hosty zostały pomyślnie dodane. Dodaj hosty wymienione w tym błędzie ponownie przy użyciu opcji **Dodaj hosta** . Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie wykonać operację **zapisywania i uruchamiania odnajdywania** .

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD (Identyfikator błędu: 60025).

Ten błąd występuje, jeśli konto użytkownika platformy Azure użyte do zainicjowania odnajdywania różni się od konta użytego do zarejestrowania urządzenia. Możesz wykonać jedną z następujących czynności:
1. Upewnij się, że konto użytkownika, które inicjuje odnajdywanie, jest takie samo jak użyte do zarejestrowania urządzenia.
1. Podaj uprawnienia dostępu do aplikacji usługi AAD dla konta innego użytkownika, dla którego operacja odnajdywania kończy się niepowodzeniem.
1. Usuń grupę zasobów utworzoną wcześniej dla Azure Migrate projektu i Utwórz kolejną grupę zasobów, aby uruchomić ją ponownie.

[Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o uprawnieniach aplikacji usługi AAD.

## <a name="discovery-issues"></a>Problemy z odnajdywaniem

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Rozpoczęto odnajdywanie, ale nie widzisz odnalezionych maszyn wirtualnych w Azure Portal. Kafelki dotyczące **oceny serwera** i **migracji serwera** pokazują stan "odnajdywanie w toku".
Po rozpoczęciu odnajdywania urządzenia poczekaj chwilę na wykrycie wykrytych maszyn na Azure Portal. Odnalezienie oprogramowania VMware trwa około 15 minut, a przez co najmniej 2 minuty na dodany host na potrzeby odnajdywania funkcji Hyper-V. Jeśli nadal widzisz "odnajdywanie w toku" nawet po tych okresach oczekujących, wybierz pozycję **Odśwież** na karcie **serwery** . Powinno to spowodować wyświetlenie liczby odnalezionych serwerów na kafelkach oceny serwera i migracji serwera.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Używam urządzenia, które stale odnajduje środowisko lokalne, ale maszyny wirtualne, które zostały usunięte w środowisku lokalnym, są nadal wyświetlane w portalu.

Dane odnajdywania zebrane przez urządzenie zostaną odzwierciedlone w portalu do 30 minut. Jeśli nie widzisz aktualnych informacji nawet po 30 minutach, Odśwież dane, wykonując następujące czynności:

1. Przejdź do pozycji **serwery** > **Azure Migrate oceny serwera**i wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**
3. Wybierz pozycję **Odśwież agenta**. Zobaczysz tę opcję poniżej listy agentów.
4. Poczekaj na zakończenie operacji odświeżania. Na swoich maszynach wirtualnych powinna być teraz wyświetlana aktualna informacja.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nie widzę najnowszych informacji na lokalnych maszynach wirtualnych w portalu.

Dane odnajdywania zebrane przez urządzenie zostaną odzwierciedlone w portalu do 30 minut. Jeśli nie widzisz aktualnych informacji nawet po 30 minutach, Odśwież dane, wykonując następujące czynności:

1. Przejdź do pozycji **serwery** > **Azure Migrate oceny serwera**i wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**
3. Wybierz pozycję **Odśwież agenta**. Zobaczysz tę opcję poniżej listy agentów.
4. Poczekaj na zakończenie operacji odświeżania. Na swoich maszynach wirtualnych powinna być teraz wyświetlana aktualna informacja.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nie można nawiązać połączenia z hostem lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu WinRM: 0x803381B9 (Identyfikator błędu: 50004).
Ten błąd występuje, gdy usługa Azure DNS dla urządzenia nie może rozpoznać podanej nazwy klastra lub hosta. Jeśli ten błąd wystąpi w klastrze, spróbuj podać w pełni kwalifikowaną nazwę domeny (FQDN) klastra.

Ten błąd może również pojawić się w przypadku hostów w klastrze. W takim przypadku urządzenie może połączyć się z klastrem, ale klaster zwraca nazwy hostów, które nie są nazwami FQDN.

Aby rozwiązać ten problem, zaktualizuj plik Hosts na urządzeniu poprzez dodanie mapowania adresów IP i nazw hostów:
1. Otwórz program Notepad jako administrator. Następnie otwórz plik C:\Windows\System32\Drivers\etc\hosts.
2. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym widzisz ten błąd.
3. Zapisz i zamknij plik hosts.
4. Możesz sprawdzić, czy urządzenie może nawiązać połączenie z hostami przy użyciu aplikacji do zarządzania urządzeniami. Po 30 minutach powinno być możliwe wyświetlenie najnowszych informacji dla tych hostów na Azure Portal.


## <a name="assessment-issues"></a>Problemy z oceną

### <a name="azure-readiness-issues"></a>Problemy z gotowością platformy Azure

Problem | Korygowanie
--- | ---
Nieobsługiwany typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zalecamy przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Do obsługi migracji takich maszyn wirtualnych można użyć migracji serwera Azure Migrate. Spowoduje to przekonwertowanie typu rozruchowego maszyny wirtualnej na system BIOS podczas migracji.
Warunkowo obsługiwane systemy operacyjne Windows | System operacyjny przeszedłł datę końca okresu obsłudze i potrzebuje niestandardowej umowy pomocy technicznej (CSA) w celu uzyskania [pomocy technicznej na platformie Azure](https://aka.ms/WSosstatement). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](https://aka.ms/WSosstatement). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny.
Warunkowo wspierany system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny.
Niewspierany system operacyjny Linux | Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego do wersji zaświadczonej w systemie [Linux](../virtual-machines/linux/endorsed-distros.md) .
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server. To zachowanie uniemożliwia Azure Migrate weryfikacji gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji maszyny upewnij się, że system operacyjny maszyny jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwany typ systemu operacyjnego (liczba bitów) | Maszyny wirtualne z 32-bitową wersją systemu operacyjnego mogą przeprowadzić rozruch na platformie Azure, ale zalecamy uaktualnienie systemu operacyjnego maszyny wirtualnej do wersji 64-bitowej przed migracją na platformę Azure.
Wymaga subskrypcji Microsoft Visual Studio | Na maszynie jest uruchomiony system operacyjny klienta systemu Windows, który jest obsługiwany tylko przez subskrypcję programu Visual Studio.
Nie znaleziono maszyny wirtualnej dla żądanej wartości wydajności magazynu | Wydajność magazynu (operacje wejścia/wyjścia na sekundę [IOPS] i przepływność) wymagane dla maszyny przekraczają obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu maszyny.
Nie znaleziono maszyny wirtualnej dla żądanej wartości wydajności sieci | Wydajność sieci (WE/wychodzącej) wymagana przez maszynę przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla maszyny.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji | Użyj innej lokalizacji docelowej przed migracją.
Znaleziono co najmniej jeden nieodpowiedni dysk | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Azure Migrate: Ocena serwera obecnie nie obsługuje SSD w warstwie Ultra dysków i ocenia dyski w oparciu o limity dysków dla dysków zarządzanych w warstwie Premium (32 TB).  Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku jest < 64 TB (obsługiwane przez dyski SSD w warstwie Ultra), w przeciwnym razie Zmniejsz rozmiar dysku przed przeprowadzeniem migracji na platformę Azure lub Użyj wielu dysków na platformie [](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) Azure, aby uzyskać wyższe limity magazynu. Należy upewnić się, że wydajność (IOPS i przepustowość) wymagana przez poszczególne dyski są obsługiwane przez [maszyny wirtualne zarządzane](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)przez platformę Azure.
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją Usuń nieużywane karty sieciowe z maszyny.
Liczba dysków przekracza limit | Usuń nieużywane dyski z maszyny przed migracją.
Rozmiar dysku przekracza limit | Azure Migrate: Ocena serwera obecnie nie obsługuje SSD w warstwie Ultra dysków i ocenia dyski w oparciu o limity dysku Premium (32 TB). Platforma Azure obsługuje jednak dyski o rozmiarze do 64 TB (obsługiwane przez dyski SSD w warstwie Ultra). Zmniejsz liczbę dysków do mniej niż 64 TB przed migracją lub Użyj wielu dysków na platformie Azure i [Rozłącz je](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , aby uzyskać wyższe limity magazynu.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Brak dostępnych dysków dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej o żądanej liczbie rdzeni i pamięci | Platforma Azure nie może znaleźć odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni maszyny lokalnej.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nie można określić Umowa Enterprise (EA) jako oferty platformy Azure we właściwościach oceny.
Azure Migrate oceny serwera nie obsługuje obecnie cen opartych na Umowa Enterprise (EA). Aby obejść to ograniczenie, Użyj pozycji **płatność zgodnie z rzeczywistym** użyciem jako oferty platformy Azure i Użyj właściwości **rabatu** , aby określić dowolny otrzymany rabat niestandardowy. [Dowiedz się więcej o tym, w jaki sposób można dostosować ocenę](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Dlaczego jest oznaczana Ocena serwera dla moich maszyn wirtualnych systemu Linux "warunkowo"?
Istnieje znana przerwa w ocenie serwera, która uniemożliwia wykrycie pomocniczej wersji systemu operacyjnego Linux zainstalowanej na lokalnych maszynach wirtualnych (na przykład w przypadku RHEL 6,10, obecnie Ocena serwera wykrywa tylko RHEL 6 jako wersję systemu operacyjnego). Ponieważ platforma Azure poświadcza tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako gotowe do oceny serwera. Możesz określić, czy system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [dokumentację pomocy technicznej systemu Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) Po zweryfikowaniu poświadczonej dystrybucji można zignorować to ostrzeżenie.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Dlaczego jednostka SKU maszyny wirtualnej zalecana przez ocenę serwera ma więcej rdzeni i więcej pamięci niż alokacja lokalna?
Zalecenie dotyczące jednostki SKU maszyny wirtualnej w narzędziu Server Assessment zależy od właściwości oceny. Można utworzyć dwa rodzaje ocen w ocenie serwera: *Oparta na wydajności* i *jako lokalna*. W przypadku ocen opartych na wydajności Ocena serwera traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Ponadto w przypadku ustalania wielkości na podstawie wydajności czynnik komfortu jest uznawany za efektywne wykorzystanie. W przypadku lokalnego określania wielkości dane wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana w zależności od tego, co jest przydzielono lokalnie.

Załóżmy na przykład, że istnieje lokalna maszyna wirtualna z 4 rdzeniami i 8 GB pamięci dzięki 50% użycia procesora CPU i 50% wykorzystania pamięci, a czynnik komfortu 1,3 został określony w ocenie. Jeśli kryteria ustalania wielkości oceny są **tak samo lokalne**, zaleca się używanie jednostki SKU maszyny wirtualnej platformy Azure z 4 rdzeniami i 8 gigabajtów (GB) pamięci.

Należy jednak przyjąć, że kryteria ustalania wielkości są oparte na wydajności. W oparciu o efektywne wykorzystanie procesora i pamięci (50% z 4 rdzeni * 1,3 = 2,6 rdzenie i 50% 8 GB pamięci * 1,3 = 5,3 GB pamięci) zaleca się użycie najtańszej jednostki SKU maszyny wirtualnej czterech rdzeni (najbliższej obsługiwanej liczby rdzeni) i 8 GB pamięci (najbliższy obsługiwany rozmiar pamięci). [Dowiedz się więcej o tym, jak narzędzie Server Assessment ustala rozmiar.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Dlaczego jednostka SKU dysku jest zalecana przez ocenę serwera większą niż alokacja lokalna?
Ustalanie rozmiarów dysków w ocenie serwera zależy od dwóch właściwości oceny: ustalania rozmiarów kryteriów i typu magazynu. Jeśli kryteria ustalania wielkości są **oparte na wydajności** , a typ magazynu jest ustawiony na **automatyczny**, wartości IOPS i przepływność dysku są brane pod uwagę w celu zidentyfikowania typu dysku docelowego (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub dysków Premium). Zalecana jest jednostka SKU dysku z typu dysku, a to zalecenie uwzględnia wymagania dotyczące rozmiaru dysku lokalnego. Jeśli kryterium ustalania rozmiaru jest **oparte na wydajności**, a typ magazynu to **Premium**, zalecana jest jednostka SKU dysku Premium na platformie Azure na podstawie wymagań operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do przeprowadzania ustalania rozmiarów dysków, gdy kryteria ustalania wielkości są **jako lokalne** , a typ magazynu to **HDD w warstwie Standardowa**, **SSD w warstwie Standardowa**lub **Premium**.

Na przykład jeśli masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, Ocena serwera będzie zalecać typ dysku w warstwie Premium (z powodu wyższych wymagań IOPS), a następnie zalecać dyskową jednostkę SKU, która może obsługiwać wymagana liczba IOPS i rozmiar. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). Mimo że rozmiar wymagany przez dysk lokalny to 32 GB, Ocena serwera zaleca większy dysk z powodu dużego wymagania IOPS dysku lokalnego.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Dlaczego mój raport oceny wskazuje "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing" dla niektórych maszyn wirtualnych?
Te problemy są zgłaszane, gdy urządzenie Azure Migrate nie może zbierać danych o wydajności lokalnych maszyn wirtualnych. Takie zachowanie może się zdarzyć, jeśli maszyny wirtualne są wyłączone na czas trwania podczas tworzenia oceny (ostatni dzień/tydzień/miesiąc). Urządzenie nie może zbierać danych wydajności dla maszyny wirtualnej, gdy jest ona wyłączona. Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy na tych maszynach wirtualnych jest włączona pamięć dynamiczna. Istnieje znany problem polegający na tym, że urządzenie Azure Migrate nie może zbierać danych użycia pamięci dla maszyn wirtualnych, na których nie jest włączona pamięć dynamiczna. Ten problem dotyczy tylko maszyn wirtualnych funkcji Hyper-V, a nie maszyn wirtualnych VMware. Jeśli brakuje któregokolwiek z liczników wydajności, Azure Migrate oceny serwera powróci do przyznanych rdzeni i pamięci, a następnie zaleca rozmiar maszyny wirtualnej.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Czy koszt licencji systemu operacyjnego maszyny wirtualnej uwzględniony w kosztach obliczeniowych szacowany przez ocenę serwera?
Ocena serwera uwzględnia obecnie tylko koszt licencji systemu operacyjnego dla maszyn z systemem Windows. Koszty licencji systemu operacyjnego dla maszyn z systemem Linux nie są obecnie uwzględniane.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ ma historia wydajności i użycie percentylu na zalecenia dotyczące rozmiaru?
Te właściwości mają zastosowanie tylko do ustalania rozmiarów na podstawie wydajności. Narzędzie Server Assessment nieprzerwanie zbiera dane wydajności maszyn lokalnych i używa ich do określania zalecanych jednostek SKU maszyn wirtualnych i jednostek SKU dysków na platformie Azure. Dane dotyczące wydajności są zbierane przez ocenę serwera w następujący sposób:
- Urządzenie Azure Migrate ciągle profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund dla maszyn wirtualnych VMware i co 30 sekund dla maszyn wirtualnych funkcji Hyper-V.
- Urządzenie zbiera próbki 20 sekund i 30 sekund, aby utworzyć jeden punkt danych na każde 10 minut. Aby utworzyć pojedynczy punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych i 30-sekundowych, a następnie wysyła je do platformy Azure.
- Podczas tworzenia oceny w narzędziu Server Assessment (na podstawie czasu trwania wydajności i wartości percentylu historii wydajności) określana jest reprezentatywna wartość użycia. Na przykład jeśli historia wydajności ma jeden tydzień, a wykorzystanie percentylu to używany 95., Azure Migrate sortuje wszystkie 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera używany 95. percentyl jako wartość reprezentatywną.
Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu. Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, należy wybrać 99 percentyl dla użycia percentylu.

## <a name="dependency-visualization-issues"></a>Problemy wizualizacji zależności

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nie mogę znaleźć funkcji wizualizacji zależności dla projektów Azure Government.

Azure Migrate zależy od Service Map funkcji wizualizacji zależności. Ponieważ Service Map jest obecnie niedostępna w Azure Government, ta funkcja nie jest dostępna w Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Zainstalowano Microsoft Monitoring Agent (MMA) i agenta zależności na moich lokalnych maszynach wirtualnych, ale zależności są teraz wyświetlane w portalu Azure Migrate.

Po zainstalowaniu agentów, Azure Migrate zwykle trwa 15-30 minut, aby wyświetlić zależności w portalu. Jeśli oczekujesz przez ponad 30 minut, upewnij się, że MMA może komunikować się z obszarem roboczym pakietu OMS, wykonując następujące kroki.

Dla maszyny wirtualnej z systemem Windows:
1. Przejdź do panelu sterowania i zacznij Microsoft Monitoring Agent.
2. Na karcie **Azure log Analytics (OMS)** w oknie dialogowym **właściwości Microsoft Monitoring Agent** upewnij się, że **stan** obszaru roboczego to zielony.
3. Jeśli stan jest inny niż zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do MMA.

      ![Okno dialogowe właściwości MMA](./media/troubleshooting-general/mma-status.png)

W przypadku maszyny wirtualnej z systemem Linux upewnij się, że polecenia instalacji usługi MMA i agenta zależności powiodły się.

### <a name="what-operating-systems-does-mma-support"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

 [Poniżej znajduje się lista systemów operacyjnych Windows obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
[Poniżej znajduje się lista systemów operacyjnych Linux obsługiwanych przez program MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Jakie systemy operacyjne obsługuje Agent zależności?

[Poniżej znajduje się lista systemów operacyjnych Windows obsługiwanych przez agenta zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems). [Poniżej znajduje się lista systemów operacyjnych Linux obsługiwanych przez agenta zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Nie można wizualizować zależności w Azure Migrate przez okres więcej niż jeden godzinę.
W Azure Migrate można wizualizować zależności przez maksymalnie jedną godzinę. Chociaż Azure Migrate pozwala na powrót do określonej daty w ostatnim miesiącu, maksymalny czas trwania wizualizacji zależności wynosi godzinę.

Można na przykład użyć funkcji czas trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednej godziny. Można jednak użyć dzienników Azure Monitor, aby [wykonać zapytanie o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Nie można wizualizować zależności dla grup, które mają więcej niż 10 maszyn wirtualnych.
Możesz [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , które mają maksymalnie 10 maszyn wirtualnych, jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy poddzielenie grupy na mniejsze grupy i wizualizację zależności.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Agenci są instalowani i używali wizualizacji zależności do tworzenia grup. Teraz po zakończeniu pracy w trybie failover maszyny pokazują akcję "Zainstaluj agenta" zamiast "Wyświetl zależności".
* Po zaplanowanym lub nieplanowanym przejściu w tryb failover maszyny lokalne są wyłączone, a na platformie Azure są równoważne maszyny. Te maszyny uzyskują inny adres MAC. Mogą uzyskać inny adres IP na podstawie tego, czy użytkownik wybrał opcję zachowania lokalnego adresu IP.

  Jeśli oba adresy MAC i IP różnią się, Azure Migrate nie kojarzy maszyn lokalnych z dowolnymi Service Map danymi zależności. Zamiast tego prosi użytkownika o zainstalowanie agentów zamiast wyświetlania zależności.
* Po zakończeniu testowania trybu failover maszyny lokalne pozostają włączone zgodnie z oczekiwaniami. Równoważne komputery z systemem na platformie Azure uzyskują różne adresy MAC i mogą uzyskiwać różne adresów IP. Jeśli użytkownik nie zablokuje wychodzącego ruchu dziennika Azure Monitor z tych maszyn, Azure Migrate nie będzie kojarzyć maszyn lokalnych z żadnymi Service Map danymi zależności i poprosi użytkowników o zainstalowanie agentów zamiast wyświetlania zależności.

## <a name="collect-azure-portal-logs"></a>Zbieranie dzienników Azure Portal

**Jak mogę zbierać Azure Portal dzienników ruchu sieciowego?**

1. Otwórz przeglądarkę, przejdź [do portalu](https://portal.azure.com)i zaloguj się.
2. Naciśnij klawisz F12, aby rozpocząć Narzędzia deweloperskie. W razie konieczności Wyczyść ustawienie **Wyczyść wpisy przy nawigacji** .
3. Wybierz kartę **Sieć** i Rozpocznij przechwytywanie ruchu sieciowego:
   - W programie Chrome wybierz opcję **Zachowaj dziennik**. Nagrywanie powinno być uruchamiane automatycznie. Czerwony okrąg wskazuje na to, że ruch jest przechwytywany. Jeśli czerwony okrąg nie jest wyświetlany, wybierz czarny okrąg, aby rozpocząć.
   - W programie Microsoft Edge i Internet Explorer nagrywanie powinno być uruchamiane automatycznie. Jeśli tak nie jest, wybierz zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas rejestrowania, zatrzymywania rejestrowania i zapisywania kopii zapisanego działania:
   - W programie Chrome kliknij prawym przyciskiem myszy i wybierz pozycję **Zapisz jako Har z zawartością**. Ta akcja kompresuje i eksportuje dzienniki jako plik. HAR.
   - W przeglądarce Microsoft Edge lub Internet Explorer wybierz opcję **Eksportuj ruch** przechwycony. Ta akcja kompresuje i eksportuje dziennik.
6. Wybierz kartę **konsola** , aby sprawdzić, czy występują ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
   - W programie Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz pozycję **Zapisz jako**, aby wyeksportować i zip log.
   - W przeglądarce Microsoft Edge lub Internet Explorer, kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystko**.
7. Zamknij Narzędzia deweloperskie.
