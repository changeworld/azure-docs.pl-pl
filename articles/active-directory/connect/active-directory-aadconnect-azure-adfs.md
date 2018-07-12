---
title: Usługi Active Directory Federation Services na platformie Azure | Microsoft Docs
description: W tym dokumencie omówiono procedurę wdrożenia usług AD FS na platformie Azure w celu zapewnienia wysokiej dostępności.
keywords: wdrażanie usług AD FS na platformie Azure, wdrażanie azure adfs, azure adfs, azure ad fs, wdrażanie adfs, wdrażanie ad fs, adfs w azure, wdrażanie adfs w azure, wdrażanie usług AD FS na platformie azure, adfs azure, wprowadzenie do usług AD FS, Azure, wprowadzenie do usług AD FS na platformie Azure, iaas, ADFS, przenoszenie adfs do azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2ebe6c7a70e4e574ea4953ca9ed01801190f80e
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917139"
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Wdrażanie usług Active Directory Federation Services na platformie Azure
Usługi AD FS udostępniają uproszczone, zabezpieczone funkcje federacji tożsamości i logowania jednokrotnego (SSO) w sieci Web. Federacja z usługą Azure AD lub O365 umożliwia użytkownikom uwierzytelnianie się przy użyciu poświadczeń lokalnych i uzyskiwanie dostępu do wszystkich zasobów w chmurze. Tym samym ważne staje się zapewnienie infrastruktury usług AD FS o wysokiej dostępności, która gwarantuje dostęp zarówno do zasobów lokalnych, jak i przechowywanych w chmurze. Wdrożenie usług AD FS na platformie Azure może pomóc w osiągnięciu wymaganej wysokiej dostępności w prosty sposób.
Wdrożenie usług AD FS na platformie Azure niesie ze sobą szereg korzyści, takich jak na przykład:

* **Wysoka dostępność** — zaawansowane możliwości zapewniane przez zestawy dostępności Azure gwarantują wysoką dostępność infrastruktury.
* **Łatwe skalowanie** — chcesz zwiększyć wydajność? Platforma Azure pozwala łatwo przeprowadzić migrację na bardziej wydajne maszyny.
* **Nadmiarowość geograficzna między lokalizacjami** — nadmiarowość geograficzna platformy Azure zapewnia wysoką dostępność infrastruktury na całym świecie.
* **Łatwe zarządzanie** — opcje zarządzania w witrynie Azure Portal są bardzo uproszczone, co sprawia, że zarządzanie infrastrukturą jest bardzo łatwe i bezproblemowe. 

