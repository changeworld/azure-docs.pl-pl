---
title: 'Samouczek: Tworzenie i testowanie bramy translatora adresów sieciowych — Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu Azure Portal i przetestować usługę translatora adresów sieciowych
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 4baf12533bed523c81ff41a81975f5bf5b918ac2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250821"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure Portal i testowanie usługi translatora adresów sieciowych

W tym samouczku utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć źródłową i docelową maszynę wirtualną. Przetestujesz bramę translatora adresów sieciowych, wykonując połączenia wychodzące z publicznego adresu IP z lokalizacji źródłowej do docelowej maszyny wirtualnej.  W tym samouczku przedstawiono lokalizację źródłową i docelową dwóch różnych sieci wirtualnych w tej samej grupie zasobów tylko dla uproszczenia.

>[!NOTE] 
>Usługa Azure Virtual Network translator adresów sieciowych jest teraz dostępna jako publiczna wersja zapoznawcza i dostępna w ograniczonym zestawie [regionów](./nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Azure portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotuj Źródło dla ruchu wychodzącego

Przeprowadzimy Cię przez proces konfigurowania pełnego środowiska testowego i wykonywania testów w następnych krokach. Zaczniemy od źródła, które będzie używać zasobu bramy translatora adresów sieciowych, który utworzysz w dalszych krokach.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

Przed wdrożeniem maszyny wirtualnej i użyciem bramy NAT należy utworzyć grupę zasobów i sieć wirtualną.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<nazwy grupy zasobów >**  | myResourceGroupNAT |
| **\<nazwę sieci wirtualnej >** | myVNetsource          |
| **\<nazwę regionu >**          | Wschodnie stany USA 2      |
| **\<adres IPv4 >**   | 192.168.0.0 \ 16          |
| **\<nazwę podsieci >**          | mySubnetsource        |
| **\<zakres adresów podsieci >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Utwórz źródłową maszynę wirtualną

Teraz utworzymy maszynę wirtualną do korzystania z usługi translatora adresów sieciowych. Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, który umożliwia dostęp do maszyny wirtualnej. Usługa translatora adresów sieciowych obsługuje kierunek przepływu i zastępuje domyślne miejsce docelowe w podsieci. Publiczny adres IP maszyny wirtualnej nie będzie używany dla połączeń wychodzących.

Aby przetestować bramę NAT, przypiszemy zasób publicznego adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko w celu uzyskania dostępu do niego dla testu.  Pokazujemy, jak usługa translatora adresów sieciowych ma pierwszeństwo przed innymi opcjami wychodzącymi.

Możesz również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć kolejną maszynę wirtualną do użycia jako serwera przesiadkowego bez publicznego adresu IP jako ćwiczenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **COMPUTE** > **Ubuntu Server 18,04 LTS**lub Wyszukaj **Ubuntu Server 18,04 LTS** w wyszukiwaniu portalu Marketplace.

2. W obszarze **Utwórz maszynę wirtualną**wprowadź lub wybierz następujące wartości z karty **podstawowe** :
   - **Grupa zasobów** > **subskrypcji** : wybierz pozycję **myResourceGroupNAT**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: wprowadź **myVMsource**.
   - **Szczegóły wystąpienia** > **regionu** > wybierz **Wschodnie stany USA 2**.
   - **Konto administratora** > **uwierzytelnianiem**: wybierz opcję **hasło**.
   - **Konto administratora** > wprowadź **nazwę użytkownika**, **hasło**i potwierdź informacje o **haśle** .
   - **Reguły portów przychodzących** > **publicznych portów przychodzących**: wybierz opcję **Zezwalaj na wybrane porty**.
   - **Reguły portów przychodzących** > **Wybieranie portów ruchu przychodzącego**: Wybierz **SSH (22)**
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że wybrano następujące elementy:
   - **Sieć wirtualna**: **myVnetsource**
   - **Podsieć**: **mySubnetsource**
   - > **Publicznego adresu IP** wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wprowadź **MyPublicIPsourceVM** w polu **Nazwa** . Wybierz pozycję **standardowa** dla **jednostki SKU**. Pozostaw pozostałe wartości domyślne i kliknij przycisk **OK**.
   - **Grupa zabezpieczeń sieci karty sieciowej**: wybierz pozycję **podstawowa**.
   - **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - **Wybierz porty wejściowe**: Upewnij się, że wybrano protokół **SSH** .

4. Na karcie **Zarządzanie** w obszarze **monitorowanie**ustaw opcję **Diagnostyka rozruchu** na **wyłączona**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

Można użyć co najmniej jednego publicznego zasobu adresów IP, publicznych prefiksów IP lub obu z bramą translatora adresów sieciowych. Dodamy zasób publicznego adresu IP, prefiks publicznego adresu IP i zasób bramy NAT.

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **sieci** > **publicznym adresie IP**lub Wyszukaj **publiczny adres IP** w wyszukiwaniu portalu Marketplace. 

2. W obszarze **Utwórz publiczny adres IP**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Wersja protokołu IP | Wybierz pozycję **IPv4**.
    | SKU | Wybierz opcję **Standardowa**.
    | Name (Nazwa) | Wprowadź **myPublicIPsource**. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **myResourceGroupNAT**. |
    | Lokalizacja | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).|

