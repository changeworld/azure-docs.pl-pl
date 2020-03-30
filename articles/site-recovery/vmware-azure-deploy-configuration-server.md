---
title: Wdrażanie serwera konfiguracji w usłudze Azure Site Recovery
description: W tym artykule opisano sposób wdrażania serwera konfiguracji odzyskiwania po awarii VMware za pomocą usługi Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257383"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Wdrażasz lokalny serwer konfiguracji podczas korzystania z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure. Serwer konfiguracji koordynuje komunikację między lokalnymi urządzeniami VMware i platformą Azure. Zarządza również replikacją danych. W tym artykule otrzymasz od kroków potrzebnych do wdrożenia serwera konfiguracji podczas replikowania maszyn wirtualnych VMware na platformie Azure. Jeśli chcesz skonfigurować serwer konfiguracji dla replikacji serwera fizycznego, zobacz [Konfigurowanie serwera konfiguracji do odzyskiwania po awarii serwerów fizycznych na platformie Azure](physical-azure-set-up-source.md).

> [!TIP]
> Aby dowiedzieć się więcej o roli serwera konfiguracji w ramach architektury usługi Azure Site Recovery, zobacz [architektura odzyskiwania po awarii VMware na platformie Azure.](vmware-azure-architecture.md)

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Wdrażanie serwera konfiguracji za pomocą szablonu ova

Serwer konfiguracji musi być skonfigurowany jako wysoce dostępna maszyna wirtualna VMware z pewnymi minimalnymi wymaganiami sprzętowymi i dotyczącymi rozmiaru. W celu wygodnego i łatwego wdrażania usługa Site Recovery udostępnia szablon OVA (Open Virtualization Application) do pobrania w celu skonfigurowania serwera konfiguracji zgodnego ze wszystkimi wymaganymi wymaganiami wymienionymi tutaj.

## <a name="prerequisites"></a>Wymagania wstępne

Minimalne wymagania sprzętowe dla serwera konfiguracji są podsumowane w poniższych sekcjach.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Wymagania dotyczące uprawnień usługi Azure Active Directory

Aby zarejestrować serwer konfiguracji w usłudze Azure Site Recovery, użytkownik musi mieć jedno z następujących uprawnień ustawionych w usłudze Azure Active Directory (Azure AD).

