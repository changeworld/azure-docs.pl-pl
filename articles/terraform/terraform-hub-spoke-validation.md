---
title: Sprawdź poprawność gwiazdy sieci przy użyciu programu Terraform na platformie Azure
description: Samouczka, aby zweryfikować sieci topologia gwiazdy z wszystkie sieci wirtualne są połączone ze sobą.
services: terraform
ms.service: azure
keywords: terraform, gwiazdy, sieci, sieci hybrydowych, metodyki devops, maszyna wirtualna, azure, wirtualne sieci równorzędne,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 157be65a19a1f790b911aa9d861c5f18fc8c0813
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128263"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Samouczek: Sprawdź poprawność gwiazdy sieci przy użyciu programu Terraform na platformie Azure

W tym artykule możesz wykonać pliki terraform, utworzone w poprzednim artykule w tej serii. Wynik jest weryfikacji łączności między sieciami wirtualnymi w wersji demonstracyjnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użycie HCL (HashiCorp Language) w celu wdrożenia sieci wirtualnej koncentratora w topologii piasty i szprych
> * Aby sprawdzić zasoby, które mają zostać wdrożone przy użyciu planu narzędzia Terraform
> * Użyj narzędzia Terraform zastosować do utworzenia zasobów na platformie Azure
> * Sprawdź łączność między różnymi sieciami
> * Aby zniszczyć wszystkie zasoby za pomocą narzędzia Terraform

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz koncentrator i topologię sieci hybrydowej za pomocą programu Terraform na platformie Azure typu gwiazda](./terraform-hub-spoke-introduction.md).
1. [Tworzenie sieci wirtualnej w środowisku lokalnym za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md).
1. [Tworzenie sieci wirtualnej koncentratora za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-hub-network.md).
1. [Utwórz urządzenie w sieci wirtualnej koncentratora za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-hub-nva.md).
1. [Tworzenie sieci wirtualnych przy użyciu programu Terraform na platformie Azure szprychy](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Sprawdź konfigurację

Po zakończeniu [wymagania wstępne](#prerequisites), sprawdź, czy pliki odpowiedniej konfiguracji są obecne.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Zmień katalog na nowy:

    ```bash
    cd hub-spoke
    ```

1. Uruchom `ls` polecenie, aby sprawdzić, czy `.tf` pliki konfiguracji, utworzony w poprzednich samouczkach należą:

    ![Pliki konfiguracji na wersji demonstracyjnej programu Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Wdrażanie zasobów

1. Inicjowanie dostawcy Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Przykładowe wyniki polecenia "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Uruchom `terraform plan` polecenie, aby zobaczyć efekt wdrożenia przed wykonaniem:

    ```bash
    terraform plan
    ```
    
    ![Przykładowe wyniki polecenia "terraform plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Wdrażanie rozwiązania:

    ```bash
    terraform apply
    ```
    
    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie wdrożenia.

    ![Przykładowe wyniki polecenia "terraform Zastosuj"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testowanie piastą i sieci wirtualne będące szprychami

W tej sekcji pokazano, jak przetestować połączenie z symulowanego lokalnego środowiska do sieci wirtualnej serwera centralnego.

1. W witrynie Azure portal przejdź do **lokalnego-sieć wirtualna rg** grupy zasobów.

1. W **lokalnego-sieć wirtualna rg** , a następnie wybierz maszynę Wirtualną o nazwie **maszyny wirtualnej lokalnego**.

1. Wybierz przycisk **Połącz**.

1. Obok tekstu **Zaloguj się przy użyciu lokalnego konta maszyny Wirtualnej**, kopia **ssh** polecenia do Schowka.

1. W wierszu polecenia systemu Linux Uruchom `ssh` połączyć się z symulowanego lokalnego środowiska. Użyj hasło określone w `on-prem.tf` pliku parametrów.

1. Uruchom `ping` polecenie, aby przetestować łączność z maszyną Wirtualną serwera przesiadkowego w sieci wirtualnej serwera centralnego:

   ```bash
   ping 10.0.0.68
   ```

1. Uruchom `ping` polecenie, aby przetestować łączliwość do serwera przesiadkowego maszyn wirtualnych w każdej szprysze:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Aby zakończyć działanie ssh sesji na **maszyny wirtualnej lokalnego** maszyny wirtualnej, wprowadź `exit` i naciśnij klawisz &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Rozwiązywanie problemów z usługą VPN

Aby uzyskać informacje o rozwiązywaniu problemów z błędami sieci VPN, zobacz artykuł [Rozwiązywanie problemów z hybrydowego rozwiązania łączącego program połączenia sieci VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tej serii samouczka.

1. Usuń zasoby zadeklarowane w planie:

    ```bash
    terraform destroy
    ```

    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie usunięcia zasobów.

1. Zmień katalogi na katalog nadrzędny:

    ```bash
    cd ..
    ```

1. Usuń `hub-scope` katalogu (w tym wszystkie jego pliki):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o używaniu programu Terraform na platformie Azure](/azure/terraform)