---
title: Konfigurowanie odzyskiwania po awarii fo wielowarstwowej aplikacji sieci web opartych na usługach IIS przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak replikować IIS sieci web farmy maszyn wirtualnych przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 66b9342f1a67c4c9d35fda447a297cc64d048c1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480298"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji sieci web opartych na usługach IIS

Oprogramowanie jest aparat wydajność biznesową w organizacji. Różne aplikacje sieci web może służyć do różnych celów, w organizacji. Niektóre aplikacje, takie jak aplikacje używane do przetwarzania listy płac, aplikacje finansowe i witryn sieci Web przeznaczonych dla klientów, może być mają kluczowe znaczenie dla organizacji. Aby zapobiec utracie produktywność, jest ważne dla organizacji te aplikacje stale się i działa. Co ważniejsze mających te aplikacje, które są stale dostępne może zapobiec szkody na marki lub obrazu w organizacji.

Aplikacji internetowych o krytycznym znaczeniu są zazwyczaj ustawiane jako aplikacje wielowarstwowe: sieci web, bazy danych i aplikacji znajdują się w różnych warstwach. Oprócz rozkłada się w różnych warstwach, aplikacje mogą także używać wielu serwerów w przypadku każdej warstwy do równoważenia ruchu sieciowego. Ponadto mapowania między różnymi warstwami, a na serwerze sieci web może opierać się na statyczne adresy IP. W trybie failover konieczne jest część te mapowania można zaktualizować, zwłaszcza, jeśli skonfigurowano wiele witryn sieci Web na serwerze sieci web. Aplikacje sieci web, użycie protokołu SSL, należy zaktualizować powiązania certyfikatu serwera.

Metod tradycyjnych odzyskiwania, które nie są oparte na replikację obejmują tworzenie kopii zapasowych różnych plików konfiguracji, ustawienia rejestru, powiązania, niestandardowych składników (COM lub platformy .NET), zawartości i certyfikatów. Pliki mogą zostać odzyskane, za pomocą zestawu działań ręcznych. Metody odzyskiwania tradycyjnych, wykonywania kopii zapasowych i ręcznie odzyskiwanie plików są kłopotliwe podatne na błędy i nie jest skalowalna. Na przykład użytkownik może łatwo zapomnieć utworzyć kopię zapasową certyfikaty. Po przejściu w tryb failover są pozostawiane z Brak wyboru, ale także kupić nowych certyfikatów dla serwera.

Rozwiązanie odzyskiwania po awarii dobre obsługuje funkcję odzyskiwania na modelowanie plany dotyczące architektury złożonych aplikacji. Należy także możliwość dodawania niestandardowych kroków do planu odzyskiwania, aby obsłużyć mapowania aplikacji między warstwami. W przypadku awarii, mapowania aplikacji zapewniają jednym kliknięciem, czy zrzut rozwiązanie, które pomaga prowadzić do niższych cel czasu odzyskiwania.

