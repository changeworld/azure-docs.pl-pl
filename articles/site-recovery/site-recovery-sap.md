---
title: Ochrona za pomocą usługi Azure Site Recovery wdrożenia aplikacji wielowarstwowych SAP NetWeaver | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak chronić SAP NetWeaver wdrożenia aplikacji za pomocą usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: e2107177663163259d1f731717c4910bc986fc1f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Wdrożenie aplikacji wielowarstwowych SAP NetWeaver jest chroniony przy użyciu usługi Site Recovery

Większości wdrożeń SAP dużego rozmiaru i średniej wielkości niektórych formularz rozwiązanie odzyskiwania po awarii. Znaczenie rozwiązania w zakresie odzyskiwania po awarii niezawodny i testować wzrosło jako więcej podstawowe procesy biznesowe zostaną przeniesione do aplikacji, takich jak SAP. Usługa Azure Site Recovery został przetestowany i zintegrowany z aplikacjami SAP. Usługa Site Recovery przekracza możliwości większości rozwiązań odzyskiwania po awarii lokalnie i na niższy całkowity koszt posiadania (TCO) niż konkurencyjnych rozwiązania.

Za pomocą usługi Site Recovery można:
* **Włącz ochronę SAP NetWeaver i z systemem innym niż NetWeaver aplikacji produkcyjnych, które lokalnie** przez replikację składników do platformy Azure.
* **Włącz ochronę SAP NetWeaver i z systemem innym niż NetWeaver aplikacji produkcyjnych, uruchamianych na platformie Azure** przez replikację składników do innego centrum danych Azure.
* **Uproszczenie migracji chmury** przy użyciu usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* **Uprościć SAP uaktualniania projektu, testowania i prototypowania** tworząc produkcji sklonować na żądanie do testowania aplikacji SAP.

W tym artykule opisano, jak chronić SAP NetWeaver wdrożenia aplikacji za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Artykuł obejmuje najlepsze rozwiązania w zakresie ochrony wdrożenia SAP NetWeaver trójwarstwowa na platformie Azure przez replikację do innego centrum danych Azure przy użyciu usługi Site Recovery. Opisuje obsługiwane scenariusze i konfiguracje i wykonać test pracy awaryjnej (testowanie odzyskiwania po awarii), a rzeczywista praca awaryjna.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że należy znać sposób wykonywania następujących zadań:

