---
title: Aktualizacja usługi Azure Stack 1811 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o aktualizacji 1811 systemy zintegrowane w usłudze Azure Stack, w tym, co nowego, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: c9f43eb1623ec784bd960fb182dffec48e7b0833
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481250"
---
# <a name="azure-stack-1811-update"></a>Aktualizacja usługi Azure Stack 1811

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1811 aktualizacji. Pakiet aktualizacji zawiera ulepszenia, poprawki i nowe funkcje dla tej wersji usługi Azure Stack. Również w tym artykule opisano znane problemy w tej wersji i zawiera łącze, aby pobrać aktualizację. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1811 **1.1811.0.101**.

## <a name="hotfixes"></a>Poprawki

Usługa Azure Stack wydaje poprawki na bieżąco. Pamiętaj zainstalować [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1809 przed zaktualizowaniem usługi Azure Stack do 1811.

> [!TIP]  
> Subskrybowania następujących *RSS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Usługa Azure Stack poprawki

- **1809**: [KB 4481548 — usługa Azure Stack poprawkę 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nie dostępnych poprawki bieżącego.

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Podczas instalacji aktualizacji 1811 należy się upewnić, że wszystkie wystąpienia elementu z portalu administratora są zamknięte. Portal użytkowników może pozostawać otwarte, ale z portalu administratora musi zostać zamknięty.

- Przygotuj wdrożenia usługi Azure Stack na host rozszerzenia usługi Azure Stack. Przygotowanie systemu przy użyciu poniższych wskazówek: [Przygotowanie do hosta rozszerzenia dla usługi Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Zainstaluj [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1809 przed zaktualizowaniem do 1811.

- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Jeśli nie masz wymagania dotyczące hosta rozszerzenia, które zostały spełnione, `Test-AzureStack` dane wyjściowe wyświetla następujący komunikat: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Aktualizacja usługi Azure Stack 1811 wymaga prawidłowo zaimportowane certyfikaty hosta obowiązkowe rozszerzenie do środowiska usługi Azure Stack. Aby kontynuować instalację aktualizacji 1811, należy zaimportować certyfikaty SSL, wymagane dla hosta rozszerzenia. Aby zaimportować certyfikaty, zobacz [w tej sekcji](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Jeśli możesz zignorować ostrzeżenie co i nadal zdecydować się na zainstalowanie aktualizacji 1811 aktualizacji zakończy się niepowodzeniem w ciągu około godziny z następującym komunikatem:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Po zaimportowaniu poprawnie certyfikatów hosta rozszerzenia obowiązkowe, można wznowić aktualizację 1811 z portalu administratora. Gdy firmy Microsoft z informacją o tym operatorom usługi Azure Stack w celu zaplanowania okna obsługi podczas procesu aktualizacji, awarii ze względu na Brak rozszerzenia certyfikatów hosta powinien nie wpływa na istniejące obciążenia lub usługi.  

    Podczas instalacji tej aktualizacji portal użytkowników usługi Azure Stack jest niedostępna, gdy host rozszerzenia jest konfigurowane. Konfiguracja hosta rozszerzenia może potrwać do 5 godzin. W tym czasie można sprawdzić stan aktualizacji lub wznowić niepomyślną instalację aktualizacji przy użyciu [programu PowerShell administratora usługi Azure Stack lub punktu końcowego z uprzywilejowanym dostępem](azure-stack-monitor-update.md).

- Gdy usługi Azure Stack jest zarządzane przez System Center Operations Manager (SCOM), pamiętaj, że aktualizacji pakietu administracyjnego dla usługi Microsoft Azure Stack do wersji 1.0.3.11 przed zastosowaniem 1811.

## <a name="new-features"></a>Nowe funkcje

Ta aktualizacja obejmuje następujące nowe funkcje i ulepszenia dla usługi Azure Stack:

- W tej wersji [host rozszerzenia](azure-stack-extension-host-prepare.md) jest włączona. Host rozszerzenia upraszcza Integracja sieci i zwiększa ogólny poziom zabezpieczeń usługi Azure Stack.

- Dodano obsługę uwierzytelniania urządzenia z usługami Active Directory Sfederowana (AD FS), korzystając z wiersza polecenia platformy Azure w szczególności. Aby uzyskać więcej informacji, zobacz [korzystanie z interfejsu API w wersji profilów za pomocą wiersza polecenia platformy Azure w usłudze Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Dodano obsługę jednostki usługi przy użyciu klucz tajny klienta z usługami Active Directory Sfederowana (AD FS). Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi dla usług AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- W tej wersji dodano obsługę następujących wersji interfejsu API usługi Storage platformy Azure: **2017-07-29**, **2017-11-09**. Ponadto dodano obsługę następujących wersji interfejsu API dostawcy zasobów usługi Azure Storage: **2016-05-01**, **2016-12-01**, **2017-06-01**, i **2017-10-01**. Aby uzyskać więcej informacji, zobacz [magazynu usługi Azure Stack: Różnice i zagadnienia](./user/azure-stack-acs-differences.md).

- Dodano nowe polecenia uprzywilejowanych punkt końcowy do aktualizacji i usuwania zasad usługi dla usług AD FS. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi dla usług AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Dodano nowe operacje węzła jednostki skalowania, umożliwiające operatorów usługi Azure Stack rozpocząć, Zatrzymaj, a następnie zamknij węzeł jednostki skalowania. Aby uzyskać więcej informacji, zobacz [skalowanie akcji węzła jednostki w usłudze Azure Stack](azure-stack-node-actions.md).

- Dodano nowy blok właściwości region, który wyświetla szczegóły rejestracji środowiska. Te informacje można wyświetlić, klikając **zarządzanie regionami** Kafelek na pulpicie nawigacyjnym domyślne w portalu administratora, a następnie wybierając **właściwości**.

- Dodano nowe polecenie do endpoint uprzywilejowanego, można zaktualizować poświadczeń kontrolera zarządzania płytą GŁÓWNĄ nazwą użytkownika i hasło używane do komunikowania się z maszyn fizycznych. Aby uzyskać więcej informacji, zobacz [zaktualizować Kontroler zarządzania płytą główną \(BMC) poświadczeń](azure-stack-rotate-secrets.md).

- Dodano możliwość dostępu plan usługi Azure, mimo że ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu administratora i użytkownika portali, podobny sposób, który jest dostępny w witrynie Azure portal.

- Dodano Ulepszone zarządzanie Marketplace środowisko dla użytkowników bez połączenia. Proces przekazywania publikowanie elementu portalu Marketplace w środowisku bez połączenia została uproszczona w celu jeden krok, zamiast oddzielnie przekazywania obrazu i pakiet portalu Marketplace. Przekazano produktu będzie również widoczne w bloku zarządzania portalu Marketplace. Aby uzyskać więcej informacji, zobacz [w tej sekcji](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- W tej wersji redukuje okno wymagane działania konserwacyjne dla wpisu tajnego obrotu, dodając możliwość obracania tylko certyfikaty zewnętrzne podczas [obrotu wpisu tajnego z usługi Azure Stack](azure-stack-rotate-secrets.md).

- [Usługa Azure Stack PowerShell](azure-stack-powershell-install.md) został zaktualizowany do wersji 1.6.0. Aktualizacja obsługuje nowe funkcje związane z magazynowaniem w usłudze Azure Stack. Aby uzyskać więcej informacji, zobacz informacje o wersji dla [moduł administracji usługi Azure Stack 1.6.0 w galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0) uzyskać informacji o aktualizacji lub instalowanie programu Azure Stack PowerShell, zobacz [Instalowanie programu PowerShell Usługa Azure Stack](azure-stack-powershell-install.md).

- Managed Disks jest teraz włączony domyślnie podczas tworzenia maszyn wirtualnych przy użyciu portalu Azure Stack. Zobacz [znane problemy dotyczące](#known-issues-post-installation) sekcja dotycząca dodatkowych kroków wymaganych do dysków Managed Disks w celu uniknięcia niepowodzeń tworzenia maszyny Wirtualnej.

- W tej wersji wprowadzono alert **naprawy** akcje operatora usługi Azure Stack. Podaj niektórych alertów w programie 1811 **naprawy** przycisk alert, który można wybrać, aby rozwiązać ten problem. Aby uzyskać więcej informacji, zobacz [monitorowania kondycji i alertów w usłudze Azure Stack](azure-stack-monitor-health.md).

- Aktualizacje środowiska aktualizacji w usłudze Azure Stack. Ulepszenia aktualizacji obejmują: 
  - Karty, na których podzielić aktualizacji historii aktualizacji lepsze aktualizacji śledzenia w toku i ukończone aktualizacje.
  - Stan rozszerzone wizualizacje w sekcji podstawy, nowe ikony i układu do wersji bieżącej i OEM, a także ostatni aktualizowane daty.
  - **Widok** link dla kolumny informacje o wersji powoduje otwarcie bezpośrednio do dokumentacji określonych tej aktualizacji, a nie na stronie Ogólne aktualizacji.
  - **Aktualizacji historii** kartę używany do określenia czasu wykonywania dla każdej z aktualizacji, a także rozszerzone funkcje filtrowania.  
  - Jednostki usługi Azure skalowania stosu, które są połączone, będą nadal automatycznie otrzymywać **dostępna aktualizacja** po ich udostępnieniu.
  - Azure stosu jednostek skalowania, które nie są połączone można zaimportować aktualizacje tak samo jak przed. 
  - Nie wprowadzono żadnych zmian w procesie, aby pobrać dzienniki JSON z poziomu portalu. Operatorzy usługi Azure Stack będą widzieć, rozszerzając kroki wyrażania postępu.

    Aby uzyskać więcej informacji, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Rozwiązane problemy

<!-- TBD - IS ASDK --> 
- Rozwiązano problem, w którym dane licznika użycia adresów IP publicznego wykazało, że takie same **Data/godzina zdarzenia** wartość dla każdego rekordu, zamiast **TimeDate** sygnatury, który pokazuje, kiedy rekord został utworzony. Można teraz używać tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- 3099544 – IS, ASDK --> 
- Rozwiązano problem, który wystąpił podczas tworzenia nowej maszyny wirtualnej (VM) przy użyciu portalu Azure Stack. Wybieranie rozmiaru maszyny Wirtualnej spowodowane **USD/miesiąc** kolumny do wyświetlenia **Unavailable** wiadomości. Ta kolumna nie jest już wyświetlany; Wyświetlanie maszyny Wirtualnej cen kolumna nie jest obsługiwana w usłudze Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Rozwiązano problem z portalu administratora przy uzyskiwaniu dostępu do szczegółów dowolnej subskrypcji użytkownika po zamknięciu bloku i klikając **ostatnie**, Nazwa subskrypcji użytkownika nie były wyświetlane. Nazwa subskrypcji użytkownika pojawia się.

<!-- 3060156 - IS ASDK --> 
- Rozwiązano problem w portalach administratora i użytkownika: klikając ustawienia portalu i wybierając **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** nie działał zgodnie z oczekiwaniami i powiadomienia o błędzie został wyświetlony. Obecnie ta opcja działa prawidłowo.

<!-- 2930799 - IS ASDK --> 
- Rozwiązano problem w portalach administratora i użytkownika: w obszarze **wszystkich usług**, element zawartości **planów ochrony przed atakami DDoS** niepoprawnie został wystawiony. Nie jest dostępna w usłudze Azure Stack. Usunięto listę.
 
<!--2760466 – IS  ASDK --> 
- Rozwiązano problem, który wystąpił podczas instalacji nowego środowiska usługi Azure Stack, w którym alert, który wskazuje **wymagana aktywacja** nie były wyświetlane. Jest teraz poprawnie wyświetlany.

<!--1236441 – IS  ASDK --> 
- Rozwiązano problem, który uniemożliwia stosowanie zasad RBAC do grupy użytkowników, korzystając z usług AD FS.

<!--3463840 - IS, ASDK --> 
- Rozwiązano problem z kopiami zapasowymi infrastruktury kończy się niepowodzeniem z powodu serwer_plików niedostępny od sieci publicznych adresów VIP. Ta poprawka jest przenoszony infrastruktury usługi kopii zapasowej do sieci publicznych infrastruktury. Jeśli zastosowano najnowsze [poprawka usługi Azure Stack dla 1809](#azure-stack-hotfixes) , rozwiązuje ten problem, aktualizacja 1811 nie dokona żadnych dalszych modyfikacji. 

<!-- 2967387 – IS, ASDK --> 
- Rozwiązano problem, w którym konto używane do logowania do portalu administratora lub użytkownika usługi Azure Stack jest wyświetlany jako **Niezidentyfikowany użytkownik**. Ten komunikat został wyświetlony, gdy konta nie miał albo **pierwszy** lub **ostatniego** nazwy.   

<!--  2873083 - IS ASDK --> 
- Rozwiązano problem, w których korzystanie z portalu do tworzenia zestawu skalowania maszyn wirtualnych (VMSS) spowodowała **rozmiaru wystąpienia** listę rozwijaną, aby nie załadować się poprawnie, korzystając z programu Internet Explorer. Ta przeglądarka działa teraz poprawnie.  

<!-- 3190553 - IS ASDK -->
- Rozwiązano problem, który wygenerował alerty generujące dużo alertów, wskazującą, czy wystąpienie roli infrastruktury był niedostępny lub węzła jednostki skali była w trybie offline.

<!-- 2724961 - IS ASDK -->
- Fiexed wiadomość problem, w którym na stronie Przegląd maszyny Wirtualnej nie można poprawnie wyświetlić wykres metryk maszyny Wirtualnej. 

## <a name="changes"></a>Zmiany

- Nowy sposób na wyświetlanie i edytowanie przydziały w planie został wprowadzony w 1811. Aby uzyskać więcej informacji, zobacz [Wyświetl istniejący przydział](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Usprawnienia zabezpieczeń w ramach tej aktualizacji powoduje wzrost rozmiaru kopii zapasowej roli usługi katalogu. Aby zaktualizować zmiany rozmiaru wskazówki dotyczące lokalizacji magazynu zewnętrznego, zobacz [dokumentacja usługi backup infrastruktury](azure-stack-backup-reference.md#storage-location-sizing). Ta zmiana powoduje dłuższy czas, aby utworzyć kopię zapasową, ze względu na większy rozmiar transferu danych. Ta zmiana ma wpływ na systemach zintegrowanych. 

- Istniejące polecenia cmdlet program ten, aby pobrać klucze odzyskiwania funkcji BitLocker została zmieniona w 1811 z Get-AzsCsvsRecoveryKeys do Get AzsRecoveryKeys. Aby uzyskać więcej informacji na temat pobrać klucze odzyskiwania funkcji BitLocker, zobacz [instrukcje dotyczące sposobu pobierania kluczy](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń

Ta aktualizacja instaluje następujące aktualizacje zabezpieczeń:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki, lub zobacz artykuły bazy wiedzy Microsoft Knowledge Base [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Po uruchomieniu **Get AzureStackLog** polecenia cmdlet programu PowerShell po uruchomieniu **AzureStack testu** w tej samej sesji uprzywilejowanych punktu końcowego (program ten) **Get AzureStackLog** zakończy się niepowodzeniem. Aby obejść ten problem, Zamknij sesję program ten, w którym jest wykonywane **AzureStack testu**, a następnie otwórz nową sesję, aby uruchomić **Get AzureStackLog**.

- Podczas instalacji 1811 aktualizacji, upewnij się, że wszystkie wystąpienia elementu z portalu administratora są zamknięte w tym czasie. Portal użytkowników może pozostawać otwarte, ale z portalu administratora musi zostać zamknięty.

- Podczas uruchamiania [AzureStack testu](azure-stack-diagnostic-test.md), jeśli **AzsInfraRoleSummary** lub **AzsPortalApiSummary** test zakończy się niepowodzeniem, zostanie wyświetlony monit o uruchomienie  **Test AzureStack** z `-Repair` flagi.  Po uruchomieniu tego polecenia, jego wykonanie nie powiodło się następujący komunikat o błędzie:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Ten problem zostanie rozwiązany w przyszłej wersji.

- Podczas instalacji aktualizacji 1811 w obsłudze portalu usługi Azure Stack jest niedostępna, gdy host rozszerzenia jest konfigurowane. Konfiguracja hosta rozszerzenia może potrwać do 5 godzin. W tym czasie można sprawdzić stan aktualizacji lub wznowić niepomyślną instalację aktualizacji przy użyciu [programu PowerShell administratora usługi Azure Stack lub punktu końcowego z uprzywilejowanym dostępem](azure-stack-monitor-update.md). 

- Podczas instalacji aktualizacji 1811 pulpitu nawigacyjnego portalu użytkownika mogą nie być dostępne, a dostosowań mogą zostać utracone. Pulpit nawigacyjny można przywrócić na ustawienie domyślne, po zakończeniu aktualizacji, otwierając ustawienia portalu i wybierając **przywrócić ustawienia domyślne**.

- Po uruchomieniu [AzureStack testu](azure-stack-diagnostic-test.md), zostanie wyświetlony komunikat ostrzegawczy z kontrolera zarządzania płytą główną (BMC). Można bezpiecznie zignorować to ostrzeżenie.

- <!-- 2468613 - IS --> Podczas instalacji tej aktualizacji, użytkownik może widzieć alerty z tytułem `Error – Template for FaultType UserAccounts.New is missing.` te alerty można bezpiecznie zignorować. Alerty zamknięte automatycznie po zakończeniu instalacji tej aktualizacji.

- <!-- 3139614 | IS --> Po zastosowaniu aktualizacji do usługi Azure Stack z producent OEM, **dostępna aktualizacja** powiadomień nie może występować w portalu administratora usługi Azure Stack. Aby zainstalować usługi Microsoft update, Pobierz i zaimportuj go ręcznie wykonując instrukcje znajdujące się w tym miejscu [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).

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

<!-- TBD - IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- TBD - IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu, należy użyć [programu PowerShell w celu sprawdzenia uprawnień](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Monitorowanie kondycji i

<!-- 1264761 - IS ASDK -->  
- Użytkownik może widzieć alerty dla **kondycji kontrolera** składnik, który ma następujące informacje:  

  - Zgłoś alert, #1:
     - NAZWA:  Rola infrastruktury złej kondycji
     - WAŻNOŚĆ: Ostrzeżenie
     - SKŁADNIK: Kondycji kontrolera
     - OPIS: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  - Zgłoś alert, #2:
     - NAZWA:  Rola infrastruktury złej kondycji
     - WAŻNOŚĆ: Ostrzeżenie
     - SKŁADNIK: Kondycji kontrolera
     - OPIS: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

    Obydwa alerty można bezpiecznie zignorować. Będą one zamknięte automatycznie wraz z upływem czasu.  

### <a name="compute"></a>Wystąpienia obliczeniowe

- Podczas tworzenia nowego Windows maszyn wirtualnych (VM), **ustawienia** bloku wymaga, aby kontynuować, wybierz publicznego portu wejściowego. W 1811 to ustawienie jest wymagane, ale nie ma wpływu. Jest tak, ponieważ ta funkcja jest zależna od zaporę Windows Azure, która nie jest zaimplementowana w usłudze Azure Stack. Możesz wybrać **publiczne porty dla ruchu przychodzącego nie**, ani żadnych innych opcji, aby kontynuować tworzenie maszyny Wirtualnej. To ustawienie nie odniesie żadnego skutku.

- Podczas tworzenia nowej maszyny wirtualnej Windows (VM), może zostać wyświetlony następujący błąd:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Ten błąd występuje, jeśli Włącz diagnostykę rozruchu na maszynie Wirtualnej, ale usunięcia konta magazynu diagnostyki rozruchu. Aby obejść ten problem, należy ponownie utworzyć konto magazynu o takiej samej nazwie jak użyte wcześniej.

- Podczas tworzenia [maszyny Wirtualnej serii Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), maszyn wirtualnych D11 14v2 pozwalają tworzyć 4, 8, 16 i dysków z danymi 32 odpowiednio. Jednak w okienku maszyny Wirtualnej Utwórz pokazuje 8, 16, 32 i 64 dyski z danymi.

- Liczba rekordów użycia w usłudze Azure Stack może zawierać nieoczekiwanych wielkość liter. na przykład:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   W tym przykładzie nazwa grupy zasobów powinna być **AndrewRG**. Można bezpiecznie zignorować tę niespójność.

<!-- 3235634 – IS, ASDK -->
- Aby wdrożyć maszyny wirtualne o rozmiarach zawierający **v2** sufiks; na przykład **maszyna wirtualna standard_a2_v2 —**, określ sufiks jako **maszyna wirtualna standard_a2_v2 —** (v małe litery). Nie używaj **maszyna wirtualna Standard_A2_V2** (wielkie litery V). To działa na platformie Azure globalnych i niezgodności w usłudze Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenie cmdlet kończy się niepowodzeniem z następującym błędem: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. Można bezpiecznie zignorować to ostrzeżenie, który jest wyświetlany komunikat: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Środowisko tworzenia maszyny wirtualnej scale set (VMSS) zapewnia opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz innego systemu operacyjnego dla danego wdrożenia lub użyj innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez określenie szablonu usługi Azure Resource Manager operator.  

<!-- 2724873 - IS --> 
- Korzystając z polecenia cmdlet programu PowerShell **Start AzsScaleUnitNode** lub **Stop AzsScaleunitNode** Zarządzanie jednostek skalowania, pierwsza próba uruchomienia lub zatrzymania jednostki skalowania może zakończyć się niepowodzeniem. Jeśli polecenie cmdlet nie powiedzie się przy pierwszym uruchomieniu, należy uruchomić polecenie cmdlet po raz drugi. Drugiego przebiegu powinien pomyślnie ukończyć operacji. 

<!-- TBD - IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, poinformuj limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  

<!-- 3507629 - IS, ASDK --> 
- Dyski zarządzane tworzy dwa nowe [typy limitów przydziału obliczeniowych](azure-stack-quota-types.md#compute-quota-types) ograniczenie maksymalnej pojemności dysków zarządzanych, które mogą być udostępniane. Domyślnie 2048 GiB jest przydzielany dla każdego typu przydziału dysków zarządzanych. Jednakże można napotkać następujące problemy:

   - Aby przydziały utworzone przed aktualizacją 1808 przydziału dysków Managed Disks wyświetli wartości 0 w portalu administratora, mimo że jest przydzielany 2048 GiB. Można zwiększyć lub zmniejszyć wartość na podstawie Twoich potrzeb rzeczywiste i nowo ustawiony wartości przydziału na zastępuje domyślne GiB 2048.
   - Jeśli zaktualizujesz wartości limitu przydziału na 0 jest równoważna wartość domyślną 2048 GiB. Obejść ten problem należy ustawić wartość limitu przydziału na 1.

<!-- TBD - IS ASDK --> 
- Po zastosowaniu 1811 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   - Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Wybierz **dostawców zasobów**, a następnie wybierz **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **AzureStack DiskRP klienta** roli znajduje się na liście.
   - Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.

- Maszyny Wirtualnej systemu Ubuntu 18.04 utworzone za pomocą autoryzacji SSH włączone uniemożliwi używanie kluczy SSH do logowania. Jako obejście użycie dostępu do maszyny Wirtualnej dla rozszerzenia systemu Linux w celu wdrożenia kluczy SSH po zainicjowaniu obsługi administracyjnej lub korzystanie z uwierzytelniania opartego na hasłach.

### <a name="networking"></a>Networking  

<!-- 1766332 - IS ASDK --> 
- W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są odrzucane.

<!-- 16309153 - IS ASDK --> 
- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z **automatyczne**, zmiana niestandardowego serwera DNS zakończy się niepowodzeniem. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

<!-- 2529607 - IS ASDK --> 
- W usłudze Azure Stack *obrotu klucz tajny*, istnieje okres, w której publiczne adresy IP są niedostępne dla 2 do 5 minut.

<!-- 2664148 - IS ASDK --> 
- W scenariuszach, w którym dzierżawy uzyskuje dostęp do maszyn wirtualnych przy użyciu tunelu S2S VPN mogą występować w scenariuszu, gdzie połączenia zakończy się niepowodzeniem, gdy został dodany w podsieci lokalnej bramy sieci lokalnej, po brama została już utworzona. 

- W portalu usługi Azure Stack po zmianie statyczny adres IP dla konfiguracji adresu IP, który jest powiązany z kartą sieciową, dołączony do wystąpienia maszyny Wirtualnej, zobaczysz komunikat ostrzegawczy z informacją, że 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Możesz bezpiecznie zignorować ten komunikat; adres IP zostanie zmieniony, nawet wtedy, gdy wystąpienie maszyny Wirtualnej nie jest ponownie uruchamiany.

- W portalu na **właściwości sieci** bloku znajduje się link do **aktywne reguły zabezpieczeń** dla każdej karty sieciowej. Jeśli wybierzesz ten link, zostanie otwarty nowy blok zawierający komunikat o błędzie `Not Found.` ten błąd występuje, ponieważ nie obsługuje jeszcze usługi Azure Stack **aktywne reguły zabezpieczeń**.

- W portalu, jeśli Dodawanie reguły zabezpieczeń dla ruchu przychodzącego i wybierz pozycję **Tag usługi** jako źródło, kilka opcji są wyświetlane w **znacznik źródłowy** listy, które nie są dostępne dla usługi Azure Stack. Tylko opcje, które są prawidłowe w usłudze Azure Stack, są następujące:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    Inne opcje nie są obsługiwane jako źródło tagi w usłudze Azure Stack. Podobnie jeśli Dodawanie reguły zabezpieczeń dla ruchu wychodzącego i wybierz pozycję **Tag usługi** jako miejsce docelowe, ta sama lista opcji **znacznik źródłowy** jest wyświetlana. Jedyne prawidłowe opcje są takie same jak w przypadku **znacznik źródłowy**, zgodnie z opisem w poprzedniej liście.

- **New-AzureRmIpSecPolicy** polecenia cmdlet programu PowerShell nie obsługuje ustawiania **DHGroup24** dla `DHGroup` parametru.

- Sieciowe grupy zabezpieczeń (NSG) nie działają w usłudze Azure Stack w ten sam sposób jak globalnego platformy Azure. Na platformie Azure, możesz ustawić wiele portów na jedną regułę sieciowej grupy zabezpieczeń (przy użyciu portalu, programu PowerShell i szablonów usługi Resource Manager). Jedna reguła sieciowej grupy zabezpieczeń w portalu usługi Azure Stack, nie można ustawić wiele portów. Aby obejść ten problem, należy użyć szablonu usługi Resource Manager można ustawić te dodatkowe reguły.

### <a name="infrastructure-backup"></a>Tworzenie kopii zapasowych

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Po włączeniu automatycznego tworzenia kopii zapasowej usługi scheduler przechodzi w stan wyłączenia nieoczekiwanie. Usługa tworzenia kopii zapasowej kontrolera wykryje, że automatyczne kopie zapasowe są wyłączone i podnieść ostrzeżenia w portalu administratora. To ostrzeżenie jest oczekiwane w przypadku automatycznego tworzenia kopii zapasowej są wyłączone. 
    - Przyczyna: Jest to problem z powodu błędów w usłudze, które powoduje utratę konfiguracji harmonogramu. Ta usterka nie zmienia lokalizację magazynu, nazwę użytkownika, hasła lub klucza szyfrowania.   
    - Środki zaradcze: Aby rozwiązać ten problem, otwórz blok ustawień kopii zapasowej kontrolera kopii zapasowej infrastruktury dostawcy zasobów i wybierz **włączyć automatyczne tworzenie kopii zapasowych**. Upewnij się, że dla żądanego okresu częstotliwość i przechowywania.
    - Wystąpienie: Małe 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem pierwszej funkcji platformy Azure w ramach subskrypcji.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji usługi Azure Stack 1811 z [tutaj](https://aka.ms/azurestackupdatedownload). 

W scenariuszach na połączonych wdrożenia usługi Azure Stack okresowo sprawdzać bezpiecznego punktu końcowego i automatycznie powiadomienie, jeśli aktualizacja jest dostępna dla chmury. Aby uzyskać więcej informacji, zobacz [zarządzanie aktualizacjami dla usługi Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Kolejne kroki

- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).  
