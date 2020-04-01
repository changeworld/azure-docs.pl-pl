---
title: Filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure Firewall DNAT przy użyciu portalu
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować funkcję DNAT usługi Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/02/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7220e48c6103352108bdb89e107bb862ee194040
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251490"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Samouczek: Filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure Firewall DNAT przy użyciu witryny Azure portal

Można skonfigurować translację adresów sieciowych (DNAT) docelowej zapory platformy Azure w celu tłumaczenia i filtrowania przychodzącego ruchu internetowego do podsieci. Podczas konfigurowania funkcji DNAT akcja kolekcji reguł NAT jest ustawiana na wartość **Dnat**. Każda reguła w kolekcji reguł NAT umożliwia wykonanie translacji publicznego adresu IP i portu zapory na prywatny adres IP i port. Reguły DNAT niejawnie dodają odpowiednią regułę sieci zezwalającą na przetłumaczony ruch. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Testowanie zapory

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

W tym samouczku utworzysz dwie sieci wirtualne połączone przy użyciu komunikacji równorzędnej:

- **VN-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VN-Spoke** — w tej sieci wirtualnej znajduje się serwer obciążeń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. Na stronie głównej portalu platformy Azure wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **Dodaj**.
3. W polu **Nazwa grupy zasobów** wpisz **RG-DNAT-Test**.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie kolejne zasoby, które utworzysz, muszą znajdować się w tej samej lokalizacji.
6. Wybierz **pozycję Utwórz**.

## <a name="set-up-the-network-environment"></a>Konfigurowanie środowiska sieciowego

Najpierw utwórz sieci wirtualne, a następnie połącz je przy użyciu komunikacji równorzędnej.

