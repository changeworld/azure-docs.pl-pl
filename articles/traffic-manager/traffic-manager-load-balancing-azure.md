---
title: Przy użyciu usługi równoważenia obciążenia na platformie Azure | Dokumentacja firmy Microsoft
description: 'W tym samouczku pokazano, jak utworzyć scenariusz przy użyciu platformy Azure portfolio równoważenia obciążenia: Usługa Traffic Manager, usługa Application Gateway i modułu równoważenia obciążenia.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: dkays
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 906e1840f35ab14997c727551b893a0219eb78d8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330521"
---
# <a name="using-load-balancing-services-in-azure"></a>Przy użyciu usługi równoważenia obciążenia na platformie Azure

## <a name="introduction"></a>Wprowadzenie

Microsoft Azure oferuje wiele usług zarządzania, w jaki sposób dystrybucji ruchu sieciowego i z równoważeniem obciążenia. Można korzystać z tych usług indywidualnie lub łączenie ich metod w zależności od potrzeb, aby skompilować optymalne rozwiązanie.

W tym samouczku będziemy najpierw zdefiniować przypadek użycia klienta i zobacz jak może on bardziej niezawodne i wydajne przy użyciu następujących portfela równoważenia obciążenia platformy Azure: Usługa Traffic Manager, usługa Application Gateway i modułu równoważenia obciążenia. Firma Microsoft następnie zawierają instrukcje krok po kroku dotyczące tworzenia wdrożenia, które geograficznie nadmiarowy, dystrybuuje ruch do maszyn wirtualnych i ułatwia zarządzanie różnych typów żądań.

Na poziomie pojęciach każda z tych usług odgrywa rolę distinct w hierarchii równoważenia obciążenia.

* **Usługa Traffic Manager** zapewnia równoważenie obciążenia globalnego DNS. Ona patrzy na przychodzące żądania DNS i odpowiada za pomocą dobrej kondycji punktu końcowego, zgodnie z zasadami routingu, wybranej przez klienta. Dostępne są opcje dla metody routingu:
  * Wydajność routingu, aby wysłać obiekt żądający do najbliższego punktu końcowego pod kątem opóźnień.
  * Priorytet routingu, aby kierować cały ruch do punktu końcowego, z innych punktów końcowych przechowywania kopii zapasowych.
  * Ważone działanie okrężne routingu, który rozprowadza ruchu w oparciu o wagi, która jest przypisana do każdego punktu końcowego.
  * Lokalizacja geograficzna na podstawie routingu w celu dystrybucji ruchu do punktów końcowych aplikacji na podstawie lokalizacji geograficznej użytkownika.
  * Na podstawie podsieci routingu w celu dystrybucji ruchu do punktów końcowych aplikacji na podstawie podsieci (zakres adresów IP) użytkownika.
  * Wiele wartości, routingu, który umożliwia wysyłanie adresy IP punktów końcowych aplikacji więcej niż jeden w jednej odpowiedzi DNS.

  Klient łączy się bezpośrednio do punktu końcowego zwracane przez usługę Traffic Manager. Usługa Azure Traffic Manager wykrywa, gdy punkt końcowy jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia dobrej kondycji. Zapoznaj się [dokumentacji usługi Azure Traffic Manager](traffic-manager-overview.md) Aby dowiedzieć się więcej o usłudze.
* **Usługa Application Gateway** oferuje kontrolera dostarczania aplikacji (ADC) jako usługi, która oferuje różne możliwości warstwy 7 równoważenia obciążenia dla danej aplikacji. Umożliwia to klientom optymalizowanie wydajności farmy sieci web dzięki przeniesieniu kończenia żądań SSL mocy procesora CPU do usługi application gateway. Inne możliwości routingu warstwy 7 obejmują-okrężna Dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za bramą pojedynczej aplikacji. Usługa Application Gateway można skonfigurować jako bramę dostępnego z Internetu, bramę tylko wewnętrzne lub jako kombinację obu tych. Usługa Application Gateway jest w pełni Azure zarządzane, skalowalne i o wysokiej dostępności. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
* **Moduł równoważenia obciążenia** stanowią integralną część stosu SDN platformy Azure, zapewnienie wysokiej wydajności i niskich opóźnieniach warstwy 4 równoważenia obciążenia usług dla wszystkich protokołów UDP i TCP. Zarządza połączeń przychodzących i wychodzących. Można skonfigurować public i internal końcowe ze zrównoważonym obciążeniem i zdefiniuj reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, zarządzanie za pomocą protokołu TCP i HTTP opcje usługi badania kondycji dostępności usługi.

