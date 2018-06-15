---
title: Przy użyciu usługi równoważenia obciążenia na platformie Azure | Dokumentacja firmy Microsoft
description: 'W tym samouczku przedstawiono sposób tworzenia scenariusza za pomocą usługi Azure portfolio równoważenia obciążenia: Traffic Manager, bramę aplikacji i moduł równoważenia obciążenia.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: vitinnan
editor: ''
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 86867a9d6d2c43e6505b1a06672546a017172bfe
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401110"
---
# <a name="using-load-balancing-services-in-azure"></a>Przy użyciu usługi równoważenia obciążenia na platformie Azure

## <a name="introduction"></a>Wprowadzenie

Microsoft Azure udostępnia wiele usług do zarządzania ruchem w sieci i równoważenia obciążenia. Możesz użyć tych usług indywidualnie lub łączenie ich metod w zależności od potrzeb optymalnego rozwiązania.

W tym samouczku, możemy najpierw zdefiniować przypadek użycia klienta i wyświetlić sposób można go było bardziej niezawodne i wydajności za pomocą następujących Azure portfolio równoważenia obciążenia: Traffic Manager, bramę aplikacji i moduł równoważenia obciążenia. Następnie udostępniamy instrukcje tworzenia wdrożenia, które jest geograficznie nadmiarowy, dystrybuuje ruch do maszyn wirtualnych i pomaga w zarządzaniu różnych typów żądań.

Na poziomie pojęciach każdy z tych usług pełni rolę distinct w hierarchii równoważenia obciążenia.

* **Menedżer ruchu** zapewnia DNS globalnego równoważenia obciążenia. Analizuje przychodzące żądania DNS, a odpowiada dobrej kondycji punktu końcowego, zgodnie z zasadami routingu, wybranych przez klienta. Dostępne są następujące opcje dla metody routingu:
  * Wydajność routingu, aby wysłać obiekt żądający do najbliższego punktu końcowego pod względem opóźnienia.
  * Priorytet routingu, aby kierować cały ruch do punktu końcowego z innych punktów końcowych na kopii zapasowej.
  * Ważone działanie okrężne routingu, która dystrybuuje ruch według wagi, która jest przypisana do każdego punktu końcowego.

  Klient łączy się bezpośrednio do tego punktu końcowego. Menedżer ruchu Azure wykrywa punkt końcowy jest zła, a następnie przekierowuje klientów do innego wystąpienia dobrej kondycji. Zapoznaj się [dokumentacji usługi Azure Traffic Manager](traffic-manager-overview.md) Aby dowiedzieć się więcej o usłudze.
* **Brama aplikacji w** zawiera kontroler dostarczania aplikacji (ADC) jako usługa, oferty różnych warstwy 7 Równoważenie obciążenia sieciowego funkcji aplikacji. Umożliwia ona klientom zoptymalizować wydajność kolektywu serwerów sieci web dzięki przeniesieniu kończenia żądań SSL użycie Procesora CPU na bramie aplikacji. Inne funkcje routingu warstwy 7 obejmują rozdzielanie ruchu przychodzącego, koligacji na podstawie plików cookie sesji, routingu adresów URL na podstawie ścieżki i może obsługiwać wiele witryn sieci Web za bramy pojedynczej aplikacji. Brama aplikacji można skonfigurować jako bramy łączącej z Internetem, bramę wyłącznie wewnętrznym lub obie te grupy. Brama aplikacji jest w pełni Azure zarządzanych, skalowalności i wysokiej dostępności. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
* **Moduł równoważenia obciążenia** jest integralną częścią stosu Azure SDN udostępnia wysokiej wydajności i małych opóźnieniach 4 warstwy równoważenia obciążenia usługi do wszystkich protokołów UDP i TCP. Zarządza połączeń przychodzących i wychodzących. Można skonfigurować publicznych oraz wewnętrznych końcowych z równoważeniem obciążenia i zdefiniuj reguły mapowania połączenia przychodzące do miejsc docelowych puli zaplecza przy użyciu opcji TCP i HTTP badania kondycji dostępności usługi zarządzania.

