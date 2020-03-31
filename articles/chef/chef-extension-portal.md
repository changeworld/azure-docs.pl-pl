---
title: Instalowanie klienta szefa kuchni z witryny Azure portal
description: Dowiedz się, jak wdrożyć i skonfigurować klienta szefa kuchni z witryny Azure Portal
keywords: azure, szef kuchni, devops, klient, instalacja, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586363"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalowanie klienta szefa kuchni z witryny Azure portal
Rozszerzenie klienta programu Chef można dodać bezpośrednio do komputera z systemem Linux lub Windows z witryny Azure portal. W tym artykule przeprowadzi Cię przez proces przy użyciu nowej maszyny wirtualnej systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Szef kuchni**: Jeśli nie masz aktywnego konta Chef, zarejestruj się, aby [uzyskać bezpłatną wersję próbną Hosted Chef](https://manage.chef.io/signup). Aby postępować zgodnie z instrukcjami zawartymi w tym artykule, musisz mieć następujące wartości z konta szefa kuchni:
  - organization_validation klucz
  - Rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Zainstaluj rozszerzenie Chef na nowej maszynie wirtualnej Systemu Linux
W tej sekcji najpierw użyjesz witryny Azure portal do utworzenia komputera z systemem Linux. Podczas tego procesu zobaczysz również, jak zainstalować rozszerzenie chef na nowej maszynie wirtualnej.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz opcję **Maszyny wirtualne.** Jeśli opcja **Maszyny wirtualne** nie jest obecna, wybierz **pozycję Wszystkie usługi,** a następnie wybierz pozycję **Maszyny wirtualne**.

1. Na karcie **Maszyny wirtualne** wybierz pozycję **Dodaj**.

    ![Dodawanie nowej maszyny wirtualnej w witrynie Azure portal](./media/chef-extension-portal/add-vm.png)

1. Na karcie **Obliczanie** wybierz żądany system operacyjny. Dla tego demo jest wybrany **serwer Ubuntu.**

1. Na karcie **Serwer Ubuntu** wybierz **Ubuntu Server 16.04 LTS**.

    ![Podczas tworzenia maszyny wirtualnej Ubuntu określ wersję, której potrzebujesz](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na karcie **Ubuntu Server 16.04 LTS** wybierz pozycję **Utwórz**.

    ![Ubuntu dostarcza dodatkowych informacji na temat swojego produktu](./media/chef-extension-portal/create-vm.png)

1. Na karcie **Tworzenie maszyny wirtualnej** wybierz pozycję **Podstawy**.

1. Na karcie **Podstawy** określ następujące wartości, a następnie wybierz przycisk **OK**.

   - **Nazwa** — wprowadź nazwę nowej maszyny wirtualnej.
   - **Typ dysku maszyny Wirtualnej** — umożliwia określenie dysku **SSD** lub **HDD** dla typu dysku magazynu. Aby uzyskać więcej informacji na temat typów dysków maszyn wirtualnych na platformie Azure, zobacz artykuł [Wybieranie typu dysku](../virtual-machines/windows/disks-types.md).
   - **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu przyznano uprawnienia administratora na maszynie wirtualnej.
   - **Typ uwierzytelniania** - Wybierz **hasło**. Można również wybrać **klucz publiczny SSH**i podać wartość klucza publicznego SSH. Na potrzeby tego demo (i na zrzutach ekranu) **wybrano hasło.**
   - **Hasło** i **Potwierdź hasło** — wprowadź hasło użytkownika.
   - **Zaloguj się za pomocą usługi Azure Active Directory** — wybierz **wyłączone**.
   - **Subskrypcja** — wybierz żądaną subskrypcję platformy Azure, jeśli masz więcej niż jedną subskrypcję.
   - **Grupa zasobów** — umożliwia wprowadzenie nazwy grupy zasobów.
   - **Location** (Lokalizacja) — Wybierz pozycję **East US** (Wschodnie stany USA).

     ![Karta Podstawowe informacje dotyczące tworzenia maszyny wirtualnej](./media/chef-extension-portal/add-vm-basics.png)

1. Na karcie **Wybieranie rozmiaru** wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.

1. Na karcie **Ustawienia** większość wartości zostanie wypełniona na podstawie wartości wybranych na poprzednich kartach. Wybierz pozycję **Rozszerzenia**.

     ![Rozszerzenia są dodawane do maszyn wirtualnych za pośrednictwem karty Ustawienia](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na karcie **Rozszerzenia** wybierz pozycję **Dodaj rozszerzenie**.

     ![Wybierz dodaj rozszerzenie, aby dodać rozszerzenie do maszyny wirtualnej](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na karcie **Nowy zasób** wybierz pozycję **Linux Chef Extension (1.2.3)**.

     ![Chef ma rozszerzenia dla maszyn wirtualnych Linux i Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na karcie **Rozszerzenie szefa kuchni systemu Linux** wybierz pozycję **Utwórz**.

1. Na karcie **Rozszerzenie instalacji** określ następujące wartości, a następnie wybierz przycisk **OK**.

    - **Adres URL serwera szefa kuchni** — wprowadź adres URL *https://api.chef.io/organization/mycompany*serwera szefa kuchni zawierający na przykład nazwę organizacji .
    - **Nazwa węzła szefa kuchni** — wprowadź nazwę węzła szefa kuchni.
    - **Uruchom listę** - Wprowadź listę uruchamiania szefa kuchni, która jest dodawana do komputera. Tę wartość można pozostawić pustą.
    - **Nazwa klienta sprawdzania poprawności** — wprowadź nazwę klienta sprawdzania poprawności szefa kuchni. na przykład `tarcher-validator`.
    - **Klucz sprawdzania poprawności** — wybierz plik zawierający klucz sprawdzania poprawności używany podczas uruchamiania komputerów.
    - **Plik konfiguracji klienta** — wybierz plik konfiguracyjny dla szefa kuchni klienta. Tę wartość można pozostawić pustą.
    - **Wersja klienta szefa kuchni** — wprowadź wersję klienta szefa kuchni do zainstalowania. Tę wartość można pozostawić pustą, która instaluje najnowszą wersję.
    - **Tryb weryfikacji SSL** - Wybierz opcję **Brak** lub **Peer**. *Żadna nie* została wybrana do wersji demonstracyjnej.
    - **Środowisko szefa kuchni** — wprowadź środowisko szefa kuchni, do których ten węzeł powinien być członkiem. Tę wartość można pozostawić pustą.
    - **Zaszyfrowany klucz tajny worka z danymi** — wybierz plik zawierający klucz tajny zaszyfrowanej torby na dane, do których ten komputer musi uzyskać dostęp. Tę wartość można pozostawić pustą.
    - **Certyfikat SSL serwera chef** — wybierz certyfikat SSL przypisany do serwera chef. Tę wartość można pozostawić pustą.

      ![Instalowanie serwera chefa na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/install-extension.png)

1. Po wyświetleniu karty **Rozszerzenia** wybierz przycisk **OK**.

1. Po wyświetleniu karty **Ustawienia** wybierz przycisk **OK**.

1. Po wyświetleniu karty **Utwórz** zostanie wyświetlone podsumowanie wybranych i wprowadzonych opcji. Sprawdź informacje oraz **Warunki użytkowania**i wybierz pozycję **Utwórz**.

Po zakończeniu procesu tworzenia i wdrażania maszyny wirtualnej z rozszerzeniem chef, powiadomienie wskazuje na powodzenie lub niepowodzenie operacji. Ponadto strona zasobu dla nowej maszyny wirtualnej zostanie automatycznie otwarta w witrynie Azure portal po jej utworzeniu.

![Instalowanie serwera chefa na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie maszyny wirtualnej systemu Windows na platformie Azure przy użyciu programu Chef](chef-automation.md)