W tym artykule opisano, jak zabezpieczyć aplikację internetową, która jest oparta na Internet Information Services (IIS) za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Artykuł opisuje najlepsze wskazówki dotyczące Replikowanie aplikacji trójwarstwowej, na podstawie usług IIS sieci web platformy Azure, jak przeprowadzić odzyskiwanie po awarii oraz sposób awaryjnego przełączania aplikacji na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikacja maszyny wirtualnej do platformy Azure](vmware-azure-tutorial.md)
* [Projektowanie sieć odzyskiwania](site-recovery-network-design.md)
* [Wykonaj test trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
* [Czy tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia
Aplikacji sieci web opartych na usługach IIS zazwyczaj następuje po jednej z następujących wzorców wdrożenia:

**Wzorzec wdrożenia 1**

Internetowe usługi informacyjne kolektywu serwerów sieci web za pomocą Routing żądań aplikacji (ARR), serwer usług IIS i SQL Server.

![Diagram przedstawiający farmy sieci web opartych na usługach IIS, która ma trzy warstwy](./media/site-recovery-iis/deployment-pattern1.png)

**Sekwencja wdrażania 2**

Internetowe usługi informacyjne kolektywu serwerów sieci web za pomocą modułu AAR, serwer usług IIS, serwera aplikacji i programu SQL Server.

![Diagram przedstawiający farmy oparty na usługach sieci web, która ma cztery warstwy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

W przykładach w tym artykule używamy maszyn wirtualnych VMware za pomocą usług IIS 7.5 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacji usługi Site Recovery nie jest specyficzna dla aplikacji, zalecenia przedstawione w tym artykule mają zastosowanie w scenariuszach wymienionych w poniższej tabeli i różnymi wersjami usług IIS.

### <a name="source-and-target"></a>Źródłowe i docelowe

Scenariusz | Do lokacji dodatkowej | Na platformę Azure
--- | --- | ---
Funkcja Hyper-V | Yes | Tak
VMware | Yes | Yes
Serwer fizyczny | Nie | Tak
Azure|Nie dotyczy|Tak

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich usług IIS sieci web farmy maszyn wirtualnych na platformie Azure, postępuj zgodnie ze wskazówkami w [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

Jeśli używasz statyczny adres IP można określić adresu IP, który ma maszynę wirtualną. Aby ustawić adres IP, przejdź do **ustawień obliczenia i sieć** > **DOCELOWY adres IP**.

![Zrzut ekranu, który pokazuje, jak ustawić docelowy adres IP w okienku Site Recovery obliczenia i sieć](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w aplikacji wielowarstwowej podczas przejścia w tryb failover. Sekwencjonowanie pomaga w utrzymaniu spójności aplikacji. Po utworzeniu planu odzyskiwania w przypadku aplikacji sieci web w wielowarstwowych pełną kroki opisane w [utworzyć plan odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodaj maszyny wirtualne do grupy trybu failover
Typowe wielowarstwową aplikację sieci web usług IIS składa się z następujących składników:
* Warstwa bazy danych zawierającej maszyny wirtualne SQL.
* Warstwa sieci web, która składa się z serwera usług IIS i warstwy aplikacji. 

Dodawanie maszyn wirtualnych do różnych grup na podstawie warstwy:

1. Tworzenie planu odzyskiwania. Dodaj maszyny wirtualne warstwy bazy danych w ramach grupy 1. Dzięki temu maszyny wirtualne warstwy bazy danych jest zamknięty ostatni, a wznowione pierwszy.
1. Dodaj maszyny wirtualne warstwy aplikacji, w obszarze grupy 2. Daje to gwarancję, że maszyny wirtualne warstwy aplikacji są przenoszone po warstwy bazy danych zostało wznowione.
1. Dodaj maszyny wirtualne warstwy sieci web w wersji 3 grupy. Daje to gwarancję, że maszyny wirtualne warstwy sieci web są przenoszone po wznowione w warstwie aplikacji.
1. Dodaj maszyny wirtualne Równoważenie obciążenia w grupy 4. Daje to gwarancję, że Równoważenie obciążenia maszyny wirtualne są przenoszone po wznowione warstwa sieci web.

Aby uzyskać więcej informacji, zobacz [dostosowywać plan odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania
Dla kolektywu serwerów sieci web usług IIS do poprawnego działania może być konieczne są pewne operacje na maszynach wirtualnych platformy Azure po przełączeniu w tryb failover lub podczas testowania trybu failover. Można zautomatyzować niektóre operacje po przejściu do trybu failover. Na przykład możesz zaktualizować wpisu DNS, zmienić powiązania witryny lub zmienić parametry połączenia przez dodanie odpowiedniej skrypty do planu odzyskiwania. [Skrypt programu VMM do planu odzyskiwania](site-recovery-how-to-add-vmmscript.md) w tym artykule opisano sposób konfigurowania automatycznych zadań za pomocą skryptu.

#### <a name="dns-update"></a>Aktualizacja DNS
Jeśli usługa DNS jest skonfigurowana dla aktualizacji dynamicznych DNS, maszyny wirtualne zwykle aktualizacji DNS przy użyciu nowego adresu IP, po uruchomieniu. Jeśli chcesz dodać jawne krok aktualizowanie systemu DNS o nowe adresy IP maszyn wirtualnych, należy dodać [skrypt w celu zaktualizowania adresu IP w systemie DNS](https://aka.ms/asr-dns-update) jako akcję po przełączeniu w tryb failover dla grup planu odzyskiwania.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Parametry połączenia w pliku web.config aplikacji
Parametry połączenia określają bazy danych, która komunikuje się witryny sieci Web. Jeśli parametry połączenia zawiera nazwę maszyny wirtualnej bazy danych, nie trzeba wykonywać dalszych czynności są wymagane po przejściu do trybu failover. Aplikacja automatycznie mogą komunikować się z bazą danych. Ponadto jeśli adres IP dla maszyny wirtualnej bazy danych jest zachowywana, ona nie, należy zaktualizować parametry połączenia. 

Jeśli parametry połączenia odwołuje się do maszyny wirtualnej bazy danych przy użyciu adresu IP, musi zostać zaktualizowane po przejściu do trybu failover. Na przykład następujące punkty połączeń w ciągu, do bazy danych z adresem IP adresów 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Aby zaktualizować parametry połączenia w warstwie sieci web, Dodaj [skrypt aktualizacji połączenia usług IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) po 3 grupy w ramach planu odzyskiwania.

#### <a name="site-bindings-for-the-application"></a>Powiązania witryny dla aplikacji
Każda witryna zawiera informacje o powiązaniu. Informacje o powiązaniu zawiera typ powiązania, adres IP, w którym na serwerze usług IIS nasłuchuje żądań dla witryny, numer portu i nazwy hostów dla witryny. Podczas pracy awaryjnej konieczne może być aktualizowanie tych powiązań, jeśli nie nastąpiła zmiana w adresie IP, która jest skojarzona z nimi.

> [!NOTE]
>
> Jeśli ustawisz powiązania witryny **wszystkie nieprzypisane**, nie musisz zaktualizować tego powiązania po przełączeniu w tryb failover. Ponadto jeśli adres IP skojarzony z witryną programu nie zostanie zmienione po przełączeniu w tryb failover, nie musisz zaktualizować powiązania witryny. (Okres przechowywania adresów IP zależy od architektury sieci i podsieci przypisane do podstawową i witrynami odzyskiwania. Aktualizuje je może nie być zastosowane w danej organizacji).

![Zrzut ekranu pokazujący ustawienia powiązania SSL](./media/site-recovery-iis/sslbinding.png)

Jeśli adres IP jest skojarzony z lokacją, należy zaktualizować wszystkie powiązania witryny przy użyciu nowego adresu IP. Aby zmienić powiązania witryny, należy dodać [skrypt aktualizacji warstwy sieci web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) po 3 grupy w ramach planu odzyskiwania.

#### <a name="update-the-load-balancer-ip-address"></a>Zaktualizuj adres IP modułu równoważenia obciążenia
Jeśli masz maszynę wirtualną modułu ARR można zaktualizować adresu IP należy dodać [skryptu trybu failover IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) po 4 grupy.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Powiązania certyfikatu SSL dla połączeń HTTPS
Witryny sieci Web może być skojarzony certyfikatu SSL, który ułatwia, zapewnienia bezpiecznej komunikacji między serwerem sieci web i przeglądarki użytkownika. Jeśli witryna sieci Web ma połączenie HTTPS, a ma również skojarzonych lokacji powiązanie HTTPS na adres IP serwera usług IIS za pomocą powiązania certyfikatu SSL, należy dodać nowe powiązanie witryny dla certyfikatu przy użyciu adresu IP usług IIS maszyny wirtualnej po przełączeniu w tryb failover.

Certyfikat SSL mogą być wystawiane dla tych składników:

* W pełni kwalifikowana nazwa domeny witryny sieci Web.
* Nazwa serwera.
* Certyfikat uniwersalny dla nazwy domeny.  
* Adres IP. Jeśli certyfikat SSL jest wystawiony na podstawie adresu IP serwera usług IIS, inny certyfikat SSL musi być wystawiony na podstawie adresu IP serwera usług IIS w witrynie platformy Azure. Dodatkowe powiązania SSL dla tego certyfikatu musi zostać utworzona. W związku z tym nie zalecamy korzystania certyfikatu SSL wystawionego przed adresu IP. Ta opcja jest mniej powszechnie używana i wkrótce zostaną wycofane zgodnie z nowego certyfikatu urzędu/przeglądarki forum zmiany.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizuj zależności między warstwami sieci web i aplikacji
W przypadku zależności specyficzne dla aplikacji, która opiera się na adres IP maszyny wirtualnej należy zaktualizować tej zależności po przełączeniu w tryb failover.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
2. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci web usług IIS.
3. Wybierz pozycję **Test pracy w trybie failover**.
4. Aby rozpocząć proces testu trybu failover, wybierz punkt odzyskiwania i usługa Azure virtual network.
5. Po skonfigurowaniu dodatkowej środowiska można wykonywać sprawdzanie poprawności.
6. Po zakończeniu walidacji można wyczyścić testowe środowisko trybu failover wybierz **ukończenia operacji sprawdzania poprawności**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
1. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci web usług IIS.
1. Wybierz pozycję **Tryb failover**.
1. Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [trybu Failover w usłudze Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [replikacji z innych aplikacji](site-recovery-workload.md) przy użyciu usługi Site Recovery.
