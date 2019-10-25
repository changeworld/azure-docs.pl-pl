---
title: Wdróż serwer konfiguracji na potrzeby odzyskiwania po awarii programu VMware z Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii oprogramowania VMware przy użyciu Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f5fe49130742d116775b75f17c726b56150c574f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792350"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Lokalny serwer konfiguracji jest wdrażany w przypadku używania [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure. Umożliwia również zarządzanie replikacją danych. W tym artykule przedstawiono kroki niezbędne do wdrożenia serwera konfiguracji w przypadku replikowania maszyn wirtualnych VMware na platformę Azure. [Postępuj zgodnie z tym artykułem](physical-azure-set-up-source.md) , jeśli musisz skonfigurować serwer konfiguracji do replikacji serwera fizycznego.

> [!TIP]
> W [tym miejscu](vmware-azure-architecture.md)możesz dowiedzieć się więcej o roli serwera konfiguracji w ramach architektury Azure Site Recovery.

## <a name="deployment-of-configuration-server-through-ova-template"></a>Wdrażanie serwera konfiguracji za poorednictwem szablonu komórki jajowe

Serwer konfiguracji należy skonfigurować jako maszynę wirtualną VMware o wysokiej dostępności z niektórymi minimalnymi wymaganiami dotyczącymi sprzętu i wielkości. W celu dogodnej i łatwej wdrażania Site Recovery zapewnia do pobrania szablon komórki jajowe (Open Virtualization) służący do konfigurowania serwera konfiguracji, który spełnia wszystkie wymagane wymagania wymienione poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono podsumowanie minimalnych wymagań sprzętowych dotyczących serwera konfiguracji.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Wymagania dotyczące uprawnień Azure Active Directory

Użytkownik musi mieć **jeden z następujących** uprawnień ustawionych w usłudze AAD (Azure Active Directory), aby zarejestrować serwer konfiguracji za pomocą usług Azure Site Recovery Services.

