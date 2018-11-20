---
title: 'Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal'
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować usługę Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8a3a9e4019be0b6039fe43df11a5f6093545f9cd
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685364"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Może na przykład zajść konieczność ograniczenia dostępu do witryn internetowych lub do wychodzących adresów IP i udostępnionych portów.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym samouczku utworzysz uproszczoną pojedynczą sieć wirtualną z trzema podsieciami w celu łatwego wdrażania. W przypadku wdrożeń produkcyjnych zalecany jest [model typu gwiazda](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), w którym zapora znajduje się we własnej sieci wirtualnej, a serwery obciążeń znajdują się w wirtualnych sieciach równorzędnych w tym samym regionie z co najmniej jedną podsiecią.

- **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
- **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
- **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie aplikacji w celu umożliwienia dostępu do witryny github.com
> * Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
> * Testowanie zapory

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby wymagane w tym samouczku.

1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
2. Na stronie głównej witryny Azure Portal kliknij pozycję **Grupy zasobów** > **Dodaj**.
3. W polu **Nazwa grupy zasobów** wpisz **Test-FW-RG**.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie kolejne zasoby, które utworzysz, muszą znajdować się w tej samej lokalizacji.
6. Kliknij pozycję **Utwórz**.

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna będzie zawierać trzy podsieci.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Sieci wirtualne**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **Test-FW-VN**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej** > **Test-FW-RG**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**. Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
10. W polu **Zakres adresów** wpisz wartość **10.0.1.0/24**.
11. Użyj innych domyślnych ustawień, a następnie kliknij przycisk **Utwórz**.

> [!NOTE]
> Minimalny rozmiar podsieci AzureFirewallSubnet to /25.

### <a name="create-additional-subnets"></a>Tworzenie dodatkowych podsieci

Następnie należy utworzyć podsieci dla serwera przesiadkowego oraz podsieci dla serwerów obciążeń.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Grupy zasobów** > **Test-FW-RG**.
2. Kliknij sieć wirtualną **Test-FW-VN**.
3. Kliknij pozycję **Podsieci** > **+Podsieć**.
4. W polu **Nazwa** wpisz wartość **Workload-SN**.
5. W polu **Zakres adresów** wpisz wartość **10.0.2.0/24**.
6. Kliknij przycisk **OK**.

Utwórz kolejną podsieć z nazwą **Jump-SN** i zakresem adresów **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**.
2. Kliknij pozycję **Compute** (Wystąpienia obliczeniowe), a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - *Test-FW-RG* — jako grupę zasobów.
    - *Srv-Jump* — jako nazwę maszyny wirtualnej.
    - *azureuser* — jako nazwę użytkownika administratora.
    - *Azure123456!* jako hasło.

4. W obszarze **Reguły portów wejściowych** dla ustawienia **Publiczne porty wejściowe** kliknij opcję **Zezwalaj na wybrane porty**.
5. W obszarze **Wybierz porty wejściowe** wybierz pozycję **RDP (3389)**.

6. Zaakceptuj pozostałe wartości domyślne, a następnie kliknij pozycję **Dalej: Dyski**.
7. Zaakceptuj ustawienia domyślne dysku, a następnie kliknij pozycję **Dalej: Sieć**.
8. Upewnij się, że wybrano sieć wirtualną **Test-FW-VN** i podsieć **Jump-SN**.
9. W obszarze **Publiczny adres IP** kliknij pozycję **Utwórz nowy**.
10. Wpisz **Srv-Jump-PIP** jako nazwę publicznego adresu IP i kliknij przycisk **OK**.
11. Zaakceptuj pozostałe wartości domyślne, a następnie kliknij pozycję **Dalej: Zarządzanie**.
12. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu. Zaakceptuj pozostałe wartości domyślne, a następnie kliknij pozycję **Przegląd + utwórz**.
13. Przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.

Powtórz ten proces, aby utworzyć inną maszynę wirtualną o nazwie **Srv-Work**.

Skorzystaj z informacji w poniższej tabeli, aby skonfigurować maszynę wirtualną Srv-Work. Pozostała część konfiguracji jest taka sama jak w przypadku maszyny wirtualnej Srv-Jump.

|Ustawienie  |Wartość  |
|---------|---------|
|Podsieć|Workload-SN|
|Publiczny adres IP|Brak|
|Publiczne porty wejściowe|Brak|

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Wdróż zaporę w sieci wirtualnej.

