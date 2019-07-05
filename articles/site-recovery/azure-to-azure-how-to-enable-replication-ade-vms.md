---
title: Konfigurowanie replikacji dla maszyn wirtualnych z włączoną obsługą usługi Azure Disk Encryption w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania replikacji z obsługą usługi Azure Disk Encryption maszyn wirtualnych między regionami platformy Azure do innego za pomocą usługi Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b2e9bf7fbe7d5940b517d97dcc15d21c30835001
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449217"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikowanie maszyn wirtualnych z obsługą usługi Azure Disk Encryption do innego regionu platformy Azure

W tym artykule opisano sposób replikowania maszyn wirtualnych z włączoną funkcją szyfrowania dysków Azure między regionami platformy Azure do innego.

>[!NOTE]
>Usługa Azure Site Recovery aktualnie obsługuje tylko maszyny wirtualne platformy Azure z systemem Windows OS, które są [włączonego szyfrowania w usłudze Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a id="required-user-permissions"></a> Uprawnienia użytkownika wymagane
Usługa Site Recovery wymaga od użytkownika musi mieć uprawnienia do tworzenia magazynu kluczy w docelowym regionie i kopiowanie kluczy do regionu.

Aby włączyć replikację włączone szyfrowanie dysków maszyn wirtualnych w witrynie Azure portal, użytkownik wymaga następujących uprawnień:

- Uprawnienia usługi Key vault
    - List
    - Przycisk Utwórz
    - Get

-   Uprawnienia klucza tajnego usługi Key vault
    - List
    - Przycisk Utwórz
    - Get

- Uprawnienia klucza magazynu kluczy (wymagane tylko wtedy, gdy klucz szyfrowania używany przez maszyny wirtualne do szyfrowania kluczy szyfrowania dysków)
    - List
    - Get
    - Przycisk Utwórz
    - Szyfrowanie
    - Odszyfrowywanie

Aby zarządzać uprawnieniami, przejdź do zasobu magazynu kluczy w portalu. Dodaj wymagane uprawnienia dla użytkownika. Poniższy przykład pokazuje, jak włączyć uprawnienia do magazynu kluczy *ContosoWeb2Keyvault*, która znajduje się w regionie źródłowym.

1. Przejdź do **Home** > **Keyvaults** > **ContosoWeb2KeyVault > zasady dostępu**.

   ![Okno uprawnień usługi Key vault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Widać, że nie istnieją żadne uprawnienia użytkownika. Wybierz **Dodaj nowe**. Wprowadź informacje o użytkowniku i uprawnienia.

   ![uprawnienia usługi keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Użytkownik, który jest włączenie odzyskiwania po awarii (DR) nie ma uprawnień, aby skopiować kluczy, administratora zabezpieczeń, który ma odpowiednie uprawnienia służy poniższy skrypt do skopiowania szyfrowania, wpisy tajne i klucze w regionie docelowym.

Rozwiązywanie problemów z uprawnieniami, znaleźć [klucza magazynu problemów związanych z uprawnieniami](#trusted-root-certificates-error-code-151066) w dalszej części tego artykułu.

>[!NOTE]
>Aby włączyć replikację włączone szyfrowanie dysków maszyn wirtualnych z poziomu portalu, możesz potrzebujesz co najmniej "List" uprawnień do magazynów kluczy, wpisów tajnych i kluczy.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiowanie kluczy szyfrowania dysków w regionie odzyskiwania po awarii przy użyciu skryptu programu PowerShell

1. [Otwórz kod skryptu raw "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Skopiuj skrypt do pliku i nadaj mu nazwę **keys.ps1 kopiowania**.
3. Otwórz aplikację programu Windows PowerShell i przejdź do folderu, w którym został zapisany plik.
4. Wykonaj kopię keys.ps1.
5. Podaj poświadczenia platformy Azure, aby zalogować się.
6. Wybierz **subskrypcji platformy Azure** maszyn wirtualnych.
7. Poczekaj, aż grup zasobów, aby załadować, a następnie wybierz **grupy zasobów** maszyn wirtualnych.
8. Wybierz maszyny wirtualne z wyświetlonej listy. Tylko maszyny wirtualne, które aktywowano na potrzeby szyfrowania dysków znajdują się na liście.
9. Wybierz **lokalizacja docelowa**.

    - **Magazyny kluczy szyfrowania dysku**
    - **Magazyny kluczy szyfrowania**

   Domyślnie usługa Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa magazynu ma sufiks "asr", który jest oparty na kluczy szyfrowania dysków maszyny Wirtualnej źródłowego. Jeśli magazyn kluczy jest już utworzony przez usługę Site Recovery, zostanie on użyty ponownie. Wybierz inny magazyn kluczy z listy, jeśli to konieczne.

## <a name="enable-replication"></a>Włączanie replikacji

Na przykład Azure region podstawowy jest Azja Wschodnia, a w regionie pomocniczym jest Azja południowo-wschodnia.

1. W magazynie, wybierz **+ Replikuj**.
2. Należy zauważyć następujące pola.
    - **Źródło**: Punkt początkowy maszyn wirtualnych, czyli w tym przypadku **Azure**.
    - **Lokalizacja źródłowa**: Region platformy Azure, które chcesz chronić maszyny wirtualne. W tym przykładzie lokalizacja źródłowa jest "Azja Wschodnia".
    - **Model wdrażania**: Model wdrażania platformy Azure maszyna źródłowa.
    - **Źródło subskrypcji**: Subskrypcja, do której należą źródłowych maszyn wirtualnych. Może to być dowolnej subskrypcji, która znajduje się w tej samej dzierżawie usługi Azure Active Directory jako magazynu usługi recovery services.
    - **Grupa zasobów**: Grupa zasobów, do której należą źródłowych maszyn wirtualnych. Wszystkie maszyny wirtualne w wybranej grupy zasobów są wyświetlane do ochrony w następnym kroku.

3. W **maszyn wirtualnych** > **wybierz maszyny wirtualne**, wybierz każdej maszyny Wirtualnej, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.

4. W **ustawienia**, można skonfigurować następujące ustawienia lokacji docelowej.

    - **Lokalizacja docelowa**: Lokalizacja, w którym będą replikowane dane źródłowe maszyny wirtualnej. Usługa Site Recovery zawiera listę regionów udało się na podstawie lokalizacji wybranej maszyny. Zalecamy użycie tej samej lokalizacji co lokalizacja magazynu usługi Recovery Services.
    - **Subskrypcja docelowa**: Subskrypcja docelowa, który służy do odzyskiwania po awarii. Domyślnie subskrypcja docelowa jest taka sama jak subskrypcja źródłowa.
    - **Docelowa grupa zasobów**: Grupa zasobów, w którym wszystkie replikowane maszyny wirtualne należą. Domyślnie usługa Site Recovery tworzy nową grupę zasobów w regionie docelowym. Nazwa pobiera sufiks "asr". Jeśli grupa zasobów już istnieje która została utworzona przez usługę Azure Site Recovery, zostanie on użyty ponownie. Możesz również dostosować, jak pokazano w poniższej sekcji. Lokalizacja docelowa grupa zasobów może być dowolnego regionu systemu Azure, z wyjątkiem regionu hostujące źródłowych maszyn wirtualnych.
    - **Docelowa sieć wirtualna**: Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym. Nazwa pobiera sufiks "asr". Ma mapowane na sieci źródłowej i używane do wszelkich przyszłych ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
    - **Docelowe konta magazynu (Jeśli źródło maszyny Wirtualnej nie korzysta z dysków zarządzanych)** : Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego przez naśladując konfigurację magazynu źródłowej maszyny Wirtualnej. Jeśli konto magazynu już istnieje, zostanie on użyty ponownie.
    - **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna używa dysków zarządzanych)** : Usługa Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym w celu zdublowania dysków zarządzanych źródłowej maszyny Wirtualnej ten sam typ magazynu (standardowa lub premium) jako dysków zarządzanych źródłowej maszyny Wirtualnej.
    - **Konta magazynu pamięci podręcznej**: Usługa Site Recovery wymaga konta dodatkowego magazynu o nazwie *magazynu w pamięci podręcznej* w regionie źródłowym. Wszystkie zmiany na źródłowe maszyny wirtualne są śledzone i wysyłane do konta magazynu pamięci podręcznej. Następnie są replikowane do lokalizacji docelowej.
    - **Zestaw dostępności**: Domyślnie usługa Site Recovery tworzy nowy zestaw dostępności w regionie docelowym. Nazwa ma sufiks "asr". Jeśli zestaw dostępności, który został utworzony przez usługę Site Recovery, już istnieje, zostanie on użyty ponownie.
    - **Magazyny kluczy szyfrowania dysków**: Domyślnie usługa Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Ma sufiks "asr", który jest oparty na kluczy szyfrowania dysków maszyny Wirtualnej źródłowego. Jeśli magazyn kluczy, który został utworzony przez usługę Azure Site Recovery, już istnieje, zostanie on użyty ponownie.
    - **Magazyny kluczy szyfrowania kluczy**: Domyślnie usługa Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa ma sufiks "asr", który jest oparty na kluczach szyfrowania maszyny Wirtualnej źródłowego. Jeśli magazyn kluczy utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie.
    - **Zasady replikacji**: Definiuje ustawienia dla historii przechowywania punktów odzyskiwania i częstotliwość migawek spójności aplikacji. Domyślnie usługa Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi z *24 godziny* przechowywania punktu odzyskiwania i *60 minut* częstotliwości migawki dotyczącej spójności aplikacji.

## <a name="customize-target-resources"></a>Dostosuj zasoby docelowe

Wykonaj następujące kroki, aby zmodyfikować domyślne ustawienia regionu docelowego Site Recovery.

1. Wybierz **Dostosuj** obok "Subskrypcji docelowej" Aby zmodyfikować domyślne subskrypcji docelowej. Wybierz subskrypcję z listy subskrypcji, które są dostępne w dzierżawie usługi Azure AD.

2. Wybierz **Dostosuj** obok pozycji "grupy zasobów, sieci, magazynu i zestawach dostępności" Aby zmodyfikować następujące ustawienia domyślne:
    - Aby uzyskać **docelowa grupa zasobów**, wybierz grupę zasobów z listy grup zasobów w lokalizacji docelowej subskrypcji.
    - Aby uzyskać **docelowa sieć wirtualna**, wybierz sieć, z listy sieci wirtualnych w lokalizacji docelowej.
    - Aby uzyskać **zestawu dostępności**, możesz dodać ustawienia zestawu dostępności maszyny wirtualnej, jeśli są one częścią zestaw dostępności w regionie źródłowym.
    - Aby uzyskać **kont magazynu docelowego**, wybierz konto do użycia.

2. Wybierz **Dostosuj** obok "Ustawienia szyfrowania" Aby zmodyfikować następujące ustawienia domyślne:
   - Aby uzyskać **magazynu kluczy szyfrowania dysku docelowego**, wybierz magazyn kluczy szyfrowania dysków docelowych z listy magazynów kluczy w lokalizacji docelowej subskrypcji.
   - Aby uzyskać **magazynu kluczy szyfrowania klucza docelowego**, wybierz docelowy magazyn kluczy szyfrowania z listy magazynów kluczy w lokalizacji docelowej subskrypcji.

3. Wybierz **Utwórz zasób docelowy** > **włączania replikacji**.
4. Po włączeniu replikacji maszyn wirtualnych można sprawdzić stan kondycji maszyn wirtualnych w ramach **zreplikowane elementy**.

>[!NOTE]
>Podczas replikacji początkowej stanu może potrwać pewien czas, aby odświeżyć, bez jawnego postępu. Kliknij przycisk **Odśwież** uzyskać najnowszy stan.

## <a name="update-target-vm-encryption-settings"></a>Aktualizowanie ustawień szyfrowania maszyn wirtualnych docelowej
W następujących scenariuszach będzie wymagane do aktualizacji ustawień szyfrowania docelowej maszyny Wirtualnej:
  - Możesz włączyć replikacji usługi Site Recovery na maszynie Wirtualnej. Później włączone jest szyfrowanie dysków na źródłowej maszynie Wirtualnej.
  - Możesz włączyć replikacji usługi Site Recovery na maszynie Wirtualnej. Później należy zmienić klucz szyfrowania dysku lub klucz szyfrowania klucza na źródłowej maszynie Wirtualnej.

Możesz użyć [skrypt](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) kopiowanie kluczy szyfrowania w regionie docelowym, a następnie zaktualizować ustawienia szyfrowania docelowego w **magazyn usługi Recovery services** > *zreplikowany element*  >  **Właściwości** > **obliczenia i sieć**.

![Okno dialogowe Ustawienia ADE aktualizacji](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Rozwiązywanie problemów uprawnień usługi key vault podczas replikacji maszyny Wirtualnej platformy Azure do platformy Azure

Usługa Azure Site Recovery wymaga uprawnień do co najmniej odczytu w regionie źródła Key vault i uprawnienia do zapisu w magazynie kluczy regionu docelowego do odczytu wpisu tajnego i skopiuj go do magazynu kluczy w regionie docelowym. 

**Przyczyny 1:** Nie przyznano uprawnienia "GET" **źródłowy region Key vault** do odczytu klucze. </br>
**Jak naprawić:** Niezależnie od tego, czy jesteś administratorem subskrypcji lub nie ważne jest, czy masz uprawnienie get w magazynie kluczy.

1. Przejdź do źródła region Key vault, czyli w tym przykładzie "ContososourceKeyvault" > **zasady dostępu** 
2. W obszarze **Wybierz podmiot zabezpieczeń** Dodaj swoją nazwę użytkownika, na przykład: "dradmin@contoso.com"
3. W obszarze **uprawnienia klucza** wybierz polecenie Pobierz 
4. W obszarze **uprawnienia klucza tajnego** wybierz polecenie Pobierz 
5. Zapisać zasady dostępu

**Przyczyny 2:** Nie masz wymaganych uprawnień na **docelowy region Key vault** do zapisu kluczy. </br>

*Na przykład*: Próby replikacji maszyny Wirtualnej, który ma usługi key vault *ContososourceKeyvault* w regionie źródłowym.
Masz wszystkie uprawnienia w magazynie kluczy regionu źródłowego. Jednak podczas ochrony, wybierz jest już utworzony magazyn kluczy ContosotargetKeyvault, która nie ma uprawnień. Występuje błąd.

Uprawnienia wymagane na [docelowej Key vault](#required-user-permissions)

**Jak naprawić:** Przejdź do **Home** > **Keyvaults** > **ContosotargetKeyvault** > **zasady dostępu** i dodaj odpowiednie uprawnienia.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowy tryb failover.