1. Aby można było utworzyć aplikację, użytkownik powinien mieć rolę "Deweloper aplikacji".
    - Aby sprawdzić, zaloguj się do Azure Portal</br>
    - Przejdź do Azure Active Directory > ról i administratorów</br>
    - Sprawdź, czy rola "Deweloper aplikacji" jest przypisana do użytkownika. W przeciwnym razie należy użyć użytkownika z tym uprawnieniem lub skontaktować się z [administratorem, aby włączyć uprawnienie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Jeśli nie można przypisać roli dewelopera aplikacji, upewnij się, że flaga "użytkownik może zarejestrować aplikację" została ustawiona jako true dla użytkownika w celu utworzenia tożsamości. Aby włączyć powyższe uprawnienia,
    - Zaloguj się w witrynie Azure Portal
    - Przejdź do Azure Active Directory > ustawień użytkownika
    - W obszarze * * Rejestracje aplikacji "użytkownicy mogą rejestrować aplikacje" należy wybrać opcję "tak".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (ADFS) **nie jest obsługiwana**. Użyj konta zarządzanego za pomocą [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>Pobieranie szablonu

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon Open Virtualization Application (komórki jajowe) dla serwera konfiguracji.

   > [!TIP]
   >Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licencja udostępniona z szablonem komórki jajowe to licencja ewaluacyjna ważna przez 180 dni. Opublikuj ten okres, aby klient mógł aktywować system Windows przy użyciu licencji z licencją.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W obszarze **Wybierz źródło**wprowadź lokalizację pobranego OVF.
4. W obszarze **szczegóły przeglądu**wybierz pozycję **dalej**.
5. W obszarze **Wybierz nazwę i folder** i **Wybierz pozycję Konfiguracja**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**. Użycie opcji alokowania elastycznego może mieć wpływ na wydajność serwera konfiguracji.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Gotowe do ukończenia**:

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Aby dodać dodatkowy interfejs sieciowy, wyczyść pole wyboru **Włącz po wdrożeniu**, a następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

> [!IMPORTANT]
> Nie zmieniaj konfiguracji zasobów (pamięć/rdzenie/ograniczenie procesora), Modyfikuj/Usuń zainstalowane usługi lub pliki na serwerze konfiguracji po wdrożeniu. Będzie to miało wpływ na rejestrację serwera konfiguracji przy użyciu usług platformy Azure i wydajności serwera konfiguracji.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

> [!NOTE]
> Dwie karty sieciowe są wymagane, jeśli planujesz zachować adresy IP maszyn źródłowych w trybie failover, a następnie powrócić do trybu powrotu po awarii do lokalizacji lokalnej. Jedna karta sieciowa zostanie połączona z maszynami źródłowymi, a druga karta sieciowa będzie używana do łączności z platformą Azure.

Jeśli chcesz dodać dodatkową kartę sieciową do serwera konfiguracji, Dodaj ją przed zarejestrowaniem serwera w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć.
4. Aby połączyć wirtualną kartę sieciową po włączeniu maszyny wirtualnej, wybierz pozycję **Połącz przy zasilaniu**. Następnie wybierz pozycję **Next** > **Zakończ** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Rejestrowanie serwera konfiguracji przy użyciu usług Azure Site Recovery Services

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Przy pierwszym logowaniu w ciągu kilku sekund zostanie uruchomione narzędzie do konfiguracji Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.</br>
    a. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.</br>
    b. Upewnij się, że wybrane konto użytkownika ma uprawnienia do tworzenia aplikacji na platformie Azure. Aby włączyć wymagane uprawnienia, postępuj zgodnie z wytycznymi podanymi [tutaj](#azure-active-directory-permission-requirements).
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Kreator zarządzania serwerem konfiguracji jest uruchamiany **automatycznie** w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Instalacja łączność**. Z listy rozwijanej wybierz najpierw kartę sieciową używaną przez serwer przetwarzania w celu odnajdywania i instalacji wypychanej usługi mobilności na maszynach źródłowych, a następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić. Zdecydowanie zaleca się, aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, że adres IP przypisany do serwera konfiguracji to STATYCZNy adres IP, a nie adres IP serwera DHCP.
2. W obszarze **Wybierz magazyn Recovery Services**Zaloguj się do Microsoft Azure z poświadczeniami używanymi w **kroku 6** z "[rejestrowanie serwera konfiguracji z usługami Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services)".
3. Po zalogowaniu wybierz swoją subskrypcję platformy Azure i odpowiednią grupę zasobów i magazyn.

    > [!NOTE]
    > Po zarejestrowaniu nie ma możliwości zmiany magazynu usługi Recovery Services.
    > Zmiana magazynu usługi Recovery Services wymaga usunięcia skojarzenia z serwerem konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych na serwerze konfiguracji zostanie zatrzymana. Dowiedz się [więcej](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. W obszarze **Instalowanie oprogramowania innych**firm,

    |Scenariusz   |Kroki do wykonania  |
    |---------|---------|
    |Czy można pobrać & zainstalować MySQL ręcznie?     |  Tak. Pobierz aplikację MySQL & Umieść ją w folderze **C:\Temp\ASRSetup**, a następnie zainstaluj ręcznie. Teraz po zaakceptowaniu warunków > kliknij pozycję **Pobierz i zainstaluj**, Portal jest *już zainstalowany*. Możesz przejść do następnego kroku.       |
    |Czy mogę uniknąć pobierania programu MySQL online?     |   Tak. Umieść aplikację instalatora MySQL w folderze **C:\Temp\ASRSetup**. Zaakceptuj warunki > kliknij pozycję **Pobierz i zainstaluj**, Portal użyje Instalatora dodanego przez Ciebie i zainstaluje aplikację. Możesz przejść do następnego kroku po instalacji.    |
    |Chcę pobrać & zainstalować MySQL przez Azure Site Recovery     |  Zaakceptuj umowę licencyjną & kliknij pozycję **Pobierz i zainstaluj**. Następnie można przejść do kolejnego kroku po instalacji.       |

5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie **Kontynuuj**. Podane tutaj poświadczenia zostały zapisane lokalnie.
8. W obszarze **Konfiguruj poświadczenia maszyny wirtualnej**wprowadź nazwę użytkownika i hasło maszyn wirtualnych, aby automatycznie zainstalować usługę mobilności podczas replikacji. W przypadku maszyn z **systemem Windows** konto musi mieć uprawnienia administratora lokalnego na maszynach, które mają być replikowane. W przypadku systemu **Linux**Podaj szczegóły konta głównego.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
10. Po zakończeniu rejestracji Otwórz Azure Portal, sprawdź, czy serwer konfiguracji i serwer VMware znajdują się na liście **Recovery Services magazynu** > **Zarządzaj** > **Site Recovery infrastruktura** > **Konfiguracja Serwery**.

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, wykonaj następujące [kroki](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Szczegółowe instrukcje dotyczące uaktualniania wszystkich składników Site Recovery można znaleźć w temacie [Service Update Management](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Zarządzanie serwerem konfiguracji

Aby uniknąć przerw w trwającej replikacji, upewnij się, że adres IP serwera konfiguracji nie zmienia się po zarejestrowaniu serwera konfiguracji w magazynie. Więcej informacji o typowych zadaniach zarządzania serwerem konfiguracji można znaleźć [tutaj](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Często zadawane pytania

1. Jak długo licencja podana na serwerze konfiguracji wdrożona za pomocą OVF jest prawidłowa? Co się stanie, jeśli nie ponownie aktywuję licencji?

    Licencja udostępniona z szablonem komórki jajowe to licencja ewaluacyjna ważna przez 180 dni. Przed wygaśnięciem należy aktywować licencję. W przeciwnym razie może to spowodować częste zamknięcie serwera konfiguracji i w efekcie przeszkodę na działania replikacji. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zarządzania licencją serwera konfiguracji](vmware-azure-manage-configuration-server.md#update-windows-license).

2. Czy mogę użyć maszyny wirtualnej, na której zainstalowano serwer konfiguracji, w różnych celach?

    **Nie**, zalecamy korzystanie z maszyny wirtualnej wyłącznie do celów związanych z serwerem konfiguracji. Upewnij się, że wszystkie specyfikacje wymienione w [wymaganiach wstępnych](#prerequisites) mają na celu wydajne zarządzanie odzyskiwaniem po awarii.
3. Czy mogę przełączyć magazyn już zarejestrowany na serwerze konfiguracji przy użyciu nowo utworzonego magazynu?

    **Nie**, gdy magazyn jest zarejestrowany na serwerze konfiguracji, nie można go zmienić.
4. Czy można używać tego samego serwera konfiguracji do ochrony maszyn fizycznych i wirtualnych?

    **Tak**. ten sam serwer konfiguracji może służyć do replikowania maszyn fizycznych i wirtualnych. Maszyna fizyczna może jednak się odwrócić tylko do maszyny wirtualnej VMware.
5. Jaki jest cel serwera konfiguracji i gdzie jest używany?

    Aby dowiedzieć się więcej o programie Configuration Server i jego funkcjach, zapoznaj się z tematem [Architektura replikacji VMware do platformy Azure](vmware-azure-architecture.md) .
6. Gdzie mogę znaleźć najnowszą wersję serwera konfiguracji?

    Aby uzyskać instrukcje dotyczące uaktualniania serwera konfiguracji za pomocą portalu, zobacz [uaktualnianie serwera konfiguracji](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Szczegółowe instrukcje dotyczące uaktualniania wszystkich składników Site Recovery można znaleźć [tutaj](https://aka.ms/asr_how_to_upgrade).
7. Gdzie można pobrać hasło dla serwera konfiguracji?

    Zapoznaj się z [tym artykułem](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) , aby pobrać hasło.
8. Czy mogę zmienić hasło?

    **Nie**, **stanowczo zalecamy, aby nie zmieniać hasła** serwera konfiguracji. Zmiany hasła przerywają replikację chronionych maszyn i prowadzą do krytycznego stanu kondycji.
9. Gdzie można pobrać klucze rejestracji magazynu?

    W **magazynie Recovery Services** **Zarządzaj** > **Site Recovery infrastrukturą** > **serwerów konfiguracji**. W obszarze serwery wybierz pozycję **Pobierz klucz rejestracji** , aby pobrać plik poświadczeń magazynu.
10. Czy można sklonować istniejący serwer konfiguracji i używać go do organizowania replikacji?

    **Nie**, użycie sklonowanego składnika serwera konfiguracji nie jest obsługiwane. Klon serwera przetwarzania skalowalnego w poziomie jest również nieobsługiwanym scenariuszem. Klonowanie składników Site Recovery wpływa na bieżące replikacje.

11. Czy mogę zmienić adres IP serwera konfiguracji?

    **Nie**. zdecydowanie zaleca się, aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP przypisane do serwera konfiguracji to statyczne adresy IP, a nie adresy IP DHCP.
12. Czy mogę skonfigurować serwer konfiguracji na platformie Azure?

    Zaleca się skonfigurowanie serwera konfiguracji w środowisku lokalnym z bezpośrednim wglądem w szczegółowe dane przy użyciu programu v-Center i zminimalizowanie opóźnień transferu danych. Zaplanowane kopie zapasowe serwera konfiguracji można wykonać na potrzeby [powrotu po awarii](vmware-azure-manage-configuration-server.md#failback-requirements).

Aby uzyskać więcej często zadawanych pytań na temat serwera konfiguracji, zapoznaj się z naszą [dokumentacją dotyczącą typowych pytań dotyczących serwera konfiguracji](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Następne kroki

Skonfiguruj odzyskiwanie po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
