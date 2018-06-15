---
title: Instalowanie klienta Chef z portalu Azure
description: Dowiedz się, jak wdrożyć i skonfigurować klienta Chef z portalu Azure
keywords: Azure, chef, devops, klient, install, portalu
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 52f34361d7c1f3dff47f2571a714b8be7764cc6f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260150"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalowanie klienta Chef z portalu Azure
Podczas tworzenia lub modyfikowania maszyny wirtualnej systemu Linux lub Windows z portalu Azure, można dodać rozszerzenia Chef do maszyny wirtualnej. Ten artykuł przeprowadzi Cię przez ten proces, korzystając z nowej maszyny wirtualnej systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Chef**: Jeśli nie masz aktywnego konta Chef zarejestrować się w celu [bezpłatnej wersji próbnej programu hostowanej Chef](https://manage.chef.io/signup). Aby skorzystać z instrukcji w tym artykule, będą potrzebne następujące wartości z Twojego konta Chef: 
    - klucz organization_validation
    - RB
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Zainstaluj rozszerzenie Chef na maszynie wirtualnej systemu Linux
W tej sekcji zostanie pierwszym użyciu portalu Azure do utworzenia maszyny z systemem Linux. W trakcie widoczne jest również sposób instalowania rozszerzenia Chef na nowej maszynie wirtualnej.

1. Przejdź do [portalu Azure](http://portal.azure.com).

1. Wybierz z menu po lewej stronie **maszyn wirtualnych** opcji. Jeśli **maszyn wirtualnych** opcja nie jest obecny, wybierz pozycję **wszystkie usługi** , a następnie wybierz **maszyn wirtualnych**.

1. Na **maszyn wirtualnych** wybierz opcję **Dodaj**.

    ![Dodaj nową maszynę wirtualną w portalu Azure](./media/chef-extension-portal/add-vm.png)

1. Na **obliczeniowe** , a następnie wybierz odpowiedni system operacyjny. Dla tego pokazu **Ubuntu Server** jest zaznaczone.

1. Na **Ubuntu Server** wybierz opcję **Ubuntu Server 16.04 LTS**.

    ![Podczas tworzenia maszyny wirtualnej systemu Ubuntu, określ wersję należy](./media/chef-extension-portal/ubuntu-server-version.png)

1. Na **Ubuntu Server 16.04 LTS** wybierz opcję **Utwórz**.

    ![Ubuntu udostępnia dodatkowe informacje o swoich produktach](./media/chef-extension-portal/create-vm.png)

1. Na **tworzenia maszyny wirtualnej** wybierz opcję **podstawy**.

1. Na **podstawy** , określ następujące wartości, a następnie wybierz **OK**.

    - **Nazwa** — wprowadź nazwę dla nowej maszyny wirtualnej.
    - **Typ dysku maszyny Wirtualnej** — określ **SSD** lub **HDD** dla typ dysku magazynu. Aby uzyskać więcej informacji o typach dysków maszyny wirtualnej na platformie Azure, zobacz artykuł [zarządzanych dysków dla maszyny wirtualne i Magazyn w warstwie Premium wysokiej wydajności](/azure/virtual-machines/windows/premium-storage).
    - **Nazwa użytkownika** — wprowadź nazwę użytkownika, któremu udzielono uprawnień administratora na maszynie wirtualnej.
    - **Typ uwierzytelniania** — wybierz tę opcję **hasło**. Możesz też wybrać **klucz publiczny SSH**i podaj wartość klucza publicznego SSH. Dla celów tego pokazu (na zrzutach ekranu) **hasło** jest zaznaczone.
    - **Hasło** i **Potwierdź hasło** — wprowadź hasło dla użytkownika.
    - **Zaloguj się za pomocą usługi Azure Active Directory** — wybierz tę opcję **wyłączone**.
    - **Subskrypcja** — wybierz odpowiednią subskrypcję platformy Azure, jeśli masz więcej niż jeden.
    - **Grupa zasobów** — wprowadź nazwę grupy zasobów.
    - **Lokalizacja** — wybierz tę opcję **wschodnie stany USA**.

    ![Karta podstawowe informacje dotyczące tworzenia maszyny wirtualnej](./media/chef-extension-portal/add-vm-basics.png)

1. Na **wybierz rozmiar** , wybierz rozmiar maszyny wirtualnej, a następnie wybierz **wybierz**.

1. Na **ustawienia** kartę, większość wartości zostaną wypełnione na podstawie wartości wybrane w poprzednim karty. Wybierz **rozszerzenia**.

    ![Rozszerzenia są dodawane do maszyn wirtualnych za pomocą karty Ustawienia](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Na **rozszerzenia** wybierz opcję **dodanie rozszerzenia**.

    ![Wybierz opcję Dodaj rozszerzenie można dodać rozszerzenia do maszyny wirtualnej](./media/chef-extension-portal/add-vm-add-extension.png)

1. Na **nowy zasób** wybierz opcję **rozszerzenia Chef systemu Linux (1.2.3)**.

    ![Chef ma rozszerzenia dla maszyn wirtualnych systemu Linux i Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Na **rozszerzenia Linux Chef** wybierz opcję **Utwórz**.

1. Na **zainstalować rozszerzenie** , określ następujące wartości, a następnie wybierz **OK**.

    - **Adres URL serwera chef** — wprowadź adres URL serwera Chef, która zawiera nazwę organizacji. Po użyciu *https://api.chef.io/organization/hessco* dla pokaz.
    - **Nazwa węzła chef** — wprowadź nazwę węzła Chef. Może to być dowolna wartość.
    - **Uruchom polecenie List** — wprowadź listę Chef Uruchom dodanego do komputera. Może to być puste.
    - **Sprawdzanie poprawności klienta nazwa** — wprowadź nazwę Chef weryfikacji klienta. Po użyciu *modułu sprawdzania poprawności tarcher* dla pokaz.
    - **Klucz sprawdzania poprawności** — wybierz plik zawierający klucz sprawdzania poprawności używany podczas ładowania maszynach. 
    - **Plik konfiguracji klienta** — wybierz plik konfiguracji klienta chef. Może to być puste.
    - **Wersja klienta chef** — wprowadź wersja klienta chef do zainstalowania. Wartość pusta spowoduje, że do zainstalowania najnowszej wersji. Może to być puste.
    - **Tryb weryfikacji SSL** — wybierz opcję **Brak** lub **równorzędnej**. Po wybraniu *Brak* dla pokaz.
    - **Środowisko chef** — wprowadź środowiska Chef powinna należeć ten węzeł. Może to być puste.
    - **Zaszyfrowany klucz tajny Databag** — wybierz plik zawierający klucz tajny dla Databag szyfrowane tego komputera powinni mieć dostęp do. Może to być puste.
    - **Certyfikat SSL serwera chef** — wybierz certyfikat protokołu SSL, przypisane do serwera Chef. Może to być puste.

    ![Instalowanie serwera Chef na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/install-extension.png)

1. Gdy zwrócony do **rozszerzenia** wybierz opcję **OK**.

1. Gdy zwrócony do **ustawienia** wybierz opcję **OK**.

1. Gdy zwrócony do **Utwórz** kartę (reprezentujący podsumowanie wybrane opcje i wprowadzone), sprawdź informacje, jak również **warunki użytkowania**i wybierz **Utwórz**.

Po zakończeniu procesu tworzenia i wdrażania maszyny wirtualnej z rozszerzeniem Chef powiadomienie wskazuje powodzenie lub Niepowodzenie operacji. Ponadto strona zasobów dla nowej maszyny wirtualnej automatycznie otwiera w portalu Azure po jego utworzeniu.

![Instalowanie serwera Chef na maszynie wirtualnej systemu Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz maszynę wirtualną systemu Windows na platformie Azure przy użyciu Chef](/azure/virtual-machines/windows/chef-automation)