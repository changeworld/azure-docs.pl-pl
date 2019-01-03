---
title: Informacje o wersji programu Microsoft Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące usługi Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: d1ff154c42709f0c672b30f7ec51a436fb44ce13
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724746"
---
# <a name="asdk-release-notes"></a>Informacje o wersji ASDK 
 
Ten artykuł zawiera informacje dotyczące ulepszeń, poprawek i znanych problemach w usłudze Azure Stack Development Kit (ASDK). Jeśli nie masz pewności, której wersji używasz, możesz to zrobić [korzystanie z portalu, aby sprawdzić](../azure-stack-updates.md#determine-the-current-version).

> Na bieżąco what's new in ASDK subskrybując [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanału informacyjnego](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-118110101"></a>Tworzenie 1.1811.0.101

### <a name="new-features"></a>Nowe funkcje

Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack:  

- Istnieje zestaw nowych minimalne i zalecane wymagania sprzętowe i programowe dla ASDK. Te nowe zalecane specyfikacje są udokumentowane w artykule [zagadnienia dotyczące wdrażania usługi Azure Stack](asdk-deploy-considerations.md). Jak platforma Azure Stack został przekształcony, więcej usług są teraz dostępne i inne zasoby mogą być wymagane. Zwiększona specyfikacje odzwierciedlać te poprawione zalecenia.

### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- TBD - IS ASDK --> 
- Rozwiązano problem, w którym dane licznika użycia adresów IP publicznego wykazało, że takie same **Data/godzina zdarzenia** wartość dla każdego rekordu, zamiast **TimeDate** sygnatury, który pokazuje, kiedy rekord został utworzony. Można teraz używać tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- 3099544 – IS, ASDK --> 
- Rozwiązano problem, który wystąpił podczas tworzenia nowej maszyny wirtualnej (VM) przy użyciu portalu Azure Stack. Wybieranie rozmiaru maszyny Wirtualnej spowodowane kolumna USD/miesiąc, aby wyświetlić **Unavailable** wiadomości. Ta kolumna nie jest już wyświetlany; Wyświetlanie maszyny Wirtualnej cen kolumna nie jest obsługiwana w usłudze Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Rozwiązano problem z portalu administratora przy uzyskiwaniu dostępu do szczegółów dowolnej subskrypcji użytkownika po zamknięciu bloku i klikając **ostatnie**, Nazwa subskrypcji użytkownika nie były wyświetlane. Nazwa subskrypcji użytkownika pojawia się.

<!-- 3060156 - IS ASDK --> 
- Rozwiązano problem w portalach administratora i użytkownika: klikając ustawienia portalu i wybierając **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** nie działał zgodnie z oczekiwaniami i powiadomienia o błędzie został wyświetlony. 

<!-- 2930799 - IS ASDK --> 
- Rozwiązano problem w portalach administratora i użytkownika: w obszarze **wszystkich usług**, element zawartości **planów ochrony przed atakami DDoS** został niepoprawnie na liście, gdy nie jest dostępna w usłudze Azure Stack.
 
<!--2760466 – IS  ASDK --> 
- Rozwiązano problem, który wystąpił podczas instalacji nowego środowiska usługi Azure Stack, w którym alert, który wskazuje **wymagana aktywacja** nie były wyświetlane. Jest teraz poprawnie wyświetlany.

<!--1236441 – IS  ASDK --> 
- Rozwiązano problem, który uniemożliwia stosowanie zasad RBAC do grupy użytkowników, korzystając z usług AD FS.

<!--3463840 - IS, ASDK --> 
- Rozwiązano problem z kopiami zapasowymi infrastruktury kończy się niepowodzeniem ze względu na serwer plików niedostępny od sieci publicznych adresów VIP. Ta poprawka jest przenoszony infrastruktury usługi kopii zapasowej do sieci publicznych infrastruktury. Jeśli zastosowano najnowsze [poprawka usługi Azure Stack dla 1809](#azure-stack-hotfixes) , rozwiązuje ten problem, aktualizacja 1811 nie dokona żadnych dalszych modyfikacji. 

<!-- 2967387 – IS, ASDK --> 
- Rozwiązano problem, w którym konto używane do logowania do portalu administratora lub użytkownika usługi Azure Stack jest wyświetlany jako **Niezidentyfikowany użytkownik**. Ten komunikat został wyświetlony, gdy konta nie miał albo *pierwszy* lub *ostatniego* nazwy.   

<!--  2873083 - IS ASDK --> 
- Rozwiązano problem, w których korzystanie z portalu do tworzenia maszyny wirtualnej zestawu skalowania (zestawu skalowania maszyn wirtualnych), *rozmiaru wystąpienia* listy rozwijanej nie został załadowany poprawnie, korzystając z programu Internet Explorer. Ta przeglądarka działa teraz poprawnie.  

<!-- 3190553 - IS ASDK -->
- Rozwiązano problem, który wygenerował alerty generujące dużo alertów, wskazującą, czy wystąpienie roli infrastruktury był niedostępny lub węzła jednostki skali była w trybie offline.

### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal  

<!-- 2930820 - IS ASDK --> 
- W portalach administratora i użytkownika jeśli wyszukasz "Docker" jest niepoprawnie zwrócony element. Nie jest dostępna w usłudze Azure Stack. Jeśli spróbujesz go utworzyć, zostanie wyświetlony blok ze wskazaniem błędu. 

<!-- 2931230 – IS  ASDK --> 
- Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

<!-- TBD - IS ASDK --> 
- Nie można używać dwa typy subskrypcji administracyjne, które zostały wprowadzone w wersji 1804. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **domyślny dostawca** typu subskrypcji.

<!-- TBD - IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- TBD - IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

#### <a name="compute"></a>Wystąpienia obliczeniowe 

<!-- 3235634 – IS, ASDK -->
- Aby wdrożyć maszyny wirtualne o rozmiarach zawierający **v2** sufiks; na przykład **maszyna wirtualna standard_a2_v2 —**, określ sufiks jako **maszyna wirtualna standard_a2_v2 —** (v małe litery). Nie używaj **maszyna wirtualna Standard_A2_V2** (wielkie litery V). To działa na platformie Azure globalnych i niezgodności w usłudze Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenie cmdlet kończy się niepowodzeniem z następującym błędem: *Długie wykonywanie operacji nie powiodło się ze stanem "Niepowodzenie"*. 

<!--  2795678 – IS, ASDK --> 
- Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. Można bezpiecznie zignorować to ostrzeżenie, który jest wyświetlany komunikat: **Wybrano używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).**

<!-- 2967447 - IS, ASDK --> 
- Środowisko tworzenia maszyny wirtualnej scale set (VMSS) zapewnia opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo skorzystać z szablonu usługi Azure Resource Manager, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.  

<!-- TBD - IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  

<!-- 2724961- IS ASDK --> 
- Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows przy użyciu gościa systemu operacyjnego diagnostycznych włączone, wykres procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie wyświetla danych metryk.

   Aby znaleźć danych metryk, takich jak wykres procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do okna metryk i wyświetlić wszystkie obsługiwane metryki gościa maszyny Wirtualnej Windows.

<!-- 3507629 - IS, ASDK --> 
- Dyski zarządzane tworzy dwa nowe [typy limitów przydziału obliczeniowych](../azure-stack-quota-types.md#compute-quota-types) ograniczenie maksymalnej pojemności dysków zarządzanych, które mogą być udostępniane. Domyślnie 2048 GiB jest przydzielany dla każdego typu przydziału dysków zarządzanych. Jednakże można napotkać następujące problemy:

   - Aby przydziały utworzone przed aktualizacją 1808 przydziału dysków Managed Disks wyświetli wartości 0 w portalu administratora, mimo że jest przydzielany 2048 GiB. Można zwiększyć lub zmniejszyć wartość na podstawie Twoich potrzeb rzeczywiste i nowo ustawiony wartości przydziału na zastępuje domyślne GiB 2048.
   - Jeśli zaktualizujesz wartości limitu przydziału na 0 jest równoważna wartość domyślną 2048 GiB. Obejść ten problem należy ustawić wartość limitu przydziału na 1.

<!-- TBD - IS ASDK --> 
- Po zastosowaniu 1811 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   1. Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Kliknij przycisk **dostawców zasobów**, następnie kliknij przycisk **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
   2. Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.

#### <a name="networking"></a>Networking

<!-- 1766332 - IS ASDK --> 
- W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

<!-- 16309153 - IS ASDK --> 
- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

<!-- 2529607 - IS ASDK --> 
- W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

<!-- 2664148 - IS ASDK --> 
-   W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia się nie powieść, jeśli podsieć lokalna została dodana do bramy sieci lokalnej, po brama została już utworzona. 

#### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>Tworzenie 1.1809.0.90

### <a name="new-features"></a>Nowe funkcje
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

<!--  2712869   | IS  ASDK -->  
- **Klient usługi syslog usługi Azure Stack (ogólnie)** ten klient umożliwia przekazywanie inspekcje, alerty i dzienniki zabezpieczeń związane z infrastruktury Azure Stack w celu syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrzne w stosunku do usługi Azure Stack. Klient usługi syslog obsługuje teraz, określając port, na którym nasłuchuje serwer syslog.

W tej wersji klienta programu syslog jest ogólnie dostępna, i mogą być używane w środowiskach produkcyjnych.

Aby uzyskać więcej informacji, zobacz [przekazywania usługi syslog usługi Azure Stack](../azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- TBD - IS ASDK -->
- W portalu wykres pamięci raportowania bezpłatnie używać pojemności jest dokładne. Możesz teraz bardziej niezawodne przewidzieć liczbę maszyn wirtualnych można utworzyć.

<!-- TBD - IS ASDK --> 
- Rozwiązano problem, w której maszyny wirtualne są tworzone w portalu użytkowników usługi Azure Stack, a portalem wyświetlane niepoprawną liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- 2702741 -  IS, ASDK --> Rozwiązano problem, w której publiczne adresy IP, które zostały wdrożone za pomocą dynamicznej alokacji metoda nie jest gwarantowana zostaną zachowane po wystawieniu Zatrzymaj anulowanie. Teraz są zachowywane.

- <!-- 3078022 - IS, ASDK --> Jeśli maszyna wirtualna została zatrzymana z cofniętą alokacją przed 1808 nie można jej ponownie przydzielić po 1808 aktualizacji.  Ten problem został rozwiązany w 1809. Wystąpienia, które zostały w tym stanie i nie może zostać uruchomiony, może zostać uruchomiony w 1809 dzięki tej poprawce. Poprawka zapobiega także ten problem pojawiał.

<!-- TBD -  IS ASDK --> 
- W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Można następnie ponów próbę pobierania obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack

### <a name="changes"></a>Zmiany

### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal  

<!-- 2515955   | IS ,ASDK--> 
- *Wszystkie usługi* zastępuje *więcej usług* w portalach administratora i użytkownika usługi Azure Stack. Teraz możesz używać *wszystkich usług* jako alternatywę, można przejść w portalach usługi Azure Stack w taki sam sposób jak w witrynie Azure Portal.

<!--  TBD – IS, ASDK --> 
- *Podstawowa A* rozmiarów maszyn wirtualnych są wycofywane w przypadku [tworzenie zestawów skalowania maszyn wirtualnych](../azure-stack-compute-add-scalesets.md) (VMSS) za pośrednictwem portalu. Do utworzenia zestawu skalowania maszyn wirtualnych o tym rozmiarze, użyj programu PowerShell lub w szablonie.

#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i

<!-- 1264761 - IS ASDK -->  
- Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - NAZWA:  Rola infrastruktury złej kondycji
   - WAŻNOŚĆ: Ostrzeżenie
   - SKŁADNIK: Kondycji kontrolera
   - OPIS: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - NAZWA:  Rola infrastruktury złej kondycji
   - WAŻNOŚĆ: Ostrzeżenie
   - SKŁADNIK: Kondycji kontrolera
   - OPIS: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu.  

<!-- 2368581 - IS. ASDK --> 
- Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.


#### <a name="compute"></a>Wystąpienia obliczeniowe 

<!-- 3164607 – IS, ASDK -->
- Ponowne dołączenie odłączono dysk do tej samej maszyny wirtualnej (VM) z taką samą nazwę i numer LUN zakończy się niepowodzeniem z powodu błędu takich jak **nie można dołączyć dysku danych "datadisk" do maszyny Wirtualnej "vm1"**. Ten błąd występuje, ponieważ dysk jest obecnie odłączany lub ostatnia odłączanie operacja nie powiodła się. Zaczekaj, aż dysk zostanie całkowicie odłączony a następnie spróbuj ponownie lub usuń bądź Odłącz dysk jawnie ponownie. Obejście polega na dołączyć go ponownie z inną nazwą lub w innej jednostce LUN. 

<!-- 3235634 – IS, ASDK -->
- Aby wdrożyć maszyny wirtualne o rozmiarach zawierający **v2** sufiks; na przykład **maszyna wirtualna standard_a2_v2 —**, określ sufiks jako **maszyna wirtualna standard_a2_v2 —** (v małe litery). Nie używaj **maszyna wirtualna Standard_A2_V2** (wielkie litery V). To działa na platformie Azure globalnych i niezgodności w usłudze Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Jeśli podczas tworzenia nowej maszyny wirtualnej (VM) przy użyciu portalu Azure Stack, wybierz rozmiar maszyny Wirtualnej, w kolumnie USD/miesiąc jest wyświetlany z **Unavailable** wiadomości. Ta kolumna nie powinien pojawić się; Wyświetlanie maszyny Wirtualnej cen kolumna nie jest obsługiwana w usłudze Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenie cmdlet kończy się niepowodzeniem z następującym błędem: *Długie wykonywanie operacji nie powiodło się ze stanem "Niepowodzenie"*. 

<!--  2966665 – IS, ASDK --> 
- Dołączanie dysków SSD dyski z danymi do rozmiaru premium zarządzanych dysków maszyn wirtualnych (DS, DSv2, Fs i Fs_V2) kończy się niepowodzeniem z powodu błędu:  *Nie można zaktualizować dysków maszyny wirtualnej "vmname" Błąd: Żądanie, nie można wykonać operacji, ponieważ typ konta magazynu "Premium_LRS" nie jest obsługiwana dla rozmiaru maszyny Wirtualnej "Standard_DS/Ds_V2/FS/Fs_v2)*

   Aby obejść ten problem, należy użyć *Standard_LRS* dyski danych zamiast *dysków Premium_LRS*. Korzystanie z *Standard_LRS* dysków z danymi nie zmienia się operacji We/Wy lub rozliczeń kosztów.  

<!--  2795678 – IS, ASDK --> 
- Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. 

   Można bezpiecznie zignorować to ostrzeżenie, który jest wyświetlany komunikat: *Wybrano używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Zestaw skalowania maszyn wirtualnych (VMSS) utworzyć środowisko zawiera opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo skorzystać z szablonu usługi Azure Resource Manager, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.

<!-- TBD -  IS ASDK --> 
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.



<!-- TBD -  IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 - IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.

<!-- 2724961- IS ASDK --> 
- Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows z włączoną gościa systemu operacyjnego diagnostyczne, wykres procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie będzie mogła wyświetlać dane metryk.
 
  Aby znaleźć wykresu procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do **metryki** bloku i wyświetlanie wszystkich obsługiwanych Windows maszyn wirtualnych gościa metryki.

 

#### <a name="networking"></a>Networking
<!-- 1766332 - IS, ASDK --> 
- W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

<!-- 16309153 -  IS ASDK --> 
- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

<!-- 2702741 -  IS ASDK --> 
- Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

<!-- 2529607 - IS ASDK --> 
- W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

<!-- 2664148 - IS ASDK --> 
- W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

<!-- TBD - IS ASDK --> 
- Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  


#### <a name="usage"></a>Sposób użycia  
<!-- TBD -  IS ASDK --> 
- Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->

## <a name="build-11808097"></a>Tworzenie 1.1808.0.97

### <a name="new-features"></a>Nowe funkcje
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

<!-- 1658937 | ASDK, IS --> 
- **Rozpocznij tworzenie kopii zapasowych zgodnie ze wstępnie zdefiniowanym harmonogramem** — znajduje się urządzenie, w usłudze Azure Stack można teraz automatycznie wyzwoli infrastruktury kopii zapasowych, okresowo, aby wyeliminować interwencji człowieka. Usługi Azure Stack będą również automatycznie wyczyścić udziału zewnętrznego dla kopii zapasowych, które są starsze niż okres przechowywania zdefiniowany. Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Dodane dane transferu czasu, do łącznego czasu tworzenia kopii zapasowej.** Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **Pojemność zewnętrznych kopii zapasowych zawiera obecnie prawidłowe pojemności udziału zewnętrznego.** (Wcześniej było to trwale kodować do 10 GB.) Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](../azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Szablony usługi Azure Resource Manager obsługuje teraz elementu warunek** — teraz można wdrożyć zasobów w szablonie usługi Azure Resource Manager przy użyciu warunku. Można zaprojektować szablon, aby wdrożyć zasób, w oparciu o warunek, np. Sprawdzanie, czy występuje wartość parametru. Aby dowiedzieć się, jak przy użyciu szablonu jako warunek, zobacz [warunkowe wdrażanie zasobu](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) i [sekcji zmiennych szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) w dokumentacji platformy Azure. 

   Można również użyć szablonów [wdrażanie zasobów na więcej niż jedną subskrypcję lub grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **Obsługa wersji zasobu Microsoft.Network interfejsu API zostały zaktualizowane** obsługę interfejsu API w wersji 2017-10-01 od 2015-06-15 dotyczące zasobów sieciowych w usłudze Azure Stack. Obsługa wersji zasobu od 2017-10-01 i 2015-06-15 jest niedostępna w tej wersji. Zapoznaj się [zagadnienia dotyczące sieci usługi Azure Stack](../user/azure-stack-network-differences.md) dla różnice w funkcjonalności.

<!-- 2272116 | IS, ASDK   -->  
- **Usługa Azure Stack dodano obsługę wstecznego wyszukiwania DNS dla zewnętrznie połączonego z punktów końcowych infrastruktury Azure Stack** (które jest przeznaczone dla portalu, adminportal, zarządzania i adminmanagement). Dzięki temu nazwy zewnętrzny punkt końcowy usługi Azure Stack, aby być rozwiązane wyłącznie z adresu IP.

<!-- 2780899 |  IS, ASDK   --> 
- **Usługa Azure Stack obsługuje teraz dodawanie interfejsów sieciowych dodatkowe do istniejącej maszyny Wirtualnej.**  Ta funkcja jest dostępna za pomocą portalu, programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) w dokumentacji platformy Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Ulepszenia dokładności i zwiększa odporność wprowadzono sieć mierników użycia**. Liczniki użycia sieci są teraz bardziej precyzyjne i uwzględniać subskrypcji konto zostało zawieszone, okresów awarii i sytuacje wyścigu.

<!-- 2297790 | IS, ASDK -->  
- **Ulepszenia usługi Azure Stack klienta syslog (funkcja w wersji zapoznawczej)**. Ten klient umożliwia przekazywanie inspekcji i dzienniki związane z infrastruktury Azure Stack w celu syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrznego do usługi Azure Stack. Klient usługi syslog obsługuje teraz protokół TCP za pomocą zwykłego tekstu lub szyfrowania protokołu TLS 1.2, ostatnim jest domyślna konfiguracja. Tylko do serwera lub wzajemnego uwierzytelniania można skonfigurować połączenia TLS.

  Aby skonfigurować jak komunikuje się klient syslog (takich jak protokół, szyfrowania i uwierzytelniania) z serwera syslog, należy użyć polecenia cmdlet Set-SyslogServer. To polecenie cmdlet jest dostępna z uprzywilejowanym punktu końcowego (program ten). 

- <!-- ASDK --> **Galeria elementów dla zestawów skalowania maszyn wirtualnych są teraz wbudowane**.  Zestaw skalowania maszyn wirtualnych elementy galerii są teraz udostępniane w portalach użytkowników i administratorów bez konieczności pobierania ich. 

- <!-- IS, ASDK --> **Skalowanie zestawu skalowania maszyn wirtualnych**.  Można użyć portalu [skalowanie zestawu skalowania maszyn wirtualnych](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (zestawu skalowania maszyn wirtualnych).   

- <!-- 2489570 | IS ASDK--> **Obsługa niestandardowych konfiguracji zasad protokołu IPSec/IKE** dla [bram sieci VPN w usłudze Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Elementu portalu marketplace Kubernetes**. Teraz można wdrożyć klastry Kubernetes za pomocą [elementu portalu Kubernetes Marketplace](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Użytkownicy mogą wybrać element Kubernetes i wypełnij kilka parametrów do wdrażania klastra Kubernetes w usłudze Azure Stack. Szablony ma na celu ułatwić użytkownikom w celu wdrożenia rozwiązania Kubernetes i testowania instalacji w kilku krokach.

<!-- ####### | IS, ASDK -->  
- **Usługa Azure Resource Manager zawiera nazwę regionu.** W tej wersji pobrane z usługi Azure Resource Manager obiekty będzie teraz obejmować atrybucie nazwy w regionie. Jeśli istniejący skrypt programu PowerShell bezpośrednio przekazuje obiekt do innego polecenia cmdlet, skrypt może powodować błąd i zakończyć się niepowodzeniem. To jest zachowanie zgodne z usługi Azure Resource Manager i wymaga klienta wywołującego do odjęcia atrybut regionu. Aby uzyskać więcej informacji na temat usługi Azure Resource Manager, zobacz [dokumentacja usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Przenieś subskrypcje między dostawcami delegowane.** Możesz teraz przejść subskrypcje subskrypcjami nowej lub istniejącej delegowane dostawcy, które należą do tej samej dzierżawie katalogu. Subskrypcje należące do subskrypcji dostawcy domyślne można także przenosić do subskrypcji dostawcy delegowana w tej samej dzierżawie katalogu. Aby uzyskać więcej informacji, zobacz [delegowanie ofert w usłudze Azure Stack](../azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Ulepszone czas tworzenia maszyny Wirtualnej** dla maszyn wirtualnych, które zostały utworzone z obrazów, możesz pobrać z witryny Azure marketplace.

### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS ASDK--> Rozwiązaliśmy problem w przypadku tworzenia zestaw dostępności w portalu, co spowodowało w zestawie, domeny błędów i domeny aktualizacji 1.

<!-- TBD | ASDK, IS --> 
- Aby bardziej niezawodna wprowadzono ulepszenia różnych procesu aktualizacji. Ponadto poprawki zostały wprowadzone do podstawowej infrastruktury, co zwiększa opróżnianie węzła, w tym samym minimalizując potencjalnych przerw dla obciążeń podczas aktualizacji.

<!--2292271 | ASDK, IS --> 
- Rozwiązano problem, gdy zmodyfikowane limit przydziału nie zostało zastosowane względem w przypadku istniejących subskrypcji.  Teraz kiedy zwiększyć limit przydziału dla zasobu sieciowego, który jest częścią oferty i Plan skojarzony z subskrypcji użytkownika, nowy limit dotyczy istniejące subskrypcje, a także nowe subskrypcje.

<!-- 2448955 | IS ASDK --> 
- Teraz można pomyślnie odpytać dzienników aktywności dla systemów, które są wdrażane w strefie czasowej UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Sprawdzanie wymagań wstępnych dla parametrów konfiguracji kopii zapasowej (ścieżka/nazwa użytkownika/hasła/szyfrowania klucza) nie jest już ustawia nieprawidłowe ustawienia konfiguracji kopii zapasowej. (Wcześniej ustawiono nieprawidłowe ustawienia do tworzenia kopii zapasowej i kopii zapasowej może następnie zakończyć się niepowodzeniem po wyzwoleniu.)

<!-- 2215948 |  ASDK, IS --> 
- Listy kopii zapasowych Odświeża teraz, gdy ręcznie usunąć kopii zapasowej z udziału zewnętrznego.

<!-- 2360715 |  ASDK, IS -->  
- Po skonfigurowaniu integracji centrum danych nie jest już dostępu usług AD FS plik metadanych z udziału. Aby uzyskać więcej informacji, zobacz [konfigurowania integracji usług AD FS, zapewniając pliku metadanych Federacji](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Rozwiązano problem uniemożliwił użytkownikom możliwość przypisania istniejącego publicznego adresu IP, gdyby zostały wcześniej przypisane do interfejsu sieciowego lub modułu równoważenia obciążenia do nowego interfejsu sieciowego lub modułu równoważenia obciążenia.  

<!-- 2551834 - IS, ASDK --> 
- Po wybraniu *Przegląd* dla konta magazynu w portalach administrator lub użytkownik, *Essentials* okienku zostaną wyświetlone wszystkie oczekiwane informacje poprawnie. 

<!-- 2551834 - IS, ASDK --> 
- Po wybraniu *tagi* dla konta magazynu w portalach administratora lub użytkownika, informacje teraz wyświetlany poprawnie.

<!-- TBD - IS ASDK --> 
- Ta wersja usługi Azure Stack rozwiązuje problem, który uniemożliwił stosowania aktualizacji sterowników z pakietów rozszerzeń OEM.

<!-- 2055809- IS ASDK --> 
- Rozwiązano problem uniemożliwiający usunięcie maszyn wirtualnych z poziomu bloku obliczeń, gdy maszyna wirtualna nie powiodło się.  

<!--  2643962 IS ASDK -->  
- Zgłoszono alert dotyczący *niską pojemność pamięci* nie jest już wyświetlany niepoprawnie.

<!--  TBD ASDK --> 
- Maszyny wirtualnej, która obsługuje ten punkt końcowy uprawnień (program ten) został zwiększony do 4GB. W ASDK ta maszyna wirtualna ma nazwę AzS ERCS01.

- <!--  TBD – IS, ASDK --> *Podstawowa A* rozmiarów maszyn wirtualnych są wycofywane w przypadku [tworzenie zestawów skalowania maszyn wirtualnych](../azure-stack-compute-add-scalesets.md) (VMSS) za pośrednictwem portalu. Do utworzenia zestawu skalowania maszyn wirtualnych o tym rozmiarze, użyj programu PowerShell lub w szablonie. 

### <a name="known-issues"></a>Znane problemy

#### <a name="portal"></a>Portal  
- <!-- 2967387 – IS, ASDK --> Konto używane do logowania do portalu administratora lub użytkownika usługi Azure Stack jest wyświetlany jako **Niezidentyfikowany użytkownik**. Dzieje się tak, gdy konto nie ma albo *pierwszy* lub *ostatniego* nazwy. Aby obejść ten problem, należy edytować konto użytkownika, aby podać nazwę pierwszego lub ostatniego. Należy następnie zaloguj i zaloguj ponownie do portalu. 

-  <!--  2873083 - IS ASDK --> Kiedy używać portalu, aby utworzyć zestaw skalowania maszyn wirtualnych zestawu (zestawu skalowania maszyn wirtualnych), *rozmiaru wystąpienia* listy rozwijanej nie załadować się poprawnie, gdy używasz programu Internet Explorer. Aby obejść ten problem, należy użyć innej przeglądarki podczas korzystania z portalu do tworzenia zestawu skalowania maszyn wirtualnych.  

#### <a name="portal"></a>Portal  
<!-- 2931230 – IS  ASDK --> 
- Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

<!--2760466 – IS  ASDK --> 
- Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](../azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji. 

<!-- TBD - IS ASDK --> 
- Nie można używać dwa typy subskrypcji administracyjne, które zostały wprowadzone w wersji 1804. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **subskrypcję dostawcy domyślne** typu.

<!-- 2403291 - IS ASDK --> 
- Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- TBD -  IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

<!--  TBD | ASDK -->  
- Domyślna strefa czasowa dla wdrożenia usługi Azure Stack ustawi Pobierz teraz na czas UTC. Strefa czasowa można wybrać podczas instalowania usługi Azure Stack, jednak automatycznie zostanie przywrócona na czas UTC domyślnie podczas instalacji.

#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
<!-- 1264761 - IS ASDK -->  
- Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - NAZWA:  Rola infrastruktury złej kondycji
   - WAŻNOŚĆ: Ostrzeżenie
   - SKŁADNIK: Kondycji kontrolera
   - OPIS: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - NAZWA:  Rola infrastruktury złej kondycji
   - WAŻNOŚĆ: Ostrzeżenie
   - SKŁADNIK: Kondycji kontrolera
   - OPIS: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu.  

<!-- 2368581 - IS. ASDK --> 
- Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.

<!-- TBD - IS. ASDK --> 
- Podczas uruchamiania **AzureStack testu** polecenia cmdlet w punkcie końcowym uprzywilejowanego (program ten), **wydajność roli infrastruktury Azure Stack w wystąpienia** test, spowoduje wygenerowanie komunikatu OSTRZEGAJ ERCS maszyny wirtualnej. Możesz bezpiecznie zignorować ten komunikat OSTRZEGAJ i nadal używać ASDK.

#### <a name="compute"></a>Wystąpienia obliczeniowe
<!-- 2494144 - IS, ASDK --> 
- Wybierając rozmiar maszyny wirtualnej do wdrażania maszyn wirtualnych, niektóre rozmiary serii F maszyn wirtualnych nie są widoczne jako część selektor rozmiar podczas tworzenia maszyny Wirtualnej. W selektorze nie ma następujących rozmiarów maszyn wirtualnych: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Obejść ten problem należy użyć jednej z następujących metod do wdrożenia maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, którego chcesz użyć.

- <!-- 3099544 – IS, ASDK --> Jeśli podczas tworzenia nowej maszyny wirtualnej (VM) przy użyciu portalu Azure Stack, wybierz rozmiar maszyny Wirtualnej, w kolumnie USD/miesiąc jest wyświetlany z **Unavailable** wiadomości. Ta kolumna nie powinien pojawić się; Wyświetlanie maszyny Wirtualnej cen kolumna nie jest obsługiwana w usłudze Azure Stack.

- <!-- 2869209 – IS, ASDK --> Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenie cmdlet kończy się niepowodzeniem z następującym błędem: *Długie wykonywanie operacji nie powiodło się ze stanem "Niepowodzenie"*. 

- <!--  2966665 – IS, ASDK --> Dołączanie dysków SSD dyski z danymi do rozmiaru premium zarządzanych dysków maszyn wirtualnych (DS, DSv2, Fs i Fs_V2) kończy się niepowodzeniem z powodu błędu:  *Nie można zaktualizować dysków maszyny wirtualnej "vmname" Błąd: Żądanie, nie można wykonać operacji, ponieważ typ konta magazynu "Premium_LRS" nie jest obsługiwana dla rozmiaru maszyny Wirtualnej "Standard_DS/Ds_V2/FS/Fs_v2)*

   Aby obejść ten problem, należy użyć *Standard_LRS* dyski danych zamiast *dysków Premium_LRS*. Korzystanie z *Standard_LRS* dysków z danymi nie zmienia się operacji We/Wy lub rozliczeń kosztów.  

- <!--  2795678 – IS, ASDK --> Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. 

   Można bezpiecznie zignorować to ostrzeżenie, który jest wyświetlany komunikat: *Wybrano używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Zestaw skalowania maszyn wirtualnych (VMSS) utworzyć środowisko zawiera opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo użyj szablonu usługi ARM, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.

<!-- TBD -  IS ASDK --> 
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

<!-- TBD -  IS ASDK --> 
- W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Można następnie ponów próbę pobierania obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

<!-- TBD -  IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 - IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.

<!-- 2724961- IS ASDK --> 
- Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows przy użyciu gościa systemu operacyjnego diagnostycznych włączona, na stronie Przegląd maszyny Wirtualnej nie wyświetla danych metryk. 

 

#### <a name="networking"></a>Networking
<!-- 1766332 - IS, ASDK --> 
- W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

<!-- 16309153 -  IS ASDK --> 
- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

<!-- 2702741 -  IS ASDK --> 
- Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

<!-- 2529607 - IS ASDK --> 
- W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

<!-- 2664148 - IS ASDK --> 
- W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL
<!-- TBD - ASDK --> 
- Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

<!-- IS, ASDK --> 
- Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

<!-- TBD - IS ASDK --> 
- Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  

<!-- TBD - IS ASDK --> 
- Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili.

#### <a name="usage"></a>Sposób użycia  
<!-- TBD -  IS ASDK --> 
- Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->

