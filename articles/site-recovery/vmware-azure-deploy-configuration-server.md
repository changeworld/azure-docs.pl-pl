---
title: Wdrażanie serwera konfiguracji na potrzeby odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wdrażania serwera konfiguracji w celu odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: fb558644f29cd7b84156f976f525a88080f101dc
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449207"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Wdrażanie serwera konfiguracji w środowisku lokalnym, korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Konfiguracja współrzędne międzyserwerowej komunikacji lokalnych programu VMware i platformą Azure. Umożliwia także zarządzanie replikacją danych. W tym artykule przedstawiono kroki niezbędne do wdrożenia serwera konfiguracji w przypadku replikacji maszyn wirtualnych VMware na platformę Azure. [Postępuj zgodnie z tym artykule](physical-azure-set-up-source.md) Jeśli musisz skonfigurować serwer konfiguracji, replikacji serwera fizycznego.

> [!TIP]
> Informacje na temat roli serwera konfiguracji w ramach architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Wdrażanie serwera konfiguracji za pomocą szablonu OVA

Serwer konfiguracji należy skonfigurować jako o wysokiej dostępności maszyny Wirtualnej programu VMware przy użyciu niektóre minimalne wymagania sprzętowe i rozmiaru. W przypadku wdrożenia wygodnie i łatwo Site Recovery udostępnia do pobrania szablonu OVA (Open Virtualization aplikacji), aby skonfigurować serwer konfiguracji, który jest zgodny ze wszystkimi wymaganiami obowiązkowego wymienione poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Minimalne wymagania sprzętowe dla serwera konfiguracji są podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Wymagania dotyczące uprawnień usługi Active Directory systemu Azure

Możesz zdecydować, że użytkownik z **jedną z następujących** uprawnieniami ustawionymi w usłudze AAD (Azure Active Directory), aby zarejestrować serwer konfiguracji za pomocą usługi Azure Site Recovery.

1. Użytkownik powinien mieć rolę "Deweloper aplikacji" do tworzenia aplikacji.
   1. Aby sprawdzić, zaloguj się do witryny Azure portal</br>
   1. Przejdź do usługi Azure Active Directory > Role i Administratorzy</br>
   1. Sprawdź, jeśli "Deweloper aplikacji" rola jest przypisywana do użytkownika. Jeśli nie, użytkownik za pomocą tego uprawnienia, lub skontaktowanie się z [administratorem, aby włączyć uprawnienia](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Nie można przypisać roli "Deweloper aplikacji", upewnij się, że "Użytkownik może zarejestrować aplikację" Flaga jest ustawiona jako PRAWDA dla użytkownika, można utworzyć tożsamości. Aby włączyć powyżej uprawnienia
   1. Logowanie do witryny Azure Portal
   1. Przejdź do usługi Azure Active Directory > Ustawienia użytkownika
   1. W obszarze ** rejestracje aplikacji ","Użytkownicy mogą rejestrować aplikacje"powinna być wybrana jako"Yes".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Services(ADFS) federacji w usłudze Active Directory jest **nieobsługiwane**. Użyj konta zarządzane za pośrednictwem [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>Planowanie pojemności

Wymagania w zakresie rozmiaru serwera konfiguracji zależą od potencjalnych współczynnik zmian danych. Użyj tej tabeli jako wskazówki.

| **CPU** | **Pamięć** | **Rozmiar dysku w pamięci podręcznej** | **Współczynnik zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- | --- |
| 8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz) |16 GB |300 GB |500 GB lub mniej |Replikowanie maszyn mniej niż 100. |
| 12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) |18 GB |600 GB |Od 500 GB do 1 TB |Replikowanie maszyn 100 150. |
| 16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni \@ 2,5 GHz) |32 GB |1 TB |1 TB do 2 TB |Replikowanie maszyn 150 – 200. |

Jeśli replikujesz więcej niż jednej maszyny Wirtualnej VMware, zapoznaj się z [zagadnienia dotyczące planowania pojemności](site-recovery-plan-capacity-vmware.md). Uruchom [narzędzie planista wdrażania](site-recovery-deployment-planner.md) potrzeby replikacji oprogramowania VMWare.

## <a name="download-the-template"></a>Pobieranie szablonu

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon Open Virtualization aplikacji (OVA) dla serwera konfiguracji.

   > [!TIP]
   >Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licencja dostarczane z szablonem OVA jest ocena prawidłowe przez 180 dni. Post tego okresu klient musi wykonać aktywację systemu windows za pomocą licencji uzyskiwanych.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W **wybierz źródło**, wprowadź lokalizację pobranego szablonu OVF.
4. W **Przejrzyj szczegóły**, wybierz opcję **dalej**.
5. W **wybierz nazwę i folder** i **wybierz konfigurację**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**. Użycie opcji inicjowania obsługi alokowania elastycznego, mogą mieć wpływ na wydajność serwera konfiguracji.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Gotowe do ukończenia**:

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Aby dodać więcej kart sieciowych, wyczyść **Włącz po wdrożeniu**, a następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

