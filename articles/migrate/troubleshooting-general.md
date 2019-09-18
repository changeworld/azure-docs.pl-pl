---
title: Rozwiązywanie problemów z Azure Migrate | Microsoft Docs
description: Zawiera omówienie znanych problemów z usługą Azure Migrate, a także wskazówki dotyczące rozwiązywania problemów z typowymi błędami.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 49c43d393ef0722424088e0073942b56787f1bc7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067809"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

[Azure Migrate](migrate-services-overview.md) oferuje centrum narzędzi do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Ten artykuł pomaga w rozwiązywaniu problemów dotyczących Azure Migrate, oceny serwera Azure Migrate i migracji serwera Azure Migrate.


## <a name="find-a-project"></a>Znajdź projekt

Istnieją [dwie wersje](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.


Jeśli projekt Azure Migrate został utworzony w bieżącej wersji Azure Migrate, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Migrate**.
2. Na **serwerach**> pulpitu nawigacyjnego Azure Migrate wybierz pozycję **Zmień** w prawym górnym rogu.

    ![Przełącz do istniejącego projektu Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Wybierz odpowiednią subskrypcję i projekt Azure Migrate.


Jeśli projekt został utworzony w poprzedniej wersji Azure Migrate, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Migrate**.
2. Na pulpicie nawigacyjnym Azure Migrate, jeśli projekt został utworzony w poprzedniej wersji, pojawi się transparent odwołujący się do starszych projektów. Wybierz transparent.

    ![Dostęp do istniejących projektów](./media/troubleshooting-general/access-existing-projects.png)

3. Przejrzyj listę starych projektów.


## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

Utwórz nowy projekt Azure Migrate w następujący sposób:

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Migrate**.
2. Na **Azure Migrate > pulpitu**nawigacyjnego wybierz pozycję **Zmień** w prawym górnym rogu.

   ![Zmień projekt Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Aby utworzyć nowy projekt, wybierz **pozycję kliknij tutaj**.

   ![Tworzenie drugiego projektu Azure Migrate](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Przejrzyj obsługiwane lokalizacje geograficzne

Przejrzyj obsługiwane lokalizacje geograficzne dla oprogramowania [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Usuń projekty/obszary robocze

Podczas usuwania Azure Migrate projektów i Log Analytics obszarów roboczych należy pamiętać, że:

- Po usunięciu projektu Azure Migrate, projekt *i* metadane dotyczące odnalezionych maszyn są usuwane.
- Jeśli dołączono obszar roboczy Log Analytics do narzędzia do oceny serwera, obszar roboczy nie zostanie automatycznie usunięty.
- Ten sam obszar roboczy Log Analytics może być używany w wielu scenariuszach.
- Jeśli chcesz usunąć obszar roboczy Log Analytics, musisz to zrobić ręcznie.


### <a name="delete-a-project"></a>Usuwanie projektu

Aby usunąć projekt w bieżącej wersji Azure Migrate:

1. Otwórz grupę zasobów platformy Azure, w której projekt został utworzony.
2. Na stronie Grupa zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt migracji, który chcesz usunąć. Typ zasobu to Microsoft. zmigrować/migrateprojects i usuwa go.

Aby usunąć projekt w starszej wersji Azure Migrate:

1. Otwórz grupę zasobów platformy Azure, w której projekt został utworzony.
2. Wybierz projekt migracji, który chcesz usunąć. Typ zasobu to projekt migracji, a następnie jego usunięcie.


### <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Przejdź do obszaru roboczego Log Analytics dołączonego do projektu.
* Jeśli projekt Azure Migrate nie został usunięty, można znaleźć link do obszaru roboczego w**ocenie serwera** **Essentials** > .
       ![LA — obszar roboczy](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Błąd "żądania muszą zawierać nagłówki tożsamości użytkownika"

Podczas tworzenia projektu ten błąd może wskazywać, że nie masz dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji.

- Gdy po raz pierwszy dodasz do dzierżawy usługi Azure AD, otrzymasz wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy.
- Zaakceptuj zaproszenie do pomyślnego dodania do dzierżawy.
    - Jeśli nie widzisz wiadomości e-mail, skontaktuj się z użytkownikiem w celu uzyskania dostępu do dzierżawy i poproś o [ponowne wysłanie zaproszenia](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) do Ciebie.
    - Po odebraniu wiadomości e-mail z zaproszeniem Otwórz ją i wybierz link, aby zaakceptować zaproszenie. Następnie wyloguj się z Azure Portal i zaloguj się ponownie. (odświeżanie przeglądarki nie będzie działało). Następnie można rozpocząć tworzenie projektu migracji.


## <a name="error-invalid-ovf-manifest-entry"></a>Błąd "nieprawidłowy wpis manifestu OVF"

Jeśli zostanie wyświetlony komunikat o błędzie "podany plik manifestu jest nieprawidłowy: Nieprawidłowy wpis manifestu OVF ", wykonaj następujące czynności:

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate został poprawnie pobrany, sprawdzając jego wartość skrótu. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli wdrożenie nadal kończy się niepowodzeniem i używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci Web vSphere. Jeśli wdrożenie nadal kończy się niepowodzeniem, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę jego wdrożenia na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) z klientem sieci Web (https://<*adres IP hosta*>/UI).
   -  > W obszarze spis sprzętu wybierz pozycję plik Wdróż OVF szablon. >  Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

## <a name="appliance-cant-connect-to-the-internet"></a>Urządzenie nie może połączyć się z Internetem

Taka sytuacja może wystąpić, jeśli komputer urządzenia znajduje się za serwerem proxy.

- Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy ich potrzebuje.
- Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Dodaj te adresy URL do listy dozwolonych:

    - [Adresy URL dla oceny oprogramowania VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Adresy URL dla oceny funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [Adresy URL migracji bez agentów programu VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [Adresy URL migracji na podstawie agenta VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Adresy URL migracji funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, zaimportuj certyfikat serwera proxy na maszynę wirtualną urządzenia, wykonując [następujące kroki](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Błąd: synchronizacja daty/czasu

Błąd dotyczący synchronizacji daty i godziny (802) wskazuje, że zegar serwera może nie być zsynchronizowany z bieżącym czasem przez więcej niż pięć minut. Zmień czas zegara na maszynie wirtualnej modułu zbierającego tak, aby był zgodny z bieżącą godziną:

1. Otwórz wiersz polecenia administratora na maszynie wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom polecenie **W32tm/TZ**.
3. Aby zsynchronizować godzinę, uruchom polecenie **w32tm/resync**.


## <a name="error-unabletoconnecttoserver"></a>Błąd: UnableToConnectToServer

Jeśli wystąpi błąd połączenia, może nie być możliwe nawiązanie połączenia z serwerem vCenter Server *servername*. com: 9443. Szczegóły błędu wskazują, że nie ma punktu końcowego nasłuchu na*serwerze https://servername*. com: 9443/SDK, który może akceptować komunikat.

- Sprawdź, czy korzystasz z najnowszej wersji urządzenia. Jeśli nie, Uaktualnij urządzenie do [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Jeśli problem nadal występuje w najnowszej wersji, urządzenie może nie być w stanie rozpoznać określonej nazwy vCenter Server lub określony port może być nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający podejmie próbę nawiązania połączenia z numerem portu 443.

    1. Wyślij polecenie ping *servername*. com z urządzenia.
    2. Jeśli krok 1 nie powiedzie się, spróbuj połączyć się z serwerem vCenter przy użyciu adresu IP.
    3. Określ poprawny numer portu, aby nawiązać połączenie z vCenter Server.
    4. Sprawdź, czy vCenter Server działa.


## <a name="error-appliance-might-not-be-registered"></a>Błąd: Urządzenie może nie być zarejestrowane

- Błąd 60052, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" występuje, jeśli konto platformy Azure użyte do zarejestrowania urządzenia ma niewystarczające uprawnienia.
    - Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
    - [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) na temat wymaganych ról i uprawnień platformy Azure.
- Błąd 60039, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" może wystąpić, jeśli rejestracja nie powiedzie się, ponieważ nie można odnaleźć projektu Azure Migrate użytego do rejestracji urządzenia.
    - W Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów.
    - Jeśli projekt nie istnieje, Utwórz nowy projekt Azure Migrate w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) utworzyć nowy projekt.

## <a name="error-key-vault-management-operation-failed"></a>Błąd: Nie można wykonać operacji zarządzania Key Vault

Jeśli zostanie wyświetlony błąd 60030 lub 60031, "Azure Key Vault operacji zarządzania nie powiodła się", wykonaj następujące czynności:
- Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
- Upewnij się, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację.
- Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o wymaganych rolach i uprawnieniach platformy Azure.

## <a name="fix-discovery-couldnt-be-initiated"></a>Wiązane Nie można zainicjować odnajdywania

Błąd 60028: "Nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów "oznacza, że nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o maszynie wirtualnej. Pozostałe hosty zostały pomyślnie dodane.

- Ponownie Dodaj hosty wymienione w błędzie przy użyciu opcji **Dodaj hosta** .
- Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie wykonać operację **zapisywania i uruchamiania odnajdywania** .

## <a name="fix-azure-ad-operation-failed-60025"></a>Wiązane Operacja usługi Azure AD nie powiodła się (60025)

Błąd 60025: "Operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD "występuje, gdy konto użytkownika platformy Azure używane do inicjowania odnajdywania jest inne niż konto użyte do zarejestrowania urządzenia. Wykonaj jedną z następujących czynności:

- Upewnij się, że konto użytkownika, które inicjuje odnajdywanie, jest takie samo jak użyte do zarejestrowania urządzenia.
- Podaj Azure Active Directory uprawnienia dostępu do aplikacji dla konta użytkownika, dla którego operacja odnajdywania kończy się niepowodzeniem.
- Usuń grupę zasobów utworzoną wcześniej dla projektu Azure Migrate. Utwórz kolejną grupę zasobów, aby ponownie uruchomić.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o Azure Active Directory uprawnieniach aplikacji.


## <a name="discovered-vms-not-in-portal"></a>Odnalezione maszyny wirtualne nie są w portalu

Po rozpoczęciu odnajdywania, aby **Ocena serwera** i **Migracja serwera** były wyświetlane **w toku**, ale nie są jeszcze wyświetlane maszyny wirtualne w portalu, należy zwrócić uwagę na następujące kwestie:

- Po rozpoczęciu odnajdywania z urządzenia przez program VMware VM Discovery zajmie około 15 minut, a dla każdego dodanego hosta na potrzeby odnajdywania maszyn wirtualnych z funkcją Hyper-V może znajdować się co najmniej dwie minuty.
- Jeśli **odnajdywanie** będzie nadal widoczne nawet po upływie tych okresów oczekujących, wybierz pozycję **Odśwież** na karcie **serwery** . Powinno to wskazywać liczbę odnalezionych serwerów w **ocenie serwera** i **migracji serwera**.


## <a name="deleted-vms-in-the-portal"></a>Usunięte maszyny wirtualne w portalu

Jeśli urządzenie zostało wdrożone w sposób ciągły do wykrywania środowiska lokalnego, ale usunięte maszyny wirtualne nadal są wyświetlane w portalu, należy zwrócić uwagę na następujące kwestie:  

- Dane odnajdywania zebrane przez urządzenie zostaną odzwierciedlone w portalu do 30 minut.
- Jeśli nie widzisz aktualnych informacji po 30 minutach, Odśwież dane, wykonując następujące czynności:

    1. W obszarze **serwery** > **Azure Migrate oceny serwera**wybierz pozycję **Przegląd**.
    2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**
    3. Wybierz pozycję **Odśwież agenta**.
    1. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="vm-information-isnt-in-the-portal"></a>Informacje o maszynie wirtualnej nie są w portalu

- Dane odnajdywania zebrane przez urządzenie zostaną odzwierciedlone w portalu do 30 minut.
- Jeśli nie widzisz aktualnych informacji po 30 minutach, Odśwież dane, wykonując następujące czynności:

    1. W obszarze **serwery** > **Azure Migrate oceny serwera**wybierz pozycję **Przegląd**.
    2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**
    3. Wybierz pozycję **Odśwież agenta**.
    1. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Wiązane Nie można nawiązać połączenia z hostem lub klastrem

Błąd 50004: "Nie można nawiązać połączenia z hostem lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu WinRM: 0x803381B9 "może wystąpić, jeśli usługa Azure DNS dla urządzenia nie może rozpoznać dostarczonej nazwy klastra lub hosta.

- Jeśli ten błąd jest wyświetlany w klastrze, nazwa FQDN klastra.
- Ten błąd może również pojawić się w przypadku hostów w klastrze. Oznacza to, że urządzenie może połączyć się z klastrem, ale klaster zwraca nazwy hostów, które nie są nazwami FQDN. Aby rozwiązać ten problem, zaktualizuj plik Hosts na urządzeniu poprzez dodanie mapowania adresów IP i nazw hostów:
    1. Otwórz Notatnik jako administrator.
    2. Otwórz plik C:\Windows\System32\Drivers\etc\hosts.
    3. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym widzisz ten błąd.
    4. Zapisz i zamknij plik hosts.
    5. Sprawdź, czy urządzenie może nawiązać połączenie z hostami przy użyciu aplikacji do zarządzania urządzeniami. Po 30 minutach powinny zostać wyświetlone najnowsze informacje dotyczące tych hostów w Azure Portal.



## <a name="fix-assessment-readiness"></a>Napraw gotowość do oceny

Rozwiąż problemy z gotowością do oceny w następujący sposób:

**Wykonaj** | **Fix**
--- | ---
Nieobsługiwany typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zalecamy przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Do obsługi migracji takich maszyn wirtualnych można użyć migracji serwera Azure Migrate. Spowoduje to przekonwertowanie typu rozruchowego maszyny wirtualnej na system BIOS podczas migracji.
Warunkowo obsługiwany system operacyjny Windows | System operacyjny przeszedłł datę końca okresu obsłudze i potrzebuje niestandardowej umowy pomocy technicznej (CSA) w celu uzyskania [pomocy technicznej na platformie Azure](https://aka.ms/WSosstatement). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](https://aka.ms/WSosstatement). Rozważ uaktualnienie maszyny przed przeprowadzeniem migracji na platformę Azure.
Warunkowo wspierany system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md). Rozważ uaktualnienie maszyny przed przeprowadzeniem migracji na platformę Azure.
Niewspierany system operacyjny Linux | Maszyna może zaczynać się na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie do [zatwierdzonej wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) .
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server. To zachowanie uniemożliwia Azure Migrate weryfikacji gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji maszyny upewnij się, że system operacyjny jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwana wersja bitowa | Maszyny wirtualne z 32-bitowymi systemami operacyjnymi mogą przeprowadzić rozruch na platformie Azure, ale zalecamy uaktualnienie do wersji 64-bitowej przed migracją na platformę Azure.
Wymaga subskrypcji Microsoft Visual Studio | Na komputerze jest uruchomiony system operacyjny klienta systemu Windows, który jest obsługiwany tylko przez subskrypcję programu Visual Studio.
Nie znaleziono maszyny wirtualnej wymaganej wydajności magazynu | Wydajność magazynu (operacje wejścia/wyjścia na sekundę [IOPS] i przepływność) wymagane dla maszyny przekraczają obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu maszyny.
Nie znaleziono maszyny wirtualnej dla wymaganej wydajności sieci | Wydajność sieci (WE/wychodzącej) wymagana przez maszynę przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla maszyny.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji | Użyj innej lokalizacji docelowej przed migracją.
Znaleziono co najmniej jeden nieodpowiedni dysk | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Z<br/><br/> Azure Migrate: Ocena serwera obecnie nie obsługuje SSD w warstwie Ultra dysków i ocenia dyski w oparciu o limity dysków dla dysków zarządzanych w warstwie Premium (32 TB).<br/><br/> Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku to < 64 TB (obsługiwane przez dyski SSD w warstwie Ultra).<br/><br/> Jeśli tak nie jest, zmniejsz rozmiar dysku przed przeprowadzeniem migracji na platformę Azure lub Użyj wielu dysków [na platformie Azure, aby uzyskać](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) wyższe limity magazynu. Należy upewnić się, że wydajność (IOPS i przepustowość) wymagana przez poszczególne dyski są obsługiwane przez [maszyny wirtualne zarządzane](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)przez platformę Azure.
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją Usuń nieużywane karty sieciowe z maszyny.
Liczba dysków przekracza limit | Usuń nieużywane dyski z maszyny przed migracją.
Rozmiar dysku przekracza limit | Azure Migrate: Ocena serwera obecnie nie obsługuje SSD w warstwie Ultra dysków i ocenia dyski w oparciu o limity dysku Premium (32 TB).<br/><br/> Platforma Azure obsługuje jednak dyski o rozmiarze do 64 TB (obsługiwane przez dyski SSD w warstwie Ultra). Zmniejsz liczbę dysków do mniej niż 64 TB przed migracją lub Użyj wielu [dysków na platformie Azure, aby uzyskać](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) wyższe limity magazynu.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Dysk niedostępny dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej z wymaganymi rdzeniami i pamięcią | Platforma Azure nie może znaleźć odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni maszyny lokalnej.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.

## <a name="linux-vms-are-conditionally-ready"></a>Maszyny wirtualne z systemem Linux są "warunkowo gotowe"

Ocena serwera oznacza maszyny wirtualne z systemem Linux jako "warunkowo gotowe" ze względu na znaną przerwę w ocenie serwera.

- Przerwy uniemożliwiają wykrycie pomocniczej wersji systemu operacyjnego Linux zainstalowanej na lokalnych maszynach wirtualnych.
- Na przykład w przypadku RHEL 6,10 obecnie Ocena serwera wykrywa tylko RHEL 6 jako wersję systemu operacyjnego.
-  Ponieważ platforma Azure poświadcza tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako gotowe do oceny serwera.
- Możesz określić, czy system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [Pomoc techniczną platformy Azure](https://aka.ms/migrate/selfhost/azureendorseddistros)w systemie Linux.
-  Po zweryfikowaniu poświadczonej dystrybucji można zignorować to ostrzeżenie.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Jednostki SKU platformy Azure przekraczają rozmiar lokalny

Ocena serwera Azure Migrate może zalecać jednostki SKU maszyny wirtualnej platformy Azure z większą liczbą rdzeni i pamięci niż bieżąca alokacja lokalna na podstawie typu oceny:


- Zalecenie dotyczące jednostki SKU maszyny wirtualnej zależy od właściwości oceny.
- Ma to wpływ na typ oceny wykonywanej w ocenie serwera: *Oparta na wydajności*lub *w środowisku lokalnym*.
- W przypadku ocen opartych na wydajności Ocena serwera traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Dodaje również współczynnik komfortu podczas określania efektywnego wykorzystania.
- W przypadku lokalnego określania wielkości dane wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana na podstawie przydziału lokalnego.

Aby pokazać, jak to może mieć wpływ na zalecenia, przyjrzyjmy się przykładowi:

Mamy lokalną maszynę wirtualną z czterema rdzeniami i 8 GB pamięci, z użyciem procesora CPU 50% i 50% wykorzystania pamięci, a określonym czynnikiem komfortu wynoszącym 1,3.

-  Jeśli ocena jest przeprowadzana **lokalnie**, zaleca się używanie jednostki SKU maszyny wirtualnej platformy Azure z 4 rdzeniami i 8 GB pamięci.
- Jeśli ocena jest oparta na wydajności, w oparciu o efektywne wykorzystanie procesora CPU i pamięci (50% z 4 rdzeniami * 1,3 = 2,6 rdzenie i 50% 8 GB pamięci * 1,3 = 5,3 GB pamięci), najtańszą jednostką SKU maszyny wirtualnej czterech rdzeni (najbliższą obsługiwaną liczbą rdzeni) i osiem GB pamięci (najbliższe obsługiwane rozmiar pamięci) jest zalecany.
- [Dowiedz się więcej](concepts-assessment-calculation.md#sizing) o ustalaniu wielkości ocen.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Jednostki SKU dysku platformy Azure większe niż lokalne

Ocena serwera Azure Migrate może zalecić większy dysk na podstawie typu oceny.
- Ustalanie rozmiarów dysków w ocenie serwera zależy od dwóch właściwości oceny: ustalania rozmiarów kryteriów i typu magazynu.
- Jeśli kryterium ustalania wielkości jest **oparte na wydajności**, a typ magazynu jest ustawiony na **automatyczny**, wartości IOPS i przepływność dysku są brane pod uwagę podczas identyfikowania docelowego typu dysku (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium). Zalecana jest jednostka SKU dysku z typu dysku, a zalecenie uwzględnia wymagania dotyczące rozmiaru dysku lokalnego.
- Jeśli kryterium ustalania rozmiaru jest **oparte na wydajności**, a typ magazynu to **Premium**, zalecana jest jednostka SKU dysku Premium na platformie Azure na podstawie wymagań operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do przeprowadzania ustalania rozmiarów dysków, gdy kryteria ustalania wielkości są **jako lokalne** , a typ magazynu to **HDD w warstwie Standardowa**, **SSD w warstwie Standardowa**lub **Premium**.

Jeśli na przykład masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, Ocena serwera zaleca dysk w warstwie Premium (z powodu wyższych wymagań IOPS), a następnie zaleca użycie dysku SKU, który może obsługiwać język r wymagane i rozmiar operacji we/wy. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). Mimo że rozmiar wymagany przez dysk lokalny to 32 GB, Ocena serwera zaleca większy dysk z powodu dużego wymagania IOPS dysku lokalnego.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Wiązane Procent użytego rdzenia lub braku pamięci

Raporty oceny serwera "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing", gdy urządzenie Azure Migrate nie może zbierać danych wydajności dla odpowiednich lokalnych maszyn wirtualnych.

- Taka sytuacja może wystąpić, jeśli maszyny wirtualne są wyłączone w czasie trwania oceny. Urządzenie nie może zbierać danych wydajności dla maszyny wirtualnej, gdy jest ona wyłączona.
- Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy na tych maszynach wirtualnych jest włączona pamięć dynamiczna. Istnieje znany problem dotyczący tylko maszyn wirtualnych funkcji Hyper-V, w których urządzenie Azure Migrate nie może zbierać danych użycia pamięci dla maszyn wirtualnych, dla których nie włączono pamięci dynamicznej.
- Jeśli brakuje któregokolwiek z liczników wydajności, Azure Migrate oceny serwera powróci do przyznanych rdzeni i pamięci, a następnie zaleca rozmiar maszyny wirtualnej.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Czy koszt licencji na system operacyjny maszyny wirtualnej jest objęty oceną kosztów?

Ocena serwera Azure Migrate obecnie uwzględnia koszt licencji systemu operacyjnego tylko dla maszyn z systemem Windows. Koszty licencji dla maszyn z systemem Linux nie są obecnie uwzględniane.

## <a name="performance-history-and-percentile-use"></a>Historia wydajności i użycie percentylu

Te właściwości mają zastosowanie tylko do rozmiarów opartych na wydajności w ocenie serwera Azure Migrate.

Narzędzie Server Assessment nieprzerwanie zbiera dane wydajności maszyn lokalnych i używa ich do określania zalecanych jednostek SKU maszyn wirtualnych i jednostek SKU dysków na platformie Azure. Dane dotyczące wydajności są zbierane przez ocenę serwera w następujący sposób:
- Urządzenie Azure Migrate ciągle profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund dla maszyn wirtualnych VMware i co 30 sekund dla maszyn wirtualnych funkcji Hyper-V.
- Urządzenie zbiera próbki 20 lub 30 sekund, aby utworzyć jeden punkt danych przez co 10 minut. Aby utworzyć pojedynczy punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych i 30-sekundowych, a następnie wysyła je do platformy Azure.
- Podczas tworzenia oceny w narzędziu Server Assessment (na podstawie czasu trwania wydajności i wartości percentylu historii wydajności) określana jest reprezentatywna wartość użycia. Na przykład jeśli historia wydajności ma jeden tydzień, a wykorzystanie percentylu to używany 95., Azure Migrate sortuje wszystkie 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera używany 95. percentyl jako wartość reprezentatywną.
- Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
- Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, należy wybrać 99 percentyl dla użycia percentylu.



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Nie mogę znaleźć wizualizacji zależności dla Azure Government

Azure Migrate zależy od Service Map funkcji wizualizacji zależności. Ponieważ Service Map jest obecnie niedostępna w Azure Government, ta funkcja nie jest dostępna w Azure Government.

## <a name="dependencies-dont-show-after-installing-agents"></a>Zależności nie są wyświetlane po zainstalowaniu agentów


Po zainstalowaniu agentów wizualizacji zależności na lokalnych maszynach wirtualnych Azure Migrate zwykle trwa 15-30 minut, aby wyświetlić zależności w portalu. Jeśli zaczekasz dłużej niż 30 minut, upewnij się, że Microsoft Monitoring Agent (MMA) może nawiązać połączenie z obszarem roboczym Log Analytics.

Dla maszyn wirtualnych z systemem Windows:
1. W panelu sterowania uruchom MMA.
2. We **właściwościach** > Microsoft Monitoring Agent**Azure log Analytics (OMS)** upewnij się, że **stan** obszaru roboczego to zielony.
3. Jeśli stan nie jest zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do MMA.

      ![Okno dialogowe właściwości MMA](./media/troubleshooting-general/mma-status.png)

W przypadku maszyn wirtualnych z systemem Linux upewnij się, że polecenia instalacji programu MMA i agenta zależności powiodły się.

## <a name="supported-mma-os"></a>Obsługiwany system operacyjny MMA

 Zapoznaj się z obsługiwanymi systemami operacyjnymi [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) .

## <a name="supported-dependency-agent-os"></a>Obsługiwany system operacyjny agenta zależności

Zapoznaj się z obsługiwanymi systemami operacyjnymi [Windows i Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="dependencies-for-more-than-an-hour"></a>Zależności przez więcej niż godzinę

Chociaż Azure Migrate pozwala na powrót do określonej daty w ostatnim miesiącu, maksymalny czas trwania wizualizacji zależności wynosi godzinę.

Można na przykład użyć funkcji czas trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednego okresu.

Można jednak użyć dzienników Azure Monitor, aby [wykonać zapytanie o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nie można wizualizować zależności dla grup mających więcej niż 10 maszyn wirtualnych

W ocenie serwera Azure Migrate można [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) z maksymalnie 10 maszynami wirtualnymi. W przypadku większych grup zalecamy dzielenie maszyn wirtualnych na mniejsze grupy w celu wizualizacji zależności.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Maszyny pokazują "Zainstaluj agenta" nie "Wyświetl zależności"

Po przeprowadzeniu migracji maszyn z włączoną wizualizacją zależności na platformę Azure, na maszynach może zostać wyświetlona akcja "Zainstaluj agenta" zamiast "Wyświetl zależności" z powodu następujących zachowań:


- Po migracji na platformę Azure maszyny lokalne są wyłączone i równoważne maszyny wirtualne są na platformie Azure. Te maszyny uzyskują inny adres MAC.
- Maszyny mogą także mieć inny adres IP w zależności od tego, czy lokalny adres IP został zachowany.
- Jeśli adresy MAC i IP różnią się od lokalnych, Azure Migrate nie kojarzy maszyn lokalnych z dowolnymi Service Map danymi zależności. W takim przypadku zostanie wyświetlona opcja instalacji agenta zamiast wyświetlania zależności.
- Po przeprowadzeniu migracji testowej na platformę Azure maszyny lokalne pozostają włączone zgodnie z oczekiwaniami. Równoważne komputery z systemem na platformie Azure uzyskują różne adresy MAC i mogą uzyskiwać różne adresów IP. Jeśli nie zablokujesz wychodzącego ruchu dziennika Azure Monitor z tych maszyn, Azure Migrate nie będzie kojarzyć maszyn lokalnych z dowolnymi Service Map danymi zależności i w ten sposób będzie wyświetlana opcja instalacji agentów, a nie do wyświetlania zależności.


## <a name="collect-network-traffic-logs-in-portal"></a>Zbieranie dzienników ruchu sieciowego w portalu

Zbierz dzienniki w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Naciśnij klawisz F12, aby rozpocząć Narzędzia deweloperskie. W razie konieczności Wyczyść ustawienie **Wyczyść wpisy przy nawigacji** .
3. Wybierz kartę **Sieć** i Rozpocznij przechwytywanie ruchu sieciowego:
   - W programie Chrome wybierz opcję **Zachowaj dziennik**. Nagrywanie powinno być uruchamiane automatycznie. Czerwony okrąg wskazuje na to, że ruch jest przechwytywany. Jeśli czerwony okrąg nie jest wyświetlany, wybierz czarny okrąg, aby rozpocząć.
   - W programie Microsoft Edge i Internet Explorer nagrywanie powinno być uruchamiane automatycznie. Jeśli tak nie jest, wybierz zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas rejestrowania, zatrzymywania rejestrowania i zapisywania kopii zapisanego działania:
   - W programie Chrome kliknij prawym przyciskiem myszy i wybierz pozycję **Zapisz jako Har z zawartością**. Ta akcja kompresuje i eksportuje dzienniki jako plik. HAR.
   - W przeglądarce Microsoft Edge lub Internet Explorer wybierz opcję **Eksportuj ruch przechwycony** . Ta akcja kompresuje i eksportuje dziennik.
6. Wybierz kartę **konsola** , aby sprawdzić, czy występują ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
   - W programie Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz pozycję **Zapisz jako**, aby wyeksportować i zip log.
   - W przeglądarce Microsoft Edge lub Internet Explorer, kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystko**.
7. Zamknij Narzędzia deweloperskie.