## <a name="scenario"></a>Scenariusz

W tym przykładowym scenariuszu używamy prosty witryny sieci Web, który obsługuje dwa typy zawartości: obrazów i dynamicznie renderowanych stronach sieci Web. Witryna sieci Web musi być geograficznie nadmiarowy, a powinien służyć swoim użytkownikom z najbliższego (najniższym opóźnieniu) lokalizacji do nich. Deweloper aplikacji zdecydował, że wszystkie adresy URL zgodne ze wzorcem/obrazów / * są obsługiwane z dedykowanym puli maszyn wirtualnych, które różnią się od reszty kolektywu serwerów sieci web.

Ponadto domyślna pula maszyna wirtualna obsługująca zawartość dynamiczną musi komunikować się do wewnętrznej bazy danych, który znajduje się w klastrze wysokiej dostępności. Całe wdrożenie jest skonfigurowane za pomocą usługi Azure Resource Manager.

Przy użyciu usługi Traffic Manager, bramę aplikacji i usługi równoważenia obciążenia umożliwia tej witryny sieci Web na osiągnięcie tych celów projektu:

* **Nadmiarowość geograficzna Multi**: Jeśli jeden region ulegnie awarii, Traffic Manager kieruje ruchem bezproblemowo najbliżej regionu bez żadnej interwencji z właścicielem aplikacji.
* **Mniejsze opóźnienia**: ponieważ Traffic Manager automatycznie poleca klientowi najbliżej regionu, klient napotyka mniejsze opóźnienia podczas żądania zawartości strony sieci Web.
* **Skalowalność niezależne**: ponieważ obciążenia aplikacji sieci web jest oddzielona typu zawartości, właściciel aplikacji można skalować obciążeń żądania od siebie niezależne. Brama aplikacji w gwarantuje, że ruch jest kierowany do prawej pul na podstawie określonej reguły i kondycji tej aplikacji.
* **Równoważenie obciążenia wewnętrznego**: ponieważ moduł równoważenia obciążenia jest przed klastra o wysokiej dostępności, tylko aktywne i dobrej kondycji punktu końcowego dla bazy danych jest uwidaczniany w aplikacji. Ponadto administrator bazy danych można zoptymalizować obciążenie przez dystrybucję replik aktywnym i pasywnym niezależnie od aplikacji frontonu klastra. Moduł równoważenia obciążenia zapewnia połączenia klastra o wysokiej dostępności i zapewnia tylko dobrej kondycji bazy danych żądania połączenia.

Na poniższym diagramie przedstawiono architektury tego scenariusza:

