---
title: Konfigurowanie odzyskiwania po awarii dla aplikacji sieci Web usług IIS przy użyciu usługi Azure Site Recovery
description: Dowiedz się, jak replikować maszyny wirtualne farmy sieci Web usług IIS przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 513a0f28fc03cbf24e35112245c9756d5ce00783
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954666"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji sieci Web opartej na usługach IIS

Oprogramowanie aplikacyjne jest motorem produktywności biznesowej w organizacji. Różne aplikacje internetowe mogą służyć różnym celom w organizacji. Niektóre aplikacje, takie jak aplikacje używane do przetwarzania listy płac, aplikacje finansowe i witryny sieci Web skierowane do klientów, mogą mieć kluczowe znaczenie dla organizacji. Aby zapobiec utracie produktywności, ważne jest, aby organizacja stale uruchamiała te aplikacje. Co ważniejsze, posiadanie tych aplikacji stale dostępnych może pomóc zapobiec uszkodzeniu marki lub wizerunku organizacji.

Krytyczne aplikacje sieci web są zazwyczaj skonfigurowane jako aplikacje wielowarstwowe: sieć web, baza danych i aplikacja znajdują się w różnych warstwach. Oprócz rozprzestrzeniania się w różnych warstwach, aplikacje mogą również używać wielu serwerów w każdej warstwie, aby zrównoważyć ruch. Ponadto mapowania między różnymi warstwami i na serwerze sieci web mogą być oparte na statycznych adresach IP. Podczas pracy awaryjnej niektóre z tych mapowań muszą zostać zaktualizowane, zwłaszcza jeśli na serwerze sieci web skonfigurowano wiele witryn sieci Web. Jeśli aplikacje sieci web używają ssl, należy zaktualizować powiązania certyfikatów.

Tradycyjne metody odzyskiwania, które nie są oparte na replikacji obejmują tworzenie kopii zapasowych różnych plików konfiguracyjnych, ustawienia rejestru, powiązania, składniki niestandardowe (COM lub .NET), zawartość i certyfikaty. Pliki są odzyskiwane za pomocą zestawu kroków ręcznych. Tradycyjne metody odzyskiwania kopii zapasowych i ręcznego odzyskiwania plików są uciążliwe, podatne na błędy i nie skalowalne. Na przykład można łatwo zapomnieć o utworzeniu kopii zapasowej certyfikatów. Po przejściu w stan failover nie masz innego wyboru, jak tylko kupić nowe certyfikaty dla serwera.

Dobre rozwiązanie do odzyskiwania po awarii obsługuje modelowanie planów odzyskiwania dla złożonych architektur aplikacji. Należy również mieć możliwość dodawania niestandardowych kroków do planu odzyskiwania do obsługi mapowania aplikacji między warstwami. W przypadku awarii mapowania aplikacji zapewniają rozwiązanie typu "pewnie" jednym kliknięciem, które pomaga prowadzić do niższego celu odzyskiwania.

