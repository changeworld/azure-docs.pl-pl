---
title: Wdrażanie serwera konfiguracji na potrzeby odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wdrażania serwera konfiguracji w celu odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 240f5270d083fa5f4742f3ed2cd61feee2b635ec
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919352"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Wdrażanie serwera konfiguracji w środowisku lokalnym, korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Konfiguracja współrzędne międzyserwerowej komunikacji lokalnych programu VMware i platformą Azure. Umożliwia także zarządzanie replikacją danych. W tym artykule przedstawiono kroki niezbędne do wdrożenia serwera konfiguracji w przypadku replikacji maszyn wirtualnych VMware na platformę Azure. [Postępuj zgodnie z tym artykule](physical-azure-set-up-source.md) Jeśli musisz skonfigurować serwer konfiguracji, replikacji serwera fizycznego.

>[!TIP]
Informacje na temat roli serwera konfiguracji w ramach architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Wdrażanie serwera konfiguracji za pomocą szablonu OVA

Serwer konfiguracji należy skonfigurować jako o wysokiej dostępności maszyny Wirtualnej programu VMware przy użyciu niektóre minimalne wymagania sprzętowe i rozmiaru. W przypadku wdrożenia wygodnie i łatwo Site Recovery udostępnia do pobrania szablonu OVA (Open Virtualization aplikacji), aby skonfigurować serwer konfiguracji, który jest zgodny ze wszystkimi wymaganiami obowiązkowego wymienione poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Minimalne wymagania sprzętowe dla serwera konfiguracji są podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Planowanie pojemności

Wymagania w zakresie rozmiaru serwera konfiguracji zależą od potencjalnych współczynnik zmian danych. Użyj tej tabeli jako wskazówki.

| **CPU** | **Pamięć** | **Rozmiar dysku w pamięci podręcznej** | **Współczynnik zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- | --- |
| 8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie @ 2,5 GHz) |16 GB |300 GB |500 GB lub mniej |Replikowanie maszyn mniej niż 100. |
| 12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) |18 GB |600 GB |Od 500 GB do 1 TB |Replikowanie maszyn 100 150. |
| 16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz) |32 GB |1 TB |1 TB do 2 TB |Replikowanie maszyn 150 – 200. |

Jeśli replikujesz więcej niż jednej maszyny Wirtualnej VMware, zapoznaj się z [zagadnienia dotyczące planowania pojemności](/site-recovery-plan-capacity-vmware.md). Uruchom [narzędzie planista wdrażania](site-recovery-deployment-planner.md) potrzeby replikacji oprogramowania VMWare.

## <a name="download-the-template"></a>Pobierz szablon

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon Open Virtualization aplikacji (OVA) dla serwera konfiguracji.

  > [!TIP]
>Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licencja dostarczane z szablonem OVA jest pozwolenia oceny prawidłowe przez 180 dni. Post tego okresu klient musi wykonać aktywację systemu windows za pomocą licencji uzyskiwanych.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W **wybierz źródło**, wprowadź lokalizację pobranego szablonu OVF.
4. W **Przejrzyj szczegóły**, wybierz opcję **dalej**.
5. W **wybierz nazwę i folder** i **wybierz konfigurację**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Gotowe do ukończenia**:

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Aby dodać więcej kart sieciowych, wyczyść **Włącz po wdrożeniu**, a następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

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
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Zostanie uruchomiony Kreator zarządzania serwerem konfiguracji **automatycznie** w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Konfiguracja łączności**, a następnie wybierz kartę sieciową, której używa serwer przetwarzania do odbierania ruchu związanego z replikacją z maszyn wirtualnych. Następnie wybierz pozycję **Zapisz**. Nie można zmienić to ustawienie, po skonfigurowaniu go.
2. W **magazyn usługi Recovery Services zaznacz**, zaloguj się w usłudze Microsoft Azure, wybierz swoją subskrypcję platformy Azure i grupę zasobów i magazyn.

    > [!NOTE]
    > Po zarejestrowaniu, istnieje możliwość zmiany magazynu usługi recovery services.
    
