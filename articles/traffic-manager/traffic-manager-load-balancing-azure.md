---
title: Korzystanie z usług równoważenia obciążenia na platformie Azure | Dokumenty firmy Microsoft
description: 'W tym samouczku pokazano, jak utworzyć scenariusz przy użyciu portfela równoważenia obciążenia platformy Azure: Menedżer ruchu, brama aplikacji i moduł równoważenia obciążenia.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: b77248813463f51d4bd2c5186e421aec43ffaf52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939227"
---
# <a name="using-load-balancing-services-in-azure"></a>Korzystanie z usług równoważenia obciążenia na platformie Azure

## <a name="introduction"></a>Wprowadzenie

Platforma Microsoft Azure oferuje wiele usług zarządzania sposób dystrybucji ruchu sieciowego i równoważenia obciążenia. Można korzystać z tych usług indywidualnie lub połączyć ich metody, w zależności od potrzeb, aby zbudować optymalne rozwiązanie.

W tym samouczku najpierw definiujemy przypadek użycia klienta i zobacz, jak można go uzyskać bardziej niezawodne i wydajne przy użyciu następujących portfolio równoważenia obciążenia platformy Azure: Menedżer ruchu, brama aplikacji i moduł równoważenia obciążenia. Następnie udostępniamy instrukcje krok po kroku dotyczące tworzenia wdrożenia, które jest geograficznie zbędne, dystrybuuje ruch do maszyn wirtualnych i pomaga zarządzać różnymi typami żądań.

Na poziomie koncepcyjnym każda z tych usług odgrywa odrębną rolę w hierarchii równoważenia obciążenia.

* **Traffic Manager** zapewnia globalne równoważenie obciążenia DNS. Analizuje przychodzące żądania DNS i odpowiada zdrowym punktem końcowym, zgodnie z zasadami routingu wybranymi przez klienta. Opcje dla metod routingu to:
  * Routing wydajności, aby wysłać żądającego do najbliższego punktu końcowego pod względem opóźnienia.
  * Routing priorytetu, aby skierować cały ruch do punktu końcowego, z innymi punktami końcowymi jako kopią zapasową.
  * Ważona routing okrężny, który rozdziela ruch na podstawie wagi przypisanej do każdego punktu końcowego.
  * Routing oparty na geografii do dystrybucji ruchu do punktów końcowych aplikacji na podstawie lokalizacji geograficznej użytkownika.
  * Routing oparty na podsieci w celu dystrybucji ruchu do punktów końcowych aplikacji na podstawie podsieci (zakres adresów IP) użytkownika.
  * Routing wielu wartości, który umożliwia wysyłanie adresów IP więcej niż jednego punktu końcowego aplikacji w jednej odpowiedzi DNS.

  Klient łączy się bezpośrednio z punktem końcowym zwróconym przez menedżera ruchu. Usługa Azure Traffic Manager wykrywa, gdy punkt końcowy jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia w dobrej kondycji. Więcej informacji na temat usługi można znaleźć w dokumentacji usługi [Azure Traffic Manager.](traffic-manager-overview.md)
* **Brama aplikacji** udostępnia kontroler dostarczania aplikacji (ADC) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Umożliwia klientom optymalizację wydajności farmy internetowej poprzez odciążanie rozwiązania SSL intensywnie korzystającego z procesora CPU do bramy aplikacji. Inne funkcje routingu warstwy 7 obejmują dystrybucję ruchu przychodzącego okrężną, koligacji sesji opartej na plikach cookie, routing oparty na ścieżce adresów URL oraz możliwość hostowania wielu witryn sieci Web za jedną bramą aplikacji. Brama aplikacji może być skonfigurowana jako brama internetowa, brama wewnętrzna lub kombinacja obu tych bram. Brama aplikacji jest w pełni zarządzana, skalowalna i wysoce dostępna na platformie Azure. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
* **Moduł równoważenia obciążenia** jest integralną częścią stosu SDN platformy Azure, zapewniając wysokiej wydajności, niskie opóźnienia warstwy 4 równoważenia obciążenia usług dla wszystkich protokołów UDP i TCP. Zarządza połączeniami przychodzącymi i wychodzącymi. Możesz skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem i zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, wykorzystując opcje badania kondycji protokołu TCP i HTTP do zarządzania dostępnością usługi.