W tym artykule opisano sposób ochrony aplikacji sieci web opartej na internetowych usługach informacyjnych (IIS) przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md) W tym artykule opisano najlepsze rozwiązania dotyczące replikowania aplikacji sieci web opartej na trzywarstwowych usługach IIS na platformie Azure, sposobu wykonywania wiertła odzyskiwania po awarii i sposobu wykonywania aplikacji w trybie fail over na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikowanie maszyny wirtualnej na platformę Azure](vmware-azure-tutorial.md)
* [Projektowanie sieci odzyskiwania](site-recovery-network-design.md)
* [Testowanie pracy awaryjnej na platformie Azure](site-recovery-test-failover-to-azure.md)
* [Wykonaj przejście awaryjne na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrażania
Aplikacja sieci web oparta na usługach IIS zazwyczaj następuje jeden z następujących wzorców wdrażania:

**Wzorzec wdrażania 1**

Farma internetowa oparta na iis z routingiem żądań aplikacji (ARR), serwerem IIS i programem SQL Server.

![Diagram farmy internetowej opartej na usługach IIS, która ma trzy warstwy](./media/site-recovery-iis/deployment-pattern1.png)

**Wzorzec wdrażania 2**

Farma internetowa oparta na iIS z ARR, serwerem IIS, serwerem aplikacji i programem SQL Server.

![Diagram farmy internetowej opartej na usługach IIS, która ma cztery warstwy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Przykłady w tym artykule używamy maszyn wirtualnych VMware z usługami IIS 7.5 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacja usługi Site Recovery nie jest specyficzna dla aplikacji, zalecenia zawarte w tym artykule powinny być stosowane w scenariuszach wymienionych w poniższej tabeli i dla różnych wersji usług IIS.

### <a name="source-and-target"></a>Źródło i cel

Scenariusz | Do lokacji dodatkowej | Na platformę Azure
--- | --- | ---
Funkcja Hyper-V | Tak | Tak
VMware | Tak | Tak
Serwer fizyczny | Nie | Tak
Azure|Nie dotyczy|Tak

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich maszyn wirtualnych farmy sieci Web usług IIS na platformie Azure, postępuj zgodnie ze wskazówkami dotyczącymi [testowania pracy awaryjnej na platformie Azure w usłudze Site Recovery.](site-recovery-test-failover-to-azure.md)

Jeśli używasz statycznego adresu IP, możesz określić adres IP, który ma zostać przyjmowany przez maszynę wirtualną. Aby ustawić adres IP, przejdź do **ustawienia** > obliczeniowego i sieci**docelowego adresu IP**.

![Zrzut ekranu przedstawiający sposób ustawiania docelowego adresu IP w okienku Obliczeń i sieci odzyskiwania lokacji](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy awaryjnej. Sekwencjonowanie pomaga zachować spójność aplikacji. Podczas tworzenia planu odzyskiwania dla wielowarstwowej aplikacji sieci web wykonaj kroki opisane w [aplikacji Tworzenie planu odzyskiwania przy użyciu funkcji Odzysk witryny](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover
Typowa wielowarstwowa aplikacja sieci Web usług IIS składa się z następujących składników:
* Warstwa bazy danych, która ma maszyny wirtualne SQL.
* Warstwa sieci web, która składa się z serwera usług IIS i warstwy aplikacji. 

Dodaj maszyny wirtualne do różnych grup na podstawie warstwy:

1. Tworzenie planu odzyskiwania. Dodaj maszyny wirtualne warstwy bazy danych w obszarze Grupa 1. Dzięki temu maszyny wirtualne warstwy bazy danych są zamykane jako ostatnie i najpierw wyprowadzone.
1. Dodaj maszyny wirtualne warstwy aplikacji w obszarze Grupa 2. Gwarantuje to, że maszyny wirtualne warstwy aplikacji są donoszone po utworzeniu warstwy bazy danych.
1. Dodaj maszyny wirtualne warstwy sieci web w grupie 3. Gwarantuje to, że maszyny wirtualne warstwy sieci web są donoszone po utworzeniu warstwy aplikacji.
1. Dodaj maszyny wirtualne równoważenia obciążenia w grupie 4. Dzięki temu maszyny wirtualne równoważenia obciążenia są wychowyne po utworzeniu warstwy sieci web.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania
Aby farma sieci web usług IIS działała poprawnie, może być konieczne wykonać niektóre operacje na maszynach wirtualnych platformy Azure po przemiń awaryjnych lub podczas testowania trybu failover. Można zautomatyzować niektóre operacje po pracy awaryjnej. Na przykład można zaktualizować wpis DNS, zmienić powiązanie lokacji lub zmienić parametry połączenia, dodając odpowiednie skrypty do planu odzyskiwania. [Dodaj skrypt programu VMM do planu odzyskiwania](site-recovery-how-to-add-vmmscript.md) opisuje sposób konfigurowania zautomatyzowanych zadań przy użyciu skryptu.

#### <a name="dns-update"></a>Aktualizacja DNS
Jeśli usługa DNS jest skonfigurowana do dynamicznej aktualizacji DNS, maszyny wirtualne zwykle aktualizują system DNS o nowy adres IP po uruchomieniu. Jeśli chcesz dodać jawny krok, aby zaktualizować system DNS przy nowych adresach IP maszyn wirtualnych, dodaj [skrypt do aktualizacji adresu IP w systemie DNS](https://aka.ms/asr-dns-update) jako akcję po zakończeniu pracy awaryjnej w grupach planu odzyskiwania.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Ciąg połączenia w pliku web.config aplikacji
Parametry połączenia określa bazę danych, z którą komunikuje się witryna sieci Web. Jeśli parametry połączenia nosi nazwę maszyny wirtualnej bazy danych, nie są potrzebne dalsze kroki po przełączeniu w tryb failover. Aplikacja może automatycznie komunikować się z bazą danych. Ponadto jeśli adres IP maszyny wirtualnej bazy danych jest zachowywany, nie trzeba aktualizować parametry połączenia. 

Jeśli parametry połączenia odwołuje się do maszyny wirtualnej bazy danych przy użyciu adresu IP, musi być aktualizowana po pracy awaryjnej. Na przykład następujące punkty ciągu połączenia do bazy danych o adresie IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Aby zaktualizować parametry połączenia w warstwie sieci web, dodaj [skrypt aktualizacji połączenia usług IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) po grupie 3 w planie odzyskiwania.

#### <a name="site-bindings-for-the-application"></a>Powiązania witryny dla aplikacji
Każda strona składa się z wiążących informacji. Informacje o powiązaniach obejmują typ powiązania, adres IP, pod którym serwer IIS nasłuchuje żądań dotyczących lokacji, numer portu i nazwy hostów witryny. Podczas pracy awaryjnej może być konieczne zaktualizowanie tych powiązań, jeśli nastąpiła zmiana adresu IP, który jest skojarzony z nimi.

> [!NOTE]
>
> Jeśli ustawisz powiązanie witryny na **Wszystkie nieprzypisane,** nie musisz aktualizować tego powiązania po przełączeniu w stan failover. Ponadto jeśli adres IP skojarzony z witryną nie zostanie zmieniony po przełączeniu w stan failover, nie trzeba aktualizować powiązania witryny. (Przechowywanie adresu IP zależy od architektury sieci i podsieci przypisanych do lokacji podstawowych i lokacji odzyskiwania. Ich zaktualizowanie może być niemożliwe dla twojej organizacji).

![Zrzut ekranu przedstawiający ustawienie powiązania SSL](./media/site-recovery-iis/sslbinding.png)

Jeśli adres IP został skojarzony z witryną, zaktualizuj wszystkie powiązania lokacji o nowy adres IP. Aby zmienić powiązania [lokacji, dodaj skrypt aktualizacji warstwy sieci Web usług IIS](https://aka.ms/asr-web-tier-update-runbook-classic) po grupie 3 w planie odzyskiwania.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizowanie adresu IP modułu równoważenia obciążenia
Jeśli masz maszynę wirtualną ARR, aby zaktualizować adres IP, dodaj [skrypt trybu failover ARR usług IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) po grupie 4.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Powiązanie certyfikatu SSL dla połączenia HTTPS
Witryna sieci Web może mieć skojarzony certyfikat SSL, który pomaga zapewnić bezpieczną komunikację między serwerem sieci web a przeglądarką użytkownika. Jeśli witryna sieci Web ma połączenie HTTPS, a także powiązaną lokację HTTPS powiązaną z adresem IP serwera IIS z powiązaniem certyfikatu SSL, należy dodać nowe powiązanie lokacji dla certyfikatu z adresem IP maszyny wirtualnej usług IIS po przełączeniu w tryb failover.

Certyfikat SSL może być wystawiony dla następujących składników:

* W pełni kwalifikowana nazwa domeny witryny.
* Nazwa serwera.
* Certyfikat symboli wieloznacznych dla nazwy domeny.  
* Adres IP. Jeśli certyfikat SSL jest wystawiony na adres IP serwera IIS, inny certyfikat SSL musi być wystawiony na adres IP serwera IIS w witrynie platformy Azure. Należy utworzyć dodatkowe powiązanie SSL dla tego certyfikatu. Z tego powodu zaleca się nie używać certyfikatu SSL wystawionego na adres IP. Ta opcja jest mniej powszechnie stosowana i wkrótce zostanie przestarzała zgodnie z nowymi zmianami urzędu certyfikacji / forum przeglądarki.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizowanie zależności między warstwą sieci web a warstwą aplikacji
Jeśli masz zależność specyficzne dla aplikacji, która jest oparta na adres IP maszyn wirtualnych, należy zaktualizować tę zależność po pracy awaryjnej.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W witrynie Azure portal wybierz magazyn usług odzyskiwania.
2. Wybierz plan odzyskiwania utworzony dla farmy sieci Web usług IIS.
3. Wybierz pozycję **Test pracy w trybie failover**.
4. Aby rozpocząć proces pracy awaryjnej testu, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
5. Gdy środowisko pomocnicze jest w górę, można wykonać sprawdzanie poprawności.
6. Po zakończeniu sprawdzania poprawności, aby wyczyścić środowisko pracy awaryjnej testu, wybierz **opcję Sprawdzanie poprawności zakończone**.

Aby uzyskać więcej informacji, zobacz [Testowanie pracy awaryjnej na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W witrynie Azure portal wybierz magazyn usług odzyskiwania.
1. Wybierz plan odzyskiwania utworzony dla farmy sieci Web usług IIS.
1. Wybierz pozycję **Tryb failover**.
1. Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [Odzyskiwanie awaryjne w odzyskiwaniu witryny](site-recovery-failover.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [replikowaniu innych aplikacji](site-recovery-workload.md) przy użyciu funkcji Odzysk witryny.