1. Na stronie głównej portalu kliknij pozycję **Utwórz zasób**.
2. Kliknij pozycję **Sieć**, a po liście **Polecane** kliknij pozycję **Zobacz wszystko**.
3. Kliknij pozycję **Zapora** > **Utwórz**. 
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Name (Nazwa)     |Test-FW01|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Użyj istniejącej**: Test-FW-RG |
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: Test-FW-VN|
   |Publiczny adres IP     |**Utwórz nową**. Publiczny adres IP musi mieć typ Standardowa jednostka SKU.|

2. Kliknij pozycję **Przegląd + utwórz**.
3. Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz**, aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
4. Po zakończeniu wdrażania przejdź do grupy zasobów **Test-FW-RG**, a następnie kliknij zaporę **Test-FW01**.
6. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **Workload-SN** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Tabele tras**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **Firewall-route**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **Test-FW-RG**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Kliknij pozycję **Utwórz**.
9. Kliknij przycisk **Odśwież**, a następnie kliknij tabelę tras **Firewall-route**.
10. Kliknij pozycję **Podsieci** > **Skojarz**.
11. Kliknij pozycję **Sieć wirtualna** > **Test-FW-VN**.
12. W polu **Podsieć** kliknij pozycję **Workload-SN**. Upewnij się, że wybrano tylko podsieć **Workload-SN** dla tej trasy. W przeciwnym razie zapora nie będzie działać poprawnie.

13. Kliknij przycisk **OK**.
14. Kliknij pozycję **Trasy** > **Dodaj**.
15. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Kliknij przycisk **OK**.

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Jest to reguła aplikacji, która umożliwia ruchowi wychodzącemu dostęp do witryny github.com.

1. Otwórz pozycję **Test-FW-RG** i kliknij zaporę **Test-FW01**.
2. Na stronie **Test-FW01** w obszarze **Ustawienia** kliknij pozycję **Reguły**.
3. Kliknij kartę **Kolekcja reguł aplikacji**.
4. Kliknij pozycję **Dodaj kolekcję reguł aplikacji**.
5. W polu **Nazwa** wpisz wartość **App-Coll01**.
6. W polu **Priorytet** wpisz wartość **200**.
7. W polu **Akcja** wybierz opcję **Zezwalaj**.
8. W obszarze **Reguły**, **Docelowa nazwa FQDN** w polu **Nazwa** wpisz wartość **AllowGH**.
9. W polu **Adresy źródłowe** wpisz wartość **10.0.2.0/24**.
10. W polu **Protocol:port** wpisz wartość **http, https**.
11. W polu **Docelowa nazwa FQDN** wpisz wartość **github.com**
12. Kliknij pozycję **Add** (Dodaj).

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Jest to reguła sieci, która umożliwia ruchowi wychodzącemu dostęp do dwóch adresów IP na porcie 53 (DNS).

1. Kliknij kartę **Kolekcja reguł sieci**.
1. Kliknij pozycję **Dodaj kolekcję reguł sieci**.
2. W polu **Nazwa** wpisz wartość **Net-Coll01**.
3. W polu **Priorytet** wpisz wartość **200**.
4. W polu **Akcja** wybierz opcję **Zezwalaj**.

6. W obszarze **Reguły** w polu **Nazwa** wpisz wartość **AllowDNS**.
8. W polu **Protokół** wybierz **UDP**.
9. W polu **Adresy źródłowe** wpisz wartość **10.0.2.0/24**.
10. W polu Adres docelowy wpisz wartość **209.244.0.3,209.244.0.4**
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

Teraz przetestuj zaporę, aby się upewnić, że działa ona zgodnie z oczekiwaniami.

1. W witrynie Azure Portal sprawdź ustawienia sieci dla maszyny wirtualnej **Srv-Work** i zanotuj prywatny adres IP.
2. Połącz pulpit zdalny z maszyną wirtualną **Srv-Jump**, a następnie z tego miejsca otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **Srv-Work**.

5. Otwórz program Internet Explorer i przejdź do http://github.com.
6. Kliknij przyciski **OK** > **Zamknij** dla alertów zabezpieczeń.

   Powinna zostać wyświetlona strona główna usługi GitHub.

7. Przejdź do http://www.msn.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz upewnij się, czy reguły zapory działają:

- Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.
- Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **Test-FW-RG**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
