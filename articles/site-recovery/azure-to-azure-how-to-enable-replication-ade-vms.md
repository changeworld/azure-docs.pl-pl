---
title: Konfigurowanie maszyn wirtualnych z włączoną replikacją dla usługa Azure disk encryption (ADE) w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania replikacji ADE w przypadku maszyn wirtualnych między regionami platformy Azure do innego za pomocą Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/14/2018
ms.author: sutalasi
ms.openlocfilehash: 828e75a61b7f5d6d747b13069d25503b43b65222
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311934"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Replikowanie maszyn wirtualnych jest włączone szyfrowanie (ADE) dysku platformy Azure do innego regionu platformy Azure

W tym artykule opisano sposób włączania replikacji usługa Azure disk encryption (ADE) włączone maszyn wirtualnych między regionami platformy Azure do innego.

>[!NOTE]
>Tylko maszyny wirtualne platformy Azure z systemem operacyjnym Windows OS i [włączone szyfrowanie za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obecnie obsługiwane przez usługę Azure Site Recovery.
>

## <a name="required-user-permissions"></a>Uprawnienia użytkownika wymagane

Aby włączyć replikację maszyn wirtualnych ADE z portalu, użytkownik powinien mieć poniżej uprawnienia.
- Uprawnienia usługi Key vault
    - lista
    - Przycisk Utwórz
    - Get

-   Uprawnienia klucza tajnego usługi Key vault
    - List
    - Przycisk Utwórz
    - Get

- Uprawnienia klucza magazynu kluczy (wymagane tylko wtedy, gdy jest używany przez maszyny wirtualne klucza szyfrowania do szyfrowania kluczy szyfrowania dysku)
    - List
    - Get
    - Przycisk Utwórz
    - Szyfrowanie
    - Odszyfruj

Mogą zarządzać uprawnieniami, przechodząc do zasobu usługi key vault w portalu i dodanie wymaganych uprawnień dla użytkownika.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Jeśli użytkownik, odzyskiwanie po awarii (DR) nie ma wymaganych uprawnień, aby skopiować klucze, poniższego skryptu można podać administratorowi zabezpieczeń z odpowiednimi uprawnieniami, aby skopiować szyfrowania, wpisy tajne i klucze w regionie docelowym.

>[!NOTE]
>Aby włączyć replikację maszyny wirtualnej ADE z portalu, potrzebujesz co najmniej uprawnienia "List" magazynów kluczy, wpisów tajnych i kluczy
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Kopiowanie kluczy ADE w regionie odzyskiwania po awarii przy użyciu skryptu programu PowerShell

1. Otwórz kod skryptu raw "CopyKeys" w oknie przeglądarki, klikając [ten link](https://aka.ms/ade-asr-copy-keys-code).
2. Skopiuj skrypt do pliku i nadaj mu nazwę "Kopiuj-keys.ps1".
2. Otwórz aplikację programu Windows PowerShell i przejdź do lokalizacji folderu, w której istnieje plik.
3. Uruchom "Kopiuj-keys.ps1"
4. Podaj poświadczenia logowania do platformy Azure.
5. Wybierz **subskrypcji platformy Azure** maszyn wirtualnych.
6. Poczekaj, aż grup zasobów, aby załadować, a następnie wybierz pozycję **grupy zasobów** maszyn wirtualnych.
7. Wybierz maszyny wirtualne z listy maszyn wirtualnych jest wyświetlany. Na liście są wyświetlane tylko maszyny wirtualne z włączoną szyfrowania dysków Azure.
8. Wybierz **lokalizacja docelowa**.
9. **Magazyny kluczy szyfrowania dysku**: Domyślnie usługa Azure Site Recovery tworzy nowy magazyn kluczy w regionie docelowym nazwy sufiks "asr" kluczy szyfrowania dysków maszyny Wirtualnej źródłowego w oparciu o. W przypadku, gdy kluczy utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie. Z listy, jeśli to konieczne, możesz wybrać inny magazyn kluczy.
10. **Magazyny kluczy szyfrowania klucza**: Domyślnie usługa Azure Site Recovery tworzy nowego magazynu kluczy w regionie docelowym nazwy sufiks "asr" na podstawie kluczy szyfrowania maszyny Wirtualnej źródłowego o. W przypadku, gdy kluczy utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie. Z listy, jeśli to konieczne, możesz wybrać inny magazyn kluczy.

## <a name="enable-replication"></a>Włączanie replikacji

Ta procedura zakłada, że Azure region podstawowy jest Azja Wschodnia, a w regionie pomocniczym Azja południowo-wschodnia.

1. W magazynie kliknij **+ Replikuj**.
2. Należy zwrócić uwagę następujących pól:
    - **Źródło**: punkt początkowy maszyn wirtualnych, czyli w tym przypadku **Azure**.
    - **Lokalizacja źródłowa**: region platformy Azure, z którym chcesz chronić maszyny wirtualne. Na tej ilustracji lokalizacja źródłowa jest "Azja Wschodnia"
    - **Model wdrażania**: model wdrażania platformy Azure maszyna źródłowa.
    - **Źródło subskrypcji**: subskrypcja, do której należą źródłowych maszyn wirtualnych. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
    - **Grupa zasobów**: Grupa zasobów, do której należą źródłowych maszyn wirtualnych. Wszystkie maszyny wirtualne w ramach wybranej grupy zasobów są wyświetlane do ochrony w następnym kroku.

3. W **maszyny wirtualne > Wybierz maszyny wirtualne**, a następnie kliknij i zaznacz każdej maszyny Wirtualnej, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.

4. W **ustawienia**, można opcjonalnie skonfigurować ustawienia lokacji docelowej:

    - **Lokalizacja docelowa**: lokalizacja, w której zostaną zreplikowane dane źródłowe maszyny wirtualnej. W zależności od lokalizacji wybranych maszyn, Usługa Site Recovery udostępnia listę regionów udało się. Firma Microsoft zaleca zachowywanie lokalizacji docelowej taka sama jako lokalizacja magazynu usługi Recovery Services.
    - **Subskrypcja docelowa**: subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa będzie taka sama jak subskrypcja źródłowa.
    - **Docelowa grupa zasobów**: Grupa zasobów, w którym wszystkie replikowane maszyny wirtualne należą. Domyślnie usługa Azure Site Recovery tworzy nową grupę zasobów w regionie docelowym przy użyciu nazwy sufiks "asr". W przypadku, gdy grupa zasobów utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie. Możesz również dostosować go, jak pokazano w dalszej części tego artykułu. Lokalizacja docelowa grupa zasobów może być dowolnego regionu systemu Azure, z wyjątkiem regionu, w którym znajdują się źródłowych maszyn wirtualnych.
    - **Docelowa sieć wirtualna**: Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym przy użyciu nazwy sufiks "asr". To jest mapowane na sieci źródłowej i używane do wszelkich przyszłych ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
    - **Docelowe konta magazynu (Jeśli źródło maszyny Wirtualnej nie korzysta z dysków zarządzanych)**: Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego naśladując konfigurację magazynu źródłowej maszyny Wirtualnej. W przypadku, gdy konto magazynu już istnieje, zostanie on użyty ponownie.
    - **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna używa dysków zarządzanych)**: Usługa Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym w celu zdublowania dysków zarządzanych źródłowej maszyny Wirtualnej przy użyciu tego samego typu magazynu (standardowa / premium), jak dysku zarządzanego źródłowej maszyny Wirtualnej.
    - **Konta magazynu pamięci podręcznej**: Usługa Site Recovery wymaga konta dodatkowego magazynu o nazwie magazynu pamięci podręcznej w regionie źródłowym. Wszystkie zmiany, które są wykonywane na źródłowe maszyny wirtualne są śledzone i wysyłane do konta magazynu pamięci podręcznej przed zreplikowaniem tych do lokalizacji docelowej.
    - **Zestaw dostępności**: Domyślnie usługa Azure Site Recovery tworzy nowy zestaw dostępności w regionie docelowym, z nazwy sufiks "asr". W przypadku, gdy w zestawie dostępności, utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie.
    - **Magazyny kluczy szyfrowania dysku**: Domyślnie usługa Azure Site Recovery tworzy nowy magazyn kluczy w regionie docelowym nazwy sufiks "asr" kluczy szyfrowania dysków maszyny Wirtualnej źródłowego w oparciu o. W przypadku, gdy kluczy utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie.
    - **Magazyny kluczy szyfrowania klucza**: Domyślnie usługa Azure Site Recovery tworzy nowego magazynu kluczy w regionie docelowym nazwy sufiks "asr" na podstawie kluczy szyfrowania maszyny Wirtualnej źródłowego o. W przypadku, gdy kluczy utworzonych przez usługę Azure Site Recovery jest już istnieje, zostanie on użyty ponownie.
    - **Zasady replikacji**: definiuje ustawienia odzyskiwania punktu przechowywania historii i aplikacji częstotliwość wykonywania migawek. Domyślnie usługa Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi 24 godzin czas przechowywania punktu odzyskiwania i "60 minut częstotliwość migawek spójnych na poziomie aplikacji.



## <a name="customize-target-resources"></a>Dostosuj zasoby docelowe

Można zmodyfikować domyślne ustawienia regionu docelowego używane przez usługę Site Recovery.


1. Kliknij przycisk **Dostosuj:** obok "Subskrypcji docelowej" Aby zmodyfikować domyślne subskrypcji docelowej. Wybierz subskrypcję z listy wszystkich subskrypcji dostępnych w tej samej dzierżawie usługi Azure Active Directory (AAD).

2. Kliknij przycisk **Dostosuj:** obok pozycji "grupy zasobów, magazynu, sieci i dostępność ustawia zmodyfikować poniższe ustawienia domyślne:
    - W **docelowa grupa zasobów**, wybierz grupę zasobów z listy wszystkich grup zasobów w lokalizacji docelowej subskrypcji.
    - W **docelowa sieć wirtualna**, wybierz sieć, z listy sieci wirtualnej w lokalizacji docelowej.
    - W **zestawu dostępności**, możesz dodać ustawienia zestawu dostępności maszyny wirtualnej, jeśli są one częścią zestaw dostępności w regionie źródłowym.
    - W **kont magazynu docelowego**, wybierz konto, którego chcesz użyć.


2. Kliknij przycisk **Dostosuj:** obok "Ustawienia szyfrowania" Aby zmodyfikować poniższe ustawienia domyślne:
    - W **magazynu kluczy szyfrowania dysku docelowego**, wybierz magazyn kluczy szyfrowania dysków docelowych z listy wszystkich magazynów kluczy w lokalizacji docelowej subskrypcji.
  - W **magazynu kluczy szyfrowania klucza docelowego**, wybierz magazyn kluczy szyfrowania klucza docelowego z listy wszystkich magazynów kluczy w lokalizacji docelowej subskrypcji.

3. Kliknij przycisk **Utwórz zasób docelowy** > **włączania replikacji**.
4. Po włączeniu replikacji maszyn wirtualnych można sprawdzić stan kondycji maszyny Wirtualnej w obszarze **zreplikowane elementy**

>[!NOTE]
>Podczas replikacji początkowej stanu może potrwać pewien czas, aby odświeżyć bez postępu. Kliknij przycisk **Odśwież** przycisk, aby uzyskać najnowszy stan.
>

# <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowy tryb failover.