3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **sieci** > **prefiksu publicznego adresu IP**lub Wyszukaj **prefiks publicznego adresu IP** w wyszukiwaniu portalu Marketplace.

2. W obszarze **Utwórz publiczny prefiks adresu IP**wprowadź lub wybierz następujące wartości z karty **podstawowe** :
   - **Grupa zasobów** > **subskrypcji** : wybierz pozycję **myResourceGroupNAT**>
   - **Szczegóły wystąpienia** > **Nazwa**: wprowadź **myPublicIPprefixsource**.
   - **Szczegóły wystąpienia** > **regionu**: Wybierz **Wschodnie stany USA 2**.
   - **Szczegóły wystąpienia** > **rozmiar prefiksu**: Wybierz **/31 (2 adresy)**

3. Pozostaw ustawienia domyślne i wybierz pozycję **Przegląd + Utwórz**.

4. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.


### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **sieci** > **brama translatora adresów**sieciowych lub Wyszukaj **bramę translatora adresów sieciowych** w obszarze wyszukiwania w portalu Marketplace.

2. W obszarze **Utwórz bramę translacji adresów sieciowych (NAT)** wprowadź lub wybierz następujące wartości z karty **podstawowe** :
   - **Grupa zasobów** > **subskrypcji** : wybierz pozycję **myResourceGroupNAT**.
   - **Szczegóły wystąpienia** > **Nazwa bramy translatora adresów sieciowych**: wprowadź **myNATgateway**.
   - **Szczegóły wystąpienia** > **regionu**: Wybierz **Wschodnie stany USA 2**.
   - **Szczegóły wystąpienia** > **limit czasu bezczynności (w minutach)** : wprowadź wartość **10**.
   - Wybierz kartę **publiczny adres IP** lub wybierz pozycję **Dalej: publiczny adres IP**.

3. Na karcie **publiczny adres IP** wprowadź lub wybierz następujące wartości:
   - **Publiczne adresy IP**: wybierz pozycję **myPublicIPsource**.
   - **Prefiksy publicznych adresów IP**: wybierz pozycję **myPublicIPprefixsource**.
   - Wybierz kartę **podsieć** lub wybierz pozycję **Dalej: podsieć**.

4. Na karcie **podsieć** wprowadź lub wybierz następujące wartości:
   - **Virtual Network**: wybierz pozycję **myResourceGroupNAT** > **myVnetsource**.
   - **Nazwa podsieci**: zaznacz pole wyboru obok pozycji **mySubnetsource**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

Cały ruch wychodzący do miejsc docelowych w Internecie używa teraz usługi translatora adresów sieciowych.  Nie trzeba konfigurować UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Przygotuj miejsce docelowe dla ruchu wychodzącego

Teraz utworzysz miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.


## <a name="virtual-network-and-parameters-for-destination"></a>Sieć wirtualna i parametry dla miejsca docelowego

Przed wdrożeniem maszyny wirtualnej dla miejsca docelowego należy utworzyć sieć wirtualną, w której może znajdować się docelowa maszyna wirtualna. Poniższe kroki są takie same jak dla źródłowej maszyny wirtualnej z niewielkimi zmianami, aby udostępnić docelowy punkt końcowy.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<nazwy grupy zasobów >**  | myResourceGroupNAT |
| **\<nazwę sieci wirtualnej >** | myVNetdestination          |
| **\<nazwę regionu >**          | Wschodnie stany USA 2      |
| **\<adres IPv4 >**   | 192.168.0.0 \ 16          |
| **\<nazwę podsieci >**          | mySubnetdestination        |
| **\<zakres adresów podsieci >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Utwórz docelową maszynę wirtualną

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **COMPUTE** > **Ubuntu Server 18,04 LTS**lub Wyszukaj **Ubuntu Server 18,04 LTS** w wyszukiwaniu portalu Marketplace.

