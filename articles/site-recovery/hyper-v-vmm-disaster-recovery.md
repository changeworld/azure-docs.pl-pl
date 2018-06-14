---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V między lokacjami lokalnymi z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V między oddziałami lokalnych z usługą Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843463"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej lokalnymi

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym artykule przedstawiono sposób konfigurowania odzyskiwania po awarii lokacji dodatkowej, dla maszyn wirtualnych funkcji Hyper-V lokalnego zarządzane w chmurach programu System Center Virtual Machine Manager (VMM). W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Przygotowanie serwerów lokalnych, VMM i hosty funkcji Hyper-V
> * Tworzenie magazynu usług odzyskiwania Site Recovery 
> * Ustaw źródła i docelowymi środowisk replikacji. 
> * Skonfigurowanie mapowania sieci 
> * Tworzenie zasad replikacji
> * Włączanie replikacji maszyny wirtualnej

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz:

- Przegląd [architektura scenariusza i składniki](hyper-v-vmm-architecture.md).
- Upewnij się, że serwery VMM i hosty funkcji Hyper-V są zgodne z [spełnić wymagania dotyczące](hyper-v-vmm-secondary-support-matrix.md).
- Sprawdź, czy mają być replikowane maszyny wirtualne są zgodne z [replikowane maszyny Obsługa](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Przygotowanie serwerów programu VMM do mapowania sieci.

### <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapowania między lokalnej sieci maszyny Wirtualnej programu VMM w źródłowej i docelowej chmury. Mapowanie wykonuje następujące czynności:

- Maszyny wirtualne łączy się z odpowiednich docelowych sieci maszyn wirtualnych po pracy awaryjnej. 
- Optymalnie umieszcza maszyny wirtualne repliki na serwerach hostów funkcji Hyper-V docelowych. 
- Jeśli nie skonfigurujesz mapowania sieci, repliki maszyn wirtualnych nie będzie podłączona do sieci maszyn wirtualnych po pracy awaryjnej.

Przygotowanie programu VMM w następujący sposób:

1. Upewnij się, że masz [sieci logicznych program VMM](https://docs.microsoft.com/system-center/vmm/network-logical) na serwerach VMM źródłowych i docelowych.
    - Sieci logicznej na serwerze źródłowym powinna być skojarzona z chmurą źródła, w którym znajdują się hosty funkcji Hyper-V.
    - Sieci logicznej na serwerze docelowym powinna być skojarzona z chmurą docelowej.
1. Upewnij się, że masz [sieci maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-virtual) na serwerach VMM źródłowych i docelowych. Sieci maszyn wirtualnych powinna być połączona z siecią logiczną, w każdej lokalizacji.
2. Połączenie maszyn wirtualnych na hostach funkcji Hyper-V źródła do źródłowej sieci maszyny Wirtualnej. 


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu Usług odzyskiwania

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Wybierz cel ochrony

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.

1. Kliknij przycisk **usługi Site Recovery** > **krok 1: Przygotowanie infrastruktury** > **cel ochrony**.
2. Wybierz **do odzyskiwania lokacji**i wybierz **tak, z funkcją Hyper-V**.
3. Wybierz **tak** aby wskazać, że używasz programu VMM na potrzeby zarządzania hostami funkcji Hyper-V.
4. Wybierz **tak** Jeśli pomocniczy serwer programu VMM. Jeśli wdrażasz replikacji między chmurami na jednym serwerze programu VMM, kliknij przycisk **nr**. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Zainstaluj dostawcę usługi Azure Site Recovery na serwerach VMM i Odnajdź i Zarejestruj serwer w magazynie.

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Źródło**.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ VMM**, aby dodać serwer programu VMM.
3. W **Dodaj serwer**, sprawdź, czy **serwera programu System Center VMM** pojawia się w **typ serwera**.
4. Pobierz plik instalacyjny dostawcy usługi Azure Site Recovery.
5. Pobierz klucz rejestracji. Należy to po zainstalowaniu dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Zainstaluj dostawcę na każdym serwerze programu VMM. Nie trzeba już nic jawnie instalować na hostach funkcji Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Zainstaluj dostawcę usługi Azure Site Recovery

1. Uruchom plik Instalatora na każdym serwerze programu VMM dostawcę. Jeśli program VMM jest wdrożony w klastrze, zainstalować po raz pierwszy w następujący sposób:
    -  Zainstaluj dostawcę na aktywnym węźle i ukończ instalację, aby zarejestrować serwer VMM w magazynie.
    - Następnie należy zainstalować dostawcę na innych węzłach. Węzły klastra należy uruchomić tej samej wersji dostawcy.
2. Instalator uruchamia kilka wymagań wstępnych i prosi o uprawnienie do zatrzymania usługi programu VMM. Usługa VMM zostanie automatycznie uruchomiona po zakończeniu instalacji. Jeśli zostanie zainstalowana w klastrze programu VMM, zostanie wyświetlony monit o zatrzymanie roli klastra.
3. W **Microsoft Update**, można włączyć w celu określenia, czy aktualizacje dostawcy były instalowane zgodnie z zasadami Microsoft Update.
4. W **instalacji**, Zaakceptuj lub zmodyfikuj domyślną lokalizację instalacji, a następnie kliknij przycisk **zainstalować**.
5. Po zakończeniu instalacji kliknij przycisk **zarejestrować** Aby zarejestrować serwer w magazynie.

    ![Lokalizacja instalacji](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Zweryfikuj, że w polu **Nazwa magazynu** jest wpisana nazwa magazynu, w którym serwer zostanie zarejestrowany. Kliknij przycisk **Dalej**.
7. W **połączenia serwera Proxy**, określ, jak dostawca uruchomiony na serwerze programu VMM łączy do platformy Azure.
   - Można określić, że dostawca należy łączyć bezpośrednio z Internetem, lub za pośrednictwem serwera proxy. Określ ustawienia serwera proxy.
   - Jeśli używasz serwera proxy konto Uruchom jako programu VMM (DRAProxyAccount) zostanie utworzony automatycznie, przy użyciu określonych poświadczeń serwera proxy. Skonfiguruj serwer proxy tak, aby to konto mogło być pomyślnie uwierzytelnione. Ustawienia konta Uruchom jako można modyfikować w konsoli programu VMM > **ustawienia** > **zabezpieczeń** > **konta Uruchom jako**.
   - Uruchom ponownie usługę VMM, aby zaktualizować zmiany.
8. W **klucz rejestracji**, wybierz klucz, który zostanie pobrane i skopiowane do serwera programu VMM.
9. Ustawienie szyfrowania nie jest ważna w tym scenariuszu. 
10. W polu **Nazwa serwera** wprowadź przyjazną nazwę identyfikującą serwer VMM w magazynie. W klastrze Określ nazwę roli klastra VMM.
11. W **Synchronizuj metadane chmury**wybierz, czy chcesz synchronizować metadane dla wszystkich chmur na serwerze programu VMM. To działanie ma miejsce tylko raz na każdym serwerze. Jeśli nie chcesz synchronizować wszystkich chmur, zaznaczać tego ustawienia. Synchronizować poszczególne chmury indywidualnie we właściwościach chmury w konsoli programu VMM.
12. Kliknij przycisk **Dalej**, aby ukończyć proces. Po rejestracji odzyskiwania lokacji pobiera metadane z serwera programu VMM. Serwer jest wyświetlany w **serwerów** > **serwery VMM** w magazynie.
13. Po wyświetleniu serwera w magazynie, **źródła** > **Przygotuj źródło** wybierz serwer programu VMM, a następnie wybierz chmurę, w którym znajduje się na hoście funkcji Hyper-V. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz docelowy serwer programu VMM oraz chmury:

1. Kliknij przycisk **przygotowanie infrastruktury** > **docelowego**i wybierz docelowy serwer programu VMM.
2. Chmur programu VMM, które są synchronizowane z usługą Site Recovery są wyświetlane. Wybierz chmurę docelową.

   ![Środowisko docelowe](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

Przed rozpoczęciem upewnij się, że wszystkie hosty przy użyciu zasad mają ten sam system operacyjny. Hosty są uruchomione różne wersje systemu Windows Server, należy najpierw wielu skojarzeń zasad replikacji.

1. Aby utworzyć nowe zasady replikacji, kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Typ źródłowy i docelowy powinien być **funkcji Hyper-V**.
3. W **host funkcji Hyper-V w wersji**, wybierz system operacyjny jest uruchomiona na hoście.
4. W **typ uwierzytelniania** i **port uwierzytelniania**, określ, jak uwierzytelnianie ruchu między serwerami hosta funkcji Hyper-V podstawowymi i odzyskiwania.
    - Wybierz **certyfikatu** w przypadku braku działającego środowiska protokołu Kerberos. Usługa Azure Site Recovery automatycznie skonfiguruje certyfikatów do uwierzytelniania protokołu HTTPS. Nie trzeba wykonywać żadnych czynności, ręcznie.
    - Domyślnie port 8083 i 8084 (dla certyfikatów) zostanie otwarty w Zaporze systemu Windows na serwerach hostów funkcji Hyper-V.
    - Jeśli wybierzesz **Kerberos**, biletu protokołu Kerberos będzie używany do wzajemnego uwierzytelniania serwerów hosta. Protokół Kerberos jest stosowana tylko dla serwerów hostów funkcji Hyper-V uruchomiona w systemie Windows Server 2012 R2 lub nowszym.
1. W obszarze **Częstotliwość kopiowania** określ, jak często mają być replikowane dane przyrostowe po replikacji początkowej (co 30 sekund, 5 lub 15 minut).
2. W **przechowywania punktu odzyskiwania**, określ \how czas (w godzinach) będzie okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny można odzyskać do dowolnego punktu w tym oknie.
3. W **częstotliwość migawek spójności aplikacji**, określ, jak często (1 – 12 godzin) punkty odzyskiwania zawierające migawki spójne z aplikacjami są tworzone. Funkcja Hyper-V wykorzystuje dwa typy migawek:
    - **Standardowa migawka**: jest przyrostową migawką całej maszyny wirtualnej.
    - **Migawki dotyczącej spójności aplikacji**: tworzy migawkę w momencie danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje są w spójnym stanie podczas wykonywania migawki. Włączanie migawki spójne z aplikacjami, wpływa na wydajność aplikacji w źródle maszyn wirtualnych. Wartość, która jest mniejsza niż liczba punktów odzyskiwania dodatkowe, które można skonfigurować.
4. W **kompresję transferu danych**, określ, czy dane replikacji przesyłane powinny być kompresowane.
5. Wybierz **Usuń replikę maszyny Wirtualnej**, aby określić, że po wyłączeniu ochrony dla źródłowej maszyny Wirtualnej należy usunąć maszynę wirtualną repliki. Jeśli to ustawienie zostanie włączone po wyłączeniu ochrony dla źródłowej maszyny Wirtualnej zostanie usunięte z poziomu konsoli usługi Site Recovery, ustawienia usługi Site Recovery programu VMM są w konsoli programu VMM, i repliki zostaną usunięte.
6. W **metodę replikacji początkowej**, jeśli przeprowadzasz replikację za pośrednictwem sieci, określ, czy Rozpocznij replikację początkową lub zaplanowania jej. Aby oszczędzić przepustowość sieci, warto zaplanować ją poza najbardziej obciążonymi godzinami. Następnie kliknij przycisk **OK**.

     ![Zasady replikacji](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nowe zasady jest automatycznie kojarzony z chmurą VMM. W **zasad replikacji**, kliknij przycisk **OK**. 


## <a name="enable-replication"></a>Włączanie replikacji

1. Kliknij kolejno pozycje **Replikowanie aplikacji** > **Źródło**. 
2. W **źródła**, wybierz serwer programu VMM oraz chmury, w którym znajdują się hosty funkcji Hyper-V mają być replikowane. Następnie kliknij przycisk **OK**.
3. W **docelowego**, sprawdź pomocniczy serwer programu VMM i w chmurze.
4. W **maszyn wirtualnych**, wybierz maszyny wirtualne, które chcesz chronić z listy.


Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po **zakończenia ochrony** zadanie zostało ukończone, Replikacja początkowa została zakończona i maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="next-steps"></a>Kolejne kroki

[Uruchamianie próbnego odzyskiwania po awarii](hyper-v-vmm-test-failover.md)
