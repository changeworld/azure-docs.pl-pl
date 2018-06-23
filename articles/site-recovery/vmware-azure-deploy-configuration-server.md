---
title: Wdrażanie serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 841176d8c5f215d18edf25b1f191792b37555fa9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318123"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Lokalny serwer konfiguracji jest wdrażany, kiedy należy używać [usługi Azure Site Recovery](site-recovery-overview.md) odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure. Konfiguracja serwera współrzędnych komunikacji między lokalnymi VMware i na platformie Azure. Umożliwia także zarządzanie replikacji danych. W tym artykule przedstawiono kroki potrzebne do wdrożenia serwera konfiguracji w przypadku replikacji maszyn wirtualnych VMware do platformy Azure. [Wykonaj w tym artykule](physical-azure-set-up-source.md) Jeśli musisz skonfigurować serwer konfiguracji replikacji serwera fizycznego.

>[!TIP]
Informacje na temat roli serwera konfiguracji w ramach architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Wdrożenie serwera konfiguracji przy użyciu szablonu komórki jajowe

Serwer konfiguracji musi być skonfigurowany jako wysokiej dostępności maszyny Wirtualnej VMware z niektórych minimalne wymagania sprzętowe i zmiany rozmiaru. Wygodnie i łatwo wdrożenia Usługa Site Recovery zapewnia do pobrania szablonu komórki jajowe (Open Virtualization aplikacji) do konfigurowania serwera konfiguracji, które spełnia wszystkie wymagania obowiązkowego wymienionych poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Minimalne wymagania sprzętowe dotyczące serwera konfiguracji są podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Planowanie pojemności

Zmiany rozmiaru wymagania dotyczące serwera konfiguracji są zależne od potencjalnych częstotliwości zmian danych. Użyj tej tabeli jako przewodnika.

| **CPU** | **Pamięci** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz) |16 GB |300 GB |500 GB lub mniej |Replikowanie maszyn mniej niż 100. |
| 12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) |18 GB |600 GB |500 GB do 1 TB |Replikowanie maszyn 100 150. |
| 16 Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz) |32 GB |1 TB |1 TB do 2 TB |Replikowanie maszyn 150 – 200. |

Jeśli replikujesz więcej niż jednej maszyny Wirtualnej VMware odczytu [zagadnienia związane z planowaniem pojemności](/site-recovery-plan-capacity-vmware.md). Uruchom [narzędzi planowania wdrożenia](site-recovery-deployment-planner.md) replikacji VMWare.

## <a name="download-the-template"></a>Pobierz szablon

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon aplikacji komórek (Open Virtualization jajowych) dla serwera konfiguracji.

  > [!TIP]
>Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Pozwolenie dostarczane z komórek jajowych szablonu jest pozwolenia oceny prawidłowy na okres 180 dni. Post tego okresu, nabywca musi wykonać aktywację systemu windows z licencji uzyskiwanych.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W **wybierz źródło**, wprowadź lokalizację pobranego pakietu OVF.
4. W **Przejrzyj szczegóły**, wybierz pozycję **dalej**.
5. W **wybierz nazwę i folder** i **konfiguracji wybierz**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Gotowe do ukończenia**:

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Aby dodać dodatkowy interfejs sieciowy, wyczyść **włączają po wdrożeniu**, a następnie wybierz **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Jeśli chcesz dodać dodatkowe kartę Sieciową do konfiguracji serwera, należy go dodać, aby zarejestrować serwer w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć. 
4. Aby Podłącz wirtualną kartę Sieciową, gdy maszyna wirtualna jest włączona, wybierz **Connect w włączania zasilania**. Następnie wybierz **dalej** > **Zakończ** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Zarejestruj serwer konfiguracji z usług Azure Site Recovery

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Możesz się zalogować, w ciągu kilku sekund narzędzie konfiguracji odzyskiwania lokacji Azure po raz pierwszy uruchamia.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Uruchamia Kreatora konfiguracji serwera zarządzania **automatycznie** w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Konfiguracja łączności**, a następnie wybierz kartę sieciową, której używa serwer przetwarzania do odbierania ruchu związanego z replikacją z maszyn wirtualnych. Następnie wybierz pozycję **Zapisz**. Nie można zmienić to ustawienie po skonfigurowaniu go.
2. W **magazyn usług odzyskiwania wybierz**, zaloguj się do systemu Microsoft Azure, wybierz subskrypcję platformy Azure i odpowiedniej grupy zasobów i magazynu.
    >[!NOTE]
    > Po zarejestrowaniu, istnieje możliwość zmiany magazynu usług odzyskiwania.