### <a name="create-the-hub-vnet"></a>Tworzenie koncentratora sieci wirtualnej

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Wszystkie usługi**.
2. W obszarze **Sieć**wybierz pozycję **Sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **VN-Hub**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**.

     Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
     > [!NOTE]
     > Rozmiar podsieci AzureFirewallSubnet to /26. Aby uzyskać więcej informacji na temat rozmiaru podsieci, zobacz [Często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. W przypadku **zakresu adresów**wpisz **10.0.1.0/26**.
11. Użyj innych ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-spoke-vnet"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Wszystkie usługi**.
2. W obszarze **Sieć**wybierz pozycję **Sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **VN-Spoke**.
5. W polu **Przestrzeń adresowa** wpisz wartość **192.168.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieć** w polu **Nazwa** wpisz **SN-Workload**.

    Serwer będzie znajdował się w tej podsieci.
10. W polu **Zakres adresów** wpisz wartość **192.168.1.0/24**.
11. Użyj innych ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="peer-the-vnets"></a>Łączenie sieci wirtualnych przy użyciu komunikacji równorzędnej

Teraz połącz sieci wirtualne przy użyciu komunikacji równorzędnej.

1. Wybierz sieć **wirtualną VN-Hub.**
2. W obszarze **Ustawienia**wybierz **pozycję Komunikacja równorzędna**.
3. Wybierz pozycję **Dodaj**.
4. Typ **Peer-HubPoke** dla **nazwy komunikacji równorzędnej z VN-Hub do VN-Spoke**.
5. Jako sieć wirtualną wybierz **VN-Spoke**.
6. Wpisz **Peer-SpokeHub** dla **nazwy komunikacji równorzędnej z VN-Spoke do VN-Hub**.
7. Dla **zezwalaj na ruch przesyłany dalej z VN-Spoke do VN-Hub** wybierz **enabled**.
8. Kliknij przycisk **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną obciążenia i umieść ją w podsieci **SN-Workload**.

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.
2. W obszarze **Popularne**wybierz pozycję **Centrum danych systemu Windows Server 2016**.

**Podstawy**

1. W polu **Subskrypcja** wybierz subskrypcję.
1. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
1. W przypadku **nazwy maszyny wirtualnej**wpisz **Srv-Workload**.
1. W obszarze **Region**wybierz tę samą lokalizację, która była wcześniej używana.
1. Wpisz nazwę użytkownika i hasło.
1. Wybierz **dalej: Dyski**.

**Dyski**
1. Wybierz **dalej: Sieć**.

**Networking**

1. W przypadku **sieci wirtualnej**wybierz **VN-Spoke**.
2. W polu **Podsieć** wybierz pozycję **SN-Workload**.
3. W przypadku **publicznego adresu IP** wybierz opcję **Brak**.
4. W przypadku **publicznych portów przychodzących**wybierz opcję **Brak**. 
2. Pozostaw inne ustawienia domyślne i wybierz **dalej : Zarządzanie**.

**Zarządzanie**

1. W przypadku **diagnostyki rozruchu**wybierz opcję **Wył.**
1. Wybierz **pozycję Recenzja + Utwórz**.

**Recenzja + Tworzenie**

Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz**. Ukończenie tej operacji potrwa kilka minut.

Po zakończeniu wdrożenia zanotuj prywatny adres IP maszyny wirtualnej. Posłuży on później do skonfigurowania zapory. Wybierz nazwę maszyny wirtualnej i w obszarze **Ustawienia**wybierz pozycję **Sieć,** aby znaleźć prywatny adres IP.

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. Na stronie głównej portalu wybierz pozycję **Utwórz zasób**.
2. Wybierz **pozycję Sieć**i po **wybraniu opcji Polecane**wybierz pozycję Zobacz **wszystkie**.
3. Wybierz **pozycję Zapora ,** a następnie wybierz pozycję **Utwórz**. 
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Nazwa     |FW-DNAT-test|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Użyj istniejącej**: RG-DNAT-Test |
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: VN-Hub|
   |Publiczny adres IP     |**Utwórz nowy**plik . Publiczny adres IP musi mieć typ Standardowa jednostka SKU.|

5. Wybierz pozycję **Przegląd + utwórz**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz,** aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **RG-DNAT-Test** i wybierz zaporę **testową FW-DNAT.**
8. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **SN-Workload** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Wszystkie usługi**.
2. W obszarze **Sieć**wybierz pozycję **Tabele tras**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz **RT-FWroute**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Wybierz **pozycję Utwórz**.
9. Wybierz **polecenie Odśwież**, a następnie wybierz tabelę trasy **RT-FWroute.**
10. Wybierz **podsieci**, a następnie wybierz pozycję **Skojarz**.
11. Wybierz **opcję Sieć wirtualna**, a następnie wybierz pozycję **VN-Spoke**.
12. W polu **Podsieć** wybierz pozycję **SN-Workload**.
13. Kliknij przycisk **OK**.
14. Wybierz pozycję **Trasy**, a następnie wybierz pozycję **Dodaj**.
15. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Kliknij przycisk **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurowanie reguł translatora adresów sieciowych

1. Otwórz **test RG-DNAT**i wybierz zaporę **testową FW-DNAT.** 
2. Na stronie **testowej FW-DNAT** w obszarze **Ustawienia**wybierz pozycję **Reguły**. 
3. Wybierz **pozycję Dodaj kolekcję reguł NAT**. 
4. W polu **Nazwa** wpisz **RC-DNAT-01**. 
5. W polu **Priorytet** wpisz wartość **200**. 
6. W obszarze **Reguły** w polu **Nazwa** wpisz **RL-01**.
7. W polu **Protokół** wybierz **TCP**.
8. W polu **Adresy źródłowe** wpisz znak *. 
9. W polu **Adresy docelowe** wpisz publiczny adres IP zapory. 
10. W polu **Porty docelowe** wpisz **3389**. 
11. W polu **Przekształcony adres** wpisz prywatny adres IP maszyny wirtualnej Srv-Workload. 
12. W polu **Przekształcony port** wpisz **3389**. 
13. Wybierz pozycję **Dodaj**. 

## <a name="test-the-firewall"></a>Testowanie zapory

1. Połącz pulpit zdalny z publicznym adresem IP zapory. Powinno zostać nawiązane połączenie z maszyną wirtualną **Srv-Workload**.
2. Zamknij pulpit zdalny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **RG-DNAT-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Testowanie zapory

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
