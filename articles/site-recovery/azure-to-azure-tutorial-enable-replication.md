---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w innym regionie świadczenia usługi Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 13c86a38e0d894feed0d9c24dd802a09ff1d1d2d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678849"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych oraz maszyn wirtualnych platformy Azure, a także orkiestrację tych procesów.

Ten samouczek pokazuje, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, replikując je między regionami platformy Azure do innego. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu usługi Recovery Services
> * Sprawdzanie ustawień zasobów docelowych
> * Konfigurowanie wychodzącego dostępu dla maszyn wirtualnych
> * Włączanie replikacji maszyny wirtualnej

> [!NOTE]
> Ten artykuł zawiera instrukcje dotyczące wdrażania odzyskiwania po awarii przy użyciu najprostszych ustawień. Jeśli chcesz dowiedzieć się więcej o ustawieniach niestandardowych, zapoznaj się z artykułami w [sekcji Instrukcje](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](concepts-azure-to-azure-architecture.md).
- Przegląd [obsługi wymagań związanych z](site-recovery-support-matrix-azure-to-azure.md) przed rozpoczęciem.

## <a name="create-a-vault"></a>Tworzenie magazynu

Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Narzędzia do zarządzania** > **Backup i Site Recovery**.
3. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów lub wybierz istniejącą grupę. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

   ![Nowy magazyn](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.

## <a name="verify-target-resources"></a>Sprawdzanie zasobów docelowych

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.
2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Usługa Site Recovery wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

## <a name="configure-outbound-network-connectivity"></a>Konfigurowanie połączenia sieciowego ruchu wychodzącego

Aby zapewnić zgodne z oczekiwaniami działanie usługi Site Recovery, musisz zmodyfikować łączność sieciową wychodzącą z maszyn wirtualnych, które mają być replikowane.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.


### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli do sterowania łącznością wychodzącą używasz opartego na adresach URL serwera proxy z zaporą, zezwól na dostęp do następujących adresów URL.

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| *.blob.core.windows.net | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| login.microsoftonline.com | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| *.servicebus.windows.net | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli chcesz kontrolować łączność wychodzącą przy użyciu adresów IP, a nie adresów URL, zezwól na ruch z następujących adresów w przypadku zapór opartych na adresach IP, serwerów proxy lub reguł sieciowej grupy zabezpieczeń.

  - [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Zakresy adresów IP centrum danych platformy Windows Azure w Niemczech](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Zakresy adresów IP centrum danych platformy Windows Azure w Chinach](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Zakresy adresów URL i IP dla usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Adresy IP punktów końcowych usługi Site Recovery](https://aka.ms/site-recovery-public-ips)

Jeśli używasz sieciowej grupy zabezpieczeń można utworzyć tagu usługi storage reguły sieciowej grupy zabezpieczeń dla regionu źródłowego. [Dowiedz się więcej](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Weryfikowanie certyfikatów maszyn wirtualnych platformy Azure

Sprawdź, czy maszyny wirtualne, które chcesz replikować, mają najnowsze certyfikaty główne. Jeśli ich nie mają, nie można zarejestrować maszyn wirtualnych w usłudze Site Recovery ze względu na ograniczenia związane z zabezpieczeniami.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
- Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.

## <a name="set-permissions-on-the-account"></a>Ustawianie uprawnień konta

Usługa Azure Site Recovery udostępnia trzy role wbudowane, umożliwiające kontrolowanie operacji zarządzania usługą Site Recovery.

- **Współautor usługi Site Recovery** — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy mogą włączać odzyskiwanie po awarii dla aplikacji lub całych organizacji oraz zarządzać tą funkcją.

- **Operator usługi Site Recovery** — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który może przełączać maszyny wirtualne lub aplikacje w tryb failover po uzyskaniu odpowiednich instrukcji od właścicieli aplikacji i administratorów IT. Po usunięciu awarii operator może wykonać operacje ponownego włączenia ochrony maszyn wirtualnych i powrotu po awarii.

- **Czytelnik usługi Site Recovery** — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednia dla członka kadry zarządzającej, który zajmuje się monitorowaniem infrastruktury IT i bieżącego stanu jej ochrony oraz może wysyłać zgłoszenia do pomocy technicznej.

Dowiedz się więcej o [wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Włączanie replikacji

### <a name="select-the-source"></a>Wybieranie źródła

1. W obszarze magazynów usługi Recovery Services kliknij nazwę magazynu, a następnie kliknij pozycję **+ Replikuj**.
2. W obszarze **Źródło** wybierz pozycję **Azure**.
3. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
4. Wybierz **Subskrypcję źródłową**, w której działają maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
5. Wybierz **źródłowa grupa zasobów**i kliknij przycisk **OK** Aby zapisać ustawienia.

    ![Konfiguracja źródła](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Wybieranie maszyn wirtualnych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów/usługą w chmurze.

1. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
2. Kliknij przycisk **OK**.

### <a name="configure-replication-settings"></a>Konfigurowanie ustawień replikacji

Usługa Site Recovery tworzy ustawienia domyślne i zasady replikacji w regionie docelowym. Ustawienia możesz zmienić zgodnie z potrzebami.

1. Kliknij pozycję **Ustawienia**, aby wyświetlić ustawienia replikacji i regionu docelowego.
2. Aby zastąpić domyślne ustawienia regionu docelowego, kliknij pozycję **Dostosuj** obok pozycji **Grupa zasobów, sieć, magazyn i dostępność**.

   ![Konfigurowanie ustawień](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Dostosuj ustawienia docelowe, zgodnie z opisem w tabeli.

    **Ustawienie** | **Szczegóły**
    --- | ---
    **Subskrypcja docelowa** | Domyślnie subskrypcja docelowa jest taka sama jak subskrypcja źródłowa. Kliknij pozycję Dostosuj, aby wybrać inną subskrypcję docelową w ramach tej samej dzierżawy usługi Azure Active Directory.
    **Lokalizacja docelowa** | Region docelowy używany na potrzeby odzyskiwania po awarii.<br/><br/> Zaleca się, aby lokalizacja docelowa odpowiadała lokalizacji magazynu usługi Site Recovery.
    **Docelowa grupa zasobów** | Grupa zasobów w regionie docelowym, w której są przechowywane maszyny wirtualne platformy Azure po przejściu w tryb failover.<br/><br/> Domyślnie usługa Site Recovery dodaje sufiks „asr” do grupy zasobów utworzonej w regionie docelowym. Lokalizacja docelowa grupa zasobów może być dowolny region, z wyjątkiem regionu, w którym znajdują się maszyny wirtualne źródła.
    **Docelowa sieć wirtualna** | Sieć w regionie docelowym, w której znajdują się maszyny wirtualne po przejściu w tryb failover.<br/><br/> Domyślnie usługa Site Recovery dodaje sufiks „asr” do sieci wirtualnej (i podsieci) utworzonej w regionie docelowym.
    **Konta magazynu pamięci podręcznej** | Usługa Site Recovery używa konta magazynu w regionie źródłowym. Do tego konta są wysyłane zmiany źródłowych maszyn wirtualnych przed uruchomieniem replikacji do lokalizacji docelowej.<br/><br/> Jeśli używasz konta magazynu pamięci podręcznej z włączona zapora, upewnij się, Włączyłeś **dozwolonych zaufanych usług firmy Microsoft**. [Dowiedz się więcej.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Docelowe konta magazynu (źródłowa maszyna wirtualna używa dysków niezarządzanych)** | Domyślnie usługa Site Recovery tworzy w regionie docelowym nowe konto magazynu, które jest duplikatem źródłowego konta magazynu maszyn wirtualnych.<br/><br/> Włącz **dozwolonych zaufanych usług firmy Microsoft** Jeśli używasz konta magazynu z obsługą zapory pamięci podręcznej.
    **Dyski zarządzane repliki (Jeśli źródło, które maszyna wirtualna używa dysków zarządzanych)** | Domyślnie usługa Site Recovery tworzy dyski zarządzane repliki w regionie docelowym w celu zdublowania dysków zarządzanych źródłowej maszyny wirtualnej przy użyciu tego samego typu magazynu (w warstwie Standardowa lub Premium) co typ magazynu dysku zarządzanego źródłowej maszyny wirtualnej.
    **Docelowe zestawy dostępności** | Domyślnie usługa Azure Site Recovery tworzy w regionie docelowym nowy zestaw dostępności o nazwie z sufiksem „asr” dla części maszyny wirtualnej w zestawie dostępności w regionie źródłowym. Jeśli zestaw dostępności utworzony przez usługę Azure Site Recovery już istnieje, zostanie ponownie użyty.
    **Strefy dostępności docelowego** | Domyślnie usługa Site Recovery przypisuje w regionie docelowym ten sam numer strefy co regionu źródłowego, jeśli region docelowy obsługuje strefy dostępności.<br/><br/> Jeśli region docelowy nie obsługuje stref dostępności, docelowej maszyny wirtualne są konfigurowane domyślnie jako pojedynczych wystąpień.<br/><br/> Kliknij przycisk **Dostosuj** do konfiguracji maszyn wirtualnych w ramach zestaw dostępności w regionie docelowym.<br/><br/> Nie można zmienić typ dostępności (SIS, zestaw lub dostępności w strefie dostępności) po włączeniu replikacji. W celu zmiany typu dostępności należy wyłączyć i włączyć replikację.

4. Aby dostosować ustawienia zasad replikacji, kliknij przycisk **Dostosuj** obok **zasad replikacji**i zmodyfikuj ustawienia zgodnie z potrzebami.

    **Ustawienie** | **Szczegóły**
    --- | ---
    **Nazwa zasad replikacji** | Nazwa zasad.
    **Czas przechowywania punktu odzyskiwania** | Domyślnie usługa Site Recovery przechowuje punkty odzyskiwania przez 24 godziny. Można skonfigurować wartość z zakresu od 1 do 72 godzin.
    **Częstotliwość migawek spójności aplikacji** | Domyślnie usługa Site Recovery wykonuje migawki na poziomie aplikacji co 4 godziny. Można skonfigurować wartość z zakresu od 1 do 12 godzin.<br/><br/> Migawka spójności aplikacji jest w momencie migawkę danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia stan spójności aplikacji podczas wykonywania migawki.
    **Grupy replikacji** | Jeśli aplikacja wymaga spójności obejmującej wiele maszyn wirtualnych, można utworzyć grupę replikacji dla tych maszyn wirtualnych. Domyślnie wybrane maszyny wirtualne nie są częścią żadnej grupy replikacji.

5. W obszarze **Dostosowywanie** wybierz opcję **Tak**, aby zachować spójność wielu maszyn wirtualnych, jeśli chcesz dodać maszyny wirtualne do nowej lub istniejącej grupy replikacji. Następnie kliknij przycisk **OK**. 

    >[!NOTE]
    >- Udostępnione punkty odzyskiwania spójnego na poziomie aplikacji i awarii, gdy przełączone w tryb failover wszystkich maszyn w grupie replikacji.
    >- Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążeń (jest intensywnie z Procesora). Należy używać tylko wtedy, gdy maszyn są to samo obciążenie i jest wymagana spójność między wieloma maszynami.
    >- Użytkownik nie może przekraczać 16 maszyn wirtualnych w grupie replikacji.
    >- Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że Zapora nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem tego portu.
    >- Dla maszyn wirtualnych systemu Linux w grupie replikacji upewnij się, że ruch wychodzący na porcie 20004 musisz ręcznie otworzyć zgodnie ze wskazówkami dla wersji systemu Linux.



### <a name="configure-encryption-settings"></a>Konfigurowanie ustawień szyfrowania

Jeśli źródłowa maszyna wirtualna ma usługa Azure disk encryption (ADE) włączone, przejrzyj ustawienia.

1. Sprawdź ustawienia:
    - **Magazyny kluczy szyfrowania dysków**: Domyślnie usługa Site Recovery tworzy nowy magazyn kluczy na kluczy szyfrowania dysków maszyn wirtualnych na źródło, sufiks "asr". Jeśli magazyn kluczy już istnieje, zostanie on użyty ponownie.
    - **Magazyny kluczy szyfrowania kluczy**: Domyślnie usługa Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa ma sufiks "asr" i jest oparte na kluczach szyfrowania maszyny Wirtualnej źródłowego. Jeśli magazyn kluczy utworzonych przez usługę Site Recovery jest już istnieje, zostanie on użyty ponownie.

2. Kliknij pozycję **Dostosuj**, aby wybrać niestandardowe magazyny kluczy.

>[!NOTE]
>Tylko maszyny wirtualne platformy Azure z systemem operacyjnym Windows i [włączoną funkcją szyfrowania za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obecnie obsługiwane przez usługę Azure Site Recovery.
>

### <a name="track-replication-status"></a>Śledzenie stanu replikacji

1. Aby wyświetlić najnowsze informacje o stanie, w obszarze **Ustawienia** kliknij przycisk **Odśwież**.
2. Postęp i status możesz śledzić w następujący sposób:
    - Postęp zadania **Włącz ochronę** możesz śledzić w obszarze **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
    - W obszarze **Ustawienia** > **Zreplikowane elementy** możesz wyświetlić stan maszyn wirtualnych i początkowy postęp replikacji. Kliknij maszynę wirtualną, aby przejść do jej ustawień.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure. Teraz możesz zainicjować próbne odzyskiwanie po awarii w celu sprawdzenia, czy tryb failover działa zgodnie z oczekiwaniami.

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md)
