---
title: Aktualizacja usługi Azure Stack 1902 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o aktualizacji 1902 systemy zintegrowane w usłudze Azure Stack, w tym, co nowego, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: 2005cf4b1929dfe9e520f56308493db7d820226e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361101"
---
# <a name="azure-stack-1902-update"></a>Aktualizacja usługi Azure Stack 1902

*Dotyczy Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1902 aktualizacji. Aktualizacja zawiera ulepszenia, poprawki i nowe funkcje dla tej wersji usługi Azure Stack. Również w tym artykule opisano znane problemy w tej wersji i zawiera link do pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1902 **1.1902.0.69**.

## <a name="hotfixes"></a>Poprawki

Usługa Azure Stack wydaje poprawki na bieżąco. Pamiętaj zainstalować [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1901 przed zaktualizowaniem usługi Azure Stack do 1902.

Usługa Azure Stack poprawki dotyczą tylko usługi Azure Stack, zintegrowanych systemów. nie należy próbować zainstalować poprawki na ASDK.

> [!TIP]  
> Subskrybowania następujących *RSS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Usługa Azure Stack poprawki

- **1809**: [KB 4481548 — usługa Azure Stack poprawkę 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nie dostępnych poprawki bieżącego.
- **1901**: [KB 4495662 — usługa Azure Stack poprawkę 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**: [KB 4494719 — usługa Azure Stack poprawkę 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Można zainstalować 1902 bezpośrednio z poziomu [1.1901.0.95 lub 1.1901.0.99](azure-stack-update-1901.md#build-reference) wersji bez instalowania najpierw oprogramowania wszelkie poprawki 1901. Jednak jeśli zainstalowano starszej wersji **1901.2.103** poprawek, należy zainstalować nowszego [1901.3.105 poprawkę](https://support.microsoft.com/help/4495662) przed przejściem do 1902.

- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również sprawdź aktywne alerty i rozwiązywanie tych, które wymagają działań:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Jeśli `AzsControlPlane` parametr, jest uwzględniony, podczas **AzureStack testu** jest wykonywane, zostanie wyświetlony następujący błąd w **AzureStack testu** dane wyjściowe: **Niepowodzenie usługi Azure Stack kontroli płaszczyzny witryn sieci Web Podsumowanie**. Można bezpiecznie zignorować ten konkretny błąd.

- Gdy usługi Azure Stack jest zarządzane przez System Center Operations Manager (SCOM), upewnij się, że aktualizacja [pakiet administracyjny dla programu Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) do wersji 1.0.3.11 przed zastosowaniem 1902.

- Format pakietu aktualizacji usługi Azure Stack został zmieniony z **.bin/.exe/.xml** do **.zip/.xml** począwszy od wersji 1902. Klienci z połączonej jednostki skalowania usługi Azure Stack będą widzieć **dostępna aktualizacja** wiadomości w portalu. Klienci, którzy nie są połączone teraz po prostu pobrać i zaimportować plik zip z odpowiedni plik XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Ulepszenia

- Kompilacja 1902 wprowadza nowy interfejs użytkownika w portalu administratora usługi Azure Stack do tworzenia plany, oferty, przydziały i planów dodatków. Aby uzyskać więcej informacji, w tym ze zrzutami ekranu, zobacz [Tworzenie planów, ofert i przydziałów](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Ulepszenia niezawodności zwiększanie pojemności podczas operacji dodawania węzła podczas przełączania stanu jednostki skali, z "Magazyn Expanding" na "Uruchomiona".

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Aby poprawić integralność pakietu i zabezpieczeń, a także łatwiejsze zarządzanie w celu pozyskiwania w trybie offline, Microsoft zmienił się format pakietu aktualizacji z pliki .exe i bin do pliku zip. Nowy format dodaje dodatkowe niezawodność Rozpakowywanie procesu, który może spowodować w czasie przygotowywania aktualizacji zostanie zatrzymane. Z tego samego formatu pakietu dotyczy także aktualizację pakietów z producent OEM.
- Aby poprawić środowisko operatora usługi Azure Stack, podczas uruchamiania testu AzureStack, Operatorzy mogą teraz po prostu korzystać, "Test AzureStack-UpdateReadiness grupy" w przeciwieństwie do przekazywania dziesięć dodatkowe parametry po instrukcji dołączania.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- W celu zwiększenia ogólnej niezawodności i dostępności podstawowe usługi infrastruktury, podczas procesu aktualizacji firma natywnego dostawcy zasobów aktualizacji w ramach planu akcji update wykryje i automatyczne globalnego korygowania funkcję w miarę potrzeb Wywołaj. Przepływy pracy korygowania globalne "Napraw" obejmują:

  - Sprawdza, czy maszyny wirtualne infrastruktury, które są w stanie optymalnej i spróbować rozwiązać je w miarę potrzeb.
  - Sprawdź, czy są problemy z usługą SQL, w ramach planu kontroli i spróbować rozwiązać je w miarę potrzeb.
  - Sprawdź stan usługi programowego modułu równoważenia obciążenia (SLB) w ramach kontrolera sieci (NC) i spróbować rozwiązać je w miarę potrzeb.
  - Sprawdź stan usługi kontrolera sieci (NC) i spróbować go naprawić, zgodnie z potrzebami
  - Sprawdź stan węzłów sieci szkieletowej usługi awaryjnego odzyskiwania konsoli usługi (ERCS) i je naprawić, zgodnie z potrzebami.
  - Sprawdź stan infrastruktury i napraw zgodnie z potrzebami.
  - Sprawdź stan węzłów spójnego magazynu Azure (ACS) usługi Service fabric i je naprawić, zgodnie z potrzebami.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Ulepszenia usługi Azure stack narzędzia diagnostyczne, aby zwiększyć niezawodność zbieranie dzienników i wydajność. Dodatkowe opcje rejestrowania usług sieci i tożsamości. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Ulepszenia niezawodności Test AzureStack pod kątem przygotowania do rotacji wpisu tajnego testu.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Ulepszenia w celu zwiększenia niezawodności programu Graph usługi AD podczas komunikowania się za pomocą środowiska usługi Active Directory klienta

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Ulepszenia zbieranie spisu sprzętu w Get AzureStackStampInformation.

- W celu zwiększenia niezawodności operacji uruchomioną w infrastrukturze ERCS firma pamięci dla każdego wystąpienia ERCS zwiększa się kolejno w 8 GB do 12 GB. W instalacji systemów zintegrowanych w usłudze Azure Stack powoduje to zwiększenie 12 GB ogólną.

> [!IMPORTANT]
> Aby upewnić się, proces poprawek i aktualizacji powoduje najmniejszą ilością przestój dzierżawy, upewnij się, sygnatury usługi Azure Stack ma więcej niż 12 GB dostępnego miejsca w **pojemności** bloku. Widać to zwiększyć znajduje odzwierciedlenie w pamięci **pojemności** blok po pomyślnej instalacji aktualizacji.

## <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń

Ta aktualizacja instaluje następujące aktualizacje zabezpieczeń:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki, lub zobacz artykuły bazy wiedzy Microsoft Knowledge Base [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Po uruchomieniu [AzureStack testu](azure-stack-diagnostic-test.md), zostanie wyświetlony komunikat ostrzegawczy z kontrolera zarządzania płytą główną (BMC). Można bezpiecznie zignorować to ostrzeżenie.

- <!-- 2468613 - IS --> Podczas instalacji tej aktualizacji, użytkownik może widzieć alerty z tytułem `Error – Template for FaultType UserAccounts.New is missing.` te alerty można bezpiecznie zignorować. Alerty zamknięte automatycznie po zakończeniu instalacji tej aktualizacji.

## <a name="post-update-steps"></a>Kroki po aktualizacji

- Po zainstalowaniu tej aktualizacji Zainstaluj wszystkie odpowiednie poprawki. Aby uzyskać więcej informacji, zobacz [poprawki](#hotfixes), a także naszego [obsługi zasad](azure-stack-servicing-policy.md).  

- Pobieranie danych o rest kluczy szyfrowania i bezpiecznego przechowywania ich poza wdrożenia usługi Azure Stack. Postępuj zgodnie z [instrukcje dotyczące sposobu pobierania kluczy](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Poniżej przedstawiono znane problemy po instalacji tej wersji kompilacji.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- W portalach administratora i użytkownika jeśli wyszukasz "Docker" jest niepoprawnie zwrócony element. Nie jest dostępna w usłudze Azure Stack. Jeśli spróbujesz go utworzyć, zostanie wyświetlony blok ze wskazaniem błędu. 

<!-- 2931230 – IS  ASDK --> 
- Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

<!-- TBD - IS ASDK --> 
- Nie można używać dwa typy subskrypcji administracyjne, które zostały wprowadzone w wersji 1804. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **domyślny dostawca** typu subskrypcji.

<!-- 3557860 - IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- 1663805 - IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu, należy użyć [programu PowerShell w celu sprawdzenia uprawnień](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Wystąpienia obliczeniowe

- Podczas tworzenia nowej maszyny wirtualnej Windows (VM), może zostać wyświetlony następujący błąd:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Ten błąd występuje, jeśli Włącz diagnostykę rozruchu na maszynie Wirtualnej, ale usunięcia konta magazynu diagnostyki rozruchu. Aby obejść ten problem, należy ponownie utworzyć konto magazynu o takiej samej nazwie jak użyte wcześniej.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Środowisko tworzenia maszyny wirtualnej scale set (VMSS) zapewnia opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz innego systemu operacyjnego dla danego wdrożenia lub użyj innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez określenie szablonu usługi Azure Resource Manager operator.  

<!-- TBD - IS ASDK --> 
- Po zastosowaniu 1902 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   - Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Wybierz **dostawców zasobów**, a następnie wybierz **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
   - Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.

- Maszyny Wirtualnej systemu Ubuntu 18.04 utworzone za pomocą autoryzacji SSH włączone uniemożliwi używanie kluczy SSH do logowania. Jako obejście użycie dostępu do maszyny Wirtualnej dla rozszerzenia systemu Linux w celu wdrożenia kluczy SSH po zainicjowaniu obsługi administracyjnej lub korzystanie z uwierzytelniania opartego na hasłach.

### <a name="networking"></a>Networking  

<!-- 3239127 - IS, ASDK -->
- W portalu usługi Azure Stack po zmianie statyczny adres IP dla konfiguracji adresu IP, który jest powiązany z kartą sieciową, dołączony do wystąpienia maszyny Wirtualnej, zobaczysz komunikat ostrzegawczy z informacją, że 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Możesz bezpiecznie zignorować ten komunikat; adres IP zostanie zmieniony, nawet wtedy, gdy wystąpienie maszyny Wirtualnej nie jest ponownie uruchamiany.

<!-- 3632798 - IS, ASDK -->
- W portalu, jeśli Dodawanie reguły zabezpieczeń dla ruchu przychodzącego i wybierz pozycję **Tag usługi** jako źródło, kilka opcji są wyświetlane w **znacznik źródłowy** listy, które nie są dostępne dla usługi Azure Stack. Tylko opcje, które są prawidłowe w usłudze Azure Stack, są następujące:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Inne opcje nie są obsługiwane jako źródło tagi w usłudze Azure Stack. Podobnie jeśli Dodawanie reguły zabezpieczeń dla ruchu wychodzącego i wybierz pozycję **Tag usługi** jako miejsce docelowe, ta sama lista opcji **znacznik źródłowy** jest wyświetlana. Jedyne prawidłowe opcje są takie same jak w przypadku **znacznik źródłowy**, zgodnie z opisem w poprzedniej liście.

- Sieciowe grupy zabezpieczeń (NSG) nie działają w usłudze Azure Stack w ten sam sposób jak globalnego platformy Azure. Na platformie Azure, możesz ustawić wiele portów na jedną regułę sieciowej grupy zabezpieczeń (przy użyciu portalu, programu PowerShell i szablonów usługi Resource Manager). W usłudze Azure Stack, nie możesz ustawić wiele portów na jedną regułę sieciowej grupy zabezpieczeń za pośrednictwem portalu. Aby obejść ten problem, należy użyć szablonu usługi Resource Manager lub programu PowerShell można ustawić te dodatkowe reguły.

<!-- 3203799 - IS, ASDK -->
- Usługa Azure Stack nie obsługuje obecnie dołączania więcej niż 4 interfejsy sieciowe (NIC) do wystąpienia maszyny Wirtualnej już dziś, bez względu na rozmiar wystąpienia.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem pierwszej funkcji platformy Azure w ramach subskrypcji.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Dziennik systemu 

- Konfigurację dziennika systemu nie jest trwały za pośrednictwem cyklu aktualizacji, powodując klienta syslog utratę konfiguracji oraz komunikaty dziennika systemu, aby zatrzymać przesyłane dalej. Ten problem ma zastosowanie do wszystkich wersji programu Azure Stack od Ogólnodostępnej wersji klienta usługi syslog (1809). Aby obejść ten problem, należy skonfigurować klienta usługi syslog po zastosowaniu aktualizacji usługi Azure Stack.

## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji usługi Azure Stack 1902 z [tutaj](https://aka.ms/azurestackupdatedownload). 

W scenariuszach na połączonych wdrożenia usługi Azure Stack okresowo sprawdzać bezpiecznego punktu końcowego i automatycznie powiadomienie, jeśli aktualizacja jest dostępna dla chmury. Aby uzyskać więcej informacji, zobacz [zarządzanie aktualizacjami dla usługi Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