3. W **zainstalować oprogramowanie innych firm**,

    |Scenariusz   |Kroki do wykonania  |
    |---------|---------|
    |Można pobrać & ręcznie zainstalować MySQL?     |  Tak. Pobierz aplikację MySQL i umieść go w folderze **C:\Temp\ASRSetup**, następnie zainstalować ręcznie. Teraz, kiedy należy zaakceptować postanowienia > kliknij **Pobierz i zainstaluj**, portalu mówi *już zainstalowane*. Możesz przejść do następnego kroku.       |
    |Czy można uniknąć pobierania MySQL online?     |   Tak. Umieść MySQL aplikacji Instalatora w folderze **C:\Temp\ASRSetup**. Zaakceptuj postanowienia > kliknij **Pobierz i zainstaluj**, portal użyje Instalator dodane przez użytkownika i instaluje aplikację. Możesz przejść do następnego kroku po instalacji.    |
    |Chcę pobrać i zainstalować MySQL za pośrednictwem usługi Azure Site Recovery     |  Zaakceptuj Umowę licencyjną i kliknij na **Pobierz i zainstaluj**. Następnie możesz przejść do następnego kroku po instalacji.       |
4. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
5. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
6. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz **dodać**, a następnie **kontynuować**. Poświadczenia wprowadzanym w tym miejscu są zapisywane lokalnie.
7. W **skonfigurować poświadczenia maszyny wirtualnej**, wprowadź nazwę użytkownika i hasło maszyn wirtualnych, aby automatycznie zainstalować usługi mobilności podczas replikacji. Aby uzyskać **Windows** maszyny, konto wymaga uprawnień administratora lokalnego na maszynach, którą chcesz replikować. Aby uzyskać **Linux**, podaj szczegóły konta głównego.
8. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
9. Po zakończeniu rejestracji, otwórz Azure portal, sprawdź, czy serwer konfiguracji i serwera VMware są wyświetlane **magazyn usług odzyskiwania** > **Zarządzaj**  >  **Infrastrukturę odzyskiwania lokacji** > **serwery konfiguracji**.

## <a name="faq"></a>Często zadawane pytania

1. Można użyć maszyny Wirtualnej, której jest zainstalowany serwer konfiguracji do różnych celów? **Nie**, serwer konfiguracji musi być serwerem jednozadaniowe i używać go jako udostępniony serwera nie jest obsługiwany.
2. Można przełączyć magazyn już jest zarejestrowany na serwerze konfiguracji z nowo utworzonego magazynu? **Nie**, gdy magazyn jest zarejestrowany na serwerze konfiguracji, nie można zmienić.
3. Do ochrony komputerów fizycznych i maszyn wirtualnych można użyć tego samego serwera konfiguracji? **Tak**, tym samym serwerze konfiguracji może służyć do replikowania fizycznych i maszyn wirtualnych. Jednak powrotu po awarii do komputera fizycznego nie jest obsługiwana.
4. Gdy będzie używany serwer konfiguracji? Odwoływać się do naszej architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md) Aby dowiedzieć się więcej na temat konfiguracji serwera i jego funkcje.
5. Gdzie można znaleźć najnowszej wersji serwera konfiguracji? Można pobrać bezpośrednio z poziomu [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Zapoznaj się z artykułem na kroki, aby serwer konfiguracji uaktualnienia [tutaj](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, przeczytaj kroki podane [tutaj](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Kolejne kroki

Konfigurowanie odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
