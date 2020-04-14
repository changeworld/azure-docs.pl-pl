---
title: SQL Server na maszynach wirtualnych z systemem Windows w usłudze Azure — często zadawane pytania | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 0d6d69b82e80ff9bc33e49302cf59766b9c2e8d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270829"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Często zadawane pytania dotyczące programu SQL Server uruchomionego na maszynach wirtualnych z systemem Windows na platformie Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [programu SQL Server na maszynach wirtualnych z systemem Windows na platformie Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/)

> [!NOTE]
> W tym artykule skupiono się na problemach specyficznych dla programu SQL Server na maszynach wirtualnych z systemem Windows. Jeśli używasz programu SQL Server na maszynach wirtualnych z systemem Linux, zobacz często zadawane pytania dotyczące [systemu Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Obrazów

1. **Jakie obrazy galerii maszyn wirtualnych programu SQL Server są dostępne?** 

   Platforma Azure przechowuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji programu SQL Server we wszystkich wersjach dla systemów Windows i Linux. Aby uzyskać więcej informacji, zobacz pełną listę [obrazów maszyn wirtualnych](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) systemu Windows i [obrazów maszyn wirtualnych z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Czy istniejące obrazy galerii maszyn wirtualnych programu SQL Server są aktualizowane?**

   Co dwa miesiące obrazy programu SQL Server w galerii maszyn wirtualnych są aktualizowane o najnowsze aktualizacje systemu Windows i linux. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje, które są oznaczone jako ważne w usłudze Windows Update, w tym ważne aktualizacje zabezpieczeń programu SQL Server i dodatki Service Pack. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemu. Aktualizacje zbiorcze programu SQL Server są obsługiwane inaczej dla systemów Linux i Windows. W systemie Linux aktualizacje zbiorcze programu SQL Server są również uwzględniane w odświeżaniu. Ale w tej chwili maszyny wirtualne systemu Windows nie są aktualizowane za pomocą aktualizacji zbiorczych programu SQL Server lub Windows Server.

1. **Czy obrazy maszyn wirtualnych programu SQL Server mogą zostać usunięte z galerii?**

   Tak. Platforma Azure przechowuje tylko jeden obraz na wersję główną i wersję. Na przykład po wydaniu nowego dodatku Service Pack programu SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz programu SQL Server dla poprzedniego dodatku Service Pack jest natychmiast usuwany z witryny Azure portal. Jednak nadal jest dostępna do inicjowania obsługi administracyjnej z programu PowerShell przez następne trzy miesiące. Po trzech miesiącach poprzedni obraz dodatku Service Pack nie jest już dostępny. Ta zasada usuwania będzie również stosowana, jeśli wersja programu SQL Server staje się nieobsługiwała po zakończeniu cyklu życia.


1. **Czy można wdrożyć starszy obraz programu SQL Server, który nie jest widoczny w witrynie Azure portal?**

   Tak, za pomocą programu PowerShell. Aby uzyskać więcej informacji na temat wdrażania maszyn wirtualnych programu SQL Server przy użyciu programu PowerShell, zobacz [Jak aprowizować maszyny wirtualne programu SQL Server za pomocą programu Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Jak uogólnić program SQL Server na maszynie Wirtualnej platformy Azure i używać jej do wdrażania nowych maszyn wirtualnych?**

   Maszynę wirtualną systemu Windows Server (bez zainstalowanego na niej programu SQL Server) można wdrożyć maszynę wirtualną systemu Windows Server (bez zainstalowanego programu SQL Server) i użyć procesu [sysprep SQL](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) do uogólniania programu SQL Server na maszynie Wirtualnej Platformy Azure (Windows) za pomocą nośnika instalacyjnego programu SQL Server. Klienci, którzy mają [pakiet software assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klienci, którzy nie mają certyfikatu oprogramowania, mogą używać nośnika instalacyjnego z obrazu maszyny Wirtualnej programu Marketplace SQL Server, który ma żądaną wersję.

   Alternatywnie należy użyć jednego z obrazów programu SQL Server z witryny Azure marketplace, aby uogólnić program SQL Server na maszynie Wirtualnej platformy Azure. Należy zauważyć, że przed utworzeniem własnego obrazu należy usunąć następujący klucz rejestru w obrazie źródłowym. Niepowodzenie może spowodować wzdęcia folderu bootstrap instalatora programu SQL Server i/lub rozszerzenia SQL IaaS w stanie awarii.

   Ścieżka klucza rejestru:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Firma Microsoft zaleca, aby wszystkie maszyny wirtualne platformy Azure programu SQL Server, w tym te wdrożone na podstawie niestandardowych obrazów uogólnionych, [były zarejestrowane u dostawcy odwołań maszyn wirtualnych SQL](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) w celu spełnienia wymagań dotyczących zgodności i korzystania z opcjonalnych funkcji, takich jak automatyczne instalowanie poprawek i automatyczne tworzenie kopii zapasowych. Pozwoli to również [określić typ licencji](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) dla każdej maszyny Wirtualnej programu SQL Server.

1. **Czy mogę użyć własnego dysku VHD do wdrożenia maszyny Wirtualnej programu SQL Server?**

   Tak, ale następnie należy [zarejestrować każdą maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny Wirtualnej programu SQL Server,](virtual-machines-windows-sql-register-with-resource-provider.md) aby zarządzać maszyną wirtualną programu SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne instalowanie poprawek i automatyczne tworzenie kopii zapasowych.

1. **Czy można skonfigurować konfiguracje, które nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 + SQL Server 2012)?**

   Nie. W przypadku obrazów galerii maszyn wirtualnych, które zawierają program SQL Server, należy wybrać jeden z dostarczonych obrazów za pośrednictwem witryny Azure portal lub programu [PowerShell.](virtual-machines-windows-ps-sql-create.md) Istnieje jednak możliwość wdrożenia maszyny Wirtualnej systemu Windows i samodzielnej instalacji programu SQL Server. Następnie należy [zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny Wirtualnej programu SQL Server,](virtual-machines-windows-sql-register-with-resource-provider.md) aby zarządzać maszyną wirtualną programu SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne instalowanie poprawek i automatyczne tworzenie kopii zapasowych. 


## <a name="creation"></a>Tworzenie

1. **Jak utworzyć maszynę wirtualną platformy Azure za pomocą programu SQL Server?**

   Najprostszą metodą jest utworzenie maszyny wirtualnej, która zawiera program SQL Server. Aby zapoznać się z samouczkiem na temat rejestrowania się na platformie Azure i tworzenia maszyny wirtualnej programu SQL Server z portalu, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w portalu Azure.](virtual-machines-windows-portal-sql-server-provision.md) Można wybrać obraz maszyny wirtualnej, który używa płatnego licencjonowania programu SQL Server lub użyć obrazu, który umożliwia przynoszenie własnej licencji programu SQL Server. Istnieje również możliwość ręcznego instalowania programu SQL Server na maszynie Wirtualnej z swobodnie licencjonowaną wersję (Developer lub Express) lub ponownego użycia licencji lokalnej. Pamiętaj, aby [zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny Wirtualnej programu SQL Server,](virtual-machines-windows-sql-register-with-resource-provider.md) aby zarządzać maszyną wirtualną programu SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne instalowanie poprawek i automatyczne tworzenie kopii zapasowych. Jeśli masz przy sobie własną licencję, musisz mieć [licencję Mobilności za pośrednictwem pakietu Software Assurance na platformie Azure.](https://azure.microsoft.com/pricing/license-mobility/) Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak przeprowadzić migrację lokalnej bazy danych programu SQL Server do chmury?**

   Najpierw utwórz maszynę wirtualną platformy Azure z wystąpieniem programu SQL Server. Następnie migruj lokalne bazy danych do tego wystąpienia. Aby zapoznać się ze strategiami migracji danych, zobacz [Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure.](virtual-machines-windows-migrate-sql.md)

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją trzy sposoby, aby to zrobić. Jeśli jesteś klientem umowy enterprise agreement (EA), możesz aprowizować jeden z [obrazów maszyn wirtualnych obsługujących licencje,](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)który jest również znany jako bring-your-own-license (BYOL). Jeśli masz [pakiet software assurance,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)możesz włączyć [korzyść hybrydową platformy Azure](virtual-machines-windows-sql-ahb.md) na istniejącym obrazie płatności zgodnie z rzeczywistym obciążeniem (PAYG). Można też skopiować nośnik instalacyjny programu SQL Server na maszynę wirtualną systemu Windows Server, a następnie zainstalować program SQL Server na maszynie Wirtualnej. Pamiętaj, aby zarejestrować maszynę wirtualną programu SQL Server [u dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md) w celu uzyskania takich funkcji, jak zarządzanie portalami, automatyczne tworzenie kopii zapasowych i automatyczne instalowanie poprawek. 

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Możesz łatwo przełączyć obraz galerii płatności zgodnie z rzeczywistyma po to, aby uzyskać własną licencję (BYOL), włączając [korzyść hybrydową platformy Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)  Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-ahb.md). Obecnie ta funkcja jest dostępna tylko dla klientów chmury publicznej.

1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](virtual-machines-windows-sql-ahb.md) nie wymaga żadnych przestojów dla programu SQL Server, ponieważ zmiana wchodzi w życie natychmiast i nie wymaga ponownego uruchomienia maszyny Wirtualnej. Jednak aby zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny Wirtualnej programu SQL Server, [rozszerzenie IaaS SQL](virtual-machines-windows-sql-server-agent-extension.md) jest warunkiem wstępnym i zainstalowanie rozszerzenia SQL IaaS w _trybie pełnego_ uruchamia ponownie usługę PROGRAMU SQL Server. W związku z tym, jeśli rozszerzenie SQL IaaS musi być zainstalowany, należy zainstalować go w trybie _lekkim_ dla ograniczonej funkcjonalności lub zainstalować go w _trybie pełnym_ trybie podczas okna konserwacji. Rozszerzenie SQL IaaS zainstalowane w trybie _lekkim_ można uaktualnić do _trybu pełnego_ w dowolnym momencie, ale wymaga ponownego uruchomienia usługi SQL Server. 
   
1. **Czy można przełączyć model licencjonowania na maszynie wirtualnej programu SQL Server wdrożonej przy użyciu modelu klasycznego?**

   Nie. Zmiana modelu licencjonowania nie jest obsługiwana na klasycznej maszynie wirtualnej. Maszynę wirtualną można przeprowadzić do modelu usługi Azure Resource Manager i zarejestrować się u dostawcy zasobów maszyny Wirtualnej programu SQL Server. Po zarejestrowaniu maszyny Wirtualnej u dostawcy zasobów maszyny Wirtualnej programu SQL Server zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej.

1. **Czy mogę używać witryny Azure portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie portalami jest funkcją udostępnianą przez dostawcę zasobów maszyny Wirtualnej programu SQL Server, która opiera się na rozszerzeniu agenta IaaS programu SQL Server. W związku z tym te same ograniczenia mają zastosowanie do dostawcy zasobów, co do rozszerzenia. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym wystąpieniem o nazwie, o ile zostało poprawnie skonfigurowane. Aby uzyskać więcej informacji na temat tych ograniczeń, zobacz [rozszerzenie agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Czy subskrypcje usług CSP mogą aktywować korzyść hybrydową platformy Azure?**

   Tak, korzyść hybrydowa platformy Azure jest dostępna dla subskrypcji CSP. Klienci CSP powinni najpierw wdrożyć obraz płatności zgodnie z rzeczywistym użyciem, a następnie [zmienić model licencjonowania na](virtual-machines-windows-sql-ahb.md) własną licencję.
   
 
1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Aby mieć bezpłatną licencję pasywną dla dodatkowej grupy dostępności w trybie wstrzymania lub wystąpienia klastrowanego trybu failover, należy spełnić wszystkie następujące kryteria określone w [postanowieniach licencyjnych dotyczących produktu:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Masz [mobilność licencji](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) za pośrednictwem [pakietu Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server klientom ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest on używany tylko do synchronizacji z serwerem podstawowym i w inny sposób obsługiwać pasywną bazę danych w ciepłym stanie gotowości. Jeśli obsługuje dane, takie jak raporty do klientów z aktywnymi obciążeniami programu SQL Server lub wykonywania jakiejkolwiek pracy innej niż określona w warunkach produktu, musi to być płatne licencjonowane wystąpienie programu SQL Server. Następujące działanie jest dozwolone w wystąpieniu pomocniczym: sprawdzanie spójności bazy danych lub CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również uruchomić podstawowe i odpowiadające wystąpienie odzyskiwania po awarii jednocześnie dla krótkich okresów testowania odzyskiwania po awarii co 90 dni. 
   1. Aktywna licencja programu SQL Server jest objęta pakietem Software Assurance i umożliwia **tylko jedno** pasywne dodatkowe wystąpienie programu SQL Server z maksymalnie taką samą ilością obliczeń jak licencjonowany serwer aktywny. 
   1. Pomocnicza maszyna wirtualna programu SQL Server korzysta z licencji [odzyskiwania po awarii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) w witrynie Azure portal.
   
1. **Co jest uważane za bierne wystąpienie?**

   Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server klientom ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest on używany tylko do synchronizacji z serwerem podstawowym i w inny sposób obsługiwać pasywną bazę danych w ciepłym stanie gotowości. Jeśli obsługuje dane, takie jak raporty do klientów z aktywnymi obciążeniami programu SQL Server lub wykonywania jakiejkolwiek pracy innej niż określona w warunkach produktu, musi to być płatne licencjonowane wystąpienie programu SQL Server. Następujące działanie jest dozwolone w wystąpieniu pomocniczym: sprawdzanie spójności bazy danych lub CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również uruchomić podstawowe i odpowiadające wystąpienie odzyskiwania po awarii jednocześnie dla krótkich okresów testowania odzyskiwania po awarii co 90 dni.
   

1. **Jakie scenariusze mogą korzystać z korzyści odzyskiwania po awarii (DR)?**

   [Przewodnik licencjonowania](https://aka.ms/sql2019licenseguide) zawiera scenariusze, w których można wykorzystać korzyści odzyskiwania po awarii. Więcej informacji można znaleźć w Warunkach korzystania z produktu i porozmawiać z kontaktami licencyjnymi lub menedżerem konta.

1. **Które subskrypcje obsługują korzyści odzyskiwania po awarii (DR)?**

   Kompleksowe programy, które oferują równoważne prawa subskrypcji pakietu Software Assurance jako świadczenia stałe, obsługują korzyści z odzyskiwania po awarii. Obejmuje to. ale nie jest ograniczona do, Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Subscription Agreement (EAS) oraz Server and Cloud Enrollment (SCE). Aby uzyskać więcej informacji, zapoznaj się z [warunkami produktu](https://www.microsoft.com/licensing/product-licensing/products) i porozmawiaj z kontaktami licencyjnymi lub menedżerem konta. 

   
 ## <a name="resource-provider"></a>Dostawca zasobów

1. **Czy zarejestrowanie mojej maszyny Wirtualnej u nowego dostawcy zasobów maszyny Wirtualnej programu SQL Server przyniesie dodatkowe koszty?**

   Nie. Dostawca zasobów maszyny Wirtualnej programu SQL Server umożliwia dodatkowe zarządzanie programem SQL Server na maszynie Wirtualnej platformy Azure bez dodatkowych opłat. 

1. **Czy dostawca zasobów maszyny Wirtualnej programu SQL Server jest dostępny dla wszystkich klientów?**
 
   Tak, tak długo, jak sql server maszyny Wirtualnej został wdrożony w chmurze publicznej przy użyciu modelu Menedżera zasobów, a nie model klasyczny. Wszyscy inni klienci mogą zarejestrować się u nowego dostawcy zasobów maszyny Wirtualnej programu SQL Server. Jednak tylko klienci z korzyścią [software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) mogą korzystać z własnej licencji, aktywując [korzyści hybrydowe platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na maszynie wirtualnej programu SQL Server. 

1. **Co się stanie z dostawcą zasobów (_Microsoft.SqlVirtualMachine_) zasobu, jeśli zasób maszyny Wirtualnej jest przenoszony lub porzucony?** 

   Gdy zasób Microsoft.Compute/VirtualMachine zostanie usunięty lub przeniesiony, skojarzony zasób Microsoft.SqlVirtualMachine zostanie powiadomiony o asynchronicznie replikować operację.

1. **Co się stanie z maszyną wirtualną, jeśli dostawca zasobów _(Microsoft.SqlVirtualMachine)_ zasób zostanie usunięty?**

    Zasób Microsoft.Compute/VirtualMachine nie ma wpływu, gdy zasób Microsoft.SqlVirtualMachine zostanie usunięty. Jednak zmiany licencjonowania będą domyślnie z powrotem do oryginalnego źródła obrazu. 

1. **Czy można zarejestrować samodzielnie wdrożone maszyny wirtualne programu SQL Server u dostawcy zasobów maszyny wirtualnej programu SQL Server?**

    Tak. Jeśli wdrożono program SQL Server z własnego nośnika i zainstalowano rozszerzenie IaaS SQL, można zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów, aby uzyskać korzyści w zakresie zarządzania wynikające z rozszerzenia IaaS SQL. Jednak nie można przekonwertować samodzielnie wdrożonej maszyny Wirtualnej programu SQL Server na płatność zgodnie z rzeczywistym użyciem.


   


## <a name="administration"></a>Administracja

1. **Czy można zainstalować drugie wystąpienie programu SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik instalacyjny programu SQL Server znajduje się w folderze na dysku **C.** Uruchom **program Setup.exe** z tej lokalizacji, aby dodać nowe wystąpienia programu SQL Server lub zmienić inne zainstalowane funkcje programu SQL Server na komputerze. Należy zauważyć, że niektóre funkcje, takie jak automatyczna kopia zapasowa, automatyczne łatanie i integracja usługi Azure Key Vault, działają tylko dla wystąpienia domyślnego lub nazwanego wystąpienia, które zostało poprawnie skonfigurowane (zobacz pytanie 3). 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Po pierwsze, sql server skojarzone rozliczeń może nadal występować w zależności od modelu licencji dla maszyny Wirtualnej. Po drugie, jak stwierdzono w poprzedniej odpowiedzi, istnieją funkcje, które opierają się na [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Jeśli odinstalujesz wystąpienie domyślne bez konieczności usuwania rozszerzenia IaaS również, rozszerzenie nadal szukać wystąpienia domyślnego i może generować błędy dziennika zdarzeń. Te błędy pochodzą z następujących dwóch źródeł: **Zarządzanie poświadczeniami programu Microsoft SQL Server** i program Microsoft SQL Server **IaaS Agent**. Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   Jeśli zdecydujesz się odinstalować wystąpienie domyślne, należy również odinstalować [rozszerzenie agenta programu SQL Server IaaS.](virtual-machines-windows-sql-server-agent-extension.md) 

1. **Czy można używać nazwanego wystąpienia programu SQL Server z rozszerzeniem IaaS?**
   
   Tak, jeśli nazwane wystąpienie jest jedynym wystąpieniem na serwerze SQL Server i jeśli oryginalne [wystąpienie domyślne zostało poprawnie odinstalowane](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Jeśli nie ma wystąpienia domyślnego i istnieje wiele nazwanych wystąpień na jednej maszynie wirtualnej programu SQL Server, rozszerzenia agenta programu SQL Server IaaS nie powiedzie się zainstalowanie. 

1. **Czy można całkowicie usunąć program SQL Server z maszyny Wirtualnej programu SQL Server?**

   Tak, ale nadal będą naliczane opłaty za maszynę wirtualną programu SQL Server zgodnie z opisem w [wskazówki dotyczące cen maszyn wirtualnych platformy Azure programu SQL Server.](virtual-machines-windows-sql-server-pricing-guidance.md) Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.
   
## <a name="updating-and-patching"></a>Aktualizowanie i łatanie

1. **Jak zmienić wersję/edycję programu SQL Server na inną na maszynie wirtualnej platformy Azure?**

   Klienci mogą zmienić wersję/edycję programu SQL Server przy użyciu nośnika instalacyjnego zawierającego odpowiednią wersję/edycję programu SQL Server. Po zmianie edycji za pomocą witryny Azure Portal zmodyfikuj właściwość edycji maszyny wirtualnej w celu dokładnego odzwierciedlenia rozliczeń za tę maszynę wirtualną. Aby uzyskać więcej informacji, zobacz zmienianie wersji maszyny [Wirtualnej programu SQL Server](virtual-machines-windows-sql-change-edition.md). Nie ma różnicy w rozliczeniach dla różnych wersji programu SQL Server, więc po zmianie wersji programu SQL Server nie są potrzebne żadne dalsze działania.

1. **Gdzie można uzyskać nośnik instalacyjny, aby zmienić wersję lub wersję programu SQL Server?**

   Klienci, którzy mają [pakiet software assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klienci, którzy nie mają pakietu software assurance, mogą używać nośnika instalacyjnego z obrazu maszyny Wirtualnej programu Marketplace SQL Server, który ma żądaną wersję.
   
1. **W jaki sposób aktualizacje i dodatki Service Pack są stosowane na maszynie wirtualnej programu SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. W przypadku systemu operacyjnego można ręcznie zastosować aktualizacje systemu Windows lub włączyć usługę planowania o nazwie [Automatyczne łatanie](virtual-machines-windows-sql-automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

1. **Czy mogę uaktualnić wystąpienie programu SQL Server 2008 / 2008 R2 po zarejestrowaniu go u dostawcy zasobów maszyny wirtualnej programu SQL Server?**

   Tak. Można użyć dowolnego nośnika instalacyjnego, aby uaktualnić wersję i wersję programu SQL Server, a następnie uaktualnić [tryb rozszerzenia SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) z _dowolnego agenta_ do _pełnego_. W ten sposób zapewni dostęp do wszystkich korzyści z rozszerzenia SQL IaaS, takich jak zarządzanie portalem, automatyczne tworzenie kopii zapasowych i automatyczne łatanie. 

1. **Jak uzyskać bezpłatne rozszerzone aktualizacje zabezpieczeń dla moich wystąpień po zakończeniu pomocy technicznej sql server 2008 i SQL Server 2008 R2?**

   Możesz uzyskać [bezpłatne rozszerzone aktualizacje zabezpieczeń,](virtual-machines-windows-sql-server-2008-eos-extend-support.md) przenosząc program SQL Server w stanie zagrożenia na maszynę wirtualną SQL platformy Azure. Aby uzyskać więcej informacji, zobacz [koniec opcji pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Ogólne

1. **Czy wystąpienia klastra trybu failover programu SQL Server (FCI) są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Wystąpienie klastra trybu failover można zainstalować przy użyciu [udziałów plików premium (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) lub [bezpośrednich miejsc do magazynowania (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) dla podsystemu magazynu. Udziały plików w układce zapewniają możliwości we/wyspom i odpowiedzi na nie przepustowość, które spełnią potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z we/wy należy rozważyć użycie miejsc do magazynowania bezpośrednio na podstawie dysków premium lub ultra-dysków. Alternatywnie można użyć rozwiązań klastrowania lub magazynu innych firm, zgodnie z opisem w [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines.](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)

   > [!IMPORTANT]
   > W tej chwili _pełne_ [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwane dla programu SQL Server FCI na platformie Azure. Zaleca się odinstalowanie _pełnego_ rozszerzenia z maszyn wirtualnych, które uczestniczą w FCI i zainstalować rozszerzenie w trybie _uproszczonym_ zamiast tego. To rozszerzenie obsługuje funkcje, takie jak automatyczna kopia zapasowa i łatanie oraz niektóre funkcje portalu dla programu SQL Server. Te funkcje nie będą działać dla maszyn wirtualnych programu SQL Server po odinstalowaniu _pełnego_ agenta.

1. **Jaka jest różnica między maszynami wirtualnymi programu SQL Server a usługą bazy danych SQL?**

   Koncepcyjnie uruchamianie programu SQL Server na maszynie wirtualnej platformy Azure nie różni się od uruchamiania programu SQL Server w zdalnym centrum danych. Z drugiej strony [baza danych SQL](../../../sql-database/sql-database-technical-overview.md) oferuje bazę danych jako usługę. Z bazy danych SQL nie masz dostępu do maszyn, które hostują bazy danych. Aby uzyskać pełne porównanie, zobacz [Wybieranie opcji programu SQL Server w chmurze: Baza danych SQL (PaaS) platformy Azure (PaaS) lub program SQL Server na maszynach wirtualnych platformy Azure (IaaS).](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

1. **Jak zainstalować narzędzia SQL Data na mojej maszynie Wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia SQL Data z [programu Microsoft SQL Server Data Tools — Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Czy transakcje rozproszone z usługą MSDTC są obsługiwane na maszynach wirtualnych programu SQL Server?**
   
    Tak. Lokalny kod DTC jest obsługiwany dla dodatku SP2 dla programu SQL Server 2016 i większej. Jednak aplikacje muszą być testowane podczas korzystania z zawsze na grupy dostępności, jak transakcje w locie podczas pracy awaryjnej zakończy się niepowodzeniem i muszą być ponowione. Usługa Clustered DTC jest dostępna począwszy od systemu Windows Server 2019. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprowizuj maszynę wirtualną systemu Windows z systemem SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie rozwoju dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprowizuj maszynę wirtualną systemu SQL Server z systemem Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