* [Replikacja maszyny wirtualnej do platformy Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Projektowanie sieci odzyskiwania](site-recovery-azure-to-azure-networking-guidance.md)
* [Wykonaj test trybu failover na platformie Azure](azure-to-azure-walkthrough-test-failover.md)
* [Czy tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Site Recovery można użyć do wdrożenia rozwiązania odzyskiwania po awarii w następujących scenariuszach:
* Systemy SAP uruchomiona w centrum danych Azure jednego, które są replikowane do innego centrum danych Azure (Azure do platformy Azure odzyskiwania po awarii). Aby uzyskać więcej informacji, zobacz [architektura replikacji Azure do platformy Azure](https://aka.ms/asr-a2a-architecture).
* Systemie SAP VMware (lub fizyczny) serwerów lokalnych tego Replikuj do lokacji odzyskiwania po awarii w centrum danych Azure (odzyskiwania po awarii VMware do platformy Azure). Ten scenariusz wymaga niektóre dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [architektura replikacji VMware do platformy Azure](https://aka.ms/asr-v2a-architecture).
* SAP z systemami na funkcji Hyper-V lokalnych które są replikowane do lokacji odzyskiwania po awarii w centrum danych Azure (odzyskiwania po awarii dla funkcji Hyper-V-do Azure). Ten scenariusz wymaga niektóre dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [architektura replikacji funkcji Hyper-V-do Azure](https://aka.ms/asr-h2a-architecture).

W tym artykule używamy sytuacji odzyskiwania po awarii Azure do platformy Azure do zaprezentowania możliwości odzyskiwania po awarii programu SAP usługi Site Recovery. Ponieważ replikacja usługi Site Recovery nie jest specyficzne dla aplikacji, proces, który opisano powinien mają zastosowanie również w innych scenariuszach.

### <a name="required-foundation-services"></a>Foundation wymagane usługi
W tym scenariuszu, który omówiono w tym artykule są wdrażane z następującymi usługami foundation:
* Brama sieci VPN platformy Azure ExpressRoute lub Microsoft Azure
* Co najmniej jeden kontroler domeny usługi Active Directory i serwera DNS, działające na platformie Azure

Firma Microsoft zaleca ustanowienia tej infrastruktury, przed przystąpieniem do wdrażania usługi Site Recovery.

## <a name="typical-sap-application-deployment"></a>Typowe wdrożenie aplikacji SAP
Duże Klienci SAP zwykle wdrażać od 6 do 20 poszczególne aplikacje SAP. Większość tych aplikacji są oparte na aparaty SAP NetWeaver ABAP lub Java. Wiele aparatów autonomiczny mniejszych, określonej z systemem innym niż NetWeaver SAP i zwykle niektóre aplikacje z systemem innym niż SAP, obsługuje te aplikacje NetWeaver core.  

Należy dodać do spisu wszystkie aplikacje SAP, które są uruchomione w środowisku. Następnie określ tryb wdrożenia (dwuwarstwowa i trójwarstwowa), wersji, poprawki rozmiary, szybkości zmian i wymagań dotyczących trwałości dysku.

![Diagram typowy wzorzec wdrożenia SAP](./media/site-recovery-sap/sap-typical-deployment.png)

Warstwa trwałości bazy danych SAP chronić za pomocą wewnętrznych narzędzi systemu DBMS, takich jak replikacja systemu AlwaysOn programu SQL Server, Oracle Data Guard lub SAP HANA. Podobnie jak warstwę bazy danych SAP warstwie klienta nie jest chroniona przez usługę Site Recovery. Należy wziąć pod uwagę czynniki wpływające na tej warstwie. Czynniki obejmują opóźnienie propagacji DNS, zabezpieczeń i dostęp zdalny do centrum danych odzyskiwania po awarii.

Usługa Site Recovery jest zalecanym rozwiązaniem dla warstwy aplikacji, w tym dla programu SAP SCS i ASCS. Inne aplikacje, takie jak aplikacje NetWeaver SAP i aplikacji z systemem innym niż SAP częścią ogólnego środowiska wdrożenia SAP. Należy chronić je z usługą Site Recovery.

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych
Aby rozpocząć replikację wszystkich SAP aplikacji maszyn wirtualnych do odzyskiwania centrum danych Azure po awarii, postępuj zgodnie ze wskazówkami w [replikowanie maszyny wirtualnej na platformie Azure](azure-to-azure-walkthrough-enable-replication.md).

Użycie statycznego adresu IP, można określić adres IP, który ma maszynę wirtualną do wykonania. Aby ustawić adres IP, przejdź do **obliczeniowe i ustawień sieciowych** > **karty interfejsu sieciowego**.

![Zrzut ekranu pokazujący sposób ustawiania prywatnego adresu IP w okienku karty interfejsu sieciowego odzyskiwania lokacji](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w wielowarstwowej aplikacji podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla aplikacji sieci web w wielowarstwowych pełną kroki opisane w [Tworzenie planu odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Dla aplikacji działała prawidłowo może być konieczne wykonaj niektóre operacje na maszynach wirtualnych Azure po przejściu w tryb failover podczas testowania trybu failover. Można zautomatyzować niektóre operacje post pracy w trybie failover. Na przykład można zaktualizować wpisu DNS i mień skojarzenia i połączenia, dodając odpowiednie skrypty do planu odzyskiwania.

### <a name="dns-update"></a>Aktualizację DNS
Jeśli serwer DNS jest skonfigurowany dla aktualizacji dynamicznych DNS, maszyn wirtualnych zwykle zaktualizować DNS przy użyciu nowego adresu IP, podczas uruchamiania. Jeśli chcesz dodać jawne krok, aby zaktualizować DNS o nowe adresy IP maszyn wirtualnych, należy dodać [skryptu można zaktualizować adresu IP w systemie DNS](https://aka.ms/asr-dns-update) akcji po pracy w trybie failover dla grup planu odzyskiwania.  

## <a name="example-azure-to-azure-deployment"></a>Przykład wdrożenia usługi Azure do platformy Azure
Na poniższym diagramie przedstawiono scenariusz odzyskiwania po awarii Azure Azure Site Recovery:

![Diagram scenariusza replikacji Azure do platformy Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* Podstawowego centrum danych jest w Singapurze (Azja południowo-wschodnia Azure). Centrum danych odzyskiwania po awarii jest Hongkong (Azja Wschodnia Azure). W tym scenariuszu lokalną wysoką dostępność są udostępniane przez dwie maszyny wirtualne, które są uruchamiane w trybie synchronicznym w Singapurze AlwaysOn programu SQL Server.
* Udział plików SAP ASCS zapewnia wysoką dostępność dla programu SAP pojedynczych punktów awarii. Udział plików ASCS nie wymaga udostępnionego dysku klastra. Aplikacje, takie jak SIOS nie są wymagane.
* Ochrona odzyskiwania awaryjnego dla warstwy DBMS jest realizowane za pośrednictwem replikacji asynchronicznej.
* W tym scenariuszu pokazano "odzyskiwania po awarii symetryczne." Określenie to opisuje rozwiązanie odzyskiwania po awarii, która jest dokładny replik produkcji. Odzyskiwanie po awarii rozwiązania programu SQL Server ma lokalnego wysokiej dostępności. Symetryczne odzyskiwaniem po awarii nie jest wymagana przez warstwę bazy danych. Wielu klientów wykorzystać elastyczność wdrożeń chmury można szybko utworzyć węzła lokalnego wysokiej dostępności po zdarzeniu odzyskiwania po awarii.
* Diagram przedstawia SAP NetWeaver ASCS i warstwy aplikacji serwera, replikowane za pomocą usługi Site Recovery.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1.  W portalu Azure wybierz magazyn usług odzyskiwania.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacje SAP.
3.  Wybierz **testowanie trybu Failover**.
4.  Aby rozpocząć proces test trybu failover, wybierz punkt odzyskiwania i sieć wirtualna platformy Azure.
5.  Po skonfigurowaniu dodatkowej środowiska wykonywać operacji sprawdzania poprawności.
6.  Po zakończeniu operacji sprawdzania poprawności, aby wyczyścić środowisko trybu failover wybierz **oczyszczania testowy tryb failover**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W portalu Azure wybierz magazyn usług odzyskiwania.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacje SAP.
3.  Wybierz **pracy awaryjnej**.
4.  Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o tworzeniu rozwiązanie odzyskiwania po awarii dla wdrożenia SAP NetWeaver przy użyciu usługi Site Recovery, zobacz oficjalny dokument do pobrania [SAP NetWeaver: tworzenie rozwiązanie odzyskiwania po awarii z usługą Azure Site Recovery](http://aka.ms/asr-sap). Oficjalny dokument zawiera omówienie zalecenia dla różnych architektur SAP, zawiera listę obsługiwanych aplikacji i typów maszyny Wirtualnej dla programu SAP na platformie Azure, a zawiera opis opcji planu testowania rozwiązanie do odzyskiwania po awarii.
* Dowiedz się więcej o [replikowanie innych obciążeń](site-recovery-workload.md) przy użyciu usługi Site Recovery.
