---
title: Aktualizacja usługi Azure Stack 1807 | Dokumentacja firmy Microsoft
description: Informacje na temat nowości w aktualizacji 1807 dla usługi Azure Stack zintegrowane systemy, w tym znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: b46e365ea3903a8f2c805717df13d361c823148d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985600"
---
# <a name="azure-stack-1807-update"></a>Aktualizacja usługi Azure Stack 1807

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1807 aktualizacji. Ta aktualizacja obejmuje ulepszeń, poprawek i znanych problemów dotyczących tej wersji programu Azure Stack i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1807 **1.1807.0.76**.  

### <a name="new-features"></a>Nowe funkcje

Ta aktualizacja obejmuje następujące ulepszenia usługi Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Rozpocznij tworzenie kopii zapasowych zgodnie ze wstępnie zdefiniowanym harmonogramem** — znajduje się urządzenie, w usłudze Azure Stack można teraz automatycznie wyzwoli infrastruktury kopii zapasowych, okresowo, aby wyeliminować interwencji człowieka. Usługi Azure Stack będą również automatycznie wyczyścić udziału zewnętrznego dla kopii zapasowych, które są starsze niż okres przechowywania zdefiniowany. Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Dodane dane transferu czasu, do łącznego czasu tworzenia kopii zapasowej.** Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Pojemność zewnętrznych kopii zapasowych zawiera obecnie prawidłowe pojemności udziału zewnętrznego.** (Wcześniej było to trwale kodować do 10 GB.) Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2508488 |  IS   -->  **Zwiększyć pojemność** przez [dodawania węzłów jednostki rozbudowywać](azure-stack-add-scale-node.md).

- <!-- 2753130 |  IS, ASDK   -->  **Szablony usługi Azure Resource Manager obsługuje teraz elementu warunek** — teraz można wdrożyć zasobów w szablonie usługi Azure Resource Manager przy użyciu warunku. Można zaprojektować szablon, aby wdrożyć zasób, w oparciu o warunek, np. Sprawdzanie, czy występuje wartość parametru. Aby dowiedzieć się, jak przy użyciu szablonu jako warunek, zobacz [warunkowe wdrażanie zasobu](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) i [sekcji zmiennych szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) w dokumentacji platformy Azure. 

   Można również użyć szablonów [wdrażanie zasobów na więcej niż jedną subskrypcję lub grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **Obsługa wersji zasobu Microsoft.Network interfejsu API zostały zaktualizowane** obsługę interfejsu API w wersji 2017-10-01 od 2015-06-15 dotyczące zasobów sieciowych w usłudze Azure Stack.  Obsługa wersji zasobu od 2017-10-01 i 2015-06-15 jest niedostępna w tej wersji, ale będzie dostępna w przyszłej wersji.  Zapoznaj się [zagadnienia dotyczące sieci usługi Azure Stack](user/azure-stack-network-differences.md) dla różnice w funkcjonalności.

- <!-- 2272116 | IS, ASDK   -->  **Usługa Azure Stack dodano obsługę wstecznego wyszukiwania DNS dla zewnętrznie połączonego z punktów końcowych infrastruktury Azure Stack** (które jest przeznaczone dla portalu, adminportal, zarządzania i adminmanagement). Dzięki temu nazwy zewnętrzny punkt końcowy usługi Azure Stack, aby być rozwiązane wyłącznie z adresu IP.

- <!-- 2780899 |  IS, ASDK   --> **Usługa Azure Stack obsługuje teraz dodawanie interfejsów sieciowych dodatkowe do istniejącej maszyny Wirtualnej.**  Ta funkcja jest dostępna za pomocą portalu, programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) w dokumentacji platformy Azure. 

- <!-- 2222444 | IS, ASDK   -->  **Ulepszenia dokładności i zwiększa odporność wprowadzono sieć mierników użycia**.  Liczniki użycia sieci są teraz bardziej precyzyjne i uwzględniać subskrypcji konto zostało zawieszone, okresów awarii i sytuacje wyścigu.

- <!-- 2753080 | IS -->  **Zaktualizuj powiadomienie o udostępnieniu.** Połączona usługa Azure Stack wdrożeń sprawdzi teraz okresowo bezpiecznego punktu końcowego i określić, czy aktualizacja jest dostępna dla chmury. To powiadomienie pojawi się na kafelku aktualizacji, jak po ręczne sprawdzanie i zaimportować nową aktualizację. Przeczytaj więcej na temat [zarządzanie aktualizacjami dla usługi Azure Stack](azure-stack-updates.md).

