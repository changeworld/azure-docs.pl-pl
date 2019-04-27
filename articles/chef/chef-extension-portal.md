---
title: Zainstaluj klienta programu Chef w witrynie Azure portal
description: Dowiedz się, jak wdrożyć i skonfigurować klienta programu Chef w witrynie Azure portal
keywords: azure, chef, devops, client, install, portal
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: cf7afb50006fb273b4d685f9e4259be1cb60fe4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563890"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Zainstaluj klienta programu Chef w witrynie Azure portal
Można dodać rozszerzenia klienta Chef bezpośrednio do komputera z systemem Linux lub Windows w witrynie Azure portal. W tym artykule przedstawiono proces, korzystając z nowej maszyny wirtualnej systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Program chef**: Jeśli nie masz aktywnego konta programu Chef, należy zasubskrybować [bezpłatną wersję próbną hostowany program Chef](https://manage.chef.io/signup). Aby skorzystać z instrukcji w tym artykule, będą potrzebne następujące wartości z Twojego konta programu Chef:
  - klucz organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Zainstaluj rozszerzenie programu Chef na nowej maszynie wirtualnej systemu Linux
W tej sekcji najpierw użyjesz witryny Azure portal do utworzenia maszyny z systemem Linux. W trakcie również pokazano, jak można zainstalować rozszerzenia programu Chef na nowej maszynie wirtualnej.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **maszyn wirtualnych** opcji. Jeśli **maszyn wirtualnych** opcja nie jest obecny, wybierz opcję **wszystkich usług** , a następnie wybierz **maszyn wirtualnych**.

1. Na **maszyn wirtualnych** zaznacz **Dodaj**.

    ![Dodaj nową maszynę wirtualną w witrynie Azure portal](./media/chef-extension-portal/add-vm.png)

1. Na **obliczenia** , a następnie wybierz odpowiedni system operacyjny. Dla tej wersji demonstracyjnej **Ubuntu Server** jest zaznaczone.

1. Na **Ubuntu Server** zaznacz **Ubuntu Server 16.04 LTS**.

    ![Podczas tworzenia maszyny wirtualnej systemu Ubuntu, określ wersję, których potrzebujesz](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na **Ubuntu Server 16.04 LTS** zaznacz **Utwórz**.

    ![Ubuntu zawiera dodatkowe informacje na temat produktu](./media/chef-extension-portal/create-vm.png)

1. Na **Utwórz maszynę wirtualną** zaznacz **podstawy**.

1. Na **podstawy** karcie, określ następujące wartości, a następnie wybierz **OK**.

   - **Nazwa** — wprowadź nazwę dla nowej maszyny wirtualnej.
   - **Typ dysku maszyny Wirtualnej** -określ **SSD** lub **HDD** dla typu dysku magazynu. Aby uzyskać więcej informacji na temat typów dysków maszyny wirtualnej na platformie Azure, zobacz artykuł [wybierz typ dysku](../virtual-machines/windows/disks-types.md).
   - **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu przyznano uprawnienia administratora na maszynie wirtualnej.
   - **Typ uwierzytelniania** — wybierz tę opcję **hasło**. Możesz również wybrać **klucz publiczny SSH**i podaj wartość klucza publicznego SSH. Dla celów tej wersji demonstracyjnej (i na zrzutach ekranu) **hasło** jest zaznaczone.
   - **Hasło** i **Potwierdź hasło** -wprowadź hasło dla użytkownika.
   - **Zaloguj się przy użyciu usługi Azure Active Directory** — wybierz tę opcję **wyłączone**.
   - **Subskrypcja** — wybierz odpowiednią subskrypcję platformy Azure, jeśli masz więcej niż jeden.
   - **Grupa zasobów** — wprowadź nazwę grupy zasobów.
   - **Location** (Lokalizacja) — Wybierz pozycję **East US** (Wschodnie stany USA).

     ![Podstawowe informacje o karcie podczas tworzenia maszyny wirtualnej](./media/chef-extension-portal/add-vm-basics.png)

1. Na **wybierz rozmiar** karty, wybierz rozmiar maszyny wirtualnej, a następnie wybierz **wybierz**.

1. Na **ustawienia** karty i większość wartości zostaną wypełnione na podstawie wartości wybranych w poprzedniej karty. Wybierz pozycję **Rozszerzenia**.

     ![Rozszerzenia są dodawane do maszyn wirtualnych za pomocą karty Ustawienia](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na **rozszerzenia** zaznacz **Dodaj rozszerzenie**.

     ![Wybierz pozycję Dodaj rozszerzenie, aby dodać rozszerzenie do maszyny wirtualnej](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na **nowy zasób** zaznacz **rozszerzenia Chef systemu Linux (1.2.3)**.

     ![Program chef oferuje rozszerzenia dla maszyn wirtualnych z systemem Linux i Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na **rozszerzenie Linux Chef** zaznacz **Utwórz**.

1. Na **zainstalować rozszerzenie** karcie, określ następujące wartości, a następnie wybierz **OK**.

    - **Adres URL serwera programu chef** — wprowadź adres URL serwera programu Chef, który zawiera nazwę organizacji, na przykład *https://api.chef.io/organization/mycompany*.
    - **Nazwa węzła chef** — wprowadź nazwę węzła programu Chef. Może to być dowolna wartość.
    - **Lista uruchamiania** — wprowadź listę Chef uruchamiania, który jest dodawany do maszyny. To może być puste.
    - **Nazwa klienta weryfikacji** — Podaj nazwę programu Chef weryfikacji klienta. na przykład *weryfikacji tarcher*.
    - **Klucz sprawdzania poprawności** — wybierz plik zawierający klucz sprawdzania poprawności, używane podczas uruchamiania maszyny.
    - **Plik konfiguracji klienta** — wybierz plik konfiguracji dla klienta programu chef. To może być puste.
    - **Wersja klienta programu chef** -wprowadź informacje o wersji klienta programu chef do zainstalowania. To może być puste. Wartość pusta instaluje najnowszą wersję.
    - **Tryb weryfikacji SSL** — wybierz opcję **Brak** lub **równorzędnej**. *Brak* został wybrany udział w pokazie.
    - **Środowisko chef** -wprowadź środowiska Chef powinna należeć tego węzła. To może być puste.
    - **Zaszyfrowany klucz tajny Databag** — wybierz plik zawierający klucz tajny dla Databag szyfrowane tego komputera powinni mieć dostęp do. To może być puste.
    - **Certyfikat SSL serwera chef** — wybierz certyfikat protokołu SSL, przypisany do serwera programu Chef. To może być puste.

      ![Instalowanie serwera programu Chef na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/install-extension.png)

1. Po powrocie do **rozszerzenia** zaznacz **OK**.

1. Po powrocie do **ustawienia** zaznacz **OK**.

1. Po powrocie do **Utwórz** kartę (ta reprezentuje podsumowanie wybrane opcje i wprowadzone), sprawdź informacje, jak również **warunki użytkowania**i wybierz **Utwórz**.

Po zakończeniu procesu tworzenia i wdrażania maszyny wirtualnej z rozszerzeniem programu Chef powiadomienie wskazuje powodzenie lub Niepowodzenie operacji. Ponadto zasobów dla nowej maszyny wirtualnej automatycznie zostanie otwarta strona w witrynie Azure portal po jej utworzeniu.

![Instalowanie serwera programu Chef na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Kolejne kroki

- [Utwórz maszynę wirtualną Windows na platformie Azure przy użyciu programu Chef](/azure/virtual-machines/windows/chef-automation)