## <a name="scenario"></a>Scenariusz

W tym przykładowym scenariuszu używamy prostą witrynę sieci Web, który obsługuje dwa typy zawartości: obrazów i dynamicznie renderowanych stron sieci Web. Witryna sieci Web musi być magazyn geograficznie nadmiarowy i powinny służyć, jego użytkowników z najbliższego (najmniejszego opóźnienia) lokalizacji do nich. Deweloper aplikacji zdecydował, że wszystkie adresy URL zgodne ze wzorcem/obrazy / * są obsługiwane z dedykowanej puli maszyn wirtualnych, które różnią się od reszty kolektywu serwerów sieci web.

Ponadto domyślnej puli maszyn wirtualnych obsługujących zawartość dynamiczną musi komunikować się z wewnętrznej bazy danych, który znajduje się w klastrze o wysokiej dostępności. Całe wdrożenie jest skonfigurowane za pomocą usługi Azure Resource Manager.

Przy użyciu usługi Traffic Manager, usługa Application Gateway i modułu równoważenia obciążenia zezwala na tej witrynie sieci Web osiągnąć te cele projektu:

* **Nadmiarowość geograficzna Multi**: Jeśli jeden region ulegnie awarii, usługa Traffic Manager kieruje ruch bezproblemowo do najbliższego regionu bez żadnej interwencji od właściciela aplikacji.
* **Mniejsze opóźnienia**: Ponieważ usługi Traffic Manager automatyczne kierowanie klientów do najbliższego regionu, mniejsze opóźnienia i doświadczeń w klienta podczas żądania zawartość strony sieci Web.
* **Niezależną skalowalność**: Ponieważ obciążenie aplikacji sieci web jest oddzielona typu zawartości, właściciel aplikacji można skalować obciążenia żądań od siebie niezależne. Usługa Application Gateway zapewnia, że ruch jest kierowany do odpowiednie pule na podstawie określonych reguł i kondycję aplikacji.
* **Wewnętrzne Równoważenie obciążenia**: Ponieważ moduł równoważenia obciążenia przed klastrem wysokiej dostępności, tylko aktywne i dobrej kondycji punktu końcowego dla bazy danych jest uwidaczniany w aplikacji. Ponadto administrator bazy danych może zoptymalizować obciążenia dzięki rozłożeniu aktywnym i pasywnym repliki w klastrze, niezależnie od aplikacji frontonu. Moduł równoważenia obciążenia zapewnia połączeń w klastrze o wysokiej dostępności i zapewnia, że tylko dobrej kondycji bazy danych otrzymywać żądania połączenia.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Diagram przedstawiający równoważenia obciążenia architektury](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> W tym przykładzie jest tylko jeden z wielu możliwych konfiguracji usługi równoważenia obciążenia, oferowanych przez platformę Azure. Usługa Traffic Manager, usługa Application Gateway i modułu równoważenia obciążenia mogą być mieszane i dopasowywane zależności potrzeb równoważenia obciążenia. Na przykład jeśli odciążanie protokołu SSL lub nie jest konieczne przetwarzanie warstwy 7, moduł równoważenia obciążenia można zamiast Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurowanie równoważenia obciążenia stosu

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Tworzenie profilu usługi Traffic Manager

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** > **sieć** > **profilu usługi Traffic Manager**  >   **Utwórz**.
2. Wprowadź następujące informacje podstawowe:

   * **Nazwa**: Nadaj profilu usługi Traffic Manager DNS prefiks nazwy.
   * **Metody routingu opartego na**: Wybierz zasady metody routingu ruchu. Aby uzyskać więcej informacji o metodach, zobacz [metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md).
   * **Subskrypcja**: Wybierz subskrypcję, która zawiera profil.
   * **Grupa zasobów**: Wybierz grupę zasobów, która zawiera profil. Może być nowej lub istniejącej grupy zasobów.
   * **Lokalizacja grupy zasobów**: Usługa Traffic Manager jest globalna i niepowiązana do lokalizacji. Jednakże należy określić region dla grupy, w którym znajduje się metadane skojarzone z profilem usługi Traffic Manager. Ta lokalizacja nie ma wpływu na dostępność profilu w czasie wykonywania.

3. Kliknij przycisk **Utwórz** do generowania profilu usługi Traffic Manager.

   ![Blok "Tworzenie usługi Traffic Manager"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Tworzenie bramy aplikacji

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **Utwórz zasób** > **sieć** > **Application Gateway**.
2. Wprowadź następujące podstawowe informacje na temat bramy application gateway:

   * **Nazwa**: Nazwa bramy aplikacji.
   * **Rozmiar jednostki SKU**: Rozmiar bramy aplikacji, dostępne jako mały, średni lub duży.
   * **Liczba wystąpień**: Liczba wystąpień, wartość z zakresu od 2 do 10.
   * **Grupa zasobów**: Grupa zasobów, zawierającą bramy aplikacji. Może być istniejącą grupę zasobów lub do nowego.
   * **Lokalizacja**: Region bramy application Gateway, która jest w tej samej lokalizacji co grupa zasobów. Lokalizacja jest ważna, ponieważ sieć wirtualna i publiczny adres IP musi znajdować się w tej samej lokalizacji co brama.
3. Kliknij przycisk **OK**.
4. Definiowanie sieci wirtualnej, podsieć, adres IP frontonu i konfiguracji odbiornika dla usługi application gateway. W tym scenariuszu jest adres IP frontonu **publicznych**, co umożliwia ma zostać dodany jako punkt końcowy do profilu usługi Traffic Manager później.
5. Konfigurowanie odbiornika z jednym z następujących opcji:
    * W przypadku protokołu HTTP nie ma nic do skonfigurowania. Kliknij przycisk **OK**.
    * Jeśli używasz protokołu HTTPS, dodatkowo jest wymagana konfiguracja. Zapoznaj się [utworzyć bramę aplikacji](../application-gateway/application-gateway-create-gateway-portal.md), rozpoczynając od kroku 9. Po zakończeniu konfiguracji kliknij **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurowanie routingu adresów URL dla bramy application Gateway

Po wybraniu puli zaplecza, bramę aplikacji, który jest skonfigurowany z regułą opartego na ścieżkach przyjmuje wzorzec ścieżki adresu URL żądania, oprócz rozdzielanie. W tym scenariuszu dodajemy opartego na ścieżkach reguły do kierowania dowolnego adresu URL za pomocą "/images/\*" do puli serwerów obrazu. Aby uzyskać więcej informacji na temat konfigurowania adresu URL opartego na ścieżkach routingu dla usługi application gateway dotyczą [utworzyć regułę bazującą na ścieżce dla bramy aplikacji](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram warstwy internetowej bramy aplikacji](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Z grupy zasobów przejdź do wystąpienia usługi application gateway, który został utworzony w poprzedniej sekcji.
2. W obszarze **ustawienia**, wybierz opcję **pule zaplecza**, a następnie wybierz pozycję **Dodaj** można dodać maszyny wirtualne, które chcesz skojarzyć z pulami zaplecza warstwa sieci web.
3. Wprowadź nazwę puli zaplecza i wszystkie adresy IP maszyn, które znajdują się w puli. W tym scenariuszu połączono dwóch pul serwerów zaplecza maszyn wirtualnych.

   ![Usługa Application Gateway "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. W obszarze **ustawienia** bramy application Gateway wybierz **reguły**, a następnie kliknij przycisk **oparty na ścieżkach** przycisk, aby dodać regułę.

   ![Przycisk "Path na podstawie" reguł bramy aplikacji](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Skonfiguruj regułę, podając następujące informacje.

   Ustawienia podstawowe:

   + **Nazwa**: Przyjazna nazwa reguły, który jest dostępny w portalu.
   + **Odbiornik**: Odbiornik, który jest używany dla tej reguły.
   + **Domyślna pula zaplecza**: Pula zaplecza ma być używany z reguły domyślnej.
   + **Domyślne ustawienia HTTP**: Ustawienia HTTP, który ma być używany z reguły domyślnej.

   Reguły na podstawie ścieżki:

   + **Nazwa**: Przyjazna nazwa reguły opartego na ścieżkach.
   + **Ścieżki**: Reguła ścieżki, która jest używana do przesyłania ruchu.
   + **Pula zaplecza**: Pula zaplecza, która ma być używany z tą regułą.
   + **Ustawienie protokołu HTTP**: Ustawienia protokołu HTTP, który ma być używany z tą regułą.

   > [!IMPORTANT]
   > Ścieżki: Musi rozpoczynać się prawidłowe ścieżki "/". Symbol wieloznaczny "\*" jest dozwolona tylko na końcu. Przykładami są /xyz, /xyz\*, lub /xyz/\*.

   ![Blok "Dodaj regułę bazującą na ścieżce" bramy aplikacji](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Dodawanie bramy application Gateway z punktami końcowymi usługi Traffic Manager

W tym scenariuszu usługi Traffic Manager jest podłączony do bramy aplikacji (zgodnie z konfiguracją w poprzednich krokach), które znajdują się w różnych regionach. Teraz, gdy skonfigurowano bram aplikacji, następnym krokiem jest podłącz je do profilu usługi Traffic Manager.

1. Otwórz swój profil usługi Traffic Manager. Aby to zrobić, Szukaj w grupie zasobów lub wyszukaj nazwę profilu usługi Traffic Manager z **wszystkie zasoby**.
2. W okienku po lewej stronie wybierz **punktów końcowych**, a następnie kliknij przycisk **Dodaj** Dodawanie punktu końcowego.

   ![Przycisk usługi Traffic Manager punkty końcowe "Dodaj"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Utworzenie punktu końcowego, wprowadzając następujące informacje:

   * **Typ**: Wybierz typ punktu końcowego w celu równoważenia obciążenia. W tym scenariuszu wybierz **punkt końcowy platformy Azure** ponieważ połączono go z wystąpieniami bramy aplikacji, które zostały wcześniej skonfigurowane.
   * **Nazwa**: Wprowadź nazwę punktu końcowego.
   * **Typ zasobu docelowego**: Wybierz **publiczny adres IP** a następnie w obszarze **zasób docelowy**, wybierz publiczny adres IP bramy aplikacji, który został wcześniej skonfigurowany.

   ![Usługa Traffic Manager "Dodaj punkt końcowy"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Teraz możesz przetestować konfigurację, uzyskując dostęp do usłudze DNS profilu usługi Traffic Manager (w tym przykładzie: TrafficManagerScenario.trafficmanager.net). Możesz ponownie wysłać żądania, wyświetlenie lub obniżyć maszyn wirtualnych i serwerów sieci web, które zostały utworzone w różnych regionach i zmieniać ustawień profilu usługi Traffic Manager, aby przetestować konfigurację.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Tworzenie modułu równoważenia obciążenia

W tym scenariuszu moduł równoważenia obciążenia dystrybuuje połączeń z warstwy internetowej do bazy danych w ramach klastra o wysokiej dostępności.

Jeśli klaster o wysokiej dostępności bazy danych korzysta z funkcji AlwaysOn programu SQL Server, zapoznaj się [skonfigurować co najmniej jeden zawsze na odbiorników grup dostępności](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) instrukcje krok po kroku.

Aby uzyskać więcej informacji na temat konfigurowania wewnętrznego modułu równoważenia obciążenia, zobacz [tworzenia wewnętrznego modułu równoważenia obciążenia w witrynie Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia**.
2. Wybierz nazwę dla modułu równoważenia obciążenia.
3. Ustaw **typu** do **wewnętrzne**i wybierz odpowiednią sieć wirtualną i podsieć dla równoważenia obciążenia, znajdować się w.
4. W obszarze **przypisywania adresów IP**, wybierz opcję **dynamiczne** lub **statyczne**.
5. W obszarze **grupy zasobów**, wybierz grupę zasobów dla modułu równoważenia obciążenia.
6. W obszarze **lokalizacji**, wybierz odpowiedni region dla modułu równoważenia obciążenia.
7. Kliknij przycisk **Utwórz** do generowania modułu równoważenia obciążenia.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Połącz warstwy bazy danych zaplecza do modułu równoważenia obciążenia

1. Z grupy zasobów należy odnaleźć modułu równoważenia obciążenia, który został utworzony w poprzednich krokach.
2. W obszarze **ustawienia**, kliknij przycisk **pule zaplecza**, a następnie kliknij przycisk **Dodaj** można dodać do puli zaplecza.

   ![Moduł równoważenia obciążenia "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Wprowadź nazwę puli zaplecza.
4. Dodaj poszczególnych maszyn lub zestaw dostępności, aby puli zaplecza.

#### <a name="configure-a-probe"></a>Konfigurowanie sondy

1. W moduł równoważenia obciążenia w obszarze **ustawienia**, wybierz opcję **sondy**, a następnie kliknij przycisk **Dodaj** Aby dodać sondy.

   ![Moduł równoważenia obciążenia "Dodawanie sondy"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Wprowadź nazwę dla sondy.
3. Wybierz **protokołu** sondowania. Dla bazy danych możesz zechcieć sonda TCP, a nie w przypadku sondy HTTP. Aby dowiedzieć się więcej na temat sondy modułu równoważenia obciążenia, zobacz [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md).
4. Wprowadź **portu** bazy danych ma być używany do uzyskiwania dostępu do sondy.
5. W obszarze **interwał**, określ częstotliwość sondowania aplikacji.
6. W obszarze **próg złej kondycji**, określ liczbę niepowodzeń sondy ciągłego, które musi wystąpić dla maszyny Wirtualnej zaplecza zostały uznane za nieprawidłowe.
7. Kliknij przycisk **OK** Aby utworzyć sondę.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurowanie reguł równoważenia obciążenia

1. W obszarze **ustawienia** modułu równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj** można utworzyć regułę.
2. Wprowadź **nazwa** reguły równoważenia obciążenia.
3. Wybierz **adresu IP frontonu** modułu równoważenia obciążenia **protokołu**, i **portu**.
4. W obszarze **port zaplecza**, określ numer portu do użycia w puli zaplecza.
5. Wybierz **puli zaplecza** i **sondowania** utworzone w poprzednich krokach, aby zastosować regułę do.
6. W obszarze **trwałości sesji**, wybierz sposób trwałości sesji.
7. W obszarze **przekroczeń limitu czasu bezczynności**, określ liczbę minut przed upływem limitu czasu bezczynności.
8. W obszarze **pływającego adresu IP**, wybierz opcję **wyłączone** lub **włączone**.
9. Kliknij przycisk **OK**, aby utworzyć regułę.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5. Łączenie maszyn wirtualnych warstwy internetowej do modułu równoważenia obciążenia

Teraz możemy skonfigurować IP adres i modułu równoważenia obciążenia port frontonu w aplikacji, które są uruchomione na maszynach wirtualnych warstwy internetowej dla każdego połączenia bazy danych. Ta konfiguracja jest specyficzne dla aplikacji uruchamianych na tych maszynach wirtualnych. Aby skonfigurować docelowy adres IP i port, można znaleźć w dokumentacji aplikacji. Aby znaleźć adres IP frontonu, w witrynie Azure portal, przejdź do puli adresów IP frontonu na **ustawienia usługi równoważenia obciążenia**.

![Okienko nawigacji "Puli adresów IP frontonu" moduł równoważenia obciążenia](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Traffic Manager](traffic-manager-overview.md)
* [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)
* [Omówienie usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md)