- <!-- 2297790 | IS, ASDK -->  **Ulepszenia usługi Azure Stack klienta syslog (funkcja w wersji zapoznawczej)**. Ten klient umożliwia przekazywanie inspekcji i dzienniki związane z infrastruktury Azure Stack w celu syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrznego do usługi Azure Stack. Klient usługi syslog obsługuje teraz protokół TCP za pomocą zwykłego tekstu lub szyfrowania protokołu TLS 1.2, ostatnim jest domyślna konfiguracja. Tylko do serwera lub wzajemnego uwierzytelniania można skonfigurować połączenia TLS.

  Aby skonfigurować, jak klient usługi syslog komunikuje się (takich jak protokół, szyfrowania i uwierzytelniania) z serwera syslog, użyj *SyslogServer zestaw* polecenia cmdlet. To polecenie cmdlet jest dostępna z uprzywilejowanym punktu końcowego (program ten).

  Aby dodać certyfikat klienta do wzajemnego uwierzytelniania klient TLS 1.2 syslog, należy użyć polecenia cmdlet Set-SyslogClient w program ten.

  W tej wersji zapoznawczej widać znacznie większej liczby inspekcje i alerty. 

  Ponieważ ta funkcja jest nadal w wersji zapoznawczej, nie należy polegać na niej w środowisku produkcyjnym.

  Aby uzyskać więcej informacji, zobacz [przekazywania usługi syslog usługi Azure Stack](azure-stack-integrate-security.md).

<!-- - | ####### | IS, ASDK |  **Azure Resource Manager includes the region name.** With this release, objects retrieved from the Azure Resource Manager will now include the region name attribute. If an existing PowerShell script directly passes the object to another cmdlet, the script may produce an error and fail. This is Azure Resource Manager compliant behavior, and requires the calling client to subtract the region attribute. For more information about the Azure Resource Manager see [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

- <!-- TBD | IS, ASDK -->  **Zmiany w funkcjonalności delegowane dostawców.** Począwszy od 1807 delegowane dostawców modelu jest uproszczony w celu lepszego wyrównywanie przy użyciu modelu odsprzedawca platformy Azure i delegowane dostawców nie będzie mogła tworzyć inne delegowane dostawców zasadniczo spłaszczanie modelu i dokonując dostawcy delegowane funkcja dostępna na jednym poziomie. Umożliwia przejście do nowego modelu i zarządzania subskrypcjami, teraz można przenieść subskrypcji użytkownika między nowej lub istniejącej subskrypcji delegowane dostawcy, które należą do tej samej dzierżawie katalogu. Można także przenosić należącymi do subskrypcji dostawcy domyślnej subskrypcji użytkownika do subskrypcji dostawcy delegowana w tej samej dzierżawie katalogu.  Aby uzyskać więcej informacji, zobacz [delegowanie ofert w usłudze Azure Stack](azure-stack-delegated-provider.md).

- <!-- 2536808 IS ASDK --> **Ulepszone czas tworzenia maszyny Wirtualnej** dla maszyn wirtualnych, które zostały utworzone z obrazów, możesz pobrać z witryny Azure marketplace.

- <!-- TBD | IS, ASDK -->  **Usługa Azure Stack Capacity Planner usprawnienia w zakresie użyteczności**. Usługi Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) oferuje uproszczone środowisko wpisywania pamięci podręcznej S2D i pojemność S2D podczas definiowania rozwiązania jednostki SKU. Limit 1000 maszyn wirtualnych zostało usunięte.


### <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- TBD | ASDK, IS --> Aby bardziej niezawodna wprowadzono ulepszenia różnych procesu aktualizacji. Ponadto poprawki zostały wprowadzone do podstawowej infrastruktury, które zminimalizować potencjalny przestój w przypadku obciążeń podczas aktualizacji.

- <!--2292271 | ASDK, IS --> Rozwiązano problem, gdy zmodyfikowane limit przydziału nie zostało zastosowane względem w przypadku istniejących subskrypcji. Teraz kiedy zwiększyć limit przydziału dla zasobu sieciowego, który jest częścią oferty i Plan skojarzony z subskrypcji użytkownika, nowy limit dotyczy istniejące subskrypcje, a także nowe subskrypcje.

