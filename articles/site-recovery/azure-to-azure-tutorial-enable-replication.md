---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w innym regionie świadczenia usługi Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0404774f1cb347ceead8b78d1a9a6506712dea5c
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069101"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku przedstawiono sposób konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym świadczenia usługi Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu usługi Recovery Services
> * Sprawdzanie ustawień zasobów docelowych
> * Konfigurowanie wychodzącego dostępu dla maszyn wirtualnych
> * Włączanie replikacji maszyny wirtualnej


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](concepts-azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-azure-to-azure.md) wszystkich składników.

## <a name="create-a-vault"></a>Tworzenie magazynu

Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Monitorowanie i zarządzanie** > **Backup and Site Recovery**.
3. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów lub wybierz istniejącą grupę. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

   ![Nowy magazyn](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.

## <a name="verify-target-resources"></a>Sprawdzanie zasobów docelowych

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Usługa Site Recovery wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

## <a name="configure-outbound-network-connectivity"></a>Konfigurowanie połączenia sieciowego ruchu wychodzącego

Aby zapewnić właściwe działanie usługi Site Recovery, musisz wprowadzić kilka zmian w połączeniu sieciowego ruchu wychodzącego z maszyn wirtualnych, które mają być replikowane.

- Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.
- Jeśli korzystasz z uwierzytelniającego serwera proxy, nie możesz włączyć replikacji.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli do sterowania ruchem wychodzącym używasz opartego na adresach URL serwera proxy z zaporą, zezwól na dostęp do następujących adresów URL, używanych przez usługę Site Recovery.

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| *.blob.core.windows.net | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| login.microsoftonline.com | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| *.servicebus.windows.net | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli chcesz kontrolować połączenia wychodzące przy użyciu adresów IP zamiast adresów URL, umieść na białej liście odpowiednie zakresy centrum danych, adresy usługi Office 365 oraz adresy punktów końcowych usługi dla zapór protokołu IP, serwerów proxy lub reguł sieciowej grupy zabezpieczeń.

  - [Zakresy adresów IP centrum danych platformy Microsoft Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Zakresy adresów IP centrum danych platformy Windows Azure w Niemczech](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Zakresy adresów IP centrum danych platformy Windows Azure w Chinach](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Zakresy adresów URL i IP dla usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Adresy IP punktów końcowych usługi Site Recovery](https://aka.ms/site-recovery-public-ips)

Ten [skrypt](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) ułatwia tworzenie wymaganych reguł sieciowej grupy zabezpieczeń.

## <a name="verify-azure-vm-certificates"></a>Weryfikowanie certyfikatów maszyn wirtualnych platformy Azure

Sprawdź, czy na maszynach wirtualnych z systemem Windows lub Linux, które mają być replikowane, są obecne wszystkie najnowsze certyfikaty główne. Jeśli brakuje najnowszych certyfikatów głównych, nie można zarejestrować maszyny wirtualnej w usłudze Site Recovery ze względu na ograniczenia związane z zabezpieczeniami.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.

- Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.

## <a name="set-permissions-on-the-account"></a>Ustawianie uprawnień konta

Usługa Azure Site Recovery udostępnia trzy role wbudowane, umożliwiające kontrolowanie operacji zarządzania usługą Site Recovery.

- **Współautor usługi Site Recovery** — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy mogą włączać odzyskiwanie po awarii dla aplikacji lub całych organizacji oraz zarządzać tą funkcją.

- **Operator usługi Site Recovery** — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który może przełączać maszyny wirtualne lub aplikacje w tryb failover po uzyskaniu odpowiednich instrukcji od właścicieli aplikacji i administratorów IT. Po usunięciu awarii operator może wykonać operacje ponownego włączenia ochrony maszyn wirtualnych i powrotu po awarii.

- **Czytelnik usługi Site Recovery** — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednia dla członka kadry zarządzającej, który zajmuje się monitorowaniem infrastruktury IT i bieżącego stanu jej ochrony oraz może wysyłać zgłoszenia do pomocy technicznej.

Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Włączanie replikacji

### <a name="select-the-source"></a>Wybieranie źródła

1. W obszarze magazynów usługi Recovery Services kliknij nazwę magazynu, a następnie kliknij pozycję **+ Replikuj**.
2. W obszarze **Źródło** wybierz pozycję **Azure**.
3. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
4. Wybierz **model wdrażania maszyn wirtualnych platformy Azure**: **usługę Resource Manager** lub **model klasyczny**.
5. Wybierz **Subskrypcję źródłową**, w której działają maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
6. Wybierz **źródłową grupę zasobów** dla maszyn wirtualnych usługi Resource Manager lub **usługę w chmurze** dla klasycznych maszyn wirtualnych.
7. Kliknij pozycję **OK**, aby zapisać ustawienia.

### <a name="select-the-vms"></a>Wybieranie maszyn wirtualnych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów/usługą w chmurze.

1. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
2. Kliknij przycisk **OK**.

### <a name="configure-replication-settings"></a>Konfigurowanie ustawień replikacji

Usługa Site Recovery tworzy ustawienia domyślne i zasady replikacji w regionie docelowym. Ustawienia te można zmienić zgodnie z wymaganiami.

1. Kliknij pozycję **Ustawienia**, aby wyświetlić ustawienia replikacji i regionu docelowego.
2. Aby zastąpić domyślne ustawienia regionu docelowego, kliknij pozycję **Dostosuj** obok pozycji **Grupa zasobów, sieć, zestawy magazynu i dostępności**.

  ![Konfigurowanie ustawień](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Subskrypcja docelowa**: subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa będzie taka sama jak subskrypcja źródłowa. Kliknij pozycję Dostosuj, aby wybrać inną subskrypcję docelową w ramach tej samej dzierżawy usługi Azure Active Directory.

- **Lokalizacja docelowa**: region docelowy używany na potrzeby odzyskiwania po awarii. Zaleca się, aby lokalizacja docelowa odpowiadała lokalizacji magazynu usługi Site Recovery.

- **Docelowa grupa zasobów**: grupa zasobów w regionie docelowym, w której są przechowywane maszyny wirtualne platformy Azure po przejściu w tryb failover. Domyślnie usługa Site Recovery dodaje sufiks „asr” do grupy zasobów utworzonej w regionie docelowym. Lokalizacją docelowej grupy zasobów może być dowolny region, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.

- **Docelowa sieć wirtualna**: sieć w regionie docelowym, w której znajdują się maszyny wirtualne po przejściu w tryb failover.
  Domyślnie usługa Site Recovery dodaje sufiks „asr” do sieci wirtualnej (i podsieci) utworzonej w regionie docelowym.

- **Konta magazynu pamięci podręcznej**: usługa Site Recovery używa konta magazynu w regionie źródłowym. Do tego konta są wysyłane zmiany źródłowych maszyn wirtualnych przed uruchomieniem replikacji do lokalizacji docelowej.

- **Docelowe konta magazynu (jeśli źródłowa maszyna wirtualna nie używa dysków zarządzanych)**: domyślnie usługa Site Recovery tworzy nowe konto magazynu w regionie docelowym w celu zduplikowania konta magazynu źródłowej maszyny wirtualnej.

- **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna używa dysków zarządzanych)**: domyślnie usługa Site Recovery tworzy dyski zarządzane repliki w regionie docelowym w celu zdublowania dysków zarządzanych źródłowej maszyny wirtualnej za pomocą tego samego typu magazynu (w warstwie Standardowa lub Premium) jako dysku zarządzanego źródłowej maszyny wirtualnej.

- **Docelowe zestawy dostępności**: domyślnie usługa Site Recovery dodaje sufiks „asr” do zestawu dostępności utworzonego w regionie docelowym. Zestawy dostępności można dodawać tylko wtedy, gdy maszyny wirtualne są częścią zestawu w regionie źródłowym.

Aby zastąpić domyślne ustawienia zasad replikacji, kliknij przycisk **Dostosuj** obok pozycji **Zasady replikacji**.  

- **Nazwa zasad replikacji**: nazwa zasad.

- **Przechowywanie punktów odzyskiwania**: domyślnie usługa Site Recovery przechowuje punkty odzyskiwania przez 24 godziny. Można skonfigurować wartość z zakresu od 1 do 72 godzin.

- **Częstotliwość wykonywania migawek na poziomie aplikacji**: domyślnie usługa Site Recovery wykonuje migawki spójności aplikacji co 4 godziny. Można skonfigurować wartość z zakresu od 1 do 12 godzin. Migawka spójności aplikacji jest tworzona w określonym momencie i obejmuje dane aplikacji zawarte na maszynie wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia stan spójności aplikacji podczas wykonywania migawki.

- **Grupa replikacji**: jeśli aplikacja wymaga spójności obejmującej wiele maszyn wirtualnych, można utworzyć grupę replikacji dla tych maszyn wirtualnych. Domyślnie wybrane maszyny wirtualne nie są częścią żadnej grupy replikacji.

  Kliknij przycisk **Dostosuj** obok pozycji **Zasady replikacji**, a następnie wybierz opcję **Tak** w odpowiedzi na monit dotyczący spójności między wieloma maszynami wirtualnymi, aby dodać maszyny wirtualne do grupy replikacji. Można utworzyć nową grupę replikacji lub użyć istniejącej grupy replikacji. Wybierz maszyny wirtualne, które mają należeć do grupy replikacji, i kliknij przycisk **OK**.

> [!IMPORTANT]
  W przypadku przejścia w tryb failover wszystkie maszyny w grupie replikacji będą korzystać ze współdzielonych punktów odzyskiwania, spójnych na poziomie awarii i aplikacji. Spójność obejmująca wiele maszyn wirtualnych może wpłynąć na wydajność obciążeń. Należy ją włączać tylko wtedy, gdy na maszynach jest uruchomione to samo obciążenie i jest wymagana spójność między wieloma maszynami.

> [!IMPORTANT]
  Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004. Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.

### <a name="configure-encryption-settings"></a>Konfigurowanie ustawień szyfrowania

Jeśli źródłowa maszyna wirtualna ma włączoną usługę Azure Disk Encryption, pojawi się poniższa sekcja dotycząca ustawień szyfrowania.

- **Magazyny kluczy usługi Azure Disk Encryption**: domyślnie usługa Azure Site Recovery tworzy nowy magazyn kluczy w regionie docelowym — sufiks „asr” w jego nazwie pochodzi od kluczy szyfrowania dysku źródłowej maszyny wirtualnej. Jeśli magazyn kluczy utworzony przez usługę Azure Site Recovery już istnieje, zostanie ponownie użyty.
- **Magazyny kluczy szyfrowania kluczy**: domyślnie usługa Azure Site Recovery tworzy nowy magazyn kluczy w regionie docelowym — sufiks „asr” w jego nazwie pochodzi od kluczy szyfrowania klucza źródłowej maszyny wirtualnej. Jeśli magazyn kluczy utworzony przez usługę Azure Site Recovery już istnieje, zostanie ponownie użyty.

Kliknij przycisk „Dostosuj” obok ustawień szyfrowania, aby zastąpić ustawienia domyślne i wybrać niestandardowe magazyny kluczy.

>[!NOTE]
>Tylko maszyny wirtualne platformy Azure z systemem operacyjnym Windows i [włączoną funkcją szyfrowania za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obecnie obsługiwane przez usługę Azure Site Recovery.
>

### <a name="track-replication-status"></a>Śledzenie stanu replikacji

1. Aby wyświetlić najnowsze informacje o stanie, w obszarze **Ustawienia** kliknij przycisk **Odśwież**.

2. Możesz śledzić postępy zadania **Włącz ochronę** w obszarze **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.

3. W obszarze **Ustawienia** > **Zreplikowane elementy** możesz wyświetlić stan maszyn wirtualnych i początkowy postęp replikacji. Kliknij maszynę wirtualną, aby przejść do jej ustawień.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure. Następny krok polega na przetestowaniu konfiguracji.

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md)
