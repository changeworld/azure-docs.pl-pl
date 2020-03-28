---
title: Samouczek — sprawdzanie poprawności sieci koncentratora i szprychy na platformie Azure przy użyciu terraform
description: Samouczek sprawdzania poprawności topologii sieci koncentratora i szprychy ze wszystkimi sieciami wirtualnymi połączonymi ze sobą.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159192"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Samouczek: Sprawdzanie poprawności sieci koncentratora i szprychy na platformie Azure przy użyciu terraform

W tym artykule należy wykonać pliki terraform utworzone w poprzednim artykule w tej serii. Rezultatem jest sprawdzanie poprawności łączności między demonstracyjnymi sieciami wirtualnymi.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użyj HCL (HashiCorp Language) do zaimplementowania sieci wirtualnej hub w topologii szprychy koncentratora
> * Użyj planu Terraform, aby zweryfikować zasoby, które mają zostać wdrożone
> * Tworzenie zasobów na platformie Azure za pomocą funkcji Terraform
> * Sprawdzanie łączności między różnymi sieciami
> * Użyj Terraform, aby zniszczyć wszystkie zasoby

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie koncentratora i szprychy hybrydowej topologii sieci z Terraform na platformie Azure](./terraform-hub-spoke-introduction.md).
1. [Tworzenie lokalnej sieci wirtualnej z terraform na platformie Azure](./terraform-hub-spoke-on-prem.md).
1. [Utwórz sieć wirtualną centrum z Terraform na platformie Azure](./terraform-hub-spoke-hub-network.md).
1. [Utwórz urządzenie sieci wirtualnej centrum z Terraform na platformie Azure](./terraform-hub-spoke-hub-nva.md).
1. [Tworzenie sieci wirtualnych szprych z Terraform na platformie Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Weryfikowanie konfiguracji

Po wypełnieniu [wymagań wstępnych](#prerequisites)sprawdź, czy są obecne odpowiednie pliki konfiguracyjne.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

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

1. Uruchom `ls` polecenie, aby `.tf` sprawdzić, czy pliki konfiguracyjne utworzone w poprzednich samouczkach są wymienione:

    ![Pliki konfiguracji demo Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Wdrażanie zasobów

1. Zainiólaj dostawcę Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Przykładowe wyniki polecenia "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Uruchom `terraform plan` polecenie, aby zobaczyć efekt wdrożenia przed wykonaniem:

    ```bash
    terraform plan
    ```
    
    ![Przykładowe wyniki polecenia "plan terraform"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Wdrażanie rozwiązania:

    ```bash
    terraform apply
    ```
    
    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie wdrożenia.

    ![Przykładowe wyniki polecenia "terraform apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testowanie sieci wirtualnej koncentratora i sieci wirtualnych z szprychami

W tej sekcji pokazano, jak przetestować łączność z symulowanego środowiska lokalnego do sieci wirtualnej koncentratora.

1. W witrynie Azure portal przejdź do grupy zasobów **onprem-vnet-rg.**

1. Na karcie **onprem-vnet-rg** wybierz maszynę wirtualną o nazwie **onprem-vm**.

1. Wybierz przycisk **Połącz**.

1. Obok tekstu **Logowanie przy użyciu konta lokalnego maszyny Wirtualnej,** skopiuj polecenie **ssh** do schowka.

1. Z monitu systemu `ssh` Linux uruchom, aby połączyć się z symulowanym środowiskiem lokalnym. Użyj hasła określonego `on-prem.tf` w pliku parametrów.

1. Uruchom `ping` polecenie, aby przetestować łączność z maszyną wirtualną jumpbox w sieci wirtualnej koncentratora:

   ```bash
   ping 10.0.0.68
   ```

1. Uruchom `ping` polecenie, aby przetestować łączność z maszynami wirtualnymi jumpbox w każdej szprychy:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Aby zakończyć sesję ssh na maszynie wirtualnej `exit` **onprem-vm,** wprowadź i naciśnij klawisz &lt;Enter>.

## <a name="troubleshoot-vpn-issues"></a>Rozwiązywanie problemów z siecią VPN

Aby uzyskać informacje dotyczące rozwiązywania błędów sieci VPN, zobacz artykuł [Rozwiązywanie problemów z hybrydowym połączeniem sieci VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w serii samouczków.

1. Usuń zasoby zadeklarowane w planie:

    ```bash
    terraform destroy
    ```

    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie usunięcia zasobów.

1. Zmień katalogi na katalog nadrzędny:

    ```bash
    cd ..
    ```

1. Usuń `hub-scope` katalog (łącznie ze wszystkimi jego plikami):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z programu Terraform na platformie Azure](/azure/terraform)