> [!IMPORTANT]
> Nie należy zmieniać konfiguracje zasobów (ograniczenia pamięci/rdzeni/procesora CPU), modyfikowania/usuwania zainstalowane usługi lub plików na serwerze konfiguracji po wdrożeniu. Wpłynie to na rejestracji serwera konfiguracji z usługami platformy Azure i wydajności serwera konfiguracji.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Jeśli chcesz dodać dodatkową kartę Sieciową do serwera konfiguracji, należy go dodać, aby zarejestrować serwer w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć.
4. Aby połączyć z wirtualnej karty Sieciowej, gdy maszyna wirtualna jest włączona, wybierz pozycję **Połącz po włączeniu**. Następnie wybierz pozycję **dalej** > **Zakończ** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Rejestrowanie serwera konfiguracji za pomocą usługi Azure Site Recovery

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Rozpoczyna się podczas pierwszego logowania, w ciągu kilku sekund narzędzie konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.</br>
    a. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.</br>
    b. Upewnij się, że wybrane konto użytkownika ma uprawnienia do tworzenia aplikacji na platformie Azure. Aby włączyć wymagane uprawnienia, postępuj zgodnie z wytycznymi podanymi [tutaj](#azure-active-directory-permission-requirements).
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Zostanie uruchomiony Kreator zarządzania serwerem konfiguracji **automatycznie** w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W Kreatorze konfiguracji serwera zarządzania zaznacz **Konfiguracja łączności**. Z list rozwijanych najpierw wybierz kartę Sieciową, używanego przez serwer w tworzonym przez proces odnajdywania i wypychanie instalacji usługi mobilności na maszynach źródła, a następnie wybierz kartę Sieciową, która korzysta z serwera konfiguracji dla łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Nie można zmienić to ustawienie, po skonfigurowaniu go. Zdecydowanie zalecane jest aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, że adres IP przypisane do serwera konfiguracji jest statyczny adres IP i nie IP protokołu DHCP.
2. W **magazyn usługi Recovery Services zaznacz**, zaloguj się do systemu Microsoft Azure przy użyciu poświadczeń używanych w **kroku 6** z "[Zarejestruj serwer konfiguracji za pomocą usług Azure Site Recovery](#register-the-configuration-server-with-azure-site-recovery-services)" .
3. Po zalogowaniu wybierz swoją subskrypcję platformy Azure i grupę zasobów i magazyn.

    > [!NOTE]
    > Po zarejestrowaniu, istnieje możliwość zmiany magazynu usługi recovery services.
    > Zmienianie magazynu usługi recovery services wymaga usunięcia serwera konfiguracji z bieżącego magazynu, a następnie replikację chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana. Dowiedz się [więcej](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. W **instalowanie oprogramowania innych firm**,

    |Scenariusz   |Kroki do wykonania  |
    |---------|---------|
    |Można pobrać & Ręczne instalowanie programu MySQL?     |  Tak. Pobierz aplikację MySQL i umieść go w folderze **C:\Temp\ASRSetup**, następnie zainstalować ręcznie. Teraz, jeśli akceptujesz jej warunki > kliknij **Pobierz i zainstaluj**, portalu jest wyświetlany komunikat *zainstalowane*. Możesz przejść do następnego kroku.       |
    |Można uniknąć pobierania oprogramowania MySQL online?     |   Tak. Umieść aplikację MySQL Instalatora w folderze **C:\Temp\ASRSetup**. Zaakceptuj warunki > kliknij **Pobierz i zainstaluj**, portal użyje Instalatora dodane przez użytkownika i instaluje aplikację. Możesz przejść do następnego kroku po instalacji.    |
    |Chcę pobieranie i instalowanie programu MySQL za pomocą usługi Azure Site Recovery     |  Zaakceptuj Umowę licencyjną i kliknij **Pobierz i zainstaluj**. Następnie możesz przejść do następnego kroku po instalacji.       |

5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz **Dodaj**, a następnie **nadal**. Poświadczenia wprowadzone w tym miejscu lokalnie są zapisywane.
8. W **skonfiguruj poświadczenia maszyny wirtualnej**, wprowadź nazwę użytkownika i hasło maszyn wirtualnych w celu automatycznego zainstalowania usługi mobilności podczas replikacji. Aby uzyskać **Windows** maszyn, konto musi mieć uprawnienia administratora lokalnego na maszynach, którą chcesz replikować. Aby uzyskać **Linux**, podaj szczegóły konta głównego.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
10. Po zakończeniu rejestracji Otwórz witrynę Azure portal, sprawdź, czy serwer konfiguracji i serwer VMware są wyświetlane na **magazyn usługi Recovery Services** > **Zarządzaj**  >  **Infrastruktura usługi site Recovery** > **serwery konfiguracji**.

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, postępuj zgodnie z tymi [kroki](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Aby uzyskać szczegółowe instrukcje na temat uaktualniania wszystkie składniki usługi Site Recovery, kliknij przycisk [tutaj](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Zarządzanie serwerem konfiguracji

Aby uniknąć przerw w działaniu w trwającej replikacji, upewnij się, że adres IP serwera konfiguracji nie zmienia się po zarejestrowaniu serwera konfiguracji w magazynie. Dowiedz się więcej na temat typowych zadań zarządzania serwerem konfiguracji [tutaj](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Często zadawane pytania

1. Jak długo trwa, czy licencja na serwerze konfiguracji wdrożonymi za pośrednictwem pakietu OVF jest prawidłowy? Co się stanie, jeśli mam nie Uaktywniaj ponownie licencji?

    Licencja dostarczane z szablonem OVA jest ocena prawidłowe przez 180 dni. Przed wygaśnięciem musisz aktywować licencję. W przeciwnym wypadku to powodować częste zamykania serwera konfiguracji i powoduje hinderance do działań replikacji.

2. Czy można używać maszyny Wirtualnej, w którym zainstalowano serwer konfiguracji do różnych celów?

    **Nie**, firma Microsoft zaleca na potrzeby maszyny Wirtualnej wyłącznie do celów serwera konfiguracji. Należy przestrzegać wszystkich specyfikacji wymienionej w [wymagania wstępne](#prerequisites) efektywne zarządzanie odzyskiwania po awarii.
3. Można przełączyć magazyn już jest zarejestrowany na serwerze konfiguracji przy użyciu nowo utworzonego magazynu?

    **Nie**, po zarejestrowaniu magazynu z serwera konfiguracji nie można zmienić.
4. Czy można użyć tego samego serwera konfiguracji w celu ochrony komputerów fizycznych i maszyn wirtualnych?

    **Tak**, tego samego serwera konfiguracji może służyć do replikowania maszyn fizycznych i wirtualnych. Jednak można maszyny fizycznej kopii tylko do maszyny Wirtualnej VMware.
5. Jakie jest przeznaczenie serwera konfiguracji i gdzie jest używany?

    Zapoznaj się [architektura Azure replikacji VMware –](vmware-azure-architecture.md) Aby dowiedzieć się więcej na temat serwera konfiguracji i jego funkcje.
6. Gdzie można znaleźć najnowszą wersję serwera konfiguracji?

    Aby uzyskać instrukcje dotyczące uaktualniania serwera konfiguracji za pośrednictwem portalu, zobacz [uaktualnić serwer konfiguracji](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Aby uzyskać szczegółowe instrukcje na temat uaktualniania wszystkie składniki usługi Site Recovery można znaleźć [tutaj](https://aka.ms/asr_how_to_upgrade).
7. Gdzie można pobrać hasła dla serwera konfiguracji?

    Zapoznaj się [w tym artykule](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) można pobrać hasło.
8. Czy można zmienić hasło?

    **Nie**, jesteś **zdecydowanie zaleca się nie zmieniać hasło** serwera konfiguracji. Zmiana hasła przerywa replikację chronionych maszyn i prowadzi do krytycznego stanu kondycji.
9. Gdzie można pobrać klucze rejestracyjne magazynu?

    W **magazyn usług Recovery Services**, **zarządzanie** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**. Na serwerach, wybierz **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.
10. Można sklonować istniejący serwer konfiguracji i używać go do organizowania replikacji?

    **Nie**, użycie sklonowany składnik serwera konfiguracji nie jest obsługiwane. Klonuj serwera przetwarzania skalowalnego w poziomie jest również nieobsługiwany scenariusz. Składniki klonowania Usługa Site Recovery wpływu na trwającą replikacje.

11. Czy mogę zmienić adres IP serwera konfiguracji

    **Nie**, zdecydowanie zaleca się nie zmieniać adresu IP serwera konfiguracji. Upewnij się, wszystkie adresy IP przypisane do serwera konfiguracji STATYCZNYCH adresów IP i nie adresy IP protokołu DHCP.
12. Można skonfigurować serwer konfiguracji na platformie Azure?

    Zalecane jest, aby skonfigurować serwer konfiguracji w środowisku lokalnym za pomocą bezpośredniego wiersza linii wzroku z Vcenter i zminimalizować opóźnienia transferu danych. Można wykonać zaplanowane kopie zapasowe serwera konfiguracji na potrzeby [potrzeby powrotu po awarii](vmware-azure-manage-configuration-server.md#failback-requirements).

Inne często zadawane pytania na serwerze konfiguracji, można znaleźć w naszej [dokumentacji na typowe pytania dotyczące konfiguracji serwera](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Kolejne kroki

Konfigurowanie odzyskiwania po awarii [maszyny wirtualne VMware](vmware-azure-tutorial.md) na platformie Azure.
