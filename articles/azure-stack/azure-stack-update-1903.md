---
title: Aktualizacja usługi Azure Stack 1903 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o aktualizacji 1903 systemy zintegrowane w usłudze Azure Stack, w tym, co nowego, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.lastreviewed: 04/09/2019
ms.openlocfilehash: 79f61f99050748c93ca4bd17d1849f4cbba7a295
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360558"
---
# <a name="azure-stack-1903-update"></a>Aktualizacja usługi Azure Stack 1903

*Dotyczy Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1903 aktualizacji. Aktualizacja zawiera ulepszenia, poprawki i nowe funkcje dla tej wersji usługi Azure Stack. Również w tym artykule opisano znane problemy w tej wersji i zawiera link do pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1903 **1.1903.0.35**.

> [!IMPORTANT]
> Ładunek 1903 nie zawiera wersji ASDK.

## <a name="hotfixes"></a>Poprawki

Usługa Azure Stack wydaje poprawki na bieżąco. Pamiętaj zainstalować [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1902 przed zaktualizowaniem usługi Azure Stack do 1903.

Usługa Azure Stack poprawki dotyczą tylko usługi Azure Stack, zintegrowanych systemów. nie należy próbować zainstalować poprawki na ASDK.

> [!TIP]  
> Subskrybowania następujących *RSS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Usługa Azure Stack poprawki

- **1902**: [KB 4481548 — usługa Azure Stack poprawkę 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Ulepszenia

- Ładunek 1903 aktualizacji zawiera aktualizację składników usługi Azure Stack, które nie ma zasadniczego systemu operacyjnego hosta, którego usługa Azure Stack. Dzięki temu niektóre aktualizacje ograniczyć zakres. W rezultacie, oczekiwany czas potrzebny do ukończenia aktualizacji 1903 jest mniejsza (o 16 godzin, ale dokładna razy mogą się różnić). Ten spadek w środowisku uruchomieniowym jest przeznaczony do aktualizacji 1903 i kolejnych aktualizacji może zawierać aktualizacje systemu operacyjnego, obszaru różnych środowisk uruchomieniowych. Przyszłe aktualizacje zostaną podane podobne wskazówki w oczekiwanym czasie, potrzebnego do ukończenia, w zależności od ładunek dołączony w aktualizacji.

- Usunięto usterkę w sieci, który uniemożliwia zmiany **limitu czasu bezczynności (w minutach)** wartość **publiczny adres IP** z zastosowaniem. Wcześniej zmiany tej wartości zostały zignorowane, więc, że niezależnie od tego, wszystkie wprowadzone zmiany będą domyślnie wartość 4 minut. To ustawienie określa, ile minut, aby zachować połączenie TCP Otwórz bez polegania na klientach, aby wysyłać komunikaty utrzymywania aktywności. Należy pamiętać, że ten problem dotyczy tylko wystąpienia poziomu publiczne adresy IP, nie publiczne adresy IP przypisane do modułu równoważenia obciążenia.

- Ulepszenia niezawodności aparatu aktualizacji, w tym automatyczne korygowanie typowe problemy, aby nieprzerwanie stosowane aktualizacje.

- Ulepszenia wykrywania i korygowania małej ilości miejsca na warunkach.

### <a name="secret-management"></a>Zarządzanie wpisami tajnymi

- Usługa Azure Stack obsługuje teraz rotacji certyfikatu głównego używany przez certyfikaty dla zewnętrznych obrotu wpisu tajnego. Aby uzyskać więcej informacji [znajduje się w artykule](azure-stack-rotate-secrets.md).

- 1903 zawiera ulepszenia wydajności dla wpisu tajnego obrotu, które skrócić czas potrzebny na wykonanie wewnętrznego obrotu wpisu tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Zainstaluj [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1902 (jeśli istnieje), przed zaktualizowaniem do 1903.

- Upewnij się, że używasz najnowszej wersji [planisty wydajności usługi Azure Stack](https://aka.ms/azstackcapacityplanner) celu obciążenia planowania i rozmiaru. Najnowsza wersja zawiera poprawki błędów i udostępnia nowe funkcje, które są wydawane w każdej aktualizacji usługi Azure Stack.

- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) rozwiązanie następujący parametr, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również sprawdź aktywne alerty i rozwiązywanie tych, które wymagają działań:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Gdy usługi Azure Stack jest zarządzana przy użyciu programu System Center Operations Manager, upewnij się zaktualizować [pakiet administracyjny dla programu Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) do wersji 1.0.3.11 przed zastosowaniem 1903.

- Format pakietu aktualizacji usługi Azure Stack został zmieniony z **.bin/.exe/.xml** do **.zip/.xml** począwszy od wersji 1902. Klienci z połączonej jednostki skalowania usługi Azure Stack będą widzieć **dostępna aktualizacja** wiadomości w portalu. Klienci, którzy nie są połączone teraz po prostu pobrać i zaimportować plik zip z odpowiedni plik XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Po uruchomieniu [AzureStack testu](azure-stack-diagnostic-test.md), zostanie wyświetlony komunikat ostrzegawczy z kontrolera zarządzania płytą główną (BMC). Można bezpiecznie zignorować to ostrzeżenie.

- <!-- 2468613 - IS --> Podczas instalacji tej aktualizacji, użytkownik może widzieć alerty z tytułem **błędu — szablon FaultType UserAccounts.New Brak.** Te alerty można bezpiecznie zignorować. Alerty zamknięte automatycznie po zakończeniu instalacji tej aktualizacji.

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

<!-- Daniel 3/28 -->
- W aplikacji portal użytkowników, gdy przejdź do obiektu blob w ramach konta magazynu i spróbuj otworzyć **zasad dostępu** w drzewie nawigacji kolejne okno nie udało się załadować.

<!-- Daniel 3/28 -->
- W aplikacji portal użytkowników, podczas próby przekazania obiektu blob przy użyciu **OAuth(preview)** opcji, zadanie kończy się niepowodzeniem z komunikatem o błędzie. Aby obejść ten problem, przekaż obiekt blob przy użyciu **sygnatury dostępu Współdzielonego** opcji.

- Po zalogowaniu się do portali usługi Azure Stack mogą pojawiać się powiadomienia dotyczące publicznej witryny Azure portal. Powiadomienia te można bezpiecznie zignorować, ponieważ nie mają obecnie zastosowania do usługi Azure Stack (na przykład, "1 nowa aktualizacja — następujące aktualizacje są teraz dostępne: 2019 kwietnia aktualizacja witryny Azure portal").

<!-- ### Health and monitoring -->

### <a name="compute"></a>Wystąpienia obliczeniowe

- Podczas tworzenia nowej maszyny wirtualnej Windows (VM), może zostać wyświetlony następujący błąd:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Ten błąd występuje, jeśli Włącz diagnostykę rozruchu na maszynie Wirtualnej, ale usunięcia konta magazynu diagnostyki rozruchu. Aby obejść ten problem, należy ponownie utworzyć konto magazynu o takiej samej nazwie jak użyte wcześniej.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Środowisko tworzenia zestawu skalowania maszyn wirtualnych zapewnia opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz innego systemu operacyjnego dla danego wdrożenia lub użyj innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez określenie szablonu usługi Azure Resource Manager operator.  

<!-- TBD - IS ASDK --> 
- Po zastosowaniu 1903 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   - Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Wybierz **dostawców zasobów**, a następnie wybierz **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
   - Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.

- Maszyny Wirtualnej systemu Ubuntu 18.04 utworzone za pomocą autoryzacji SSH włączone uniemożliwi używanie kluczy SSH do logowania. Jako obejście użycie dostępu do maszyny Wirtualnej dla rozszerzenia systemu Linux w celu wdrożenia kluczy SSH po zainicjowaniu obsługi administracyjnej lub korzystanie z uwierzytelniania opartego na hasłach.

- Usługa Azure Stack obsługuje teraz agenci dla systemu Windows Azure Linux nowsza niż wersja 2.2.20. Ta funkcjonalność było częścią 1901 i 1902 poprawki i pozwala klientom na przechowywanie obrazów systemu linux spójne między platformą Azure i usługi Azure Stack.


- Jeśli nie masz hosta cyklu życia sprzętu (HLH): przed kompilacją 1902 trzeba było ustawić zasady grupy **komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady Lokalne\opcje** do **Wyślij LM i protokołu NTLM — Użyj zabezpieczeń sesji NTLMv2 po uzgodnieniu**. Od czasu kompilacji 1902 należy pozostawić go w formie **Niezdefiniowany** lub ustaw go na **tylko NTLMv2 wysłać odpowiedź** (czyli wartość domyślna). W przeciwnym razie nie będzie można nawiązać sesji zdalnej programu PowerShell i zostanie wyświetlony **dostęp jest zabroniony** błąd:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Networking  

<!-- 3239127 - IS, ASDK -->
- W portalu usługi Azure Stack po zmianie statyczny adres IP dla konfiguracji adresu IP, który jest powiązany z kartą sieciową, dołączony do wystąpienia maszyny Wirtualnej, zobaczysz komunikat ostrzegawczy z informacją, że 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

Możesz pobrać pakiet aktualizacji usługi Azure Stack 1903 z [tutaj](https://aka.ms/azurestackupdatedownload). 

W scenariuszach na połączonych wdrożenia usługi Azure Stack okresowo sprawdzać bezpiecznego punktu końcowego i automatycznie powiadomienie, jeśli aktualizacja jest dostępna dla chmury. Aby uzyskać więcej informacji, zobacz [zarządzanie aktualizacjami dla usługi Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