- <!-- 448955 | IS ASDK --> Teraz można pomyślnie odpytać dzienników aktywności dla systemów, które są wdrażane w strefie czasowej UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Sprawdzanie wymagań wstępnych dla parametrów konfiguracji kopii zapasowej (ścieżka/nazwa użytkownika/hasła/szyfrowania klucza) nie jest już ustawia nieprawidłowe ustawienia konfiguracji kopii zapasowej. (Wcześniej ustawiono nieprawidłowe ustawienia do tworzenia kopii zapasowej i kopii zapasowej może następnie zakończyć się niepowodzeniem po wyzwoleniu.)

- <!-- 2215948 |  ASDK, IS --> Listy kopii zapasowych Odświeża teraz, gdy ręcznie usunąć kopii zapasowej z udziału zewnętrznego.

- <!-- 2332636 | IS -->  Aktualizacja ta wersja nie jest już resetuje domyślnym właścicielem domyślną subskrypcję dostawcy do wbudowanej **CloudAdmin** użytkownika podczas wdrażania za pomocą usług AD FS.

- <!-- 2360715 |  ASDK, IS -->  Po skonfigurowaniu integracji centrum danych nie jest już dostępne plik metadanych usług AD FS z udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [konfigurowania integracji usług AD FS, zapewniając pliku metadanych Federacji](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Rozwiązano problem uniemożliwił użytkownikom możliwość przypisania istniejącego publicznego adresu IP, gdyby zostały wcześniej przypisane do interfejsu sieciowego lub modułu równoważenia obciążenia do nowego interfejsu sieciowego lub modułu równoważenia obciążenia.  

- <!-- 2551834 - IS, ASDK --> Po wybraniu *Przegląd* dla konta magazynu w portalach administrator lub użytkownik, *Essentials* okienku zostaną wyświetlone wszystkie oczekiwane informacje poprawnie. 

- <!-- 2551834 - IS, ASDK --> Po wybraniu *tagi* dla konta magazynu w portalach administratora lub użytkownika, informacje teraz wyświetlany poprawnie.

- <!-- TBD - IS ASDK --> Ta wersja usługi Azure Stack rozwiązuje problem, który uniemożliwił stosowania aktualizacji sterowników z pakietów rozszerzeń OEM.

-   <!-- 2055809- IS ASDK --> Rozwiązano problem uniemożliwiający usunięcie maszyn wirtualnych z poziomu bloku obliczeń, gdy maszyna wirtualna nie powiodło się.  

- <!--  2643962 IS ASDK -->  Zgłoszono alert dotyczący *niską pojemność pamięci* nie jest już wyświetlany niepoprawnie.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń
Instalacje Server Core systemu Windows Server 2016 do infrastruktury kluczy hosta korzysta z usługi Azure Stack. W tej wersji instaluje następujące aktualizacje systemu Windows Server 2016 na serwerach infrastruktury usługi Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki, lub zobacz artykuły bazy wiedzy Microsoft Knowledge Base [4338814](https://support.microsoft.com/help/4338814) i [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="prerequisites"></a>Wymagania wstępne

- Instalowanie usługi Azure Stack [aktualizacji 1805](azure-stack-update-1805.md) przed zastosowaniem aktualizacji usługi Azure Stack 1807.  Wystąpił brak 1806 aktualizacji.  

- Zainstaluj najnowszy dostępny [aktualizacja lub poprawka dla wersji 1805](azure-stack-update-1805.md#post-update-steps).  
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

- <!-- 2933866 – IS --> **Ulepszone stan instalacji aktualizacji nie powiodło się.** W tej wersji dodano dwie nowe kategorie stanu, aby zapewnić operatory więcej szczegółów na temat instalacji aktualizacji nie powiodło się. Dwie kategorie *PreparationFailed*, i *InstallationFailed*. Po zainstalowaniu tej wersji, możesz zobaczyć informacje dotyczące wcześniejszych niepowodzeń instalacji aktualizacji zmieniony w celu uwzględnienia tych nowych kategorii. 

<!-- *There are no post-update steps for update 1807.* -->

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Poniżej przedstawiono znane problemy po instalacji tej wersji kompilacji.

### <a name="portal"></a>Portal

- Możliwość [Otwórz nowe żądanie obsługi z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) w obrębie administrator portalu nie są dostępne. Dla usługi Azure Stack zintegrowanych systemów, użyj następującego linku: [ https://aka.ms/newsupportrequest ](https://aka.ms/newsupportrequest).

- <!-- 2931230 – IS  ASDK --> Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

- <!--2760466 – IS  ASDK --> Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji.  

- <!-- TBD - IS ASDK --> Dwa typy subskrypcji administracyjne, które zostały [wprowadzonym w wersji 1804](azure-stack-update-1804.md#new-features) nie powinny być używane. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **domyślny dostawca** typu subskrypcji.

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.

  ![Łącza do stron nadrzędnych](media/azure-stack-update-1804/breadcrumb.png)

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

- <!-- 2724873 - IS --> Korzystając z polecenia cmdlet programu PowerShell **Start AzsScaleUnitNode** lub **Stop AzsScaleunitNode** Zarządzanie jednostek skalowania, pierwsza próba uruchomienia lub zatrzymania jednostki skalowania może zakończyć się niepowodzeniem. Jeśli polecenie cmdlet nie powiedzie się przy pierwszym uruchomieniu, należy uruchomić polecenie cmdlet po raz drugi. Drugiego przebiegu ma być pomyślnie wykonane do ukończenia tej operacji. 

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


- <!-- TBD - IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD - IS --> Po utworzeniu zestaw dostępności w portalu, przechodząc do **New** > **obliczenia** > **zestawu dostępności**, można tworzyć tylko zestaw dostępności z domeny błędów i domeny aktualizacji 1. Obejść ten problem, tworząc nową maszynę wirtualną, utworzyć dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- <!-- TBD - IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD - IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  

- <!-- 2724961- IS ASDK --> Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows przy użyciu gościa systemu operacyjnego diagnostycznych włączona, na stronie Przegląd maszyny Wirtualnej nie wyświetla danych metryk. 

   Aby znaleźć danych metryk, takich jak wykres procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do **metryki** bloku i wyświetlanie wszystkich obsługiwanych Windows maszyn wirtualnych gościa metryki.

### <a name="networking"></a>Networking  

- <!-- 1766332 - IS ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 1902460 - IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 - IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- 2702741 -  IS ASDK --> Publiczne adresy IP, które są wdrażane przy użyciu metody dynamicznej alokacji nie musi zostać zachowane po wystawieniu Zatrzymaj anulowanie.

- <!-- 2529607 - IS ASDK --> W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

-   <!-- 2664148 - IS ASDK --> W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, w której próby nawiązania połączenia z się nie powieść, jeśli podsieci lokalnej został dodany do bramy sieci lokalnej, po brama została już utworzona. 


### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL



- <!-- No Number - IS, ASDK -->  Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL. 

- <!-- TBD - IS --> Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.  

> [!NOTE]  
> <!-- TBD - IS --> Po zaktualizowaniu do tej wersji usługi Azure Stack można nadal używać dostawcy zasobów SQL i MySQL, które wcześniej wdrożone.  Zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i bazy danych MySQL. Np. usługi Azure Stack należy zastosować aktualizacje do dostawcy zasobów SQL i MySQL sekwencyjnie. Na przykład jeśli używasz wersji 1804, najpierw zastosowanie wersji 1805, a następnie zaktualizuj do 1807.  
>  
> Instalacja tej aktualizacji nie ma wpływu na bieżące użycie bazy danych SQL lub MySQL dostawców zasobów przez użytkowników. 
> Niezależnie od wersji dostawców zasobów, których używasz dane użytkowników w swoich baz danych nie jest Dotyk i pozostanie dostępne.  

### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- 2489178 - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.

- <!-- TBD - IS ASDK --> Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili. W przyszłej aktualizacji usługi App Service zostanie wdrożona w nowe *pomiaru subskrypcji* która została wprowadzona w usłudze Azure Stack w wersji 1804. Gdy zliczania jest przeznaczony do użytku, wszystkie istniejące wdrożenia będą migrowane do tego nowego typu subskrypcji.


### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji usługi Azure Stack 1807 z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Kolejne kroki
- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).  