![Diagram równoważenia obciążenia architektury](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> W tym przykładzie jest tylko jeden z wielu możliwych konfiguracji usługi równoważenia obciążenia, które platforma Azure oferuje. Menedżera ruchu, brama aplikacji w moduł równoważenia obciążenia mogą być łączone i dopasować zależności potrzeb równoważenia obciążenia. Na przykład jeśli odciążanie protokołu SSL lub przetwarzania warstwy 7 nie jest konieczne, usługi równoważenia obciążenia można użyć zamiast Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurowanie równoważenia obciążenia stosu

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1: Tworzenie profilu Menedżera ruchu

1. W portalu Azure kliknij **Utwórz zasób** > **sieci** > **profilu usługi Traffic Manager**  >   **Utwórz**.
2. Wprowadź następujące informacje podstawowe:

  * **Nazwa**: należy podać nazwę prefiks DNS profilu Menedżera ruchu.
  * **Metoda routingu**: Wybierz zasady metody routingu ruchu. Aby uzyskać więcej informacji na temat metod, zobacz [Traffic Manager metody routingu ruchu](traffic-manager-routing-methods.md).
  * **Subskrypcja**: Wybierz subskrypcję, która zawiera profil.
  * **Grupa zasobów**: Wybierz grupę zasobów, który zawiera profil. Może być grupą nowy lub istniejący zasób.
  * **Lokalizacja grupy zasobów**: Usługa Menedżera ruchu jest globalna i niepowiązana do lokalizacji. Należy jednak określić region grupy, w której znajduje się metadane skojarzone z profilem Menedżera ruchu. Ta lokalizacja nie ma wpływu na dostępność środowiska uruchomieniowego profilu.

3. Kliknij przycisk **Utwórz** do generowania profil Menedżera ruchu.

  ![Blok "Tworzenie Menedżera ruchu"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2: Tworzenie bramy aplikacji

1. W portalu Azure, w okienku po lewej stronie kliknij **Utwórz zasób** > **sieci** > **brama aplikacji w**.
2. Wprowadź poniższe podstawowe informacje o bramy aplikacji:

  * **Nazwa**: Nazwa bramy aplikacji.
  * **Rozmiar jednostki SKU**: rozmiar bramy aplikacji jest dostępna jako mały, średni lub duży.
  * **Liczba wystąpień**: liczba wystąpień, wartość z zakresu od 2 do 10.
  * **Grupa zasobów**: grupy zasobów, która przechowuje bramy aplikacji. Można go istniejącą grupę zasobów lub do nowego.
  * **Lokalizacja**: region bramy aplikacji, która jest tej samej lokalizacji co grupa zasobów. Lokalizacja jest ważna, ponieważ sieć wirtualna i publiczny adres IP musi znajdować się w tej samej lokalizacji co brama.
3. Kliknij przycisk **OK**.
4. Definiowanie sieci wirtualnej, podsieci IP frontonu i konfiguracji odbiornika dla bramy aplikacji. W tym scenariuszu jest adres IP frontonu **publicznych**, dzięki czemu ma zostać dodany jako punkt końcowy profilu Menedżera ruchu na dalszym etapie.
5. Konfigurowanie odbiornika z jednym z następujących opcji:
    * Jeśli używasz protokołu HTTP nie ma nic do skonfigurowania. Kliknij przycisk **OK**.
    * Jeśli jest używany protokół HTTPS, konfiguracji są wymagane dodatkowe. Zapoznaj się [Utwórz bramę aplikacji](../application-gateway/application-gateway-create-gateway-portal.md), rozpoczynając od kroku 9. Po zakończeniu konfiguracji kliknij **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurowanie adresu URL routingu dla bramy aplikacji

Po wybraniu puli zaplecza bramy aplikacji, który jest skonfigurowany przy użyciu reguły na podstawie ścieżki trwa wzorzec ścieżki adresu URL żądania, oprócz rozdzielanie. W tym scenariuszu dodajemy regułę ścieżki bezpośrednie dowolny adres URL z "/images/\*" do puli serwerów obrazu. Aby uzyskać więcej informacji na temat konfigurowania adresu URL na podstawie ścieżki routingu dla bramy aplikacji dotyczą [Tworzenie reguły na podstawie ścieżki dla bramy aplikacji](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram warstwa sieci web bramy aplikacji](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. W grupie zasobów przejdź do wystąpienia bramy aplikacji, który został utworzony w poprzedniej sekcji.
2. W obszarze **ustawienia**, wybierz pozycję **pul zaplecza**, a następnie wybierz **Dodaj** można dodać maszyn wirtualnych, które chcesz skojarzyć z pul zaplecza warstwa sieci web.
3. Wprowadź nazwę puli zaplecza i adresów IP komputerów, które znajdują się w puli. W tym scenariuszu połączono dwie pule serwera zaplecza maszyn wirtualnych.

  ![Brama aplikacji w "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. W obszarze **ustawienia** bramy aplikacji wybierz **reguły**, a następnie kliknij przycisk **opartych na ścieżce** przycisk, aby dodać regułę.

  ![Przycisk "Na podstawie ścieżki" reguły bramy aplikacji](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Skonfiguruj regułę, podając następujące informacje.

   Podstawowe ustawienia:

   + **Nazwa**: przyjazną nazwę reguły, który jest dostępny w portalu.
   + **Odbiornik**: odbiornik, który jest używany dla reguły.
   + **Domyślna pula zaplecza**: puli zaplecza do użycia z reguły domyślnej.
   + **Domyślne ustawienia HTTP**: ustawienia używane z regułą domyślnego protokołu HTTP.

   Ścieżka reguły:

   + **Nazwa**: przyjazną nazwę reguły na podstawie ścieżki.
   + **Ścieżki**: reguły ścieżki, która jest używana do przesyłania ruchu.
   + **Puli zaplecza**: puli zaplecza do użycia z tą regułą.
   + **Ustawienie HTTP**: ustawienia protokołu HTTP do użycia z tą regułą.

   > [!IMPORTANT]
   > Ścieżki: Ścieżkach prawidłowy musi rozpoczynać się od "/". Symbol wieloznaczny "\*" jest dozwolona tylko na końcu. Nieprawidłowa przykładów /xyz, /xyz\*, lub /xyz/\*.

   ![Blok "Dodaj ścieżkę na podstawie reguły" bramy aplikacji](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3: Dodawanie bramy aplikacji do punktów końcowych usługi Traffic Manager

W tym scenariuszu Menedżera ruchu jest podłączony do bramy aplikacji (zgodnie z konfiguracją w poprzednim kroku), które znajdują się w różnych regionach. Teraz, gdy skonfigurowano bram aplikacji, następnym krokiem jest podłącz je do profilu Menedżera ruchu.

1. Otwórz profil Menedżera ruchu. Aby to zrobić, Szukaj w grupie zasobów lub wyszukiwanie nazwy profilu Menedżera ruchu z **wszystkie zasoby**.
2. W okienku po lewej stronie wybierz **punkty końcowe**, a następnie kliknij przycisk **Dodaj** Dodawanie punktu końcowego.

  ![Przycisk usługi Traffic Manager punkty końcowe "Dodaj"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Tworzenie punktu końcowego, wprowadzając następujące informacje:

  * **Typ**: Wybierz typ punktu końcowego do równoważenia obciążenia. W tym scenariuszu wybierz **punktu końcowego platformy Azure** ponieważ połączono go do wystąpień bramy aplikacji, które zostały wcześniej skonfigurowane.
  * **Nazwa**: Wprowadź nazwę punktu końcowego.
  * **Typ zasobu docelowy**: Wybierz **publicznego adresu IP** , a następnie w obszarze **zasób docelowy**, wybierz publiczny adres IP bramy aplikacji, który został wcześniej skonfigurowany.

   ![Menedżera ruchu "Dodaj punkt końcowy"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Teraz możesz przetestować konfigurację dostępu do niego profil Menedżera ruchu sieciowego w systemie DNS (w tym przykładzie: TrafficManagerScenario.trafficmanager.net). Można ponownie wysłać żądania, wywołaj lub doprowadzić do maszyn wirtualnych i serwery sieci web, które zostały utworzone w różnych regionach i zmienić ustawienia profilu Menedżera ruchu, aby przetestować konfigurację.

### <a name="step-4-create-a-load-balancer"></a>Krok 4: Tworzenie usługi równoważenia obciążenia

W tym scenariuszu usługi równoważenia obciążenia dystrybuuje połączenia z warstwą sieci web do baz danych w ramach klastra o wysokiej dostępności.

Jeśli klaster wysokiej dostępności bazy danych używa funkcji AlwaysOn programu SQL Server, zapoznaj się [skonfigurować jeden lub więcej zawsze na dostępność odbiorniki grupy](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) instrukcje krok po kroku.

Aby uzyskać więcej informacji na temat konfigurowania wewnętrznego modułu równoważenia obciążenia, zobacz [utworzyć wewnętrznego modułu równoważenia obciążenia w portalu Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. W portalu Azure, w okienku po lewej stronie kliknij **Utwórz zasób** > **sieci** > **modułu równoważenia obciążenia**.
2. Wybierz nazwę dla Twojej usługi równoważenia obciążenia.
3. Ustaw **typu** do **wewnętrzne**i wybierz odpowiednią sieć wirtualna i podsieć do znajdują się w usłudze równoważenia obciążenia.
4. W obszarze **przypisywania adresów IP**, wybierz opcję **dynamiczne** lub **statycznych**.
5. W obszarze **grupy zasobów**, wybierz grupę zasobów dla usługi równoważenia obciążenia.
6. W obszarze **lokalizacji**, wybierz odpowiedni region dla usługi równoważenia obciążenia.
7. Kliknij przycisk **Utwórz** do generowania równoważenia obciążenia.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Połącz warstwy bazy danych zaplecza modułu równoważenia obciążenia

1. Z grupy zasobów Znajdź usługę równoważenia obciążenia, który został utworzony w poprzednich krokach.
2. W obszarze **ustawienia**, kliknij przycisk **pul zaplecza**, a następnie kliknij przycisk **Dodaj** można dodać puli zaplecza.

  ![Moduł równoważenia obciążenia "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Wprowadź nazwę puli zaplecza.
4. Dodaj poszczególnych maszyn lub dostępności ustawioną puli zaplecza.

#### <a name="configure-a-probe"></a>Skonfiguruj sondę

1. W moduł równoważenia obciążenia w obszarze **ustawienia**, wybierz pozycję **sondy**, a następnie kliknij przycisk **Dodaj** Aby dodać badanie.

 ![Moduł równoważenia obciążenia "Dodaj sondy"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Wprowadź nazwę dla sondy.
3. Wybierz **protokołu** sondy. Dla bazy danych może być sondowaniem TCP, a nie badania HTTP. Aby dowiedzieć się więcej na temat sondy modułu równoważenia obciążenia, zobacz [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md).
4. Wprowadź **portu** bazy danych używanego do uzyskiwania dostępu do sondowania.
5. W obszarze **interwał**, określ częstotliwość sondowania aplikacji.
6. W obszarze **próg złej kondycji**, określ liczbę ciągłego niepowodzeń sondy, musi nastąpić dla zaplecza maszyna wirtualna może być określana jako zła.
7. Kliknij przycisk **OK** można utworzyć sondy.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurowanie reguł równoważenia obciążenia

1. W obszarze **ustawienia** moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj** do utworzenia reguły.
2. Wprowadź **nazwa** reguły równoważenia obciążenia.
3. Wybierz **adresu IP frontonu** usługi równoważenia obciążenia **protokołu**, i **portu**.
4. W obszarze **portu zaplecza**, określ port używany w puli zaplecza.
5. Wybierz **puli zaplecza** i **sondowania** utworzonych w poprzednie kroki, aby zastosować regułę.
6. W obszarze **trwałości sesji**, wybierz sposób sesji do innej witryny.
7. W obszarze **limitów czasu bezczynności**, określ liczbę minut przed upływem limitu czasu bezczynności.
8. W obszarze **pływający adres IP**, wybierz opcję **wyłączone** lub **włączone**.
9. Kliknij przycisk **OK**, aby utworzyć regułę.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5: Nawiązać połączenia z maszynami wirtualnymi warstwy sieci web usługi równoważenia obciążenia

Teraz możemy skonfigurować adres i modułu równoważenia obciążenia frontonu portu IP w aplikacjach, które są uruchomione na maszyny wirtualne warstwy sieci web dla połączeń bazy danych. Ta konfiguracja jest specyficzne dla aplikacji działających na tych maszynach wirtualnych. Aby skonfigurować docelowy adres IP i port, zajrzyj do dokumentacji aplikacji. Aby znaleźć adres IP frontonu, w portalu Azure, przejdź do puli adresów IP frontonu **ustawienia usługi równoważenia obciążenia**.

![Okienko nawigacji "Adresu IP frontonu puli" moduł równoważenia obciążenia](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie Menedżera ruchu](traffic-manager-overview.md)
* [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)
* [Omówienie usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md)
