---
title: Konfigurowanie odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w innym regionie świadczenia usługi Azure za pomocą usługi Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292482"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii, zarządzając replikacją, trybem failover i powrotem awaryjnym maszyn lokalnych i maszyn wirtualnych platformy Azure (VMs) i aranżacji ich.

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, replikując je z jednego regionu platformy Azure do drugiego. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu usługi Recovery Services
> * Sprawdzanie ustawień zasobów docelowych
> * Konfigurowanie wychodzącej łączności sieciowej dla maszyn wirtualnych
> * Włączanie replikacji maszyny wirtualnej

> [!NOTE]
> Ten artykuł zawiera instrukcje dotyczące wdrażania odzyskiwania po awarii przy użyciu najprostszych ustawień. Jeśli chcesz dowiedzieć się więcej o dostosowanych ustawieniach, zapoznaj się z artykułami w [sekcji Jak to zrobić](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zapoznaj się ze [składnikami i architekturą scenariusza](concepts-azure-to-azure-architecture.md).
- Przejrzyj [wymagania dotyczące pomocy technicznej](site-recovery-support-matrix-azure-to-azure.md) przed rozpoczęciem.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Następnie wybierz pozycję **It & Management Tools** > **Backup and Site Recovery**.
1. W **obszarze Nazwa**określ przyjazną nazwę, aby zidentyfikować przechowalnię. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
1. Utwórz grupę zasobów lub wybierz istniejącą grupę. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Aby uzyskać dostęp do przechowalni z pulpitu nawigacyjnego, wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** a następnie wybierz pozycję **Utwórz**.

   ![Nowy magazyn](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.

## <a name="verify-target-resource-settings"></a>Sprawdzanie ustawień zasobów docelowych

Sprawdź subskrypcję platformy Azure dla regionu docelowego.

- Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.
- Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Usługa Site Recovery wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Konfigurowanie wychodzącej łączności sieciowej dla maszyn wirtualnych

Aby zapewnić zgodne z oczekiwaniami działanie usługi Site Recovery, musisz zmodyfikować łączność sieciową wychodzącą z maszyn wirtualnych, które mają być replikowane.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartej na adresie URL do kontrolowania łączności wychodzącej, zezwalaj na dostęp do tych adresów URL:

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| `login.microsoftonline.com` | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| `*.servicebus.windows.net` | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz sieciowej grupy zabezpieczeń (NSG), utwórz reguły sieciowej grupy zabezpieczeń oparte na tagach usług, aby uzyskać dostęp do usługi Azure Storage, usługi Azure Active Directory, usługi site recovery i monitorowania odzyskiwania witryn. [Dowiedz się więcej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Weryfikowanie certyfikatów maszyn wirtualnych platformy Azure

Sprawdź, czy maszyny wirtualne, które chcesz replikować, mają najnowsze certyfikaty główne. Jeśli tak nie jest, maszyna wirtualna nie może być zarejestrowana w układzie Site Recovery z powodu ograniczeń zabezpieczeń.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
- Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.

## <a name="set-permissions-on-the-account"></a>Ustawianie uprawnień konta

Usługa Azure Site Recovery udostępnia trzy role wbudowane, umożliwiające kontrolowanie operacji zarządzania usługą Site Recovery.

- **Współautor usługi Site Recovery** — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy mogą włączać odzyskiwanie po awarii dla aplikacji lub całych organizacji oraz zarządzać tą funkcją.

- **Operator usługi Site Recovery** — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który może przełączać maszyny wirtualne lub aplikacje w tryb failover po uzyskaniu odpowiednich instrukcji od właścicieli aplikacji i administratorów IT. Po rozwiązaniu awarii operator odzyskiwania po awarii może ponownie przecenić i po awarii maszyny wirtualne.

- **Czytelnik usługi Site Recovery** — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednia dla członka kadry zarządzającej, który zajmuje się monitorowaniem infrastruktury IT i bieżącego stanu jej ochrony oraz może wysyłać zgłoszenia do pomocy technicznej.

Dowiedz się więcej o [wbudowanych rolach usługi Azure RBAC.](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication-for-a-vm"></a>Włączanie replikacji maszyny wirtualnej

W poniższych sekcjach opisano sposób włączania replikacji.

### <a name="select-the-source"></a>Wybieranie źródła

Aby rozpocząć konfigurację replikacji, wybierz źródło, w którym są uruchomione maszyny wirtualne platformy Azure.

1. Przejdź do **przechowalni usług odzyskiwania**, wybierz nazwę przechowalni, a następnie wybierz **+Replikuj**.
1. W przypadku **źródła**wybierz pozycję **Azure**.
1. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
1. Wybierz **Subskrypcję źródłową**, w której działają maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
1. Wybierz **grupę zasobów źródłowych**i wybierz przycisk **OK,** aby zapisać ustawienia.

   ![Konfiguracja źródła](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Wybieranie maszyn wirtualnych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów/usługą w chmurze.

1. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
1. Kliknij przycisk **OK**.

### <a name="configure-replication-settings"></a>Konfigurowanie ustawień replikacji

Usługa Site Recovery tworzy ustawienia domyślne i zasady replikacji w regionie docelowym. W razie potrzeby można zmienić te ustawienia.

1. Wybierz **ustawienia,** aby wyświetlić ustawienia docelowe i ustawienia replikacji.

1. Aby zastąpić domyślne ustawienia **docelowe,** wybierz pozycję Dostosuj obok **pozycji Grupa Zasobów, Sieć, Magazyn i Dostępność**.

   ![Konfigurowanie ustawień](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Dostosuj ustawienia docelowe zgodnie z podsumowaniem tabeli.

   | **Ustawienie** | **Szczegóły** |
   | --- | --- |
   | **Subskrypcja docelowa** | Domyślnie subskrypcja docelowa jest taka sama jak subskrypcji źródłowej. Wybierz **pozycję Dostosuj,** aby wybrać inną subskrypcję docelową w tej samej dzierżawie usługi Azure Active Directory. |
   | **Lokalizacja docelowa** | Region docelowy używany na potrzeby odzyskiwania po awarii.<br/><br/> Zaleca się, aby lokalizacja docelowa odpowiadała lokalizacji magazynu usługi Site Recovery. |
   | **Docelowa grupa zasobów** | Grupa zasobów w regionie docelowym, w której są przechowywane maszyny wirtualne platformy Azure po przejściu w tryb failover.<br/><br/> Domyślnie program Site Recovery tworzy nową grupę `asr` zasobów w regionie docelowym z sufiksem. Lokalizacja docelowej grupy zasobów może być dowolny region z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne. |
   | **Docelowa sieć wirtualna** | Sieć w regionie docelowym, w której znajdują się maszyny wirtualne po przejściu w tryb failover.<br/><br/> Domyślnie usługa Site Recovery tworzy nową sieć wirtualną (i podsieci) w regionie docelowym z sufiksem. `asr` |
   | **Konta magazynu pamięci podręcznej** | Usługa Site Recovery używa konta magazynu w regionie źródłowym. Do tego konta są wysyłane zmiany źródłowych maszyn wirtualnych przed uruchomieniem replikacji do lokalizacji docelowej.<br/><br/> Jeśli używasz konta magazynu pamięci podręcznej z włączoną zaporą, upewnij się, że **włączono zezwalanie na zaufane usługi firmy Microsoft**. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Upewnij się również, że zezwalasz na dostęp do co najmniej jednej podsieci źródłowej sieci wirtualnej. |
   | **Konta magazynu docelowego (źródłowa maszyna wirtualna używa dysków niezarządzanych)** | Domyślnie usługa Site Recovery tworzy w regionie docelowym nowe konto magazynu, które jest duplikatem źródłowego konta magazynu maszyn wirtualnych.<br/><br/> Włącz **zezwalanie na zaufane usługi firmy Microsoft,** jeśli używasz konta magazynu pamięci podręcznej z włączoną zaporą. |
   | **Replika dysków zarządzanych (jeśli źródło maszyna wirtualna używa dysków zarządzanych)** | Domyślnie usługa Site Recovery tworzy dyski zarządzane repliki w regionie docelowym w celu odzwierciedlenia dysków zarządzanych źródłowej maszyny Wirtualnej o tym samym typie magazynu (standard lub premium) co dysk zarządzany maszyny Wirtualnej źródłowej. Typ dysku można dostosować tylko. |
   | **Docelowe zestawy dostępności** | Domyślnie usługa Azure Site Recovery tworzy nowy zestaw dostępności `asr` w regionie docelowym z nazwą o sufiksie dla maszyn wirtualnych część dostępności ustawioną w regionie źródłowym. W przypadku, gdy zestaw dostępności utworzony przez usługę Azure Site Recovery już istnieje, jest ponownie używany. |
   | **Docelowe strefy dostępności** | Domyślnie usługa Site Recovery przypisuje w regionie docelowym ten sam numer strefy co regionu źródłowego, jeśli region docelowy obsługuje strefy dostępności.<br/><br/> Jeśli region docelowy nie obsługuje stref dostępności, maszyny wirtualne docelowe są domyślnie skonfigurowane jako pojedyncze wystąpienia.<br/><br/> Wybierz **pozycję Dostosuj,** aby skonfigurować maszyny wirtualne jako część dostępności ustawionej w regionie docelowym.<br/><br/> Po włączeniu replikacji nie można zmienić typu dostępności (pojedyncze wystąpienie, zestaw dostępności lub strefa dostępności). Aby zmienić typ dostępności, należy wyłączyć i włączyć replikację. |

1. Aby dostosować ustawienia zasad replikacji, wybierz pozycję **Dostosuj** obok **pozycji Zasady replikacji**i w razie potrzeby zmodyfikuj ustawienia.

   | **Ustawienie** | **Szczegóły** |
   | --- | --- |
   | **Nazwa zasady replikacji** | Nazwa zasad. |
   | **Retencja punktów odzyskiwania** | Domyślnie usługa Site Recovery przechowuje punkty odzyskiwania przez 24 godziny. Można skonfigurować wartość z zakresu od 1 do 72 godzin. |
   | **Częstotliwość migawek spójna z aplikacją** | Domyślnie usługa Site Recovery wykonuje migawki na poziomie aplikacji co 4 godziny. Można skonfigurować wartość z zakresu od 1 do 12 godzin.<br/><br/> Migawka spójna z aplikacją jest migawką punktu w czasie danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia stan spójności aplikacji podczas wykonywania migawki. |
   | **Grupa replikacji** | Jeśli aplikacja wymaga spójności obejmującej wiele maszyn wirtualnych, można utworzyć grupę replikacji dla tych maszyn wirtualnych. Domyślnie wybrane maszyny wirtualne nie są częścią żadnej grupy replikacji. |

1. W obszarze **Dostosowywanie** wybierz opcję **Tak**, aby zachować spójność wielu maszyn wirtualnych, jeśli chcesz dodać maszyny wirtualne do nowej lub istniejącej grupy replikacji. Następnie wybierz przycisk **OK**.

   > [!NOTE]
   > - Wszystkie maszyny w grupie replikacji mają wspólne punkty odzyskiwania zgodne z awariami i spójne z aplikacjami po przełączeniu w błąd.
   > - Włączenie spójności wielu maszyn wirtualnych może mieć wpływ na wydajność obciążenia (jest to procesor intensywnie korzystający z procesora). Powinien być używany tylko wtedy, gdy na komputerach jest uruchomione to samo obciążenie i wymagana jest spójność na wielu komputerach.
   > - W grupie replikacji może być maksymalnie 16 maszyn wirtualnych.
   > - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że nie ma zapory blokującej komunikację wewnętrzną między maszynami wirtualnymi za pomocą tego portu.
   > - W przypadku maszyn wirtualnych z systemem Linux w grupie replikacji upewnij się, że ruch wychodzący na porcie 20004 jest otwierany ręcznie zgodnie ze wskazówkami dotyczącymi wersji systemu Linux.

### <a name="configure-encryption-settings"></a>Konfigurowanie ustawień szyfrowania

Jeśli źródłowa maszyna wirtualna ma włączone szyfrowanie dysków platformy Azure (ADE), przejrzyj ustawienia.

1. Sprawdź ustawienia:
   1. **Magazyny kluczy szyfrowania dysku:** Domyślnie usługa Site Recovery tworzy nową przechowalnię kluczy na źródłowych kluczach szyfrowania dysku maszyny Wirtualnej z sufiksem. `asr` Jeśli magazyn kluczy już istnieje, jest ponownie używany.
   1. **Magazyny kluczy szyfrowania kluczy:** Domyślnie usługa Site Recovery tworzy nową przechowalnię kluczy w regionie docelowym. Nazwa ma `asr` sufiks i jest oparta na źródłowych kluczy szyfrowania klucza maszyny Wirtualnej. Jeśli magazyn kluczy utworzony przez odzysk witryny już istnieje, jest ponownie używany.
1. Wybierz **pozycję Dostosuj,** aby wybrać niestandardowe przechowalnia kluczy.

> [!NOTE]
> Tylko maszyny wirtualne platformy Azure z systemem operacyjnym Windows i [włączoną funkcją szyfrowania za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obecnie obsługiwane przez usługę Azure Site Recovery.

### <a name="track-replication-status"></a>Śledzenie stanu replikacji

Po włączeniu replikacji można śledzić stan zadania.

1. W **obszarze Ustawienia**wybierz pozycję **Odśwież,** aby uzyskać najnowszy stan.
1. Postęp i status możesz śledzić w następujący sposób:
   1. Postęp zadania **Włącz ochronę** możesz śledzić w obszarze **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
   1. W **ustawieniach** > **elementów replikowanych**można wyświetlić stan maszyn wirtualnych i początkowy postęp replikacji. Wybierz maszynę wirtualną, aby przejść do szczegółów jej ustawień.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure. Teraz można uruchomić wiertło odzyskiwania po awarii, aby sprawdzić, czy praca awaryjna działa zgodnie z oczekiwaniami.

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md)
