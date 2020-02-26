---
title: Zainstaluj klienta Chef z Azure Portal
description: Dowiedz się, jak wdrożyć i skonfigurować klienta programu Chef z poziomu Azure Portal
keywords: Azure, Chef, DevOps, klient, instalacja, Portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586363"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Zainstaluj klienta Chef z Azure Portal
Rozszerzenie klienta Chef można dodać bezpośrednio na komputerze z systemem Linux lub Windows z poziomu Azure Portal. Ten artykuł przeprowadzi Cię przez proces przy użyciu nowej maszyny wirtualnej z systemem Linux.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Chef**: Jeśli nie masz aktywnego konta Chef, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej hostowanej usługi Chef](https://manage.chef.io/signup). Aby wykonać instrukcje opisane w tym artykule, potrzebne są następujące wartości z konta usługi Chef:
  - klucz organization_validation
  - RB
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Instalowanie rozszerzenia Chef na nowej maszynie wirtualnej z systemem Linux
W tej sekcji użyjesz najpierw Azure Portal, aby utworzyć maszynę z systemem Linux. W trakcie procesu zobaczysz również, jak zainstalować rozszerzenie Chef na nowej maszynie wirtualnej.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz opcję **maszyny wirtualne** . Jeśli nie ma opcji **maszyny wirtualne** , wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **maszyny wirtualne**.

1. Na karcie **maszyny wirtualne** wybierz pozycję **Dodaj**.

    ![Dodaj nową maszynę wirtualną w Azure Portal](./media/chef-extension-portal/add-vm.png)

1. Na karcie **obliczenia** wybierz żądany system operacyjny. Na potrzeby tego pokazu zostanie wybrany **serwer Ubuntu** .

1. Na karcie **serwer Ubuntu** wybierz pozycję **Ubuntu Server 16,04 LTS**.

    ![W przypadku tworzenia maszyny wirtualnej Ubuntu należy określić wymaganą wersję](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na karcie **Ubuntu Server 16,04 LTS** wybierz pozycję **Utwórz**.

    ![Ubuntu zawiera dodatkowe informacje na temat ich produktu](./media/chef-extension-portal/create-vm.png)

1. Na karcie **Tworzenie maszyny wirtualnej** wybierz pozycję **podstawowe**.

1. Na karcie **podstawowe** określ następujące wartości, a następnie wybierz przycisk **OK**.

   - **Nazwa** — wprowadź nazwę nowej maszyny wirtualnej.
   - **Typ dysku maszyny wirtualnej** — Określ dysk **SSD** lub dysk **twardy** dla typu dysku magazynu. Aby uzyskać więcej informacji o typach dysków maszyn wirtualnych na platformie Azure, zobacz artykuł [Wybieranie typu dysku](../virtual-machines/windows/disks-types.md).
   - **Nazwa użytkownika** — wprowadź nazwę użytkownika, który ma przyznane uprawnienia administratora na maszynie wirtualnej.
   - **Typ uwierzytelniania** — wybierz opcję **hasło**. Możesz również wybrać **klucz publiczny SSH**i podać wartość klucza publicznego SSH. Na potrzeby tej wersji demonstracyjnej (i zrzutów ekranu) wybrano opcję **hasło** .
   - **Hasło** i **Potwierdź hasło** — wprowadź hasło dla użytkownika.
   - **Zaloguj się przy użyciu Azure Active Directory** — wybierz pozycję **wyłączone**.
   - **Subskrypcja** — wybierz żądaną subskrypcję platformy Azure, jeśli masz więcej niż jeden.
   - **Grupa zasobów** — wprowadź nazwę grupy zasobów.
   - **Location** (Lokalizacja) — Wybierz pozycję **East US** (Wschodnie stany USA).

     ![Karta podstawowe informacje na temat tworzenia maszyny wirtualnej](./media/chef-extension-portal/add-vm-basics.png)

1. Na karcie **Wybierz rozmiar** wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.

1. Na karcie **Ustawienia** większość wartości zostaną wypełnione na podstawie wartości wybranych na poprzednich kartach. Wybierz pozycję **Rozszerzenia**.

     ![Rozszerzenia są dodawane do maszyn wirtualnych za pomocą karty Ustawienia](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na karcie **rozszerzenia** wybierz pozycję **Dodaj rozszerzenie**.

     ![Wybierz pozycję Dodaj rozszerzenie, aby dodać rozszerzenie do maszyny wirtualnej](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na karcie **nowy zasób** wybierz pozycję **Linux Chef Extension (1.2.3)** .

     ![Chef ma rozszerzenia dla maszyn wirtualnych z systemem Linux i Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na karcie **rozszerzenie Chef systemu Linux** wybierz pozycję **Utwórz**.

1. Na karcie **rozszerzenie instalacji** określ następujące wartości, a następnie wybierz przycisk **OK**.

    - **Adres URL serwera Chef** — wprowadź adres URL serwera Chef, który zawiera nazwę organizacji, na przykład *https://api.chef.io/organization/mycompany* .
    - **Nazwa węzła Chef** — wprowadź nazwę węzła Chef.
    - **Lista uruchamiania** — wprowadź listę uruchamiania Chef, która jest dodawana do maszyny. Ta wartość może pozostać pusta.
    - **Nazwa klienta weryfikacji** — wprowadź nazwę klienta Chef weryfikacji. Na przykład `tarcher-validator`.
    - **Klucz weryfikacji** — wybierz plik zawierający klucz weryfikacji używany podczas uruchamiania maszyn.
    - **Plik konfiguracji klienta** — wybierz plik konfiguracji dla Chef-Client. Ta wartość może pozostać pusta.
    - **Wersja klienta Chef** — wprowadź wersję klienta Chef do zainstalowania. Ta wartość może pozostać pusta, co spowoduje zainstalowanie najnowszej wersji.
    - **Tryb weryfikacji protokołu SSL** — wybierz opcję **Brak** lub **węzeł równorzędny**. Nie wybrano *niczego* dla demonstracji.
    - **Środowisko Chef** — wprowadź środowisko Chef, do którego należy ten węzeł. Ta wartość może pozostać pusta.
    - **Zaszyfrowany zbiór danych** — wybierz plik zawierający wpis tajny dla zaszyfrowanego zbioru danych, do którego maszyna musi uzyskać dostęp. Ta wartość może pozostać pusta.
    - **Certyfikat SSL serwera Chef** — wybierz certyfikat SSL przypisany do serwera Chef. Ta wartość może pozostać pusta.

      ![Instalowanie serwera Chef na maszynie wirtualnej z systemem Linux](./media/chef-extension-portal/install-extension.png)

1. Gdy zostanie wyświetlona karta **rozszerzenia** , wybierz **przycisk OK**.

1. Gdy zostanie wyświetlona karta **Ustawienia** , wybierz **przycisk OK**.

1. Gdy zostanie wyświetlona karta **Tworzenie** , zobaczysz podsumowanie wybranych i wprowadzonych opcji. Sprawdź informacje, a także **warunki użytkowania**i wybierz pozycję **Utwórz**.

Po ukończeniu procesu tworzenia i wdrażania maszyny wirtualnej z rozszerzeniem Chef powiadomienie wskazuje na powodzenie lub niepowodzenie operacji. Ponadto strona zasobów dla nowej maszyny wirtualnej jest automatycznie otwierana w Azure Portal po jej utworzeniu.

![Instalowanie serwera Chef na maszynie wirtualnej z systemem Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Chef](chef-automation.md)