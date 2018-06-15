---
title: Replikowanie wielowarstwowych aplikacji usług IIS na podstawie sieci web przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak replikowanie IIS sieci web farmy maszyn wirtualnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809424"
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Replikowanie wielowarstwowych aplikacji usług IIS na podstawie sieci web

Oprogramowanie to aparat wydajności biznesowej w organizacji. Różne aplikacje sieci web może obsługiwać różnych celów w organizacji. Niektóre aplikacje, takie jak aplikacje używane do przetwarzania płacowego, aplikacje finansowe i witryn sieci Web skierowane do klienta, może być krytyczne znaczenie dla organizacji. Aby zapobiec zmniejszeniu wydajności pracy, jest ważne dla organizacji, aby te aplikacje stale do pracy. Co ważniejsze o te aplikacje stale dostępne może pomóc chronić markę lub obrazu w organizacji.

Aplikacje sieci web krytyczne są zwykle tworzone jako aplikacje wielowarstwowe: sieci web, bazy danych i aplikacji znajdują się na różnych warstw. Oprócz są rozkładane do różnych warstw, aplikacje mogą także używać wielu serwerów w poszczególnych warstwach ruch równoważenia obciążenia. Ponadto mapowania między warstwami różnych i na serwerze sieci web może być oparta na statycznych adresów IP. Trybu failover niektóre z tych mapowania musi zostać uaktualnione, zwłaszcza, jeśli wiele witryn sieci Web są skonfigurowane na serwerze sieci web. Jeśli aplikacje sieci web używają protokołu SSL, należy zaktualizować powiązania certyfikatu.

Metod tradycyjnych odzyskiwania, które nie są oparte na replikację obejmują tworzenie kopii zapasowej różne pliki konfiguracji, ustawienia rejestru powiązań, niestandardowych składników (COM lub .NET), zawartość i certyfikatów. Pliki są odzyskiwane za pomocą zestawu wymagane ręczne wykonanie czynności. Metod tradycyjnych odzyskiwania wykonywania kopii zapasowych i odzyskiwanie ręcznie plików są skomplikowane, podatne na błędy i nie skalowalnych. Na przykład użytkownik może łatwo zapomnieć wykonywanie kopii zapasowych certyfikatów. Po przejściu w tryb failover w przypadku pozostałych z wyborem nie, ale kupić nowe certyfikaty dla serwera.

Rozwiązanie odzyskiwania po awarii dobrej obsługuje odzyskiwanie modelowania planów dla architektur złożonych aplikacji. Należy również możliwość dodawania niestandardowych kroków do planu odzyskiwania do obsługi aplikacji mapowania między warstwami. W przypadku awarii, mapowania aplikacji Podaj jednym kliknięciem, czy zrzut rozwiązanie, które pomaga prowadzić do dolnej RTO.

W tym artykule opisano, jak chronić aplikacji sieci web, w której jest oparty na Internet Information Services (IIS) za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Artykuł obejmuje najlepsze rozwiązania dotyczące Replikowanie aplikacji trójwarstwowej, usług IIS na podstawie sieci web na platformie Azure, jak to zrobić wyszczególniania odzyskiwania po awarii oraz sposobu pracy awaryjnej aplikacji na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy znać sposób wykonywania następujących zadań:

* [Replikacja maszyny wirtualnej do platformy Azure](vmware-azure-tutorial.md)
* [Projektowanie sieci odzyskiwania](site-recovery-network-design.md)
* [Wykonaj test trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
* [Czy tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia
Usług IIS na podstawie aplikacji sieci web zwykle obejmuje jedną z następujących wzorców wdrożenia:

**Wzorzec wdrożenia 1**

Usług IIS na podstawie kolektywu serwerów sieci web z Routing żądań aplikacji (ARR), serwer usług IIS i SQL Server.

![Diagram farmy usług IIS na podstawie sieci web, który ma trzy warstwy](./media/site-recovery-iis/deployment-pattern1.png)

**Wzorzec wdrożenia 2**

Usług IIS na podstawie kolektywu serwerów sieci web z ARR, serwer usług IIS, serwer aplikacji i programu SQL Server.

![Diagram farmy oparty na usługach sieci web, który ma cztery warstwy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji

Przykłady w tym artykule używamy maszyn wirtualnych VMware z usługami IIS 7,5 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacja usługi Site Recovery nie jest specyficzne dla aplikacji, do stosowania w scenariuszach wymienionych w poniższej tabeli i dla różnych wersji programu IIS powinny zalecenia przedstawione w tym artykule.

### <a name="source-and-target"></a>Źródłowa i docelowa

Scenariusz | Do lokacji dodatkowej | Na platformie Azure
--- | --- | ---
Funkcja Hyper-V | Yes | Yes
VMware | Yes | Yes
Serwer fizyczny | Nie | Yes
Azure|Nie dotyczy|Yes

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikację wszystkich usług IIS sieci web farmy maszyn wirtualnych do platformy Azure, postępuj zgodnie ze wskazówkami w [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

Jeśli używasz statycznego adresu IP, można określić adres IP, który ma maszynę wirtualną do wykonania. Aby ustawić adres IP, przejdź do **obliczeniowe i ustawień sieciowych** > **DOCELOWY adres IP**.

![Zrzut ekranu pokazujący sposób ustawić docelowy adres IP w okienku obliczeniowe odzyskiwania lokacji i sieci](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w wielowarstwowej aplikacji podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla aplikacji sieci web w wielowarstwowych pełną kroki opisane w [Tworzenie planu odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do trybu failover grupy
Typowa aplikacja sieci web usług IIS wielowarstwowych składa się z następujących składników:
* Warstwy bazy danych SQL, maszyny wirtualne.
* Warstwa sieci web, która składa się z serwerem IIS i warstwy aplikacji. 

Dodawanie maszyn wirtualnych do różnych grup opartych na warstwę:

1. Tworzenie planu odzyskiwania. Dodaj maszyny wirtualne warstwy bazy danych w obszarze grupy 1. Dzięki temu maszyny wirtualne warstwy bazy danych są zamknięty ostatni, a włączane pierwszej.
1. Dodaj maszyny wirtualne warstwy aplikacji, w obszarze grupy 2. Dzięki temu, że maszyny wirtualne warstwy aplikacji są włączane po włączane warstwy bazy danych.
1. Dodaj maszyny wirtualne warstwy sieci web w 3 grupy. Dzięki temu, że maszyny wirtualne warstwy sieci web są włączane po włączane warstwy aplikacji.
1. Dodawanie maszyn wirtualnych równoważenia obciążenia w grupie 4. Dzięki temu, że maszyny wirtualne Równoważenie obciążenia są włączane po włączane warstwa sieci web.

Aby uzyskać więcej informacji, zobacz [dostosowywania planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Skrypt do planu odzyskiwania
Dla kolektywu serwerów sieci web usług IIS działała prawidłowo może być konieczne czy niektóre operacje na maszynach wirtualnych platformy Azure po pracy w trybie failover lub podczas testowania trybu failover. Można zautomatyzować niektóre operacje post pracy w trybie failover. Na przykład można zaktualizować wpisu DNS, Zmień powiązanie witryny lub zmienić parametry połączenia, dodając odpowiednie skrypty do planu odzyskiwania. [Skrypt programu VMM do planu odzyskiwania](site-recovery-how-to-add-vmmscript.md) zawiera opis sposobu konfigurowania automatycznych zadań za pomocą skryptu.

#### <a name="dns-update"></a>Aktualizację DNS
Jeśli serwer DNS jest skonfigurowany dla aktualizacji dynamicznych DNS, maszyn wirtualnych zwykle zaktualizować DNS przy użyciu nowego adresu IP, podczas uruchamiania. Jeśli chcesz dodać jawne krok, aby zaktualizować DNS o nowe adresy IP maszyn wirtualnych, należy dodać [skrypt w celu zaktualizowania IP w systemie DNS](https://aka.ms/asr-dns-update) akcji po pracy w trybie failover dla grup planu odzyskiwania.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Parametry połączenia w pliku web.config aplikacji
Ciąg połączenia określa witryny sieci Web, który komunikuje się z bazy danych. Jeśli parametry połączenia przyjmuje nazwę maszyny wirtualnej bazy danych, żadne dodatkowe kroki są wymagane po pracy w trybie failover. Aplikacja może automatycznie komunikować się z bazą danych. Ponadto jeśli adres IP dla maszyny wirtualnej bazy danych jest zachowywana, go nie, aby zaktualizować parametry połączenia. 

Jeśli parametry połączenia odwołuje się do maszyny wirtualnej bazy danych przy użyciu adresu IP, musi zostać zaktualizowane po pracy w trybie failover. Na przykład następujące punkty ciąg połączenia z bazą danych oraz adres IP adres 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Aby zaktualizować parametry połączenia w warstwa sieci web, Dodaj [skryptu aktualizacji połączenia usług IIS](https://aka.ms/asr-update-webtier-script-classic) po grupa 3 w planie odzyskiwania.

#### <a name="site-bindings-for-the-application"></a>Powiązania witryny dla aplikacji
Każda witryna zawiera informacje o wiązaniu. Informacje o powiązaniu zawiera typ powiązania, adres IP, jaką serwera IIS nasłuchuje żądań dla witryny, numer portu i nazwy hostów dla tej lokacji. Podczas pracy awaryjnej może być konieczna aktualizacja tych powiązań, jeśli została zmieniona w adresie IP, który został skojarzony z nimi.

> [!NOTE]
>
> Jeśli ustawisz powiązania witryny **wszystkie nieprzypisane**, nie trzeba zaktualizować tej pracy awaryjnej post powiązania. Ponadto jeśli adres IP skojarzony z lokacją nie zostanie zmienione po pracy w trybie failover, nie trzeba zaktualizować powiązania witryny. (Przechowywania adresu IP jest zależna od architektury sieci i podsieci przypisane do lokacji głównej i odzyskiwania lokacji. Aktualizowanie ich może nie być możliwe w dla Twojej organizacji.)

![Zrzut ekranu pokazujący ustawienia powiązania SSL](./media/site-recovery-iis/sslbinding.png)

Jeśli adres IP jest skojarzony z lokacją, należy zaktualizować wszystkie powiązania witryny przy użyciu nowego adresu IP. Aby zmienić powiązania witryny, należy dodać [skryptu aktualizacji warstwy sieci web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) po grupa 3 w planie odzyskiwania.

#### <a name="update-the-load-balancer-ip-address"></a>Zaktualizuj adres IP usługi równoważenia obciążenia
Jeśli masz ARR maszyny wirtualnej, można zaktualizować adresu IP, Dodaj [skryptu trybu failover IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) po 4 grupy.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Powiązanie certyfikatu SSL dla połączenia HTTPS
Witryna sieci Web może być skojarzony certyfikatu SSL, który zapewnia bezpiecznej komunikacji między serwerem sieci web i przeglądarki użytkownika. Jeśli witryna sieci Web ma połączenie HTTPS, a ma również skojarzonych witryny powiązanie HTTPS na adres IP serwera IIS wraz z powiązaniem certyfikatu SSL, należy dodać nowe powiązanie witryny dla certyfikatu przy użyciu adresu IP maszyny wirtualnej usług IIS w Failover post.

Certyfikat SSL mogą być wystawiane dla tych składników:

* W pełni kwalifikowaną nazwę witryny sieci Web.
* Nazwa serwera.
* Certyfikat uniwersalny dla nazwy domeny.  
* Adres IP. Jeśli certyfikat SSL jest wystawiony na podstawie adresu IP serwera usług IIS, inny certyfikat SSL musi być wystawiony na podstawie adresu IP serwera usług IIS w witrynie platformy Azure. Dodatkowe powiązania SSL dla tego certyfikatu musi zostać utworzona. W związku z tym nie zalecamy korzystania certyfikatu SSL wystawionego przed adresu IP. Ta opcja jest mniej powszechnie używane i wkrótce zostaną wycofane zgodnie z nowego certyfikatu urzędu/przeglądarki forum zmiany.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizacja zależność między warstwą sieci web a warstwą aplikacji
Jeśli masz zależności specyficzne dla aplikacji, która jest oparta na adresie IP maszyn wirtualnych, należy zaktualizować tej pracy awaryjnej post zależności.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W portalu Azure wybierz magazyn usług odzyskiwania.
2. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci web usług IIS.
3. Wybierz **testowanie trybu Failover**.
4. Aby rozpocząć proces test trybu failover, wybierz punkt odzyskiwania i sieć wirtualna platformy Azure.
5. Po skonfigurowaniu dodatkowej środowiska można wykonać operacji sprawdzania poprawności.
6. Po zakończeniu operacji sprawdzania poprawności, aby wyczyścić testowe środowisko trybu failover wybierz **ukończenie operacji sprawdzania poprawności**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W portalu Azure wybierz magazyn usług odzyskiwania.
1. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci web usług IIS.
1. Wybierz **pracy awaryjnej**.
1. Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [replikowanie inne aplikacje](site-recovery-workload.md) przy użyciu usługi Site Recovery.