3. W **instalowanie oprogramowania innych firm**,

    |Scenariusz   |Kroki do wykonania  |
    |---------|---------|
    |Można pobrać & Ręczne instalowanie programu MySQL?     |  Tak. Pobierz aplikację MySQL i umieść go w folderze **C:\Temp\ASRSetup**, następnie zainstalować ręcznie. Teraz, jeśli akceptujesz jej warunki > kliknij **Pobierz i zainstaluj**, portalu jest wyświetlany komunikat *zainstalowane*. Możesz przejść do następnego kroku.       |
    |Można uniknąć pobierania oprogramowania MySQL online?     |   Tak. Umieść aplikację MySQL Instalatora w folderze **C:\Temp\ASRSetup**. Zaakceptuj warunki > kliknij **Pobierz i zainstaluj**, portal użyje Instalatora dodane przez użytkownika i instaluje aplikację. Możesz przejść do następnego kroku po instalacji.    |
    |Chcę pobieranie i instalowanie programu MySQL za pomocą usługi Azure Site Recovery     |  Zaakceptuj Umowę licencyjną i kliknij **Pobierz i zainstaluj**. Następnie możesz przejść do następnego kroku po instalacji.       |
4. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
5. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
6. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz **Dodaj**, a następnie **nadal**. Poświadczenia wprowadzone w tym miejscu lokalnie są zapisywane.
7. W **skonfiguruj poświadczenia maszyny wirtualnej**, wprowadź nazwę użytkownika i hasło maszyn wirtualnych w celu automatycznego zainstalowania usługi mobilności podczas replikacji. Aby uzyskać **Windows** maszyn, konto musi mieć uprawnienia administratora lokalnego na maszynach, którą chcesz replikować. Aby uzyskać **Linux**, podaj szczegóły konta głównego.
8. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
9. Po zakończeniu rejestracji Otwórz witrynę Azure portal, sprawdź, czy serwer konfiguracji i serwer VMware są wyświetlane na **magazyn usługi Recovery Services** > **Zarządzaj**  >  **Infrastruktura usługi site Recovery** > **serwery konfiguracji**.

## <a name="faq"></a>Często zadawane pytania

1. Czy można używać maszyny Wirtualnej, w którym zainstalowany serwer konfiguracji do różnych celów?

    **Nie**, firma Microsoft zaleca na potrzeby maszyny Wirtualnej wyłącznie do celów serwera konfiguracji. Upewnij się, że należy wykonać wszystkich specyfikacji wymienionej w [poprzedniej sekcji](vmware-azure-deploy-configuration-server.md#Prerequisites) efektywne zarządzanie odzyskiwania po awarii.
2. Można przełączyć magazyn już jest zarejestrowany na serwerze konfiguracji przy użyciu nowo utworzonego magazynu?

    **Nie**, po zarejestrowaniu magazynu z serwera konfiguracji nie można zmienić.
3. Czy można użyć tego samego serwera konfiguracji w celu ochrony komputerów fizycznych i maszyn wirtualnych?

    **Tak**, tego samego serwera konfiguracji może służyć do replikowania maszyn fizycznych i wirtualnych. Jednak można maszyny fizycznej kopii tylko do maszyny Wirtualnej VMware.
4. Jakie jest przeznaczenie serwera konfiguracji i gdzie jest używany?

    Odnoszą się do naszej architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md) Aby dowiedzieć się więcej na temat serwera konfiguracji i jego funkcje.
5. Gdzie można znaleźć najnowszą wersję serwera konfiguracji?

    Zapoznaj się z artykułem na temat kroków, aby uaktualnić serwer konfiguracji [za pośrednictwem portalu](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Można również bezpośrednio pobrać go z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
6. Gdzie można pobrać hasła dla serwera konfiguracji?

    Zapoznaj się [w tym artykule](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) można pobrać hasło.
7. Gdzie można pobrać klucze rejestracyjne magazynu?

    W **magazyn usług Recovery Services**, **zarządzanie** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**. Na serwerach, wybierz **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, przeczytaj kroki podane [tutaj](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Kolejne kroki

Konfigurowanie odzyskiwania po awarii [maszyny wirtualne VMware](vmware-azure-tutorial.md) na platformie Azure.
