---
title: Informacje o wersji programu Microsoft Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące usługi Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
git ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 1d3e4724820f7109eb9b695fe06d221a2796c26f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44722209"
---
# <a name="azure-stack-development-kit-release-notes"></a>Informacje o wersji usługi Azure Stack Development Kit  
Ten artykuł zawiera informacje dotyczące ulepszeń, poprawek i znanych problemach w usłudze Azure Stack Development Kit. Jeśli nie masz pewności, której wersji używasz, możesz to zrobić [korzystanie z portalu, aby sprawdzić](.\.\azure-stack-updates.md#determine-the-current-version).

> Na bieżąco what's new in ASDK subskrybując [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanału informacyjnego](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11808097"></a>Tworzenie 1.1808.0.97

### <a name="new-features"></a>Nowe funkcje
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

- <!-- 2682594   | ASDK  -->   **Wszystkie środowiska Azure Stack teraz używać format strefy czasowej uniwersalnego czasu koordynowanego (UTC).**  Wszystkie dane dziennika, a teraz powiązane informacje wyświetlane w formacie UTC. 

- <!-- 2437250  | IS  ASDK --> **Dyski zarządzane są obsługiwane.** Możesz teraz używać usługi Managed Disks w maszynach wirtualnych usługi Azure Stack i zestawy skalowania maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [stosu usługi Azure Managed Disks: różnice i zagadnienia](/azure/azure-stack/user/azure-stack-managed-disk-considerations).
 
- <!-- 2563799  | IS  ASDK -->  **Usługa Azure Monitor**. Takich jak Azure Monitor na platformie Azure usługi Azure Monitor w usłudze Azure Stack zapewnia podstawowy poziom infrastrukturą metryk i dzienników dla większości usług. For more information, see [Azure Monitor on Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

- <!-- ASDK --> **Galeria elementów dla zestawów skalowania maszyn wirtualnych są teraz wbudowane**.  Zestaw skalowania maszyn wirtualnych elementy galerii są teraz udostępniane w portalach użytkowników i administratorów bez konieczności pobierania ich. 

- <!-- IS, ASDK --> **Skalowanie zestawu skalowania maszyn wirtualnych**.  Można użyć portalu [skalowanie zestawu skalowania maszyn wirtualnych](/azure/azure-stack/azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (zestawu skalowania maszyn wirtualnych).   

- <!-- 2489570 | IS ASDK--> **Obsługa niestandardowych konfiguracji zasad protokołu IPSec/IKE** dla [bram sieci VPN w usłudze Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Elementu portalu marketplace Kubernetes**. Teraz można wdrożyć klastry Kubernetes za pomocą [elementu portalu Kubernetes Marketplace](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Użytkownicy mogą wybrać element Kubernetes i wypełnij kilka parametrów do wdrażania klastra Kubernetes w usłudze Azure Stack. Szablony ma na celu ułatwić użytkownikom w celu wdrożenia rozwiązania Kubernetes i testowania instalacji w kilku krokach.

- <!-- | IS ASDK--> **Łańcuch bloków szablony**. Można teraz wykonać [wdrożeń konsorcjum Ethereum](/azure/azure-stack/azure-stack-ethereum) w usłudze Azure Stack. Można znaleźć trzy nowe szablony w [usługi Azure Stack szablonów Szybki Start](https://github.com/Azure/AzureStack-QuickStart-Templates). Umożliwiają one użytkownikowi wdrażanie i konfigurowanie sieci Ethereum konsorcjum zawierającym wiele elementów członkowskich przy minimalnej znajomości platformy Azure i Ethereum. Szablony ma na celu ułatwić użytkownikom do instalacji i testowania aplikacji łańcucha bloków wdrożeń w kilku krokach.


### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS ASDK--> Rozwiązaliśmy problem w przypadku tworzenia zestaw dostępności w portalu, co spowodowało w zestawie, domeny błędów i domeny aktualizacji 1.

- <!-- IS ASDK --> Ustawienia skalowania zestawów skalowania maszyn wirtualnych są teraz dostępne w portalu.  

- <!-- 2494144- IS, ASDK --> Rozwiązano problem, który uniemożliwił niektóre rozmiary maszyn wirtualnych serii F pojawiające się podczas wybierania rozmiaru maszyny Wirtualnej dla wdrożenia. 

- <!-- IS, ASDK --> Ulepszenia dotyczące wydajności podczas tworzenia maszyn wirtualnych i nie tylko zoptymalizowane pod kątem użycia podstawowego magazynu.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack


### <a name="changes"></a>Zmiany
- <!-- 1697698  | IS, ASDK --> *Samouczki szybkiego startu* w link teraz pulpitu nawigacyjnego portalu użytkowników do odpowiednich artykułów w dokumentacji online usługi Azure Stack.

- <!-- 2515955   | IS ,ASDK--> *Wszystkie usługi* zastępuje *więcej usług* w portalach administratora i użytkownika usługi Azure Stack. Teraz możesz używać *wszystkich usług* jako alternatywę, można przejść w portalach usługi Azure Stack w taki sam sposób jak w witrynie Azure Portal.

- <!-- TBD | IS, ASDK --> **+ Utwórz zasób** zastępuje **+ nowy** w portalach administratora i użytkownika usługi Azure Stack.  Teraz możesz używać *+ Utwórz zasób* jako alternatywę, można przejść w portalach usługi Azure Stack w taki sam sposób jak w witrynie Azure Portal. 

- <!--  TBD – IS, ASDK --> *Podstawowa A* rozmiarów maszyn wirtualnych są wycofywane w przypadku [tworzenie zestawów skalowania maszyn wirtualnych](.\.\azure-stack-compute-add-scalesets.md) (VMSS) za pośrednictwem portalu. Do utworzenia zestawu skalowania maszyn wirtualnych o tym rozmiarze, użyj programu PowerShell lub w szablonie. 

### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal  
- <!-- 2967387 – IS, ASDK --> Konto używane do logowania do portalu administratora lub użytkownika usługi Azure Stack jest wyświetlany jako **Niezidentyfikowany użytkownik**. Dzieje się tak, gdy konto nie ma albo *pierwszy* lub *ostatniego* nazwy. Aby obejść ten problem, należy edytować konto użytkownika, aby podać nazwę pierwszego lub ostatniego. Należy następnie zaloguj i zaloguj ponownie do portalu. 

-  <!--  2873083 - IS ASDK --> Kiedy używać portalu, aby utworzyć zestaw skalowania maszyn wirtualnych zestawu (zestawu skalowania maszyn wirtualnych), *rozmiaru wystąpienia* listy rozwijanej nie załadować się poprawnie, gdy używasz programu Internet Explorer. Aby obejść ten problem, należy użyć innej przeglądarki podczas korzystania z portalu do tworzenia zestawu skalowania maszyn wirtualnych.  

- <!-- TBD  ASDK --> Domyślna strefa czasowa dla wszystkich wdrożeń usługi Azure Stack jest teraz ustawić uniwersalny czas koordynowany (UTC). Strefa czasowa można wybrać podczas instalowania usługi Azure Stack, jednak automatycznie powraca on do UTC domyślnie podczas instalacji.

- <!-- 2931230 – IS  ASDK --> Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

- <!--2760466 – IS  ASDK --> Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](.\.\azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji. 

- <!-- TBD - IS ASDK --> Dwa typy subskrypcji administracyjne, które zostały [wprowadzonym w wersji 1804](.\.\azure-stack-update-1804.md#new-features) nie powinny być używane. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **subskrypcję dostawcy domyślne** typu.

- <!-- TBD -  IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.



#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu.  

- <!-- 2368581 - IS. ASDK --> Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.


#### <a name="compute"></a>Wystąpienia obliczeniowe  
- <!-- 2869209 – IS, ASDK --> Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenia cmdlet zakończy się niepowodzeniem z następującym błędem: *długotrwałych operacji nie powiodło się ze stanem "Niepowodzenie"*. 

- <!--  2966665 – IS, ASDK --> Dołączanie dysków SSD dyski danych do rozmiaru premium zarządzanych dysków maszyn wirtualnych (DS, DSv2, Fs i Fs_V2) kończy się niepowodzeniem z powodu błędu: *nie można zaktualizować dysków maszyny wirtualnej "vmname" Błąd: żądanie nie można wykonać operacji, ponieważ typ konta magazynu " Premium_LRS nie jest obsługiwana dla rozmiaru maszyny Wirtualnej "Standard_DS/Ds_V2/FS/Fs_v2)*

   Aby obejść ten problem, należy użyć *Standard_LRS* dyski danych zamiast *dysków Premium_LRS*. Korzystanie z *Standard_LRS* dysków z danymi nie zmienia się operacji We/Wy lub rozliczeń kosztów.  

- <!--  2795678 – IS, ASDK --> Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. 

   Możesz bezpiecznie zignorować ostrzeżenie, że jest wyświetlany komunikat: *przycisk OK, aby używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Zestaw skalowania maszyn wirtualnych (VMSS) utworzyć środowisko zawiera opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo użyj szablonu usługi ARM, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.

- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Można następnie ponów próbę pobierania obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.

- <!-- 2724961- IS ASDK --> Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows z włączoną gościa systemu operacyjnego diagnostyczne, wykres procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie będzie mogła wyświetlać dane metryk.
 
  Aby znaleźć wykresu procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do **metryki** bloku i wyświetlanie wszystkich obsługiwanych Windows maszyn wirtualnych gościa metryki.

 

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 1902460 -  IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- 2702741 -  IS ASDK --> Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

- <!-- 2529607 - IS ASDK --> W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

-   <!-- 2664148 - IS ASDK --> W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  


#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->




## <a name="build-11807076"></a>Tworzenie 1.1807.0.76

### <a name="new-features"></a>Nowe funkcje
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

- <!-- 1658937 | ASDK, IS --> **Rozpocznij tworzenie kopii zapasowych zgodnie ze wstępnie zdefiniowanym harmonogramem** — znajduje się urządzenie, w usłudze Azure Stack można teraz automatycznie wyzwoli infrastruktury kopii zapasowych, okresowo, aby wyeliminować interwencji człowieka. Usługi Azure Stack będą również automatycznie wyczyścić udziału zewnętrznego dla kopii zapasowych, które są starsze niż okres przechowywania zdefiniowany. Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Dodane dane transferu czasu, do łącznego czasu tworzenia kopii zapasowej.** Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Pojemność zewnętrznych kopii zapasowych zawiera obecnie prawidłowe pojemności udziału zewnętrznego.** (Wcześniej było to trwale kodować do 10 GB.) Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
- <!-- 2753130 |  IS, ASDK   -->  **Szablony usługi Azure Resource Manager obsługuje teraz elementu warunek** — teraz można wdrożyć zasobów w szablonie usługi Azure Resource Manager przy użyciu warunku. Można zaprojektować szablon, aby wdrożyć zasób, w oparciu o warunek, np. Sprawdzanie, czy występuje wartość parametru. Aby dowiedzieć się, jak przy użyciu szablonu jako warunek, zobacz [warunkowe wdrażanie zasobu](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) i [sekcji zmiennych szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) w dokumentacji platformy Azure. 

   Można również użyć szablonów [wdrażanie zasobów na więcej niż jedną subskrypcję lub grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **Obsługa wersji zasobu Microsoft.Network interfejsu API zostały zaktualizowane** obsługę interfejsu API w wersji 2017-10-01 od 2015-06-15 dotyczące zasobów sieciowych w usłudze Azure Stack.  Obsługa wersji zasobu od 2017-10-01 i 2015-06-15 jest niedostępna w tej wersji, ale będzie dostępna w przyszłej wersji.  Zapoznaj się [zagadnienia dotyczące sieci usługi Azure Stack](.\.\user\azure-stack-network-differences.md) dla różnice w funkcjonalności.

- <!-- 2272116 | IS, ASDK   -->  **Usługa Azure Stack dodano obsługę wstecznego wyszukiwania DNS dla zewnętrznie połączonego z punktów końcowych infrastruktury Azure Stack** (które jest przeznaczone dla portalu, adminportal, zarządzania i adminmanagement). Dzięki temu nazwy zewnętrzny punkt końcowy usługi Azure Stack, aby być rozwiązane wyłącznie z adresu IP.

- <!-- 2780899 |  IS, ASDK   --> **Usługa Azure Stack obsługuje teraz dodawanie interfejsów sieciowych dodatkowe do istniejącej maszyny Wirtualnej.**  Ta funkcja jest dostępna za pomocą portalu, programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) w dokumentacji platformy Azure. 

- <!-- 2222444 | IS, ASDK   -->  **Ulepszenia dokładności i zwiększa odporność wprowadzono sieć mierników użycia**. Liczniki użycia sieci są teraz bardziej precyzyjne i uwzględniać subskrypcji konto zostało zawieszone, okresów awarii i sytuacje wyścigu.

- <!-- 2297790 | IS, ASDK -->  **Ulepszenia usługi Azure Stack klienta syslog (funkcja w wersji zapoznawczej)**. Ten klient umożliwia przekazywanie inspekcji i dzienniki związane z infrastruktury Azure Stack w celu syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrznego do usługi Azure Stack. Klient usługi syslog obsługuje teraz protokół TCP za pomocą zwykłego tekstu lub szyfrowania protokołu TLS 1.2, ostatnim jest domyślna konfiguracja. Tylko do serwera lub wzajemnego uwierzytelniania można skonfigurować połączenia TLS.

  Aby skonfigurować jak komunikuje się klient syslog (takich jak protokół, szyfrowania i uwierzytelniania) z serwera syslog, należy użyć polecenia cmdlet Set-SyslogServer. To polecenie cmdlet jest dostępna z uprzywilejowanym punktu końcowego (program ten). 

  Aby dodać certyfikat klienta do wzajemnego uwierzytelniania klient TLS 1.2 syslog, należy użyć polecenia cmdlet Set-SyslogClient w program ten.

  W tej wersji zapoznawczej widać znacznie większej liczby inspekcje i alerty. 

  Ponieważ ta funkcja jest nadal w wersji zapoznawczej, możesz nie należy polegać na niej w środowisku produkcyjnym.

  Aby uzyskać więcej informacji, zobacz [przekazywania usługi syslog usługi Azure Stack](.\.\azure-stack-integrate-security.md).

- <!-- ####### | IS, ASDK -->  **Usługa Azure Resource Manager zawiera nazwę regionu.** W tej wersji pobrane z usługi Azure Resource Manager obiekty będzie teraz obejmować atrybucie nazwy w regionie. Jeśli istniejący skrypt programu PowerShell bezpośrednio przekazuje obiekt do innego polecenia cmdlet, skrypt może powodować błąd i zakończyć się niepowodzeniem. To jest zachowanie zgodne z usługi Azure Resource Manager i wymaga klienta wywołującego do odjęcia atrybut regionu. Aby uzyskać więcej informacji na temat usługi Azure Resource Manager, zobacz [dokumentacja usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

- <!-- TBD | IS, ASDK -->  **Przenieś subskrypcje między dostawcami delegowane.** Możesz teraz przejść subskrypcje subskrypcjami nowej lub istniejącej delegowane dostawcy, które należą do tej samej dzierżawie katalogu. Subskrypcje należące do subskrypcji dostawcy domyślne można także przenosić do subskrypcji dostawcy delegowana w tej samej dzierżawie katalogu. Aby uzyskać więcej informacji, zobacz [delegowanie ofert w usłudze Azure Stack](.\.\azure-stack-delegated-provider.md).
 
- <!-- 2536808 IS ASDK --> **Ulepszone czas tworzenia maszyny Wirtualnej** dla maszyn wirtualnych, które zostały utworzone z obrazów, możesz pobrać z witryny Azure marketplace.

### <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- TBD | ASDK, IS --> Aby bardziej niezawodna wprowadzono ulepszenia różnych procesu aktualizacji. Ponadto poprawki zostały wprowadzone do podstawowej infrastruktury, co zwiększa opróżnianie węzła, w tym samym minimalizując potencjalnych przerw dla obciążeń podczas aktualizacji.

-   <!--2292271 | ASDK, IS --> Rozwiązano problem, gdy zmodyfikowane limit przydziału nie zostało zastosowane względem w przypadku istniejących subskrypcji.  Teraz kiedy zwiększyć limit przydziału dla zasobu sieciowego, który jest częścią oferty i Plan skojarzony z subskrypcji użytkownika, nowy limit dotyczy istniejące subskrypcje, a także nowe subskrypcje.

- <!-- 2448955 | IS ASDK --> Teraz można pomyślnie odpytać dzienników aktywności dla systemów, które są wdrażane w strefie czasowej UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Sprawdzanie wymagań wstępnych dla parametrów konfiguracji kopii zapasowej (ścieżka/nazwa użytkownika/hasła/szyfrowania klucza) nie jest już ustawia nieprawidłowe ustawienia konfiguracji kopii zapasowej. (Wcześniej ustawiono nieprawidłowe ustawienia do tworzenia kopii zapasowej i kopii zapasowej może następnie zakończyć się niepowodzeniem po wyzwoleniu.)

- <!-- 2215948 |  ASDK, IS --> Listy kopii zapasowych Odświeża teraz, gdy ręcznie usunąć kopii zapasowej z udziału zewnętrznego.

- <!-- 2360715 |  ASDK, IS -->  Po skonfigurowaniu integracji centrum danych nie jest już dostępu usług AD FS plik metadanych z udziału. Aby uzyskać więcej informacji, zobacz [konfigurowania integracji usług AD FS, zapewniając pliku metadanych Federacji](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Rozwiązano problem uniemożliwił użytkownikom możliwość przypisania istniejącego publicznego adresu IP, gdyby zostały wcześniej przypisane do interfejsu sieciowego lub modułu równoważenia obciążenia do nowego interfejsu sieciowego lub modułu równoważenia obciążenia.  

- <!-- 2551834 - IS, ASDK --> Po wybraniu *Przegląd* dla konta magazynu w portalach administrator lub użytkownik, *Essentials* okienku zostaną wyświetlone wszystkie oczekiwane informacje poprawnie. 

- <!-- 2551834 - IS, ASDK --> Po wybraniu *tagi* dla konta magazynu w portalach administratora lub użytkownika, informacje teraz wyświetlany poprawnie.

- <!-- TBD - IS ASDK --> Ta wersja usługi Azure Stack rozwiązuje problem, który uniemożliwił stosowania aktualizacji sterowników z pakietów rozszerzeń OEM.

-   <!-- 2055809- IS ASDK --> Rozwiązano problem uniemożliwiający usunięcie maszyn wirtualnych z poziomu bloku obliczeń, gdy maszyna wirtualna nie powiodło się.  

- <!--  2643962 IS ASDK -->  Zgłoszono alert dotyczący *niską pojemność pamięci* nie jest już wyświetlany niepoprawnie.

- <!--  TBD ASDK --> Maszyny wirtualnej, która obsługuje ten punkt końcowy uprawnień (program ten) został zwiększony do 4GB. W ASDK ta maszyna wirtualna ma nazwę AzS ERCS01.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal  
- <!-- 2931230 – IS  ASDK --> Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

- <!--2760466 – IS  ASDK --> Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](.\.\azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji. 

- <!-- TBD - IS ASDK --> Dwa typy subskrypcji administracyjne, które zostały [wprowadzonym w wersji 1804](.\.\azure-stack-update-1804.md#new-features) nie powinny być używane. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **subskrypcję dostawcy domyślne** typu.

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- <!--  TBD | ASDK -->  Domyślna strefa czasowa dla wdrożenia usługi Azure Stack ustawi Pobierz teraz na czas UTC. Strefa czasowa można wybrać podczas instalowania usługi Azure Stack, jednak automatycznie zostanie przywrócona na czas UTC domyślnie podczas instalacji.

#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu.  

- <!-- 2368581 - IS. ASDK --> Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.

- <!-- TBD - IS. ASDK --> Podczas uruchamiania polecenia cmdlet Test-AzureStack w punkcie końcowym uprawnień (program ten), testu wygeneruje komunikat OSTRZEGAJ/niepowodzenie ERCS maszyny wirtualnej. Można nadal używać ASDK.

#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- 2494144 - IS, ASDK --> Wybierając rozmiar maszyny wirtualnej do wdrażania maszyn wirtualnych, niektóre rozmiary serii F maszyn wirtualnych nie są widoczne jako część selektor rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie są wyświetlane w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Obejść ten problem należy użyć jednej z następujących metod do wdrożenia maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, którego chcesz użyć.

  - **Szablon usługi Azure Resource Manager:** gdy używasz szablonu, ustaw *vmSize* w szablonie wartość rozmiaru maszyny Wirtualnej, w której chcesz użyć. Na przykład, następujący wpis jest używany do wdrażania maszyn wirtualnych, który używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Interfejs wiersza polecenia platformy Azure:** można użyć [tworzenie az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Program PowerShell:** przy użyciu programu PowerShell, można użyć [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Można następnie ponów próbę pobierania obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.

- <!-- 2724961- IS ASDK --> Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows przy użyciu gościa systemu operacyjnego diagnostycznych włączona, na stronie Przegląd maszyny Wirtualnej nie wyświetla danych metryk. 

   Aby znaleźć danych metryk, takich jak wykres procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do **metryki** bloku i wyświetlanie wszystkich obsługiwanych Windows maszyn wirtualnych gościa metryki.

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 1902460 -  IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- 2702741 -  IS ASDK --> Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

- <!-- 2529607 - IS ASDK --> W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

-   <!-- 2664148 - IS ASDK --> W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL
- <!-- TBD - ASDK --> Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  

- <!-- TBD - IS ASDK --> Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili. W przyszłej aktualizacji usługi App Service zostanie wdrożona w nowe *pomiaru subskrypcji* która została wprowadzona w usłudze Azure Stack w wersji 1804. Gdy zliczania jest przeznaczony do użytku, wszystkie istniejące wdrożenia będą migrowane do tego nowego typu subskrypcji.

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->






## <a name="build-11805147"></a>Tworzenie 1.1805.1.47

> [!TIP]  
> Na podstawie opinii klientów, jest dostępna aktualizacja do wersji schematu używane dla usługi Microsoft Azure Stack. Począwszy od tej aktualizacji 1805, nowy schemat lepiej reprezentuje bieżącą wersję w chmurze.  
>
> Wersja schematu jest teraz *Version.YearYearMonthMonth.MinorVersion.BuildNumber* gdzie zestawy drugi i trzeci wskazywać wersji i wydania. Na przykład 1805.1 reprezentuje *wydania do produkcji* wersji 1805 (RTM).  


### <a name="new-features"></a>Nowe funkcje
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **Usługa Azure Stack obejmuje teraz *Syslog* klienta** jako *funkcja w wersji zapoznawczej*. Ten klient umożliwia przekazywanie dzienników inspekcji i zabezpieczeń związane z infrastruktury Azure Stack w celu Syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrznej dla usługi Azure Stack. Klient usługi Syslog obsługuje obecnie tylko nieuwierzytelnione połączenia protokołu UDP przez domyślny port 514. Ładunek każdego komunikatu dziennika systemu jest sformatowany w typowych Event Format (CEF).

  Aby skonfigurować klienta usługi Syslog, należy użyć **SyslogServer zestaw** polecenia cmdlet w punkcie końcowym uprzywilejowanych.

  W tej wersji zapoznawczej można napotkać następujące trzy alerty. Gdy przedstawiony przez usługę Azure Stack, te alerty zawierają *opisy* i *korygowania* wskazówki.
  - Tytuł: Integralność kodu wyłączone  
  - Tytuł: Integralność kodu w trybie inspekcji
  - Tytuł: Konto użytkownika utworzone

  Chociaż ta funkcja jest dostępna w wersji zapoznawczej, nie powinien być powoływane w środowiskach produkcyjnych.   


### <a name="fixed-issues"></a>Rozwiązane problemy
- Rozwiązaliśmy ten problem, które zablokowały [otworzyć nowe żądanie obsługi z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z poziomu portalu administracyjnego. Obecnie ta opcja działa zgodnie z oczekiwaniami.

- <!--  TBD ASDK --> Maszyny wirtualnej, która obsługuje ten punkt końcowy uprawnień (program ten) został zwiększony do 4GB. W ASDK ta maszyna wirtualna ma nazwę AzS ERCS01.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack


<!-- ### Changes  -->


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal
- <!-- 2931230 – IS  ASDK --> Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

- <!-- 2551834 - IS, ASDK --> Po wybraniu **Przegląd** dla konta magazynu w portalach administratora lub użytkownika informacji z *Essentials* nie są wyświetlane w okienku.  Okienko podstawy Wyświetla informacje o koncie, takie jak jego *grupy zasobów*, *lokalizacji*, i *identyfikator subskrypcji*.  Omówienie inne opcje są dostępne, takich jak *usług* i *monitorowanie*, jak również jako opcji do *Otwórz w Eksploratorze* lub *Usuń konto magazynu* .  

  Aby wyświetlić dostępne informacje, należy użyć [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell.

- <!-- 2551834 - IS, ASDK --> Po wybraniu **tagi** dla konta magazynu w portalach administratora lub użytkownika, informacje nie ładuje się i nie są wyświetlane.  

  Aby wyświetlić dostępne informacje, należy użyć [Get AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell.

- <!-- TBD - IS ASDK --> Nie należy używać nowych typów subskrypcji administracyjne *pomiaru subskrypcji*, i *subskrypcji zużycie*. Te nowe typy subskrypcji zostały wprowadzone w wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać *domyślny dostawca* typu subskrypcji.  
- <!-- TBD - IS ASDK --> Nie można zastosować aktualizacje sterowników za pomocą pakietu rozszerzenia producenta OEM w tej wersji usługi Azure Stack.  Nie ma sposobu obejścia tego problemu.
 
- <!-- TBD - IS ASDK --> Możliwość [otworzyć nowe żądanie obsługi z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z w ramach administrator portalu nie jest dostępna. Zamiast tego należy użyć następującego linku:     
    - Azure Stack Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

    Obydwa alerty #1 i #2 można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu. 

  Mogą również pojawić się następujący alert dotyczący *pojemności*. Dla tego alertu wartość procentowa dostępnej pamięci określonych w opisie mogą się różnić:  

  Zgłoś alert, #3:
   - Nazwa: Brak pamięci pojemności
   - Waga: krytyczny
   - SKŁADNIK: pojemności
   - Opis: Region zużyła ponad 80,00% dostępnej pamięci. Tworzenie maszyn wirtualnych z dużą ilością pamięci może zakończyć się niepowodzeniem.  

  W tej wersji usługi Azure Stack ten alert może wyzwalać niepoprawnie. Jeśli maszyny wirtualne dzierżawcy w dalszym ciągu pomyślnie wdrożone, można bezpiecznie zignorować ten alert. 
  
  Alert #3 nie będą automatycznie zamknięte. Zamknij ten alert usługi Azure Stack utworzy tego samego alertu w ciągu 15 minut.  

- <!-- 2368581 - IS. ASDK --> Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.

- <!-- TBD - IS. ASDK --> Podczas uruchamiania polecenia cmdlet Test-AzureStack w punkcie końcowym uprawnień (program ten), spowoduje wygenerowanie komunikatu OSTRZEGAJ ERCS maszyny wirtualnej. Można nadal używać ASDK. 


#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD - IS, ASDK --> Wybierając rozmiar maszyny wirtualnej do wdrażania maszyn wirtualnych, niektóre rozmiary serii F maszyn wirtualnych nie są widoczne jako część selektor rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie są wyświetlane w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Obejść ten problem należy użyć jednej z następujących metod do wdrożenia maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, którego chcesz użyć.

  - **Szablon usługi Azure Resource Manager:** gdy używasz szablonu, ustaw *vmSize* w szablonie wartość rozmiaru maszyny Wirtualnej, w której chcesz użyć. Na przykład, następujący wpis jest używany do wdrażania maszyn wirtualnych, który używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Interfejs wiersza polecenia platformy Azure:** można użyć [tworzenie az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Program PowerShell:** przy użyciu programu PowerShell, można użyć [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Można następnie ponów próbę pobierania obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.

#### <a name="networking"></a>Networking
- <!-- TBD - IS ASDK --> Nie można utworzyć trasy zdefiniowane przez użytkownika w jednym portalu administratora lub użytkownika. Jako obejście tego problemu, należy użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 2388980 -  IS ASDK --> Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adres IP, w związku z oryginalnej maszyny Wirtualnej, a nie nowy.

- <!-- 2292271 - IS ASDK --> Jeśli zostanie podniesiony limitu przydziału dla zasobu sieciowego, który jest częścią oferty i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowych subskrypcjach, które są tworzone po nastąpi zwiększenie limitu przydziału.

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz instrukcje [udostępnić planu dodatku](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która zawiera zasoby strefy DNS lub skojarzone z nią zasoby tabeli tras. Aby pomyślnie usunąć subskrypcję, musisz najpierw usunąć strefy DNS i tabelę tras zasobów z subskrypcji dzierżawcy.


- <!-- 1902460 -  IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- TBD -  IS ASDK --> Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL
- <!-- TBD - ASDK --> Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  

- <!-- TBD - IS ASDK --> Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->

