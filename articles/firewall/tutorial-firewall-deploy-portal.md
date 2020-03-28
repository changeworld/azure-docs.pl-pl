---
title: 'Samouczek: Wdrażanie & konfigurowanie Zapory Platformy Azure przy użyciu witryny Azure portal'
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować usługę Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239574"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Na przykład można ograniczyć dostęp do witryn sieci Web. Można też ograniczyć wychodzące adresy IP i porty, do których można uzyskać dostęp.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym samouczku utworzysz uproszczoną pojedynczą sieć wirtualną z trzema podsieciami w celu łatwego wdrażania. W przypadku wdrożeń produkcyjnych zaleca się [model koncentratora i szprychy.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Zapora znajduje się we własnej sieci wirtualnej. Serwery obciążenia znajdują się w równorzędnych sieciach wirtualnych w tym samym regionie z co najmniej jedną podsiecią.

* **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
* **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
* **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły aplikacji w celu umożliwienia dostępu do www.google.com
> * Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
> * Testowanie zapory

Jeśli chcesz, możesz wykonać kroki tego samouczka przy użyciu [programu Azure PowerShell](deploy-ps.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby wymagane w tym samouczku.

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. W menu portalu platformy Azure wybierz **pozycję Grupy zasobów** lub wyszukaj i wybierz grupy *zasobów* z dowolnej strony. Następnie wybierz pozycję **Dodaj**.
3. W **yjmij nazwę grupy zasobów**wprowadź *test-FW-RG*.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie inne utworzone zasoby muszą znajdować się w tej samej lokalizacji.
6. Wybierz **pozycję Utwórz**.

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna będzie zawierać trzy podsieci.

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to /26. Aby uzyskać więcej informacji na temat rozmiaru podsieci, zobacz [Często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wybierz **sieć wirtualną** > **Virtual network**sieci .
1. W polu **Nazwa** wpisz wartość **Test-FW-VN**.
1. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
1. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów**wybierz **pozycję Test-FW-RG**.
1. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
1. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**. Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
1. W przypadku **zakresu adresów**wpisz **10.0.1.0/26**.
1. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

### <a name="create-additional-subnets"></a>Tworzenie dodatkowych podsieci

Następnie należy utworzyć podsieci dla serwera przesiadkowego oraz podsieci dla serwerów obciążeń.

1. W menu portalu platformy Azure wybierz **pozycję Grupy zasobów** lub wyszukaj i wybierz grupy *zasobów* z dowolnej strony. Następnie wybierz **Test-FW-RG**.
2. Wybierz sieć wirtualną **Test-FW-VN.**
3. Wybierz **podsieci** > **+podsieć**.
4. W polu **Nazwa** wpisz wartość **Workload-SN**.
5. W polu **Zakres adresów** wpisz wartość **10.0.2.0/24**.
6. Kliknij przycisk **OK**.

Utwórz kolejną podsieć z nazwą **Jump-SN** i zakresem adresów **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Compute**, a następnie z listy Polecane wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Grupa zasobów     |**Test-FW-RG**|
   |Nazwa maszyny wirtualnej     |**Skok Srv**|
   |Region     |Tak samo jak w poprzednich|
   |Nazwa użytkownika administratora     |**użytkownik_azure**|
   |Hasło     |**Azure123456!**|

4. W obszarze **Reguły portów przychodzących**dla **publicznych portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty**.
5. W obszarze **Wybierz porty wejściowe** wybierz pozycję **RDP (3389)**.

6. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Dalej: Dyski**.
7. Zaakceptuj ustawienia domyślne dysku i wybierz **pozycję Dalej: Sieć**.
8. Upewnij się, że wybrano sieć wirtualną **Test-FW-VN** i podsieć **Jump-SN**.
9. W przypadku **adresu IP publicznego**zaakceptuj domyślną nową nazwę publicznego adresu IP (Srv-Jump-ip).
11. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Dalej: Zarządzanie**.
12. Wybierz **opcję Wyłącz,** aby wyłączyć diagnostykę rozruchu. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Przejrzyj + utwórz**.
13. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

Użyj informacji w poniższej tabeli, aby skonfigurować inną maszynę wirtualną o nazwie **Srv-Work**. Pozostała część konfiguracji jest taka sama jak w przypadku maszyny wirtualnej Srv-Jump.

|Ustawienie  |Wartość  |
|---------|---------|
|Podsieć|**Workload-SN**|
|Publiczny adres IP|**Brak**|
|Publiczne porty wejściowe|**Brak**|

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Wdróż zaporę w sieci wirtualnej.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Wpisz **zaporę** w polu wyszukiwania i naciśnij **klawisz Enter**.
3. Wybierz **pozycję Zapora,** a następnie wybierz pozycję **Utwórz**.
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Test-FW-RG** |
   |Nazwa     |**Test-FW01**|
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącego**: **Test-FW-VN**|
   |Publiczny adres IP     |**Dodaj nowy**plik . Publiczny adres IP musi mieć typ Standardowa jednostka SKU.|

5. Wybierz pozycję **Przegląd + utwórz**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz,** aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **Test-FW-RG** i wybierz zaporę **Test-FW01.**
8. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **Workload-SN** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. W menu portalu azure wybierz **pozycję Wszystkie usługi** lub wyszukaj i wybierz pozycję Wszystkie *usługi* z dowolnej strony.
2. W obszarze **Sieć**wybierz pozycję **Tabele tras**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **Firewall-route**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów**wybierz **pozycję Test-FW-RG**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Wybierz **pozycję Utwórz**.
9. Wybierz **polecenie Odśwież**, a następnie wybierz tabelę trasy **zapory zapory.**
10. Wybierz **podsieci,** a następnie wybierz pozycję **Skojarz**.
11. Wybierz **opcję Test sieci** > **wirtualnej Test-FW-VN**.
12. W przypadku **podsieci**wybierz **pozycję Obciążenie-SN**. Upewnij się, że dla tej trasy wybrano tylko **podsieć Obciążenie-SN,** w przeciwnym razie zapora nie będzie działać poprawnie.

13. Kliknij przycisk **OK**.
14. Wybierz **pozycję Trasy,** a następnie wybierz pozycję **Dodaj**.
15. W **yjasz nazwa trasy**wpisz **fw-dg**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Kliknij przycisk **OK**.

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Jest to reguła aplikacji, która umożliwia dostęp wychodzący do www.google.com.

1. Otwórz **test-FW-RG**i wybierz zaporę **Test-FW01.**
2. Na stronie **Test-FW01** w obszarze **Ustawienia**wybierz pozycję **Reguły**.
3. Wybierz kartę **Zbieranie reguł aplikacji.**
4. Wybierz **pozycję Dodaj kolekcję reguł aplikacji**.
5. W polu **Nazwa** wpisz wartość **App-Coll01**.
6. W polu **Priorytet** wpisz wartość **200**.
7. W polu **Akcja** wybierz opcję **Zezwalaj**.
8. W obszarze **Reguły** **, Docelowe nazwy FQDN**, dla **Name**, wpisz **Allow-Google**.
9. W przypadku **typu źródła**wybierz **adres IP**.
10. Dla **źródła**, typ **10.0.2.0/24**.
11. W polu **Protocol:port** wpisz wartość **http, https**.
12. W przypadku **docelowych fqdns**wpisz **www.google.com**
13. Wybierz pozycję **Dodaj**.

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Jest to reguła sieci, która umożliwia ruchowi wychodzącemu dostęp do dwóch adresów IP na porcie 53 (DNS).

1. Wybierz kartę **Zbieranie reguł sieciowych.**
2. Wybierz **pozycję Dodaj kolekcję reguł sieciowych**.
3. W polu **Nazwa** wpisz wartość **Net-Coll01**.
4. W polu **Priorytet** wpisz wartość **200**.
5. W polu **Akcja** wybierz opcję **Zezwalaj**.
6. W obszarze **Reguły**, **adresy IP**, dla **Name**, wpisz **Allow-DNS**.
7. W polu **Protokół** wybierz **UDP**.
9. W przypadku **typu źródła**wybierz **adres IP**.
1. Dla **źródła**, typ **10.0.2.0/24**.
2. Dla **adresu docelowego**, typ **209.244.0.3,209.244.0.4**

   Są to publiczne serwery DNS obsługiwane przez CenturyLink.
1. W polu **Porty docelowe** wpisz wartość **53**.
2. Wybierz pozycję **Dodaj**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Zmienianie podstawowego i pomocniczego adresu DNS dla interfejsu sieciowego **Srv-Work**

Do celów testowania w tym samouczku skonfiguruj podstawowe i pomocnicze adresy DNS serwera. Nie jest to ogólne wymaganie zapory platformy Azure.

1. W menu portalu platformy Azure wybierz **pozycję Grupy zasobów** lub wyszukaj i wybierz grupy *zasobów* z dowolnej strony. Wybierz grupę zasobów **Test-FW-RG.**
2. Wybierz interfejs sieciowy maszyny wirtualnej **Srv-Work.**
3. W obszarze **Ustawienia**wybierz pozycję **Serwery DNS**.
4. W obszarze **Serwery DNS**wybierz pozycję **Niestandardowe**.
5. Wpisz wartość **209.244.0.3** w polu tekstowym **Dodaj serwer DNS**, a następnie wpisz wartość **209.244.0.4** w następnym polu tekstowym.
6. Wybierz **pozycję Zapisz**.
7. Uruchom ponownie maszynę wirtualną **Srv-Work**.

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz przetestuj zaporę, aby potwierdzić, że działa zgodnie z oczekiwaniami.

1. W witrynie Azure Portal sprawdź ustawienia sieci dla maszyny wirtualnej **Srv-Work** i zanotuj prywatny adres IP.
2. Podłącz pulpit zdalny do maszyny wirtualnej **Srv-Jump** i zaloguj się. Stamtąd otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **Srv-Work.**
3. Otwórz program Internet Explorer i przejdź do https://www.google.com.
4. Wybierz **przycisk OK** > **Zamknij** w alertach zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna Google.

5. Przejdź do https://www.microsoft.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Więc teraz masz zweryfikowane, że reguły zapory działają:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.
* Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **Test-FW-RG**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