## <a name="scenario"></a>Scenariusz

W tym przykładzie scenariusza używamy prostej witryny sieci Web, która obsługuje dwa typy zawartości: obrazy i dynamicznie renderowane strony sieci Web. Witryna sieci Web musi być geograficznie nadmiarowa i powinna służyć użytkownikom z najbliższej lokalizacji (najniższe opóźnienie) do nich. Deweloper aplikacji zdecydował, że wszystkie adresy URL, które pasują do wzorca /images/* są obsługiwane z dedykowanej puli maszyn wirtualnych, które różnią się od reszty farmy sieci web.

Ponadto domyślna pula maszyn wirtualnych obsługująca zawartość dynamiczną musi rozmawiać z bazą danych zaplecza, która jest hostowana w klastrze o wysokiej dostępności. Całe wdrożenie jest skonfigurowane za pośrednictwem usługi Azure Resource Manager.

Korzystanie z menedżera ruchu, bramy aplikacji i modułu równoważenia obciążenia umożliwia tej witrynie sieci Web osiągnięcie następujących celów projektowych:

* **Nadmiarowość wielu obszarów geograficznych:** jeśli jeden region ujrzy, Usługa Traffic Manager bezproblemowo kieruje ruch do najbliższego regionu bez interwencji właściciela aplikacji.
* **Zmniejszone opóźnienie:** Ponieważ usługa Traffic Manager automatycznie kieruje klienta do najbliższego regionu, klient doświadcza mniejszego opóźnienia podczas żądania zawartości strony sieci Web.
* **Niezależna skalowalność:** Ponieważ obciążenie aplikacji sieci web jest oddzielone od typu zawartości, właściciel aplikacji może skalować obciążenia żądań niezależnie od siebie. Brama aplikacji zapewnia, że ruch jest kierowany do odpowiednich pul na podstawie określonych reguł i kondycji aplikacji.
* **Wewnętrzne równoważenie obciążenia:** Ponieważ moduł równoważenia obciążenia znajduje się przed klastrem o wysokiej dostępności, tylko aktywny i zdrowy punkt końcowy bazy danych jest narażony na działanie aplikacji. Ponadto administrator bazy danych może zoptymalizować obciążenie, rozprowadzając repliki aktywne i pasywne w klastrze niezależnie od aplikacji front-end. Moduł równoważenia obciążenia zapewnia połączenia z klastrem o wysokiej dostępności i zapewnia, że tylko zdrowe bazy danych odbierają żądania połączenia.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Diagram architektury równoważenia obciążenia](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> W tym przykładzie jest tylko jedną z wielu możliwych konfiguracji usług równoważenia obciążenia, które oferuje platforma Azure. Menedżer ruchu, brama aplikacji i moduł równoważenia obciążenia można mieszać i dopasowywać do potrzeb równoważenia obciążenia. Na przykład jeśli odciążanie SSL lub przetwarzanie warstwy 7 nie jest konieczne, moduł równoważenia obciążenia może być używany zamiast bramy aplikacji.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurowanie stosu równoważenia obciążenia

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Tworzenie profilu usługi Traffic Manager

1. W portalu Azure kliknij pozycję **Utwórz** > **profil** > usługi**Networking** > Traffic Manager**create**.
2. Wprowadź następujące podstawowe informacje:

   * **Nazwa**: Nadaj profilowi usługi Traffic Manager nazwę prefiksu DNS.
   * **Metoda routingu:** Wybierz zasady metody routingu ruchu. Aby uzyskać więcej informacji na temat metod, zobacz [Informacje o metodach routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md).
   * **Subskrypcja**: Wybierz subskrypcję zawierającą profil.
   * **Grupa zasobów**: Wybierz grupę zasobów zawierającą profil. Może to być nowa lub istniejąca grupa zasobów.
   * **Lokalizacja grupy zasobów:** Usługa Usługi Traffic Manager jest globalna i nie jest powiązana z lokalizacją. Należy jednak określić region grupy, w którym znajdują się metadane skojarzone z profilem usługi Traffic Manager. Ta lokalizacja nie ma wpływu na dostępność środowiska wykonawczego profilu.

3. Kliknij **przycisk Utwórz,** aby wygenerować profil Usługi Traffic Manager.

   ![Ostrze "Utwórz menedżera ruchu"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Tworzenie bram aplikacji

1. W portalu Azure w lewym okienku kliknij pozycję **Utwórz bramę** > **aplikacji****sieciowych** > .
2. Wprowadź następujące podstawowe informacje o bramie aplikacji:

   * **Nazwa**: Nazwa bramy aplikacji.
   * **Rozmiar jednostki SKU:** rozmiar bramy aplikacji, dostępny jako Mały, Średni lub Duży.
   * **Liczba wystąpień:** Liczba wystąpień, wartość od 2 do 10.
   * **Grupa zasobów:** Grupa zasobów, która przechowuje bramę aplikacji. Może to być istniejąca grupa zasobów lub nowa.
   * **Lokalizacja:** Region bramy aplikacji, która jest tą samą lokalizacją co grupa zasobów. Lokalizacja jest ważna, ponieważ sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej lokalizacji co brama.
3. Kliknij przycisk **OK**.
4. Zdefiniuj konfiguracje sieci wirtualnej, podsieci, adresu IP front-end i odbiornika bramy aplikacji. W tym scenariuszu adres IP frontonu jest **publiczny**, który umożliwia jego późniejsze dodawanie jako punktu końcowego do profilu usługi Traffic Manager.
5. Skonfiguruj odbiornik za pomocą jednej z następujących opcji:
    * Jeśli używasz protokołu HTTP, nie ma nic do skonfigurowania. Kliknij przycisk **OK**.
    * Jeśli używasz protokołu HTTPS, wymagana jest dalsza konfiguracja. Zapoznaj się [z pozycję Tworzenie bramy aplikacji](../application-gateway/application-gateway-create-gateway-portal.md), począwszy od kroku 9. Po zakończeniu konfiguracji kliknij przycisk **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurowanie routingu adresów URL dla bram aplikacji

Po wybraniu puli zaplecza brama aplikacji skonfigurowana z regułą opartą na ścieżce przyjmuje wzorzec ścieżki adresu URL żądania oprócz dystrybucji okrężnej. W tym scenariuszu dodajemy regułę opartą na ścieżce,\*aby skierować dowolny adres URL z "/images/ " do puli serwerów obrazów. Aby uzyskać więcej informacji na temat konfigurowania routingu opartego na ścieżce adresów URL dla bramy aplikacji, zobacz [Tworzenie reguły opartej na ścieżce dla bramy aplikacji](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram warstwy sieci Web bramy aplikacji](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Z grupy zasobów przejdź do wystąpienia bramy aplikacji utworzonej w poprzedniej sekcji.
2. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy danych**, a następnie wybierz pozycję **Dodaj,** aby dodać maszyny wirtualne, które chcesz skojarzyć z pulami zaplecza warstwy sieci Web.
3. Wprowadź nazwę puli zaplecza i wszystkie adresy IP komputerów, które znajdują się w puli. W tym scenariuszu łączymy dwie pule serwerów zaplecza maszyn wirtualnych.

   ![Brama aplikacji "Dodaj pulę wewnętrznej bazy danych"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. W obszarze **Ustawienia** bramy aplikacji wybierz pozycję **Reguły**, a następnie kliknij przycisk **Oparty na ścieżce,** aby dodać regułę.

   ![Przycisk Reguły bramy aplikacji "Na podstawie ścieżki"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Skonfiguruj regułę, podając następujące informacje.

   Podstawowe ustawienia:

   + **Nazwa**: Przyjazna nazwa reguły, która jest dostępna w portalu.
   + **Odbiornik:** odbiornik, który jest używany do reguły.
   + **Domyślna pula wewnętrznej bazy**danych: Pula zaplecza, która ma być używana z regułą domyślną.
   + **Domyślne ustawienia HTTP**: Ustawienia HTTP używane z regułą domyślną.

   Reguły oparte na ścieżce:

   + **Nazwa**: Przyjazna nazwa reguły opartej na ścieżce.
   + **Ścieżki:** reguła ścieżki używana do przekazywania ruchu.
   + **Pula zaplecza:** Pula zaplecza do użycia z tą regułą.
   + **Ustawienie HTTP**: Ustawienia HTTP, które mają być używane z tą regułą.

   > [!IMPORTANT]
   > Ścieżki: Prawidłowe ścieżki muszą zaczynać się od "/". Symbol wieloznaczny " "\*jest dozwolony tylko na końcu. Prawidłowe przykłady to /xyz,\*/xyz lub\*/xyz/ .

   ![Blok bramy aplikacji "Dodawanie reguł opartych na ścieżce"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Dodawanie bram aplikacji do punktów końcowych usługi Traffic Manager

W tym scenariuszu usługa Traffic Manager jest połączona z bramami aplikacji (skonfigurowanym w poprzednich krokach), które znajdują się w różnych regionach. Teraz, gdy bramy aplikacji są skonfigurowane, następnym krokiem jest połączenie ich z profilem usługi Traffic Manager.

1. Otwórz swój profil Usługi Traffic Manager. Aby to zrobić, poszukaj w grupie zasobów lub wyszukaj nazwę profilu Usługi Traffic Manager ze **wszystkich zasobów**.
2. W lewym okienku wybierz pozycję **Punkty końcowe**, a następnie kliknij przycisk **Dodaj,** aby dodać punkt końcowy.

   ![Przycisk "Dodaj" w usłudze Traffic Manager](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Utwórz punkt końcowy, wprowadzając następujące informacje:

   * **Typ**: Wybierz typ punktu końcowego do równoważenia obciążenia. W tym scenariuszu wybierz **punkt końcowy platformy Azure,** ponieważ łączymy go z wystąpieniami bramy aplikacji, które zostały skonfigurowane wcześniej.
   * **Nazwa**: Wprowadź nazwę punktu końcowego.
   * **Typ zasobu docelowego:** Wybierz **publiczny adres IP,** a następnie w obszarze **Zasób docelowy**wybierz publiczny adres IP bramy aplikacji, która została wcześniej skonfigurowana.

   ![Menedżer ruchu "Dodaj punkt końcowy"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Teraz możesz przetestować konfigurację, uzyskując do niej dostęp za pomocą systemu DNS profilu Usługi Traffic Manager (w tym przykładzie: TrafficManagerScenario.trafficmanager.net). Możesz ponownie wysuwać żądania, wprowadzać lub wywracać maszyny wirtualne i serwery sieci web utworzone w różnych regionach, a także zmieniać ustawienia profilu usługi Traffic Manager, aby przetestować konfigurację.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Tworzenie modułu równoważenia obciążenia

W tym scenariuszu moduł równoważenia obciążenia dystrybuuje połączenia z warstwy sieci web do baz danych w klastrze o wysokiej dostępności.

Jeśli klaster bazy danych o wysokiej dostępności używa programu SQL Server AlwaysOn, zapoznaj się z instrukcjami krok po kroku [dotyczącymi konfigurowania jednego lub więcej odbiorników grup zawsze włączonych do dostępności.](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)

Aby uzyskać więcej informacji na temat konfigurowania wewnętrznego modułu równoważenia obciążenia, zobacz [Tworzenie wewnętrznego modułu równoważenia obciążenia w witrynie Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. W portalu Azure w lewym okienku kliknij pozycję Utwórz moduł > **równoważenia obciążenia****sieciowego** **zasobu** > .
2. Wybierz nazwę modułu równoważenia obciążenia.
3. Ustaw **typ** na **Wewnętrzny**i wybierz odpowiednią sieć wirtualną i podsieć dla modułu równoważenia obciążenia, w
4. W obszarze **Przypisanie adresu IP**wybierz opcję **Dynamiczne** lub **Statyczne**.
5. W obszarze **Grupa zasobów**wybierz grupę zasobów dla modułu równoważenia obciążenia.
6. W obszarze **Lokalizacja**wybierz odpowiedni region dla modułu równoważenia obciążenia.
7. Kliknij **przycisk Utwórz,** aby wygenerować moduł równoważenia obciążenia.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Łączenie warstwy bazy danych zaplecza z modułem równoważenia obciążenia

1. W grupie zasobów znajdź moduł równoważenia obciążenia utworzony w poprzednich krokach.
2. W obszarze **Ustawienia**kliknij pozycję **Pule wewnętrznej bazy,** a następnie kliknij przycisk **Dodaj,** aby dodać pulę zaplecza.

   ![Moduł równoważenia obciążenia "Dodaj pulę wewnętrznej bazy danych"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Wprowadź nazwę puli zaplecza.
4. Dodaj pojedyncze maszyny lub dostępność ustawioną do puli zaplecza.

#### <a name="configure-a-probe"></a>Konfigurowanie sondy

1. W modułze równoważenia obciążenia w obszarze **Ustawienia**wybierz pozycję **Sondy**, a następnie kliknij przycisk **Dodaj,** aby dodać sondę.

   ![Moduł równoważenia obciążenia "Dodaj sondę"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Wprowadź nazwę sondy.
3. Wybierz **protokół** dla sondy. W przypadku bazy danych może być sonda TCP, a nie sonda HTTP. Aby dowiedzieć się więcej o sondach modułu równoważenia obciążenia, zobacz [Opis sondy modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md).
4. Wprowadź **port** bazy danych, który ma być używany do uzyskiwania dostępu do sondy.
5. W obszarze **Interwał**określ, jak często należy sondować aplikację.
6. W obszarze **próg w złej kondycji**określ liczbę ciągłych błędów sondy, które muszą wystąpić, aby maszyna wirtualna zaplecza została uznana za złą w złej kondycji.
7. Kliknij **przycisk OK,** aby utworzyć sondę.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurowanie reguł równoważenia obciążenia

1. W obszarze **Ustawienia** modułu równoważenia obciążenia wybierz pozycję **Reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj,** aby utworzyć regułę.
2. Wprowadź **nazwę** reguły równoważenia obciążenia.
3. Wybierz **adres IP frontu** modułu równoważenia obciążenia, **protokołu**i **portu**.
4. W **obszarze Port wewnętrznej bazy**określ port, który ma być używany w puli zaplecza.
5. Wybierz **pulę wewnętrznej bazy** danych i **Probe,** które zostały utworzone w poprzednich krokach, aby zastosować regułę.
6. W obszarze **Trwałość sesji**wybierz sposób utrwalenia sesji.
7. W obszarze **Limity czasu bezczynności**określ liczbę minut przed limitem czasu bezczynności.
8. W obszarze **Przestawny adres IP**wybierz opcję **Wyłączone** lub **Włączone**.
9. Kliknij przycisk **OK**, aby utworzyć regułę.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: Łączenie maszyn wirtualnych w warstwie internetowej z modułem równoważenia obciążenia

Teraz konfigurujemy adres IP i port front-end modułu równoważenia obciążenia w aplikacjach, które są uruchomione na maszynach wirtualnych warstwy sieci Web dla wszystkich połączeń z bazą danych. Ta konfiguracja jest specyficzna dla aplikacji, które działają na tych maszynach wirtualnych. Aby skonfigurować docelowy adres IP i port, zapoznaj się z dokumentacją aplikacji. Aby znaleźć adres IP frontu, w witrynie Azure portal przejdź do puli adresów IP front-endu w **ustawieniach modułu równoważenia obciążenia**.

![Okienko nawigacji modułu równoważenia obciążenia "Pula adresów IP umienia frontowego"](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Traffic Manager](traffic-manager-overview.md)
* [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)
* [Omówienie usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md)