2. W obszarze **Utwórz maszynę wirtualną**wprowadź lub wybierz następujące wartości z karty **podstawowe** :
   - **Grupa zasobów** > **subskrypcji** : wybierz pozycję **myResourceGroupNAT**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: wprowadź **myVMdestination**.
   - **Szczegóły wystąpienia** > **regionu** > wybierz **Wschodnie stany USA 2**.
   - **Konto administratora** > **uwierzytelnianiem**: wybierz opcję **hasło**.
   - **Konto administratora** > wprowadź **nazwę użytkownika**, **hasło**i potwierdź informacje o **haśle** .
   - **Reguły portów przychodzących** > **publicznych portów przychodzących**: wybierz opcję **Zezwalaj na wybrane porty**.
   - **Reguły portów przychodzących** > **Wybieranie portów ruchu przychodzącego**: Wybierz **SSH (22)** i **http (80)** .
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że wybrano następujące elementy:
   - **Sieć wirtualna**: **myVnetdestination**
   - **Podsieć**: **mySubnetdestination**
   - > **Publicznego adresu IP** wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wprowadź **MyPublicIPdestinationVM** w polu **Nazwa** . Wybierz pozycję **standardowa** dla **jednostki SKU**. Pozostaw pozostałe wartości domyślne i kliknij przycisk **OK**.
   - **Grupa zabezpieczeń sieci karty sieciowej**: wybierz pozycję **podstawowa**.
   - **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - **Wybierz porty wejściowe**: Upewnij się, że wybrano opcję **SSH** i **http** .

4. Na karcie **Zarządzanie** w obszarze **monitorowanie**ustaw opcję **Diagnostyka rozruchu** na **wyłączona**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowywanie serwera sieci Web i ładunku testowego na docelowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP docelowej maszyny wirtualnej. 

1. Po lewej stronie portalu wybierz pozycję **grupy zasobów**.
2. Wybierz pozycję **myResourceGroupNAT**.
3. Wybierz pozycję **myVMdestination**.
4. W obszarze **Przegląd**skopiuj wartość **publiczny adres IP** i wklej ją do programu Notepad, aby można było korzystać z niej w celu uzyskania dostępu do maszyny wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Zaloguj się na docelowej maszynie wirtualnej

Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Skopiuj i wklej następujące polecenia po zalogowaniu się.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Te polecenia zaktualizują maszynę wirtualną, instalują Nginx i tworzą plik 100-kilobajtów. Ten plik zostanie pobrany ze źródłowej maszyny wirtualnej przy użyciu usługi translatora adresów sieciowych.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotuj test na źródłowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP źródłowej maszyny wirtualnej.

1. Po lewej stronie portalu wybierz pozycję **grupy zasobów**.
2. Wybierz pozycję **myResourceGroupNAT**.
3. Wybierz pozycję **myVMsource**.
4. W obszarze **Przegląd**skopiuj wartość **publiczny adres IP** i wklej ją do programu Notepad, aby można było korzystać z niej w celu uzyskania dostępu do maszyny wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to źródłowa maszyna wirtualna.

### <a name="log-into-source-vm"></a>Zaloguj się do źródłowej maszyny wirtualnej

Otwórz nową kartę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi translatora adresów sieciowych.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

To polecenie zaktualizuje maszynę wirtualną [, zainstaluje](https://github.com/rakyll/hey) go, zainstaluje z usługi GitHub i zaktualizuje środowisko powłoki.

Teraz można przystąpić do testowania usługi translatora adresów sieciowych.

## <a name="validate-nat-service"></a>Weryfikowanie usługi translatora adresów sieciowych

Po zalogowaniu się do źródłowej maszyny wirtualnej można użyć jej **zwinięcie** i **Hej** do generowania żądań na docelowy adres IP.

Użyj zwinięciea, aby pobrać plik 100-kilobajtów.  W poniższym przykładzie Zastąp **\<IP-Address-destination >** z docelowym wcześniej skopiowanym adresem IP.  Parametr **--Output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Możesz również generować serię żądań za pomocą polecenia **Hej**. Ponownie Zastąp **\<IP-Address-destination >** adresem IP, który został wcześniej skopiowany.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie spowoduje wygenerowanie 100 żądań, 10 współbieżnie, z limitem czasu 30 sekund i bez ponownego użycia połączenia TCP.  Każde żądanie spowoduje pobranie 100 kilobajtów.  Na końcu przebiegu **zostanie** zaraportowana Statystyka dotycząca tego, jak dobrze była usługa translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, Brama translatora adresów sieciowych i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupNAT** , która zawiera bramę translatora adresów sieciowych, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę translatora adresów sieciowych, utworzono źródłową i docelową maszynę wirtualną, a następnie przetestowano bramę translatora adresów sieciowych.

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów protokołu IPSec jest łatwo rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych elementów.

- Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).
- [Prześlij opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

