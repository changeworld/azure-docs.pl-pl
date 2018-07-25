---
title: Wdrażanie i konfigurowanie usługi Azure Firewall przy użyciu witryny Azure Portal
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować usługę Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7a127f53985f39913c83958be1f1730c64b33a76
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001955"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal

Usługa Azure Firewall ma dwa typy reguł do kontrolowania wychodzącego dostępu:

- **Reguły aplikacji**

   Umożliwiają skonfigurowanie w pełni kwalifikowanych nazw domen (FQDN), do których można uzyskać dostęp z podsieci. Na przykład można zezwolić na dostęp do domeny *github.com* z podsieci.
- **Reguły sieci**

   Umożliwiają skonfigurowanie reguł zawierających adres źródłowy, protokół, port docelowy i adres docelowy. Na przykład można utworzyć regułę zezwalającą na ruch na porcie 53 (DNS) do adresu IP serwera DNS z podsieci.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

Reguły aplikacji i sieci są przechowywane w *kolekcjach reguł*. Kolekcja reguł to lista reguł, które mają tę samą akcję i priorytet.  Kolekcja reguł sieci to lista reguł sieci, a kolekcja reguł aplikacji to lista reguł aplikacji.

Kolekcje reguł sieci są zawsze przetwarzane przed kolekcjami reguł aplikacji. Wszystkie reguły powodują zakończenie, a więc jeśli dopasowanie zostanie znalezione w kolekcji reguł sieci, następujące kolekcje reguł aplikacji dla sekcji nie będą przetwarzane.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguł aplikacji
> * Konfigurowanie reguł sieci
> * Testowanie zapory



Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

W przykładach podanych w artykułach na temat usługi Azure Firewall założono, że publiczna wersja zapoznawcza usługi Azure Firewall została już włączona. Aby uzyskać więcej informacji, zobacz [Włączanie publicznej wersji zapoznawczej usługi Azure Firewall](public-preview.md).

W tym samouczku utworzysz pojedynczą sieć wirtualną z trzema podsieciami:
- **FW-SN** — w tej podsieci znajduje się zapora.
- **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
- **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

W tym samouczku jest używana uproszczona konfiguracja sieci w celu łatwego wdrażania. W przypadku wdrożeń produkcyjnych zalecany jest [model typu gwiazda](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), w którym zapora znajduje się we własnej sieci wirtualnej, a serwery obciążeń znajdują się w wirtualnych sieciach równorzędnych w tym samym regionie z co najmniej jedną podsiecią.



## <a name="set-up-the-network-environment"></a>Konfigurowanie środowiska sieciowego
Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
1. Na stronie głównej witryny Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **Dodaj**.
2. W polu **Nazwa grupy zasobów** wpisz **Test-FW-RG**.
3. W polu **Subskrypcja** wybierz subskrypcję.
4. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie kolejne zasoby, które utworzysz, muszą znajdować się w tej samej lokalizacji.
5. Kliknij przycisk **Utwórz**.


### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej
1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Sieci wirtualne**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **Test-FW-VN**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
7. W polu **Subskrypcja** wybierz subskrypcję.
8. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **Test-FW-RG**.
9. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
10. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**.

    Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
11. W polu **Zakres adresów** wpisz wartość **10.0.1.0/24**.
12. Użyj innych domyślnych ustawień, a następnie kliknij przycisk **Utwórz**.

### <a name="create-additional-subnets"></a>Tworzenie dodatkowych podsieci

Następnie należy utworzyć podsieci dla serwera przesiadkowego oraz podsieci dla serwerów obciążeń.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **Test-FW-RG**.
2. Kliknij sieć wirtualną **Test-FW-VN**.
3. Kliknij pozycję **Podsieci**, a następnie kliknij pozycję **+Podsieć**.
4. W polu **Nazwa** wpisz wartość **Workload-SN**.
5. W polu **Zakres adresów** wpisz wartość **10.0.2.0/24**.
6. Kliknij przycisk **OK**.