1. Aby utworzyć aplikację, użytkownik musi mieć rolę dewelopera aplikacji.
    - Aby zweryfikować, zaloguj się do witryny Azure portal.</br>
    - Przejdź do usługi Role**i administratorzy** **usługi Azure Active Directory** > .</br>
    - Sprawdź, czy rola dewelopera aplikacji jest przypisana do użytkownika. Jeśli nie, użyj użytkownika z tym uprawnieniem lub skontaktuj się z [administratorem, aby włączyć uprawnienie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Jeśli nie można przypisać roli dewelopera aplikacji, upewnij się, że **użytkownicy mogą rejestrować aplikacje** flagi jest ustawiona jako **true** dla użytkownika, aby utworzyć tożsamość. Aby włączyć te uprawnienia:
    - Zaloguj się do Portalu Azure.
    - Przejdź do**ustawień użytkownika**usługi Azure **Active Directory** > .
    - W obszarze **Rejestracje aplikacji** **użytkownicy mogą rejestrować aplikacje**, wybierz **tak**.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Usługi federacyjne Active Directory *nie są obsługiwane*. Użyj konta zarządzanego za pośrednictwem [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>Pobieranie szablonu

1. W przechowalni przejdź do **strony Przygotuj** > **źródło**infrastruktury .
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon ova dla serwera konfiguracji.

   > [!TIP]
   >Najnowszą wersję szablonu serwera konfiguracji można również pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licencja dostarczona z szablonem OVA jest licencją ewaluacją ważną przez 180 dni. Po tym okresie należy uzyskać licencję.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **Plik** wybierz polecenie **Wdrażanie szablonu OVF,** aby uruchomić **kreatora wdrażania szablonu OVF.**

     ![Wdrażanie szablonu OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego). Użycie opcji inicjowania obsługi administracyjnej może mieć wpływ na wydajność serwera konfiguracji.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Ready to complete** (Gotowe do ukończenia):

    * Aby skonfigurować maszynę wirtualną z ustawieniami domyślnymi, wybierz pozycję **Włącz po wdrożeniu** > **Zakończ**.
    * Aby dodać dodatkowy interfejs sieciowy, wyczyść **opcję Włączanie zasilania po wdrożeniu,** a następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

> [!IMPORTANT]
> Nie należy zmieniać konfiguracji zasobów, takich jak pamięć, rdzenie i ograniczenia procesora CPU, ani modyfikować ani usuwać zainstalowanych usług lub plików na serwerze konfiguracji po wdrożeniu. Te typy zmian wpływają na rejestrację serwera konfiguracji za pomocą usług platformy Azure i wydajność serwera konfiguracji.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

> [!NOTE]
> Dwie karty sieciowe są wymagane, jeśli zamierzasz zachować adresy IP komputerów źródłowych w pracy awaryjnej i chcesz wrócić do trybu lokalnego później. Jedna karta sieciowa jest podłączona do komputerów źródłowych, a druga karta sieciowa jest używana dla łączności platformy Azure.

Jeśli chcesz dodać dodatkową kartę sieciową do serwera konfiguracji, dodaj ją przed zarejestrowaniem serwera w przechowalni. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz **przycisk Dalej**.
3. Wybierz typ karty i sieć.
4. Aby połączyć wirtualną kartę sieciową po włączeniu maszyny Wirtualnej, wybierz pozycję **Połącz przy włączonym zasilaniu**. Następnie wybierz **pozycję Następne** > **zakończenie** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Rejestrowanie serwera konfiguracji w usługach Azure Site Recovery

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Przy pierwszym logacji w ciągu kilku sekund uruchamia się narzędzie konfiguracji odzyskiwania usługi Azure site recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz **przycisk Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.</br>
    a. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.</br>
    b. Upewnij się, że wybrane konto użytkownika ma uprawnienia do tworzenia aplikacji na platformie Azure. Aby włączyć wymagane uprawnienia, postępuj zgodnie z wytycznymi zawartymi w sekcji [Wymagania dotyczące uprawnień usługi Azure Active Directory](#azure-active-directory-permission-requirements).
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Kreator zarządzania serwerem konfiguracji uruchamia się automatycznie w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Łączność instalatora**. Z listy rozwijanej najpierw wybierz kartę sieciową, której używany jest wbudowany serwer procesowy do odnajdowania i wypychania instalacji usługi mobilności na komputerach źródłowych. Następnie wybierz kartę sieciową używana przez serwer konfiguracji do łączności z platformą Azure. Wybierz **pozycję Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić. Nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że adres IP przypisany do serwera konfiguracji jest statycznym adresem IP, a nie adresem IP DHCP.
2. W **przechowalni Wybierz usługi odzyskiwania**zaloguj się do platformy Microsoft Azure przy użyciu poświadczeń użytych w kroku 6 rejestru serwera konfiguracji za pomocą usług Azure Site Recovery [services](#register-the-configuration-server-with-azure-site-recovery-services).
3. Po zalogowaniu wybierz subskrypcję platformy Azure oraz odpowiednią grupę zasobów i magazyn.

    > [!NOTE]
    > Po rejestracji nie ma elastyczności, aby zmienić magazyn usług odzyskiwania.
    > Zmiana magazynu usług odzyskiwania wymaga odłączenia serwera konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych w serwerze konfiguracji zostanie zatrzymana. Aby dowiedzieć się więcej, zobacz [Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VM.](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)

4. Przy **instalacji oprogramowania innych firm:**

    |Scenariusz   |Kroki do naśladowania  |
    |---------|---------|
    |Czy mogę pobrać i zainstalować MySQL ręcznie?     |  Tak. Pobierz aplikację MySQL, umieść ją w folderze **C:\Temp\ASRSetup,** a następnie zainstaluj ręcznie. Po zaakceptowaniu warunków i wybraniu **opcji Pobierz i zainstaluj**, portal mówi już *zainstalowany*. Możesz przejść do następnego kroku.       |
    |Czy mogę uniknąć pobierania mysql online?     |   Tak. Umieść aplikację instalatora MySQL w folderze **C:\Temp\ASRSetup**. Zaakceptuj warunki, wybierz **pozycję Pobierz i zainstaluj**, a portal użyje instalatora dodanego do zainstalowania aplikacji. Po zakończeniu instalacji przejdź do następnego kroku.    |
    |Chcę pobrać i zainstalować mysql za pośrednictwem usługi Azure Site Recovery.    |  Zaakceptuj umowę licencyjną i wybierz pozycję **Pobierz i zainstaluj**. Po zakończeniu instalacji przejdź do następnego kroku.       |

5. W **sprawie Sprawdzania poprawności konfiguracji urządzenia**wymagania wstępne są weryfikowane przed kontynuowaniem.
6. W **obszarze Konfiguruj serwer vCenter Server/vSphere ESXi**wprowadź adres FQDN lub IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które mają być replikowane. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz **pozycję Dodaj** > **kontynuuj**. Poświadczenia wprowadzone w tym miejscu są zapisywane lokalnie.
8. W **obszarze Konfigurowanie poświadczeń maszyny wirtualnej**wprowadź nazwę użytkownika i hasło maszyn wirtualnych, aby automatycznie zainstalować usługę mobilności podczas replikacji. W przypadku komputerów z **systemem Windows** konto wymaga uprawnień administratora lokalnego na komputerach, które chcesz replikować. W przypadku **systemu Linux**podaj szczegóły dotyczące konta głównego.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
10. Po zakończeniu rejestracji otwórz portal Azure i sprawdź, czy serwer konfiguracji i serwer VMware są wymienione na**serwerach** > konfiguracji infrastruktury odzyskiwania **usługi** > recovery Services Manage**Site Recovery Infrastructure** > **.**

## <a name="upgrade-the-configuration-server"></a>Uaktualnianie serwera konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, zobacz [Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VMware VM.](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) Aby uzyskać instrukcje dotyczące uaktualniania wszystkich składników odzyskiwania witryny, zobacz [Aktualizacje usługi w programie Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Zarządzanie serwerem konfiguracji

Aby uniknąć przerw w trwającej replikacji, upewnij się, że adres IP serwera konfiguracji nie zmienia się po zarejestrowaniu serwera konfiguracji w przechowalni. Aby dowiedzieć się więcej o typowych zadaniach zarządzania serwerem konfiguracji, zobacz [Zarządzanie serwerem konfiguracji odzyskiwania po awarii vmware VMware VM .](vmware-azure-manage-configuration-server.md)

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

Zapoznaj się z naszym [artykułem dotyczącym rozwiązywania problemów,](vmware-azure-troubleshoot-configuration-server.md) aby rozwiązać problemy z & łącznością.

## <a name="faqs"></a>Często zadawane pytania

* Jak długo licencja dostarczona na serwerze konfiguracji wdrożona za pośrednictwem ovf jest ważna? Co się stanie, jeśli nie aktywuję ponownie licencji?

    Licencja dostarczona z szablonem OVA jest licencją ewaluacjową ważną przez 180 dni. Przed wygaśnięciem należy aktywować licencję. W przeciwnym razie może to spowodować częste zamykanie serwera konfiguracji i spowodować przeszkodę w działaniach replikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VM.](vmware-azure-manage-configuration-server.md#update-windows-license)

* Czy mogę używać maszyny Wirtualnej, na której serwer konfiguracji jest zainstalowany do różnych celów?

    Nie. Użyj maszyny Wirtualnej wyłącznie do celów serwera konfiguracji. Upewnij się, że należy przestrzegać wszystkich specyfikacji wymienionych w [wymaganiach wstępnych](#prerequisites) do efektywnego zarządzania odzyskiwaniem po awarii.
* Czy mogę przełączyć przechowalnię już zarejestrowaną na serwerze konfiguracji za pomocą nowo utworzonego przechowalni?

    Nie. Po zarejestrowaniu przechowalni na serwerze konfiguracji nie można go zmienić.
* Czy mogę używać tego samego serwera konfiguracji do ochrony maszyn fizycznych i wirtualnych?

    Tak. Ten sam serwer konfiguracji może służyć do replikowania maszyn fizycznych i wirtualnych. Jednak komputer fizyczny można powiodu z powrotem tylko do maszyny wirtualnej VMware.
* Jaki jest cel serwera konfiguracji i gdzie jest używany?

    Aby dowiedzieć się więcej o serwerze konfiguracji i jego funkcjach, zobacz [architektura replikacji VMware na platformę Azure](vmware-azure-architecture.md).
* Gdzie mogę znaleźć najnowszą wersję serwera konfiguracji?

    Aby uzyskać instrukcje uaktualniania serwera konfiguracji za pośrednictwem portalu, zobacz [Uaktualnianie serwera konfiguracji](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Aby uzyskać instrukcje dotyczące uaktualniania wszystkich składników odzyskiwania witryny, zobacz [Aktualizacje usługi w programie Site Recovery](https://aka.ms/asr_how_to_upgrade).
* Gdzie mogę pobrać hasło do serwera konfiguracji?

    Aby pobrać hasło, zobacz [Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VMware VM .](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)
* Czy mogę zmienić hasło?

    Nie. Nie zmieniaj hasła serwera konfiguracji. Zmiana hasła przerywa replikację chronionych maszyn i prowadzi do krytycznego stanu kondycji.
* Gdzie mogę pobrać klucze rejestracyjne skarbca?

    W **przechowalni usług odzyskiwania**wybierz pozycję **Zarządzaj serwerami** > konfiguracji**infrastruktury** > odzyskiwania**lokacji**. W **obszarze Serwery**wybierz pozycję **Pobierz klucz rejestracji,** aby pobrać plik poświadczeń przechowalni.
* Czy mogę sklonować istniejący serwer konfiguracji i używać go do aranżacji replikacji?

    Nie. Użycie składnika serwera konfiguracji sklonowanej nie jest obsługiwane. Klonowanie serwera procesów skalowanych w poziomie jest również nieobsługiwym scenariuszem. Klonowanie składników odzyskiwania lokacji wpływa na trwające replikacje.

* Czy mogę zmienić adres IP serwera konfiguracji?

    Nie. Nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP przypisane do serwera konfiguracji są statyczne adresy IP, a nie adresy IP DHCP.
* Czy mogę skonfigurować serwer konfiguracji na platformie Azure?

    Skonfiguruj serwer konfiguracji w środowisku lokalnym z bezpośrednim punktem widzenia z v-Center i aby zminimalizować opóźnienia transferu danych. W celu powrotu [po awarii](vmware-azure-manage-configuration-server.md#failback-requirements)można wykonać zaplanowane kopie zapasowe serwera konfiguracji.

* Czy można zmienić sterownik pamięci podręcznej na serwerze konfiguracji lub na serwerze procesów skalowanych w poziomie?

    Nie, nie można zmienić sterownika pamięci podręcznej po zakończeniu konfiguracji.

Aby uzyskać więcej często zadawanych pytań dotyczących serwerów konfiguracyjnych, zobacz [Typowe pytania dotyczące serwera konfiguracji](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Następne kroki

Konfigurowanie odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
