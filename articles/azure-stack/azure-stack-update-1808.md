---
title: Aktualizacja usługi Azure Stack 1808 | Dokumentacja firmy Microsoft
description: Informacje na temat nowości w aktualizacji 1808 dla usługi Azure Stack zintegrowane systemy, w tym znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: f9973f2099ed5c6dda38c6569f42995b6903ef5e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162414"
---
# <a name="azure-stack-1808-update"></a>Aktualizacja usługi Azure Stack 1808

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1808 aktualizacji. Pakiet aktualizacji zawiera ulepszenia, poprawki i znane problemy dotyczące tej wersji usługi Azure Stack. Ten artykuł zawiera również link, aby pobrać aktualizację. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1808 **1.1808.0.97**.  

### <a name="new-features"></a>Nowe funkcje

Ta aktualizacja obejmuje następujące ulepszenia usługi Azure Stack.

- <!--  2682594   | IS  -->  **Wszystkie środowiska Azure Stack teraz używać format strefy czasowej uniwersalnego czasu koordynowanego (UTC).**  Wszystkie dane dziennika, a teraz powiązane informacje wyświetlane w formacie UTC. Jeśli zaktualizujesz z poprzedniej wersji, który nie został zainstalowany przy użyciu czasu UTC, środowiska jest aktualizowany do używają czasu UTC. 

- <!-- 2437250  | IS  ASDK --> **Dyski zarządzane są obsługiwane.** Możesz teraz używać usługi Managed Disks w maszynach wirtualnych usługi Azure Stack i zestawy skalowania maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [stosu usługi Azure Managed Disks: różnice i zagadnienia](/azure/azure-stack/user/azure-stack-managed-disk-considerations).