Utwórz kolejną podsieć z nazwą **Jump-SN** i zakresem adresów **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Obliczanie** kliknij pozycję **Maszyny wirtualne**.
3. Kliknij przycisk **Dodaj** i kliknij pozycję **system Windows Server**, kliknij pozycję **Windows Server 2016 Datacenter**, a następnie kliknij pozycję **Utwórz**.

**Podstawy**

1. W obszarze **Nazwa** wpisz wartość **Srv-Jump**.
5. Wpisz nazwę użytkownika i hasło.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W obszarze **Grupa zasobów** kliknij opcję **Użyj istniejącej**, a następnie wybierz pozycję **Test-FW-RG**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. Kliknij przycisk **OK**.

**Rozmiar**

1. Wybierz odpowiedni rozmiar dla testowej maszyny wirtualnej z systemem Windows Server. Na przykład **B2ms** (8 GB pamięci RAM, 16 GB magazynu).
2. Kliknij pozycję **Wybierz**.

**Ustawienia**

1. W obszarze **Sieć** w polu **Sieć wirtualna** wybierz pozycję **Test-FW-VN**.
2. W polu **Podsieć** wybierz pozycję **Jump-SN**.
3. W obszarze **Wybierz publiczne porty wejściowe** wybierz pozycję **RDP (3389)**. 

    Będziesz chcieć ograniczyć dostęp do swojego publicznego adresu IP, ale musisz otworzyć port 3389, aby móc nawiązać połączenie pulpitu zdalnego z serwerem przesiadkowym. 
2. Pozostaw pozostałe ustawienia domyślne i kliknij przycisk **OK**.

**Podsumowanie**

Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz**. Ukończenie tej operacji potrwa kilka minut.

Powtórz ten proces, aby utworzyć inną maszynę wirtualną o nazwie **Srv-Work**.

Skorzystaj z informacji w poniższej tabeli, aby skonfigurować **ustawienia** dla maszyny wirtualnej Srv-Work. Pozostała część konfiguracji jest taka sama jak w przypadku maszyny wirtualnej Srv-Jump.


|Ustawienie  |Wartość  |
|---------|---------|
|Podsieć|Workload-SN|
|Publiczny adres IP|Brak|
|Wybieranie publicznych portów wejściowych|Brak publicznych portów wejściowych|


## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. Na stronie głównej portalu kliknij pozycję **Utwórz zasób**.
2. Kliknij pozycję **Sieć**, a po liście **Polecane** kliknij pozycję **Zobacz wszystko**.
3. Kliknij pozycję **Zapora**, a następnie kliknij pozycję **Utwórz**. 
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Name (Nazwa)     |Test-FW01|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Użyj istniejącej**: Test-FW-RG |
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: Test-FW-VN|
   |Publiczny adres IP     |Tworzenie nowego elementu|

2. Kliknij pozycję **Przegląd + utwórz**.
3. Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz**, aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
4. Po zakończeniu wdrażania przejdź do grupy zasobów **Test-FW-RG**, a następnie kliknij zaporę **Test-FW01**.
6. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

