---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V między lokacjami lokalnymi za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych funkcji Hyper-V między oddziałami w środowisku lokalnym za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 4cc4da130d9253bf40e5d02806088a95b2195e7c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531725"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej w środowisku lokalnym

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym artykule pokazano, jak skonfigurować odzyskiwanie po awarii do lokacji dodatkowej dla lokalnych maszyn wirtualnych z funkcją Hyper-V zarządzane w chmurach programu System Center Virtual Machine Manager (VMM). W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotuj lokalne serwery programu VMM i hosty funkcji Hyper-V
> * Utwórz magazyn usługi Recovery Services dla usługi Site Recovery 
> * Ustaw źródła i docelowych środowisk replikacji. 
> * Skonfiguruj mapowanie sieci 
> * Tworzenie zasad replikacji
> * Włączanie replikacji maszyny wirtualnej

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz:

- Przegląd [składnikach i architekturze scenariusza](hyper-v-vmm-architecture.md).
- Upewnij się, że serwery VMM i hosty funkcji Hyper-V są zgodne z [obsługi wymagań związanych z](hyper-v-vmm-secondary-support-matrix.md).
- Sprawdź, czy mają być replikowane maszyny wirtualne spełniają [replikowane maszyny obsługi](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Przygotowanie serwerów programu VMM do mapowania sieci.

### <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapowania między lokalnymi maszyny Wirtualnej VMM w źródłową i docelową. Mapowanie wykonuje następujące czynności:

- Maszyny wirtualne łączy się z odpowiedniego obiektu docelowego, sieci maszyn wirtualnych po pracy awaryjnej. 
- Optymalnie umieszcza maszyny wirtualne replik na serwerach hostów funkcji Hyper-V docelowych. 
- Jeśli nie skonfigurujesz mapowania sieci, maszyn wirtualnych repliki nie będzie połączony z siecią maszyny Wirtualnej po włączeniu trybu failover.

Przygotowywanie programu VMM w następujący sposób:

1. Upewnij się, że masz [sieciami logicznymi programu VMM](https://docs.microsoft.com/system-center/vmm/network-logical) na serwerach VMM źródłowym i docelowym.
    - Sieci logicznej na serwerze źródłowym powinna być skojarzona z chmurą źródła, w którym znajdują się hosty funkcji Hyper-V.
    - Sieci logicznej na serwerze docelowym powinna być skojarzona z chmurę docelową.
1. Upewnij się, że masz [sieci maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-virtual) na serwerach VMM źródłowym i docelowym. Sieci maszyn wirtualnych powinna być połączona z siecią logiczną, w każdej lokalizacji.
2. Łączenie maszyn wirtualnych na hostach funkcji Hyper-V źródłowej do źródłowej sieci maszyny Wirtualnej. 


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Wybierz cel ochrony

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.

1. Kliknij przycisk **Site Recovery** > **krok 1: Przygotowanie infrastruktury** > **cel ochrony**.
2. Wybierz **do lokacji odzyskiwania**i wybierz **tak, przy użyciu funkcji Hyper-V**.
3. Wybierz **tak** do wskazania, że używasz programu VMM do zarządzania hostami funkcji Hyper-V.
4. Wybierz **tak** Jeśli pomocniczy serwer programu VMM. Jeśli wdrażasz replikacji między chmurami na jednym serwerze programu VMM kliknij **nie**. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Zainstaluj dostawcę usługi Azure Site Recovery na serwerach programu VMM i Odnajdź i Zarejestruj serwer w magazynie.

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Źródło**.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ VMM**, aby dodać serwer programu VMM.
3. W **Dodaj serwer**, sprawdź, czy **serwer programu System Center VMM** pojawia się w **typ serwera**.
4. Pobierz plik instalacyjny dostawcy usługi Azure Site Recovery.
5. Pobierz klucz rejestracji. Jest to potrzebne podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Zainstaluj dostawcę na każdym serwerze programu VMM. Nie trzeba jawnie instalowania żadnych narzędzi na hostach funkcji Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Zainstaluj dostawcę usługi Azure Site Recovery

1. Uruchom plik Instalatora na każdym serwerze programu VMM dostawcy. Jeśli program VMM jest wdrożony w klastrze, zainstaluj po raz pierwszy w następujący sposób:
    -  Zainstaluj dostawcę na aktywnym węźle i ukończ instalację Aby zarejestrować serwer VMM w magazynie.
    - Następnie należy zainstalować dostawcę na innych węzłach. Węzły klastra należy uruchomić tę samą wersję dostawcy.
2. Instalator jest uruchamiany kilka wymagań wstępnych i żąda uprawnienia do zatrzymania usługi programu VMM. Usługa programu VMM zostanie automatycznie uruchomiona po zakończeniu instalacji. Po zainstalowaniu w klastrze programu VMM, wyświetlany jest monit o zatrzymanie roli klastra.
3. W **Microsoft Update**, można włączyć do określenia, czy aktualizacje dostawcy były instalowane zgodnie z zasadami Microsoft Update.
4. W **instalacji**, Zaakceptuj lub zmodyfikuj domyślną lokalizację instalacji, a następnie kliknij przycisk **zainstalować**.
5. Po zakończeniu instalacji kliknij przycisk **zarejestrować** do zarejestrowania serwera w magazynie.

    ![Lokalizacja instalacji](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Zweryfikuj, że w polu **Nazwa magazynu** jest wpisana nazwa magazynu, w którym serwer zostanie zarejestrowany. Kliknij przycisk **Dalej**.
7. W **połączenia serwera Proxy**, określ, jak dostawca uruchomiony na serwerze programu VMM łączy na platformie Azure.
   - Można określić, czy dostawca należy łączyć bezpośrednio do Internetu lub za pośrednictwem serwera proxy. Określ ustawienia serwera proxy, zgodnie z potrzebami.
   - Jeśli używasz serwera proxy, konto Uruchom jako programu VMM (DRAProxyAccount) zostanie utworzony automatycznie, przy użyciu określonych poświadczeń serwera proxy. Skonfiguruj serwer proxy tak, aby to konto mogło być pomyślnie uwierzytelnione. Ustawienia konta Uruchom jako, które można modyfikować w konsoli programu VMM > **ustawienia** > **zabezpieczeń** > **konta Uruchom jako**.
   - Uruchom ponownie usługę VMM, aby zaktualizować zmiany.
8. W **klucz rejestracji**, wybierz klucz, który został pobrany i skopiowany na serwer programu VMM.
9. Ustawienia szyfrowania nie jest ważna w tym scenariuszu. 
10. W polu **Nazwa serwera** wprowadź przyjazną nazwę identyfikującą serwer VMM w magazynie. W klastrze Określ nazwę roli klastra VMM.
11. W **synchronizacja metadanych chmury**wybierz, czy chcesz synchronizować metadane dla wszystkich chmur na serwerze programu VMM. To działanie ma miejsce tylko raz na każdym serwerze. Jeśli nie chcesz synchronizować wszystkich chmur, zaznaczać tego ustawienia. Synchronizować poszczególne chmury indywidualnie we właściwościach chmury w konsoli programu VMM.
12. Kliknij przycisk **Dalej**, aby ukończyć proces. Po rejestracji usługa Site Recovery pobiera metadane z serwera programu VMM. Serwer jest wyświetlany w **serwerów** > **serwery VMM** w magazynie.
13. Po wyświetleniu serwera w magazynie w **źródła** > **przygotowywanie źródła** wybierz serwer programu VMM, a następnie wybierz chmurę, w której zlokalizowany jest host funkcji Hyper-V. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz docelowy serwer programu VMM i chmurę:

1. Kliknij przycisk **przygotowanie infrastruktury** > **docelowej**i wybierz docelowy serwer programu VMM.
2. Wyświetlane są chmur programu VMM, które są synchronizowane z usługą Site Recovery. Wybierz chmurę docelową.

   ![Środowisko docelowe](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

Przed rozpoczęciem upewnij się, że wszystkie hosty, które korzystają z zasad mają ten sam system operacyjny. Jeśli hosty są uruchomione różne wersje systemu Windows Server, konieczne będzie wiele zasad replikacji.

1. Aby utworzyć nowe zasady replikacji, kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Typ źródłowy i docelowy powinien być **funkcji Hyper-V**.
3. W **wersja hosta funkcji Hyper-V**, wybierz system operacyjny jest uruchomiona na hoście.
4. W **typ uwierzytelniania** i **port uwierzytelniania**, określ, jak ruch jest uwierzytelniany między podstawowymi i odzyskiwania serwerów hostów funkcji Hyper-V.
    - Wybierz **certyfikatu** chyba, że masz już działający środowiska protokołu Kerberos. Usługa Azure Site Recovery automatycznie skonfiguruje certyfikatów do uwierzytelniania protokołu HTTPS. Nie trzeba wykonywać żadnych czynności ręcznie.
    - Domyślnie przez port 8083 i 8084 (w przypadku certyfikatów) zostanie otwarty w Zaporze Windows na serwerach hostów funkcji Hyper-V.
    - Jeśli wybierzesz **Kerberos**, biletu protokołu Kerberos będzie służyć do wzajemnego uwierzytelniania serwerów hosta. Protokół Kerberos jest stosowana tylko dla serwerów hostów funkcji Hyper-V uruchomiony w systemie Windows Server 2012 R2 lub nowszym.
1. W obszarze **Częstotliwość kopiowania** określ, jak często mają być replikowane dane przyrostowe po replikacji początkowej (co 30 sekund, 5 lub 15 minut).
2. W **czas przechowywania punktu odzyskiwania**, określ \how czas (w godzinach) będzie okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny można odzyskać do dowolnego punktu w tym oknie.
3. W **częstotliwość migawek spójności aplikacji**, określ, jak często (1 – 12 godzin) punkty odzyskiwania zawierające migawki spójne z aplikacjami są tworzone. Funkcji Hyper-V wykorzystuje dwa typy migawek:
    - **Standardowa migawka**: jest przyrostową migawką całej maszyny wirtualnej.
    - **Migawka spójności aplikacji**: tworzy migawkę punktu w czasie danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje są w spójnym stanie, gdy wykonywana jest migawka. Włączanie migawki spójne z aplikacjami, ma wpływ na wydajność aplikacji na źródłowe maszyny wirtualne. Wartość, która jest mniejsza niż liczba punktów odzyskiwania dodatkowe, które można skonfigurować.
4. W **kompresja transferu danych**, określ, czy dane replikacji przeniesionych powinien być skompresowany.
5. Wybierz **Usuń replikę maszyny Wirtualnej**, aby określić, że maszyna wirtualna repliki, należy usunąć, po wyłączeniu ochrony dla źródłowej maszyny Wirtualnej. Jeśli włączysz to ustawienie po wyłączeniu ochrony dla źródłowej maszyny Wirtualnej, zostanie on usunięty z konsoli usługi Site Recovery, ustawienia usługi Site Recovery dla programu VMM są usuwane z konsoli programu VMM, a replika zostanie usunięty.
6. W **metodę replikacji początkowej**, jeśli przeprowadzasz replikację za pośrednictwem sieci, określ, czy rozpoczyna się Replikacja początkowa lub zaplanować je. Aby oszczędzić przepustowość sieci, warto zaplanować ją poza najbardziej obciążonymi godzinami. Następnie kliknij przycisk **OK**.

     ![Zasady replikacji](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nowe zasady zostaną automatycznie skojarzone z chmurą programu VMM. W **zasad replikacji**, kliknij przycisk **OK**. 


## <a name="enable-replication"></a>Włączanie replikacji

1. Kliknij kolejno pozycje **Replikowanie aplikacji** > **Źródło**. 
2. W **źródła**, wybierz serwer programu VMM i chmurę, w którym znajdują się hosty Hyper-V, którą chcesz replikować. Następnie kliknij przycisk **OK**.
3. W **docelowej**, sprawdź pomocniczy serwer programu VMM i w chmurze.
4. W **maszyn wirtualnych**, wybierz maszyny wirtualne, które chcesz chronić, z listy.


Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po **Finalizuj ochronę** zadanie zostanie ukończone, Replikacja początkowa zostanie zakończona i maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="next-steps"></a>Kolejne kroki

[Uruchamianie próbnego odzyskiwania po awarii](hyper-v-vmm-test-failover.md)
