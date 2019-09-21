---
title: Weryfikowanie sieci gwiazdy i satelity za pomocą Terraform na platformie Azure
description: Samouczek dotyczący weryfikowania topologii sieci Hub i szprychy ze wszystkimi sieciami wirtualnymi połączonymi ze sobą.
services: terraform
ms.service: azure
keywords: Terraform, Hub i szprych, sieci, sieci hybrydowe, DevOps, maszyna wirtualna, Azure, Komunikacja równorzędna VNET,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e35af0fcf4a8f1f8f0446be44fe5b0bb6eeec693
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169715"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Samouczek: Weryfikowanie sieci gwiazdy i satelity za pomocą Terraform na platformie Azure

W tym artykule opisano wykonywanie plików Terraform utworzonych w poprzednim artykule w tej serii. Wynikiem jest weryfikacja łączności między demonstracją sieciami wirtualnymi.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Używanie HCL (HashiCorp Language) do implementowania sieci wirtualnej centrum w topologii gwiazdy
> * Aby sprawdzić zasoby, które mają zostać wdrożone, należy użyć planu Terraform
> * Użyj Terraform do tworzenia zasobów na platformie Azure
> * Weryfikowanie łączności między różnymi sieciami
> * Użyj Terraform, aby zniszczyć wszystkie zasoby

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie topologii sieci hybrydowej Hub i satelity z Terraform na platformie Azure](./terraform-hub-spoke-introduction.md).
1. [Utwórz lokalną sieć wirtualną za pomocą Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md).
1. [Utwórz centralną sieć wirtualną z usługą Terraform na platformie Azure](./terraform-hub-spoke-hub-network.md).
1. [Utwórz centralne urządzenie sieci wirtualnej z usługą Terraform na platformie Azure](./terraform-hub-spoke-hub-nva.md).
1. [Utwórz sieci wirtualne szprych z Terraform na platformie Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Weryfikowanie konfiguracji

Po spełnieniu [wymagań wstępnych](#prerequisites)upewnij się, że istnieją odpowiednie pliki konfiguracyjne.

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

1. Uruchom polecenie, aby upewnić się `.tf` , że pliki konfiguracji utworzone w poprzednich samouczkach są wymienione poniżej: `ls`

    ![Pliki konfiguracji demonstracyjnej Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Wdrażanie zasobów

1. Zainicjuj dostawcę Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Przykładowe wyniki polecenia "Terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. `terraform plan` Uruchom polecenie, aby zobaczyć efekt wdrożenia przed wykonaniem:

    ```bash
    terraform plan
    ```
    
    ![Przykładowe wyniki polecenia "Terraform plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Wdróż rozwiązanie:

    ```bash
    terraform apply
    ```
    
    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie wdrożenia.

    ![Przykładowe wyniki polecenia "Terraform Apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testowanie sieci wirtualnej Hub i satelity sieci wirtualnych

W tej sekcji przedstawiono sposób testowania łączności z symulowanego środowiska lokalnego do sieci wirtualnej centrum.

1. W Azure Portal przejdź do grupy zasobów **lokalnego-VNET-RG** .

1. Na karcie **lokalnego-VNET-RG** wybierz maszynę wirtualną o nazwie **lokalnego-VM**.

1. Wybierz przycisk **Połącz**.

1. Obok tekstu **logowania przy użyciu lokalnego konta maszyny wirtualnej**Skopiuj polecenie **SSH** do Schowka.

1. W wierszu polecenia systemu Linux Uruchom `ssh` połączyć się z symulowanego lokalnego środowiska. Użyj hasła określonego w `on-prem.tf` pliku parametrów.

1. Uruchom polecenie `ping` , aby przetestować łączność z maszyną wirtualną serwera przesiadkowego w sieci wirtualnej centrum:

   ```bash
   ping 10.0.0.68
   ```

1. Uruchom polecenie `ping` , aby przetestować łączność z maszynami wirtualnymi serwera przesiadkowego w każdym szprychie:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Aby zakończyć sesję SSH na maszynie wirtualnej **lokalnego-VM** , wprowadź `exit` i naciśnij klawisz &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Rozwiązywanie problemów z siecią VPN

Informacje o rozwiązywaniu błędów sieci VPN można znaleźć w artykule [Rozwiązywanie problemów z hybrydowym połączeniem sieci VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w serii samouczków.

1. Usuń zasoby zadeklarowane w planie:

    ```bash
    terraform destroy
    ```

    Wprowadź `yes` po wyświetleniu monitu o potwierdzenie usunięcia zasobów.

1. Zmień katalogi na katalog nadrzędny:

    ```bash
    cd ..
    ```

1. `hub-scope` Usuń katalog (z uwzględnieniem wszystkich jego plików):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z Terraform na platformie Azure](/azure/terraform)