[//]: # (Pamiętaj, aby zanotować prywatny adres IP zapory.)

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **Workload-SN** skonfiguruj trasę domyślną ruchu wychodzącego, aby przejść przez zaporę.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Tabele tras**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **Firewall-route**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **Test-FW-RG**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Kliknij przycisk **Utwórz**.
9. Kliknij przycisk **Odśwież**, a następnie kliknij tabelę tras **Firewall-route**.
10. Kliknij pozycję **Podsieci**, a następnie kliknij pozycję **Skojarz**.
11. Kliknij pozycję **Sieć wirtualna**, a następnie wybierz pozycję **Test-FW-VN**.
12. W polu **Podsieć** kliknij pozycję **Workload-SN**.
13. Kliknij przycisk **OK**.
14. Kliknij pozycję **Trasy**, a następnie kliknij pozycję **Dodaj**.
15. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
1. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
2. Kliknij przycisk **OK**.


## <a name="configure-application-rules"></a>Konfigurowanie reguł aplikacji


1. Otwórz pozycję **Test-FW-RG** i kliknij zaporę **Test-FW01**.
1. Na stronie **Test-FW01** w obszarze **Ustawienia** kliknij pozycję **Reguły**.
2. Kliknij pozycję **Dodaj kolekcję reguł aplikacji**.
3. W polu **Nazwa** wpisz wartość **App-Coll01**.
1. W polu **Priorytet** wpisz wartość **200**.
2. W polu **Akcja** wybierz opcję **Zezwalaj**.

6. W obszarze **Reguły** w polu **Nazwa** wpisz wartość **AllowGH**.
7. W polu **Adresy źródłowe** wpisz wartość **10.0.2.0/24**.
8. W polu **Protocol:port** wpisz wartość **http, https**. 
9. W polu **Docelowa nazwa FQDN** wpisz wartość **github.com**
10. Kliknij pozycję **Add** (Dodaj).

> [!NOTE]
> Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Dozwolone nazwy FQDN infrastruktury obejmują:
>- Obliczenia dostępu do repozytorium obrazów platformy (PIR) magazynu.
>- Dostęp magazynu stanu dysków zarządzanych.
>- Diagnostyka systemu Windows
>
> Możesz zastąpić tę wbudowaną kolekcję reguł infrastruktury przez utworzenie kolekcji reguł aplikacji *odmawiaj wszystkim*, która jest przetwarzana jako ostatnie. Zawsze będzie ona przetwarzana przed kolekcją reguł infrastruktury. Wszystko, co nie znajduje się w kolekcji reguł infrastruktury, zostanie odrzucone domyślnie.

## <a name="configure-network-rules"></a>Konfigurowanie reguł sieci

1. Kliknij pozycję **Dodaj kolekcję reguł sieci**.
2. W polu **Nazwa** wpisz wartość **Net-Coll01**.
3. W polu **Priorytet** wpisz wartość **200**.
4. W polu **Akcja** wybierz opcję **Zezwalaj**.

6. W obszarze **Reguły** w polu **Nazwa** wpisz wartość **AllowDNS**.
8. W polu **Protokół** wybierz **TCP**.
9. W polu **Adresy źródłowe** wpisz wartość **10.0.2.0/24**.
10. W polu Adres docelowy wpisz wartość **209.244.0.3, 209.244.0.4**.
11. W polu **Porty docelowe** wpisz wartość **53**.
12. Kliknij pozycję **Add** (Dodaj).

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Zmienianie podstawowego i pomocniczego adresu DNS dla interfejsu sieciowego **Srv-Work**

Do celów testowych w tym samouczku skonfigurujesz podstawowe i pomocnicze adresy DNS. Nie jest to ogólne wymaganie usługi Azure Firewall. 

1. W witrynie Azure Portal otwórz grupę zasobów **Test-FW-RG**.
2. Kliknij interfejs sieciowy dla maszyny wirtualnej **Srv-Work**.
3. W obszarze **Ustawienia** kliknij pozycję **Serwery DNS**.
4. W obszarze **Serwery DNS** kliknij pozycję **Niestandardowe**.
5. Wpisz wartość **209.244.0.3** w polu tekstowym **Dodaj serwer DNS**, a następnie wpisz wartość **209.244.0.4** w następnym polu tekstowym.
6. Kliknij pozycję **Zapisz**. 
7. Uruchom ponownie maszynę wirtualną **Srv-Work**.


## <a name="test-the-firewall"></a>Testowanie zapory

1. W witrynie Azure Portal sprawdź ustawienia sieci dla maszyny wirtualnej **Srv-Work** i zanotuj prywatny adres IP.
2. Połącz pulpit zdalny z maszyną wirtualną **Srv-Jump**, a następnie z tego miejsca otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **Srv-Work**.

5. Otwórz program Internet Explorer i przejdź do http://github.com.
6. Kliknij przycisk **OK** i **Zamknij** dla alertów zabezpieczeń.

   Powinna zostać wyświetlona strona główna usługi GitHub.

7. Przejdź do http://www.msn.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz upewnij się, czy reguły zapory działają:

- Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.
- Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów **Test-FW-RG** nie jest już potrzebna, usuń ją, aby usunąć wszystkie zasoby związane z zaporą.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguł zapory aplikacji i sieci
> * Testowanie zapory

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