- <!-- 2563799  | IS  ASDK -->  **Usługa Azure Monitor**. Takich jak Azure Monitor na platformie Azure usługi Azure Monitor w usłudze Azure Stack zapewnia podstawowy poziom infrastrukturą metryk i dzienników dla większości usług. For more information, see [Azure Monitor on Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

- <!-- 2487932| IS -->  **Przygotowanie do hosta rozszerzenia**. Można użyć host rozszerzenia ułatwiające bezpieczne usługi Azure Stack, zmniejszając liczbę wymaganych portów TCP/IP. Dzięki aktualizacji 1808 można przygotować, przygotowanie hosta rozszerzenia usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [przygotować się do hosta rozszerzenia dla usługi Azure Stack](/azure/azure-stack/azure-stack-extension-host-prepare).

- <!-- IS --> **Galeria elementów dla zestawów skalowania maszyn wirtualnych są teraz wbudowane**.  Element galerii zestawu skalowania maszyn wirtualnych jest teraz udostępniane w portalach użytkowników i administratorów bez konieczności pobierania go.  Po uaktualnieniu do 1808 będzie dostępna po zakończeniu procesu uaktualniania.  

- <!-- IS, ASDK --> **Skalowanie zestawu skalowania maszyn wirtualnych**.  Można użyć portalu [skalowanie zestawu skalowania maszyn wirtualnych](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (zestawu skalowania maszyn wirtualnych).    

- <!-- 2489570 | IS ASDK--> **Obsługa niestandardowych konfiguracji zasad protokołu IPSec/IKE** dla [bram sieci VPN w usłudze Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).


 ### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS ASDK--> Rozwiązaliśmy problem w przypadku tworzenia zestaw dostępności w portalu, co spowodowało w zestawie, domeny błędów i domeny aktualizacji 1. 

- <!-- IS ASDK --> Ustawienia skalowania zestawów skalowania maszyn wirtualnych są teraz dostępne w portalu.  

- <!-- 2494144- IS, ASDK --> Rozwiązano problem, który uniemożliwił niektóre rozmiary maszyn wirtualnych serii F pojawiające się podczas wybierania rozmiaru maszyny Wirtualnej dla wdrożenia. 

- <!-- IS, ASDK --> Ulepszenia dotyczące wydajności podczas tworzenia maszyn wirtualnych i nie tylko zoptymalizowane pod kątem użycia podstawowego magazynu.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack.


### <a name="changes"></a>Zmiany
- <!-- 1697698  | IS, ASDK --> *Samouczki szybkiego startu* w link teraz pulpitu nawigacyjnego portalu użytkowników do odpowiednich artykułów w dokumentacji online usługi Azure Stack.

- <!-- 2515955   | IS ,ASDK--> *Wszystkie usługi* zastępuje *więcej usług* w portalach administratora i użytkownika usługi Azure Stack. Teraz możesz używać *wszystkich usług* jako alternatywę, można przejść w portalach usługi Azure Stack w taki sam sposób jak w witrynie Azure Portal.

- <!--  TBD – IS, ASDK --> *Podstawowa A* rozmiarów maszyn wirtualnych są wycofywane w przypadku [tworzenie zestawów skalowania maszyn wirtualnych](azure-stack-compute-add-scalesets.md) (VMSS) za pośrednictwem portalu. Do utworzenia zestawu skalowania maszyn wirtualnych o tym rozmiarze, użyj programu PowerShell lub w szablonie.  

### <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń

Ta aktualizacja instaluje następujące aktualizacje:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki lub zobacz artykule bazy wiedzy Microsoft [4343887](https://support.microsoft.com/help/4343887).

Ta aktualizacja zawiera także ograniczania ryzyka związanego z wykonywaniem spekulatywnym po stronie kanału luki w zabezpieczeniach znane jako błędów terminalu L1 (L1TF) opisanego w [Microsoft Security Advisory ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Wymagania wstępne

- Instalowanie usługi Azure Stack [aktualizacji 1807](azure-stack-update-1807.md) przed zastosowaniem aktualizacji usługi Azure Stack 1808. 

  > [!TIP]  
  > Subskrybowania następujących *RRS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
  > - REKORDY ZASOBÓW: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- <!-- 2468613 - IS --> Podczas instalacji tej aktualizacji, użytkownik może widzieć alerty z tytułem *błędu — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu instalacji tej aktualizacji.

- <!-- 2489559 - IS --> Nie należy próbować tworzyć maszyny wirtualne podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> W pewnych okolicznościach podczas aktualizacji wymaga uwagi, odpowiedni alert może nie zostać wygenerowany. Dokładny stan nadal zostaną odzwierciedlone w portalu i nie ulega zmianie.

### <a name="post-update-steps"></a>Kroki po aktualizacji

*Nie istnieją żadne kroki po aktualizacji aktualizacji 1808.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Poniżej przedstawiono znane problemy po instalacji tej wersji kompilacji.

### <a name="portal"></a>Portal
-  <!--  2873083 - IS ASDK --> Kiedy używać portalu, aby utworzyć zestaw skalowania maszyn wirtualnych zestawu (zestawu skalowania maszyn wirtualnych), *rozmiaru wystąpienia* listy rozwijanej nie załadować się poprawnie, gdy używasz programu Internet Explorer. Aby obejść ten problem, należy użyć innej przeglądarki podczas korzystania z portalu do tworzenia zestawu skalowania maszyn wirtualnych.  

- <!-- 2931230 – IS  ASDK --> Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

- <!--2760466 – IS  ASDK --> Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji.  

- <!-- TBD - IS ASDK --> Dwa typy subskrypcji administracyjne, które zostały [wprowadzonym w wersji 1804](azure-stack-update-1804.md#new-features) nie powinny być używane. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **domyślny dostawca** typu subskrypcji.

- <!-- TBD - IS --> Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyny tego problemu jest błąd podczas instalowania aktualizacji, który powoduje, że aktualizacja nieprawidłowo zgłaszane jako pomyślne. Jeśli wystąpi ten problem, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- <!-- TBD - IS --> Możesz zobaczyć pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz ikonę koła zębatego w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- <!-- TBD - IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD - IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla **kondycji kontrolera** składnik, który ma następujące informacje:  

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


- <!-- 2812138 | IS --> Można napotkać alert w przypadku **magazynu** składnik, który ma następujące informacje:

   - Nazwa: Magazyn usługi wewnętrzny błąd komunikacji  
   - Waga: krytyczny  
   - SKŁADNIK: magazynu  
   - Opis: Wystąpił błąd wewnętrzny komunikacji z usługą magazynu podczas wysyłania żądań do następujących węzłów.  

    Alert można bezpiecznie zignorować, ale musisz ręcznie zamknąć alert.

- <!-- 2368581 - IS. ASDK --> Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą **błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej**, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.


### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- 2869209 – IS, ASDK --> Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenia cmdlet zakończy się niepowodzeniem z następującym błędem: *długotrwałych operacji nie powiodło się ze stanem "Niepowodzenie"*. 

- <!--  2966665 – IS, ASDK --> Dołączanie dysków SSD dyski danych do rozmiaru premium zarządzanych dysków maszyn wirtualnych (DS, DSv2, Fs i Fs_V2) kończy się niepowodzeniem z powodu błędu: *nie można zaktualizować dysków maszyny wirtualnej "vmname" Błąd: żądanie nie można wykonać operacji, ponieważ typ konta magazynu " Premium_LRS nie jest obsługiwana dla rozmiaru maszyny Wirtualnej "Standard_DS/Ds_V2/FS/Fs_v2)*

   Aby obejść ten problem, należy użyć *Standard_LRS* dyski danych zamiast *dysków Premium_LRS*. Korzystanie z *Standard_LRS* dysków z danymi nie zmienia się operacji We/Wy lub rozliczeń kosztów. 

- <!--  2795678 – IS, ASDK --> Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. 

   Możesz bezpiecznie zignorować ostrzeżenie, że jest wyświetlany komunikat: *przycisk OK, aby używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Zestaw skalowania maszyn wirtualnych (VMSS) utworzyć środowisko zawiera opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo użyj szablonu usługi ARM, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.  

- <!-- 2724873 - IS --> Korzystając z polecenia cmdlet programu PowerShell **Start AzsScaleUnitNode** lub **Stop AzsScaleunitNode** Zarządzanie jednostek skalowania, pierwsza próba uruchomienia lub zatrzymania jednostki skalowania może zakończyć się niepowodzeniem. Jeśli polecenie cmdlet nie powiedzie się przy pierwszym uruchomieniu, należy uruchomić polecenie cmdlet po raz drugi. Drugiego przebiegu ma być pomyślnie wykonane do ukończenia tej operacji. 

- <!-- TBD - IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD - IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  

- <!-- 2724961- IS ASDK --> Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows z włączoną gościa systemu operacyjnego diagnostyczne, wykres procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie będzie mogła wyświetlać dane metryk.

   Aby znaleźć wykresu procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do **metryki** bloku i wyświetlanie wszystkich obsługiwanych Windows maszyn wirtualnych gościa metryki.



### <a name="networking"></a>Networking  

- <!-- 1766332 - IS ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 1902460 - IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 - IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- 2702741 -  IS ASDK --> Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

- <!-- 2529607 - IS ASDK --> W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

-   <!-- 2664148 - IS ASDK --> W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- 2489178 - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.



### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji usługi Azure Stack 1808 z [tutaj](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Kolejne kroki
- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).  