## <a name="design-principles"></a>Zasady projektowania
![Projekt wdrożenia](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Na powyższym diagramie przedstawiono zalecaną podstawową topologię umożliwiającą rozpoczęcie wdrażania infrastruktury usług AD FS na platformie Azure. Poniżej przedstawiono zasady dotyczące różnych składników tej topologii:

* **Kontrolery domeny (DC) / serwery usług AD FS**: jeśli liczba użytkowników nie przekracza 1000, można po prostu zainstalować rolę usług AD FS na kontrolerach domeny. Jeśli liczba użytkowników przekracza 1000 lub konieczne jest wyeliminowanie wszelkich wpływów na wydajność kontrolerów domeny, usługi AD FS należy wdrożyć na oddzielnych serwerach.
* **Serwer proxy aplikacji internetowej (WAP)** — konieczne jest wdrożenie serwerów proxy aplikacji internetowych w celu umożliwienia użytkownikom korzystania z usług AD FS również spoza sieci firmowej.
* **Strefa DMZ**: serwery proxy aplikacji internetowej zostaną umieszczone w strefie DMZ. Komunikacja między strefą DMZ i podsiecią wewnętrzną jest możliwa TYLKO przez port TCP 443.
* **Moduły równoważenia obciążenia**: aby zapewnić wysoką dostępność usług AD FS i serwerów proxy aplikacji internetowych, zaleca się użycie wewnętrznego modułu równoważenia obciążenia dla serwerów usług AD FS oraz usługi Azure Load Balancer dla serwerów proxy aplikacji internetowych.
* **Zestawy dostępności**: aby zapewnić nadmiarowość wdrożenia usług AD FS, zaleca się umieszczenie co najmniej dwóch maszyn wirtualnych w zestawie dostępności w celu uzyskania podobnych obciążeń. Taka konfiguracja zapewnia dostępność co najmniej jednej maszyny wirtualnej podczas planowanych i nieplanowanych zdarzeń związanych z konserwacją.
* **Konta magazynu**: zaleca się korzystanie z dwóch kont magazynu. Korzystanie z jednego konta magazynu może prowadzić do utworzenia pojedynczego punktu awarii. Jeśli konto magazynu przestanie funkcjonować (w mało prawdopodobnym scenariuszu), wdrożenie stanie się niedostępne. Użycie dwóch kont magazynu pozwala powiązać każde konto z linią awarii.
* **Separacja sieci**: serwery proxy aplikacji internetowych powinny zostać wdrożone w oddzielnej sieci DMZ. Sieć wirtualną można podzielić na dwie odizolowane podsieci, a następnie wdrożyć w nich serwery proxy aplikacji internetowej. Dla każdej podsieci można po prostu skonfigurować ustawienia sieciowej grupy zabezpieczeń, zezwalając tylko na wymaganą komunikację między tymi podsieciami. Więcej szczegółów podano w poniższych scenariuszach wdrażania.

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Kroki umożliwiające wdrożenie usług AD FS na platformie Azure
Kroki zawarte w tej sekcji służą jako przewodnik umożliwiający wdrożenie opisanej poniżej infrastruktury usług AD FS na platformie Azure.

### <a name="1-deploying-the-network"></a>1. Wdrażanie sieci
Zgodnie z powyższymi informacjami można utworzyć dwie podsieci w jednej sieci wirtualnej lub skonfigurować dwie całkowicie odrębne sieci wirtualne. W tym artykule omówiono wariant obejmujący wdrożenie jednej sieci wirtualnej, która zostanie podzielona na dwie podsieci. Takie podejście jest prostsze, ponieważ użycie dwóch sieci wirtualnych wymagałoby zastosowania bramy umożliwiającej komunikację.

**1.1 Tworzenie sieci wirtualnej**

![Tworzenie sieci wirtualnej](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

W witrynie Azure Portal wybierz sieć wirtualną. W bardzo prosty sposób możesz wdrożyć sieć wirtualną z jedną podsiecią. Podsieć wewnętrzna jest również zdefiniowana i gotowa do dodania maszyn wirtualnych.
Następnym krokiem jest dodanie podsieci DMZ. Aby utworzyć podsieć DMZ, wystarczy wykonać następujące czynności:

* Wybierz nowo utworzoną sieć.
* W obszarze właściwości wybierz pozycję Podsieć.
* W panelu podsieci kliknij przycisk dodawania.
* Podaj nazwę podsieci i informacje dotyczące przestrzeni adresowej w celu utworzenia podsieci.

![Podsieć](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![Podsieć DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Tworzenie sieciowych grup zabezpieczeń**

Sieciowa grupa zabezpieczeń (NSG, Network security group) zawiera listę reguł listy kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci.
Na potrzeby tego przewodnika zostaną utworzone dwie sieciowe grupy zabezpieczeń: dla sieci wewnętrznej i podsieci DMZ. Zostaną one odpowiednio oznaczone: NSG_INT i NSG_DMZ.

![Tworzenie sieciowej grupy zabezpieczeń](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Utworzona sieciowa grupa zabezpieczeń nie zawiera żadnych reguł ruchu przychodzącego ani reguł ruchu wychodzącego. Gdy zaczną działać role zainstalowane na poszczególnych serwerach, można przystąpić do konfigurowania reguł ruchu przychodzącego i wychodzącego zgodnie z odpowiednim poziomem zabezpieczeń.

![Inicjowanie sieciowej grupy zabezpieczeń](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Po utworzeniu sieciowych grup zabezpieczeń skojarz grupę NSG_INT z podsiecią INT, a grupę NSG_DMZ z podsiecią DMZ. Poniżej znajduje się przykładowy zrzut ekranu:

![Konfigurowanie sieciowej grupy zabezpieczeń](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Kliknij pozycję Podsieci, aby otworzyć panel podsieci
* Wybierz podsieć do skojarzenia z sieciową grupą zabezpieczeń 

Po wprowadzeniu zmian w konfiguracji panel podsieci powinien wyglądać tak jak poniżej:

![Podsieci skojarzone z sieciową grupą zabezpieczeń](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Tworzenie połączenia z infrastrukturą lokalną**

Aby wdrożyć kontroler domeny (DC) na platformie Azure, musisz utworzyć połączenie z infrastrukturą lokalną. Platforma Azure udostępnia różne opcje umożliwiające połączenie infrastruktury lokalnej z infrastrukturą Azure.

* Punkt-lokacja
* Lokacja-lokacja w sieci wirtualnej
* ExpressRoute

Zaleca się używanie usługi ExpressRoute. Usługa ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych Azure i infrastrukturą lokalną lub wspólnym środowiskiem. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Zapewniają one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.
Mimo że zaleca się korzystać z usługi ExpressRoute, można wybrać dowolny typ połączenia, dopasowany do potrzeb danej organizacji. Aby dowiedzieć się więcej na temat usługi ExpressRoute i różnych opcji łączności udostępnianych przez tę usługę, przeczytaj artykuł [ExpressRoute — opis techniczny](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Tworzenie kont magazynu
Aby utrzymać wysoką dostępność i uniknąć zależności od jednego konta magazynu, można utworzyć dwa konta magazynu. Maszyny znajdujące się w poszczególnych zestawach dostępności podziel na dwie grupy, a następnie przypisz do każdej grupy oddzielne konto magazynu.

![Tworzenie kont magazynu](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Tworzenie zestawów dostępności
Dla każdej roli (kontrolera domeny/serwera usług AD FS i serwera proxy aplikacji sieci Web) musisz utworzyć zestawy dostępności zawierające co najmniej po dwie maszyny. Pozwoli to osiągnąć większą dostępność poszczególnych ról. Tworząc zestawy dostępności, musisz uwzględnić parę zasadniczych kwestii:

* **Domeny błędów**: maszyny wirtualne w tej samej domenie błędów współużytkują źródło zasilania i fizyczny przełącznik sieciowy. Zaleca się korzystanie co najmniej z 2 domen błędów. Na potrzeby tego wdrożenia możesz pozostawić wartość domyślną równą 3.
* **Domeny aktualizacji**: maszyny w tej samej domenie aktualizacji będą ponownie uruchamiane razem podczas aktualizacji. Warto skonfigurować co najmniej 2 domeny aktualizacji. Na potrzeby tego wdrożenia możesz pozostawić wartość domyślną równą 5.

![Zestawy dostępności](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Utwórz następujące zestawy dostępności.

| Zestaw dostępności | Rola | Domeny błędów | Domeny aktualizacji |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4. Wdrażanie maszyn wirtualnych
Następnym krokiem jest wdrożenie maszyn wirtualnych, które będą hostować różne role w używanej infrastrukturze. Zaleca się wdrożenie co najmniej dwóch maszyn w każdym zestawie dostępności. Na potrzeby podstawowego wdrożenia zostaną utworzone cztery maszyny wirtualne.

| Maszyna | Rola | Podsieć | Zestaw dostępności | Konto magazynu | Adres IP |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |Statyczny |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |Statyczny |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |Statyczny |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |Statyczny |

Warto zauważyć, że nie określono sieciowej grupy zabezpieczeń. Wynika to z możliwości użycia sieciowej grupy zabezpieczeń na poziomie podsieci. Ruchem sieciowym maszyny możesz sterować przy użyciu określonej sieciowej grupy zabezpieczeń skojarzonej z podsiecią lub obiektem NIC. Więcej informacji można znaleźć w artykule [Co to jest grupa zabezpieczeń sieci](https://aka.ms/Azure/NSG)
Statyczny adres IP jest zalecany w przypadku samodzielnego zarządzania systemem DNS. Możesz również używać usługi Azure DNS. W takim przypadku w rekordach DNS dla domeny będziesz odwoływać się do nowych maszyn za pomocą nazw FQDN platformy Azure.
Po ukończeniu wdrażania okienko maszyn wirtualnych powinno wyglądać tak jak poniżej:

![Wdrożone maszyny wirtualne](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Konfigurowanie kontrolera domeny / serwerów usług AD FS
 W celu uwierzytelnienia dowolnego żądania przychodzącego wymagana jest komunikacja między usługami AD FS a kontrolerem domeny. Aby uniknąć kosztów związanych z przesyłaniem danych w ramach uwierzytelniania między platformą Azure a lokalnym kontrolerem domeny, zaleca się wdrożenie repliki kontrolera domeny na platformie Azure. W celu osiągnięcia wysokiej dostępności zalecane jest utworzenie zestawu dostępności obejmującego co najmniej dwa kontrolery domeny.

| Kontroler domeny | Rola | Konto magazynu |
|:---:|:---:|:---:|
| contosodc1 |Replika |contososac1 |
| contosodc2 |Replika |contososac2 |

* Podnieś poziom tych dwóch serwerów jako replika kontrolerów domeny w systemie DNS
* Skonfiguruj serwery usług AD FS, instalując rolę usług AD FS za pomocą Menedżera serwera.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. Wdrażanie wewnętrznego modułu równoważenia obciążenia (ILB)
**6.1. Tworzenie wewnętrznego modułu równoważenia obciążenia**

Aby wdrożyć wewnętrzny moduł równoważenia obciążenia, wybierz pozycję Moduły równoważenia obciążenia w witrynie Azure Portal i kliknij pozycję (+).

> [!NOTE]
> Jeśli nie widzisz polecenia **Moduły równoważenia obciążenia** w menu, kliknij pozycję **Przeglądaj** w lewym dolnym rogu portalu i przewijaj listę, aż zobaczysz polecenie **Moduły równoważenia obciążenia**.  Następnie kliknij żółtą gwiazdkę, aby dodać to polecenie do menu. Wybierz ikonę nowego modułu równoważenia obciążenia, aby otworzyć panel umożliwiający skonfigurowanie tego modułu.
> 
> 

![Przeglądanie modułu równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nazwa**: wpisz odpowiednią nazwę modułu równoważenia obciążenia
* **Schemat**: ponieważ ten moduł zostanie umieszczony przed serwerami usług AD FS i jest przeznaczony TYLKO na potrzeby połączeń sieci wewnętrznej, wybierz pozycję „Wewnętrzny”
* **Sieć wirtualna**: wybierz sieć wirtualną, w której wdrażasz usługi AD FS
* **Podsieć**: wybierz wewnętrzną podsieć
* **Przypisanie adresu IP**: Statyczne

![Wewnętrzny moduł równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Po kliknięciu pozycji Utwórz i wdrożeniu wewnętrznego modułu równoważenia obciążenia powinien on pojawić się na liście modułów równoważenia obciążenia:

![Moduły równoważenia obciążenia po skonfigurowaniu wewnętrznego modułu równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

Następnym krokiem jest skonfigurowanie puli zaplecza i sondy zaplecza.

**6.2. Konfigurowanie puli zaplecza wewnętrznego modułu równoważenia obciążenia**

Wybierz nowo utworzony wewnętrzny moduł równoważenia obciążenia na panelu Moduły równoważenia obciążenia. Zostanie otwarty panel ustawień. 

1. Wybierz pule zaplecza na panelu ustawień
2. Na panelu dodawania puli zaplecza kliknij pozycję Dodaj maszynę wirtualną
3. Zostanie wyświetlony panel umożliwiający wybranie zestawu dostępności
4. Wybierz zestaw dostępności usług AD FS

![Konfigurowanie puli zaplecza wewnętrznego modułu równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. Konfigurowanie sondy**

W panelu ustawień wewnętrznego modułu równoważenia obciążenia wybierz pozycję Sondy.

1. Kliknij pozycję Dodaj.
2. Podaj szczegóły sondy a. **Nazwa**: nazwa sondy b. **Protokół**: TCP c. **Port**: 443 (HTTPS) d. **Interwał**: 5 (wartość domyślna) — odstęp czasu, z jakim wewnętrzny moduł równoważenia obciążenia będzie sondował maszyny w puli zaplecza e. **Limit progu złej kondycji**: 2 (wartość domyślna) — wartość progowa liczby kolejnych niepowodzeń sondowania, po których wewnętrzny moduł równoważenia obciążenia uzna, że maszyna w puli zaplecza nie odpowiada i zaprzestanie wysyłania ruchu do tej maszyny.

![Konfigurowanie sondy wewnętrznego modułu równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. Tworzenie reguł równoważenia obciążenia**

Reguły równoważenia obciążenia pozwalają modułowi skutecznie równoważyć ruch sieciowy. W celu utworzenia reguły równoważenia obciążenia wykonaj następujące czynności: 

1. Wybierz pozycję Reguły równoważenia obciążenia na panelu ustawień wewnętrznego modułu równoważenia obciążenia.
2. Kliknij pozycję Dodaj na panelu Reguły równoważenia obciążenia.
3. Na panelu Dodawanie reguły równoważenia obciążenia wykonaj następujące czynności: a. **Nazwa**: podaj nazwę reguły b. **Protokół**: wybierz opcję TCP c. **Port**: 443 d. **Port zaplecza**: 443 e. **Pula zaplecza**: wybierz pulę utworzoną wcześniej dla klastra usług AD FS f. **Sonda**: wybierz sondę utworzoną wcześniej dla serwerów usług AD FS

![Konfigurowanie reguł wewnętrznego modułu równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Aktualizowanie systemu DNS o dane wewnętrznego modułu równoważenia obciążenia**

Przejdź do serwera DNS i utwórz rekord CNAME dla wewnętrznego modułu równoważenia obciążenia. Rekord CNAME jest przeznaczony dla usługi federacyjnej. Adres IP powinien wskazywać adres IP wewnętrznego modułu równoważenia obciążenia. Na przykład jeśli docelowy adres IP modułu to 10.3.0.8, a zainstalowana usługa federacyjna jest dostępna pod adresem fs.contoso.com, należy utworzyć rekord CNAME dla adresu fs.contoso.com wskazujący na adres 10.3.0.8.
Dzięki temu cała komunikacja dla adresu fs.contoso.com będzie odpowiednio kierowana i przekazywana do wewnętrznego modułu równoważenia obciążenia.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. Konfigurowanie serwera proxy aplikacji internetowej
**7.1. Konfigurowanie połączenia między serwerami proxy aplikacji internetowych i serwerami usług AD FS**

Aby zapewnić komunikację między serwerami proxy aplikacji internetowej a serwerami usług AD FS znajdującymi się za wewnętrznym modułem równoważenia obciążenia, w katalogu %systemroot%\system32\drivers\etc\hosts utwórz rekord dla wewnętrznego modułu równoważenia obciążenia. Pamiętaj o tym, że nazwa wyróżniająca (DN) powinna być nazwą usługi federacyjnej, na przykład fs.contoso.com. Ponadto wpis adresu IP powinien odpowiadać adresowi IP wewnętrznego modułu równoważenia obciążenia (w tym przykładzie: 10.3.0.8).

**7.2. Instalowanie roli serwera proxy aplikacji internetowej**

Gdy serwery proxy aplikacji internetowej mają zapewniony dostęp do serwerów usług AD FS znajdujących się za wewnętrznym modułem równoważenia obciążenia, można zainstalować serwery proxy aplikacji internetowej. Serwery proxy aplikacji internetowej nie muszą być przyłączone do domeny. Zainstaluj role serwera proxy aplikacji internetowej na dwóch serwerach proxy aplikacji internetowej, wybierając rolę dostępu zdalnego. Menedżer serwera poprowadzi Cię przez proces instalacji serwera proxy aplikacji sieci Web.
Aby uzyskać więcej informacji na temat wdrażania serwera proxy aplikacji internetowej, zapoznaj się z artykułem [Instalowanie i konfigurowanie usługi Serwer proxy aplikacji internetowej](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  Wdrażanie modułu równoważenia obciążenia połączonego z Internetem (publicznego)
**8.1.  Tworzenie modułu równoważenia obciążenia połączonego z Internetem (publicznego)**

W witrynie Azure Portal wybierz pozycję Moduły równoważenia obciążenia, a następnie kliknij pozycję Dodaj. Na panelu Tworzenie modułu równoważenia obciążenia wprowadź następujące informacje.

1. **Nazwa**: nazwa modułu równoważenia obciążenia.
2. **Schemat**: opcja Publiczny informuje platformę Azure, że ten moduł równoważenia obciążenia musi mieć adres publiczny.
3. **Adres IP**: utwórz nowy adres IP (dynamiczny).

![Moduł równoważenia obciążenia połączony z Internetem](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Wdrożony moduł pojawi się na liście modułów równoważenia obciążenia.

![Lista modułów równoważenia obciążenia](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. Przypisywanie etykiety DNS do publicznego adresu IP**

Kliknij nowo utworzony moduł w panelu modułów równoważenia obciążenia, aby wyświetlić panel konfiguracji. Wykonaj poniższe kroki, aby skonfigurować etykietę DNS dla publicznego adresu IP:

1. Kliknij publiczny adres IP. Zostanie otwarty panel umożliwiający zmianę ustawień publicznego adresu IP.
2. Kliknij pozycję Konfiguracja.
3. Podaj etykietę DNS. Ta publiczna etykieta DNS zapewnia dostęp z dowolnego miejsca, na przykład contosofs.westus.cloudapp.azure.com. Na zewnętrznym serwerze DNS usługi federacyjnej (na przykład fs.contoso.com) możesz dodać wpis, który umożliwia rozpoznanie etykiety DNS zewnętrznego modułu równoważenia obciążenia (contosofs.westus.cloudapp.azure.com).

![Konfigurowanie modułu równoważenia obciążenia połączonego z Internetem](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Konfigurowanie modułu równoważenia obciążenia połączonego z Internetem (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Konfigurowanie puli zaplecza dla modułu równoważenia obciążenia połączonego z** Internetem (publicznego) 

Konfigurowanie puli zaplecza dla modułu równoważenia obciążenia połączonego z Internetem (publicznego) jako zestawu dostępności (np. contosowapset) dla serwerów proxy aplikacji sieci Web obejmuje te same czynności co tworzenie wewnętrznego modułu równoważenia obciążenia .

![Konfigurowanie puli zaplecza modułu równoważenia obciążenia połączonego z Internetem](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. Konfigurowanie sondy**

Konfigurowanie sondy dla puli zaplecza serwerów proxy aplikacji sieci Web obejmuje te same czynności co konfigurowanie wewnętrznego modułu równoważenia obciążenia.

![Konfigurowanie sondy modułu równoważenia obciążenia połączonego z Internetem](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. Tworzenie reguł równoważenia obciążenia**

Aby skonfigurować regułę równoważenia obciążenia dla portu TCP 443, wykonaj te same czynności co w przypadku konfigurowania reguły dla wewnętrznego modułu równoważenia obciążenia.

![Konfigurowanie reguł modułu równoważenia obciążenia połączonego z Internetem](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. Zabezpieczanie sieci
**9.1. Zabezpieczanie wewnętrznej podsieci**

Wdrożenie poniższych reguł (w przedstawionej kolejności) stanowi podstawę skutecznego zabezpieczenia wewnętrznej podsieci.

| Reguła | Opis | Ruch |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Zezwala na połączenia HTTPS nawiązywane z podsieci DMZ |Przychodzący |
| DenyInternetOutbound |Zablokowany dostęp do Internetu |Wychodzący |

![Reguły dostępu WEWN. (ruch przychodzący)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

<!--
[comment]: <> (![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png))
[comment]: <> (![INT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
-->

**9.2. Zabezpieczanie podsieci DMZ**

| Reguła | Opis | Ruch |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Zezwala na połączenia HTTPS z podsiecią DMZ nawiązywane z Internetu |Przychodzący |
| DenyInternetOutbound |Blokuje wszystkie połączenia oprócz połączeń wychodzących HTTPS z Internetem |Wychodzący |

![Reguły dostępu ZEWN. (ruch przychodzący)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

<!--
[comment]: <> (![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png))
[comment]: <> (![EXT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))
-->

> [!NOTE]
> Jeśli jest wymagane uwierzytelnianie certyfikatu klienta użytkownika (uwierzytelnianie usługi ClientTLS przy użyciu certyfikatów użytkownika X509), port TCP 49443 musi być włączony dla połączeń przychodzących z usługami AD FS.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. Testowanie logowania za pomocą usług AD FS
Najprostszym sposobem przetestowania działania usług AD FS jest użycie strony IdpInitiatedSignon.aspx. Przede wszystkim trzeba ją włączyć we właściwościach usług AD FS. Wykonaj poniższe kroki, aby zweryfikować konfigurację usług AD FS.

1. Aby włączyć tę stronę, uruchom poniższe polecenie cmdlet na serwerze usług AD FS przy użyciu programu PowerShell.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Przy użyciu dowolnej maszyny zewnętrznej otwórz stronę https:\//adfs-server.contoso.com/adfs/ls/IdpInitiatedSignon.aspx.  
3. Powinna pojawić się poniższa strona usług AD FS:

![Testowa strona logowania](./media/active-directory-aadconnect-azure-adfs/test1.png)

Po pomyślnym zalogowaniu zostanie wyświetlony poniższy komunikat:

![Test zakończony powodzeniem](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Szablon na potrzeby wdrażania usług AD FS na platformie Azure
Szablon wdraża konfigurację 6 maszyn, po 2 dla kontrolerów domeny, usług AD FS i serwerów proxy aplikacji sieci Web.

[Usługi AD FS w szablonie wdrażania platformy Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Podczas wdrażania tego szablonu możesz użyć istniejącej sieci wirtualnej lub utworzyć nową. Poniżej wymieniono różnorodne parametry umożliwiające dostosowanie wdrożenia wraz z opisem użycia danego parametru w procesie wdrażania. 

| Parametr | Opis |
|:--- |:--- |
| Lokalizacja |Region, w którym można wdrożyć zasoby, na przykład Wschodnie stany USA. |
| StorageAccountType |Typ tworzonego konta magazynu. |
| VirtualNetworkUsage |Wskazuje, czy zostanie utworzona nowa sieć wirtualna, czy użyta istniejąca. |
| VirtualNetworkName |Nazwa sieci wirtualnej do utworzenia. Parametr obowiązkowy w przypadku użycia zarówno nowej, jak i istniejącej sieci wirtualnej. |
| VirtualNetworkResourceGroupName |Określa nazwę grupy zasobów, w której znajduje się istniejąca sieć wirtualna. Jeśli jest używana istniejąca sieć wirtualna, ten parametr jest obowiązkowy, aby wdrożenie mogło znaleźć identyfikator odpowiedniej istniejącej sieci wirtualnej. |
| VirtualNetworkAddressRange |Zakres adresów nowej sieci wirtualnej. Parametr obowiązkowy w przypadku tworzenia nowej sieci wirtualnej. |
| InternalSubnetName |Nazwa podsieci wewnętrznej. Parametr obowiązkowy dla obydwu opcji użycia sieci wirtualnej (nowa lub istniejąca). |
| InternalSubnetAddressRange |Zakres adresów podsieci wewnętrznej, która zawiera kontrolery domeny i serwery usługi AD FS. Parametr obowiązkowy w przypadku tworzenia nowej sieci wirtualnej. |
| DMZSubnetAddressRange |Zakres adresów podsieci DMZ, która zawiera serwery proxy aplikacji systemu Windows. Parametr obowiązkowy w przypadku tworzenia nowej sieci wirtualnej. |
| DMZSubnetName |Nazwa wewnętrznej podsieci. Parametr obowiązkowy dla obydwu opcji użycia sieci wirtualnej (nowa lub istniejąca). |
| ADDC01NICIPAddress |Wewnętrzny adres IP pierwszego kontrolera domeny. Ten adres IP zostanie statycznie przypisany do kontrolera domeny i musi być prawidłowym adresem IP w obrębie podsieci wewnętrznej. |
| ADDC02NICIPAddress |Wewnętrzny adres IP drugiego kontrolera domeny. Ten adres IP zostanie statycznie przypisany do kontrolera domeny i musi być prawidłowym adresem IP w obrębie podsieci wewnętrznej. |
| ADFS01NICIPAddress |Wewnętrzny adres IP pierwszego serwera usług ADFS. Ten adres IP zostanie statycznie przypisany do serwera usług ADFS i musi być prawidłowym adresem IP w obrębie podsieci wewnętrznej. |
| ADFS02NICIPAddress |Wewnętrzny adres IP drugiego serwera usług ADFS. Ten adres IP zostanie statycznie przypisany do serwera usług ADFS i musi być prawidłowym adresem IP w obrębie podsieci wewnętrznej. |
| WAP01NICIPAddress |Wewnętrzny adres IP pierwszego serwera proxy aplikacji sieci Web. Ten adres IP zostanie statycznie przypisany do serwera proxy aplikacji sieci Web i musi być prawidłowym adresem IP w obrębie podsieci DMZ. |
| WAP02NICIPAddress |Wewnętrzny adres IP drugiego serwera proxy aplikacji sieci Web. Ten adres IP zostanie statycznie przypisany do serwera proxy aplikacji sieci Web i musi być prawidłowym adresem IP w obrębie podsieci DMZ. |
| ADFSLoadBalancerPrivateIPAddress |Wewnętrzny adres IP modułu równoważenia obciążenia usług ADFS. Ten adres IP zostanie statycznie przypisany do modułu równoważenia obciążenia i musi być prawidłowym adresem IP w obrębie podsieci wewnętrznej. |
| ADDCVMNamePrefix |Prefiks nazwy maszyny wirtualnej dla kontrolerów domeny. |
| ADFSVMNamePrefix |Prefiks nazwy maszyny wirtualnej dla serwerów usług ADFS. |
| WAPVMNamePrefix |Prefiks nazwy maszyny wirtualnej dla serwerów proxy aplikacji sieci Web. |
| ADDCVMSize |Rozmiar maszyny wirtualnej kontrolerów domeny. |
| ADFSVMSize |Rozmiar maszyny wirtualnej serwerów usług ADFS. |
| WAPVMSize |Rozmiar maszyny wirtualnej serwerów proxy aplikacji sieci Web. |
| AdminUserName |Nazwa administratora lokalnego maszyn wirtualnych. |
| AdminPassword |Hasło do konta administratora lokalnego maszyn wirtualnych. |

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Zestawy dostępności](https://aka.ms/Azure/Availability) 
* [Azure Load Balancer](https://aka.ms/Azure/ILB)
* [Wewnętrzne równoważenie obciążenia](https://aka.ms/Azure/ILB/Internal).
* [Moduł równoważenia obciążenia połączony z Internetem](https://aka.ms/Azure/ILB/Internet)
* [Konta magazynu](https://aka.ms/Azure/Storage)
* [Sieci wirtualne platformy Azure](https://aka.ms/Azure/VNet)
* [Linki prowadzące do informacji dotyczących usług AD FS i serwera proxy aplikacji internetowej](https://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Następne kroki
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
* [Konfigurowanie usług AD FS i zarządzanie nimi za pomocą programu Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Wdrażanie geograficznie rozproszonych usług AD FS o wysokiej dostępności na platformie Azure przy użyciu usługi Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

