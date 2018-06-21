---
title: Wdrażanie serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 4d944bec9d1cf73263d9deb54d0d9b3f6fc5532f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285037"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Lokalny serwer konfiguracji jest wdrażany, kiedy należy używać [usługi Azure Site Recovery](site-recovery-overview.md) odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure. Konfiguracja serwera współrzędnych komunikacji między lokalnymi VMware i na platformie Azure. Umożliwia także zarządzanie replikacji danych. W tym artykule przedstawiono kroki potrzebne do wdrożenia serwera konfiguracji w przypadku replikacji maszyn wirtualnych VMware do platformy Azure. [Wykonaj w tym artykule](physical-azure-set-up-source.md) Jeśli musisz skonfigurować serwer konfiguracji replikacji serwera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne

Zaleca się wdrożenie serwera konfiguracji jako wysokiej dostępności maszyny Wirtualnej VMware. Poniższa tabela zawiera podsumowanie wymagań serwera konfiguracji.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



### <a name="prepare-for-mysql-installation"></a>Przygotowanie do instalacji MySQL

MySQL musi być zainstalowany na serwerze konfiguracji. Można to zrobić przy użyciu jednej z następujących metod:

- Let Site Recovery, Pobierz i zainstaluj go po uruchomieniu Kreatora konfiguracji serwera zarządzania. Nie trzeba wykonywać żadnych czynności w określonych.
- Ręcznie pobrać MySQL i umieść go w folderze C:\Temp\ASRSetup. Następnie uruchom instalację. Usługa Site Recovery rozpozna, że jest zainstalowany, po uruchomieniu kreatora.
- Ręcznie pobrać MySQL i umieść go w folderze C:\Temp\ASRSetup. Kreator podczas uruchamiania go będzie Znajdź plik Instalatora i zainstaluj go z tej lokalizacji. 


## <a name="capacity-planning"></a>Planowanie pojemności

Zmiany rozmiaru wymagania dotyczące serwera konfiguracji są zależne od potencjalnych częstotliwości zmian danych. Użyj tej tabeli jako przewodnika.

| **CPU** | **Pamięci** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz) |16 GB |300 GB |500 GB lub mniej |Replikowanie maszyn mniej niż 100. |
| 12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) |18 GB |600 GB |500 GB do 1 TB |Replikowanie maszyn 100 150. |
| 16 Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz) |32 GB |1 TB |1 TB do 2 TB |Replikowanie maszyn 150 – 200. |


Jeśli replikujesz maszyny wirtualne VMware, przeczytaj więcej na temat [zagadnienia związane z planowaniem pojemności](/site-recovery-plan-capacity-vmware.md). Uruchom [narzędzi planowania wdrożenia](site-recovery-deployment-planner.md) replikacji VMWare.



## <a name="download-the-template"></a>Pobierz szablon

Usługa Site Recovery zapewnia do pobrania szablonu, aby skonfigurować serwer konfiguracji jako wysokiej dostępności maszyny Wirtualnej VMware. 

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon serwera konfiguracji w formacie OVF (Open Virtualization Format).

  > [!TIP]
  Możesz pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware


1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W **wybierz źródło**, wprowadź lokalizację pobranego pakietu OVF.
4. W **Przejrzyj szczegóły**, wybierz pozycję **dalej**.
5. W **wybierz nazwę i folder** i **konfiguracji wybierz**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**.
4. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
5. Na stronie **Gotowe do ukończenia**:

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Aby dodać dodatkowy interfejs sieciowy, wyczyść **włączają po wdrożeniu**, a następnie wybierz **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.


## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Jeśli chcesz dodać dodatkowe kartę Sieciową do konfiguracji serwera, należy go dodać, aby zarejestrować serwer w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć. 
4. Aby nawiązać połączenie z wirtualną kartą sieciową po włączeniu maszyny wirtualnej, zaznacz pole **Połącz po włączeniu**. Następnie wybierz **dalej** > **Zakończ** > **OK**.
 

## <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji 

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Po pierwszym zalogowaniu zostanie uruchomione narzędzie do konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W Kreatorze konfiguracji serwera zarządzania zaznacz **ustawienia łączności**. Wybierz kartę Sieciową, aby odbierać ruch związany z replikacją, a następnie wybierz **zapisać**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
2. Na stronie **Wybierz magazyn usługi Recovery Services** wybierz swoją subskrypcję platformy Azure, grupę zasobów i magazyn.
3. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Zainstaluj MySQL zgodnie z [metody używane do zainstalowania MySQL](#prepare-for-mysql-installation).
4. Wybierz **zainstalować VMware PowerLCI**. Upewnij się, że wszystkie okna przeglądarki są zamknięte przed wykonaniem tego kroku. Następnie wybierz pozycję **Kontynuuj**.
5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, którym nasłuchuje serwer i przyjazną nazwę dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Kontynuuj**.
8. W **skonfigurować poświadczenia maszyny wirtualnej**, wprowadź nazwę użytkownika i hasło do użycia, aby automatycznie zainstalować usługi mobilności Azure Site Recovery na komputerach, gdy replikacja jest włączona. W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach. W przypadku systemu Linux należy podać dane superużytkownika.
9. Wybierz **konfiguracji Finalize** aby zakończyć rejestrację. 
10. Po zakończeniu rejestracji sprawdź w witrynie Azure Portal, czy serwer konfiguracji i serwer VMware są widoczne na stronie **Źródło** w magazynie. Następnie wybierz pozycję **OK**, aby skonfigurować ustawienia środowiska docelowego.


## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, przeczytaj kroki podane [tutaj](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)


## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Kolejne kroki

Konfigurowanie odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
