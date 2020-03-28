---
title: 'Szybki start: tworzenie bramy NAT — witryna Azure portal'
titlesuffix: Azure Virtual Network NAT
description: Ten przewodnik Szybki start pokazuje, jak utworzyć bramę NAT za pomocą portalu Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066386"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Szybki start: tworzenie bramy NAT za pomocą portalu Azure

Ten przewodnik Szybki start pokazuje, jak korzystać z usługi Azure Virtual Network NAT. Utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

Jeśli wolisz, możesz wykonać te kroki przy użyciu [interfejsu wiersza polecenia platformy Azure](quickstart-create-nat-gateway-cli.md), Azure [PowerShell](quickstart-create-nat-gateway-powershell.md)lub wdrożyć [szablon ARM](quickstart-create-nat-gateway-powershell.md) zamiast portalu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

Przed wdrożeniem maszyny Wirtualnej i można użyć bramy NAT, musimy utworzyć grupę zasobów i sieci wirtualnej.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupNAT |
| **\<>nazwa sieci wirtualnej** | myVNet ( myVNet )          |
| **\<nazwa regionu>**          | Wschodnie stany USA 2      |
| **\<>przestrzeni adresowej IPv4**   | 192.168.0.0\16          |
| **\<>nazwa podsieci**          | mySubnet        |
| **\<>zakresu adresu podsieci** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Tworzenie maszyny Wirtualnej w celu korzystania z bramy NAT

Teraz utworzymy maszynę wirtualną do korzystania z usługi NAT. Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, aby umożliwić dostęp do maszyny Wirtualnej. Usługa NAT jest świadoma kierunku przepływu i zastąpi domyślne miejsce docelowe Internetu w podsieci. Publiczny adres IP maszyny Wirtualnej nie będzie używany dla połączeń wychodzących.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Compute** > **Ubuntu Server 18.04 LTS**lub wyszukaj w wyszukiwarce Marketplace serwer **Ubuntu Server 18.04 LTS.**

2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** Wpisz **myVM**.
   - **Region szczegółów** > **Region** wystąpienia > wybierz **pozycję Wschodnia us 2**.
   - **Typ** > uwierzytelniania konta**administratora**: Wybierz **hasło**.
   - **Konto administratora** > Wprowadź **nazwę użytkownika,** **hasło**i pozycję Potwierdź informacje o **haśle.**
   - **Reguły portów** > **przychodzących Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Reguły portów** > **przychodzących Wybierz porty przychodzące:** Wybierz **SSH (22)**
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że zaznaczono następujące elementy:
   - **Sieć wirtualna**: **myVnet**
   - **Podsieć**: **mySubnet**
   - **Publiczne** > IP Wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wpisz **myPublicIPVM** w polu **Nazwa** i wybierz pozycję **Standardowy** dla **jednostki SKU**.  Kliknij przycisk **OK**.
   - **Grupa zabezpieczeń sieciowej karty sieciowej**: Wybierz pozycję **Podstawowa**.
   - **Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Wybierz porty przychodzące**: Upewnij się, że wybrano **SSH.**

4. Na karcie **Zarządzanie** w obszarze **Monitorowanie**ustaw **diagnostykę rozruchu** na **Wyłącz**.

5. Wybierz pozycję **Przegląd + utwórz**. 

6. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.

## <a name="create-the-nat-gateway"></a>Tworzenie bramy NAT

Można użyć co najmniej jednego zasobu publicznego adresu IP, publicznych prefiksów IP lub obu tych ekscesów. Dodamy publiczny zasób IP, publiczny prefiks IP i zasób bramy NAT.

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz** > **publiczny adres IP****sieciowy** > lub wyszukaj **publiczny adres IP** w wyszukiwarce Marketplace.

2. W **obszarze Tworzenie publicznego adresu IP**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Wersja IP | Wybierz pozycję **IPv4**.
    | SKU | Wybierz **opcję Standardowy**.
    | Nazwa | Wpisz **myPublicIP**. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz **myResourceGroupNAT**. |
    | Lokalizacja | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).|

3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz** > **prefiks publiczny adres IP****sieciowej** > lub wyszukaj **prefiks publiczny adres IP** w wyszukiwarce Marketplace. 

2. W **obszarze Tworzenie publicznego prefiksu IP**wpisz lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**>
   - **Nazwa szczegółów** > **Name**wystąpienia: Wpisz **myPublicIPprefix**.
   - **Szczegóły** > wystąpienia**Region:** Wybierz **wschodnie stany USA 2**.
   - **Szczegóły wystąpienia** > **Rozmiar prefiksu**: Wybierz **/31 (2 adresy)**

3. Pozostaw resztę wartości domyślnych i wybierz **pozycję Przejrzyj + utwórz**.

4. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
   

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz bramę** > **nat****sieciową** > lub wyszukaj **bramę NAT** w wyszukiwarce Marketplace.

2. W **obszarze Tworzenie bramy translacji adresów sieciowych (NAT)** wpisz lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**.
   - **Szczegóły** > wystąpienia**NAT nazwa bramy:** Wpisz **myNATgateway**.
   - **Szczegóły** > wystąpienia**Region:** Wybierz **wschodnie stany USA 2**.
   - **Szczegóły** > wystąpienia**Limit czasu bezczynności (minuty)**: Typ **10**.
   - Wybierz kartę **Publiczny adres IP** lub wybierz pozycję **Dalej: Publiczny adres IP**.

3. Na karcie **Publiczny adres IP** wpisz lub wybierz następujące wartości:
   - **Publiczne adresy IP**: Wybierz **myPublicIP**.
   - **Publiczne prefiksy IP**: Wybierz **myPublicIPprefix**.
   - Wybierz kartę **Podsieć** lub wybierz pozycję **Dalej: Podsieć**.

4. Na karcie **Podsieć** wpisz lub wybierz następujące wartości:
   - **Sieć wirtualna**: Wybierz **myResourceGroupNAT** > **myVnet**.
   - **Nazwa podsieci**: Zaznacz pole obok **mySubnet**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajduj adres IP maszyny Wirtualnej

1. Po lewej stronie portalu wybierz pozycję **Grupy zasobów**.
2. Wybierz **myResourceGroupNAT**.
3. Wybierz **myVM**.
4. W **przeglądzie**skopiuj wartość **publicznego adresu IP** i wklej do notatnika, aby można było jej użyć do uzyskania dostępu do maszyny Wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć do uzyskania dostępu do maszyny Wirtualnej.

## <a name="sign-in-to-vm"></a>Logowanie się do maszyny Wirtualnej

Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Teraz możesz przystąpić do korzystania z usługi NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów, bramę NAT i wszystkie powiązane zasoby. Wybierz grupę zasobów **myResourceGroupNAT** zawierającą bramę NAT, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę NAT i maszynę wirtualną, aby z niej korzystać. 

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT jest rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych zasobów.


- Dowiedz się więcej o [translatoru adresów sieci wirtualnej platformy Azure](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

