---
title: Aktualizacja usługi Azure Stack 1901 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o aktualizacji 1901 systemy zintegrowane w usłudze Azure Stack, w tym, co nowego, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: cd07ff5beddf65c9788c9ba94802ba2d37172923
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360699"
---
# <a name="azure-stack-1901-update"></a>Aktualizacja usługi Azure Stack 1901

*Dotyczy Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1901 aktualizacji. Aktualizacja zawiera ulepszenia, poprawki i nowe funkcje dla tej wersji usługi Azure Stack. Również w tym artykule opisano znane problemy w tej wersji i zawiera link do pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1901 **1.1901.0.95** lub **1.1901.0.99** po 26 lutego 2019 r. Zobacz następującą uwagę:

> [!IMPORTANT]  
> Firma Microsoft wykrył problem, który może wpłynąć na klientów, aktualizowania z 1811 (1.1811.0.101) 1901, się i zaktualizowany pakiet 1901, aby rozwiązać ten problem: tworzenie 1.1901.0.99 i aktualizowane na podstawie 1.1901.0.95. Klienci, którzy już zostały zaktualizowane do 1.1901.0.95 jest konieczne wykonywanie dodatkowych działań.
>
> Połączonych klientów, którzy znajdują się na 1811 zostanie automatycznie poinformowany o nowym pakiecie 1901 (1.1901.0.99) dostępne w portalu administratora, a następnie należy zainstalować go po wykonaniu tych czynności. Pobrać i zaimportować nowy pakiet 1901 przy użyciu tego samego procesu klientów odłączonych [opisane w tym miejscu](azure-stack-apply-updates.md).
>
> Klienci z danej wersji 1901 nie ma wpływ na podczas instalowania następnego pełnego lub poprawki pakietu.

## <a name="hotfixes"></a>Poprawki

Usługa Azure Stack wydaje poprawki na bieżąco. Pamiętaj zainstalować [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1811 przed zaktualizowaniem usługi Azure Stack do 1901.

Usługa Azure Stack poprawki dotyczą tylko usługi Azure Stack, zintegrowanych systemów. nie należy próbować zainstalować poprawki na ASDK.

> [!TIP]  
> Subskrybowania następujących *RSS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Usługa Azure Stack poprawki

Jeśli masz już 1901 i nie zainstalowano żadnych poprawek, ale możesz [bezpośrednio zainstalować 1902](azure-stack-update-1902.md), bez uprzedniego instalowania poprawki 1901.

- **1809**: [KB 4481548 — usługa Azure Stack poprawkę 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nie dostępnych poprawki bieżącego.
- **1901**: [KB 4495662 — usługa Azure Stack poprawkę 1.1901.3.105](https://support.microsoft.com/help/4495662)

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Zainstaluj [najnowsze poprawki usługi Azure Stack](#azure-stack-hotfixes) dla 1811 (jeśli istnieje), przed zaktualizowaniem do 1901. Jeśli masz już 1901, nie zainstalowano żadnych poprawek jeszcze 1902 można zainstalować bezpośrednio, bez instalowania najpierw oprogramowania 1901 poprawki.

- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również sprawdź aktywne alerty i rozwiązywanie tych, które wymagają działań:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Gdy usługi Azure Stack jest zarządzane przez System Center Operations Manager (SCOM), pamiętaj, że aktualizacji pakietu administracyjnego dla usługi Microsoft Azure Stack do wersji 1.0.3.11 przed zastosowaniem 1901.

## <a name="new-features"></a>Nowe funkcje

Ta aktualizacja obejmuje następujące nowe funkcje i ulepszenia dla usługi Azure Stack:

- Zarządzanych obrazów na włączenie usługi Azure Stack dysku można utworzyć obiektu obrazu zarządzanego uogólnionej maszyny wirtualnej (zarówno niezarządzane i zarządzane), można tworzyć tylko zarządzane maszyny wirtualne w przyszłości. Aby uzyskać więcej informacji, zobacz [stosu usługi Azure Managed Disks](user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Naprawienie usterki — `Import-AzureRmContext` poprawnie zdeserializować zapisane tokenu.  
   * **AzureRm.Resources**  
         Naprawienie usterki — `Get-AzureRmResource` przypadkiem zapytania insensitively według typów zasobów.  
   * **Azure.Storage**  
         AzureRm zbiorczy moduł zawiera teraz obsługuje już opublikowanej wersji 4.5.0 **interfejsu api w wersji 2017-07-29**.  
   * **AzureRm.Storage**  
         AzureRm zbiorczy moduł zawiera teraz obsługuje już opublikowanej wersji 5.0.4 **interfejsu api w wersji 2017-10-01**.  
   * **AzureRm.Compute**  
         Dodano parametr proste ustawia w `New-AzureRmVM` i `New-AzureRmVmss`, `-Image` parametr obsługuje określanie obrazów użytkowników.  
   * **AzureRm.Insights**  
         AzureRm zbiorczy moduł zawiera teraz obsługuje już opublikowanej wersji 5.1.5 **interfejsu api w wersji 2018-01-01** dla metryk, definicje metryk typów zasobów.

- **AzureStack 1.7.1** tej istotnej zmiany wersji. Aby uzyskać szczegółowe informacje na temat istotnych zmian, zobacz https://aka.ms/azspshmigration171
   * **Moduł Azs.Backup.Admin**  
         Zmiana powodująca niezgodność: Kopia zapasowa zmienia się na tryb szyfrowania opartego na certyfikacie. Obsługa kluczy symetrycznych jest przestarzała.  
   * **Moduł Azs.Fabric.Admin**  
         `Get-AzsInfrastructureVolume` jest przestarzała. Nowe polecenie cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` jest przestarzała.  Nowe polecenie cmdlet `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` jest przestarzała. `StorageSubSystem` Obiekt zawiera właściwości pojemności.  
   * **Moduł Azs.Compute.Admin**  
         Naprawienie usterki — `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: Wywoływanie `ConvertTo-PlatformImageObject` tylko w ścieżce sukces.  
         Poprawka - `Add-AzsVmExtension`, `Get-AzsVmExtension`: Wywoływanie ConvertTo-VmExtensionObject tylko w ścieżce sukces.  
   * **Moduł Azs.Storage.Admin**  
         Naprawienie usterki — nowy limit przydziału magazynu używa ustawień domyślnych, jeśli nie podano.

Aby przejrzeć odwołania do zaktualizowanych modułów, zobacz [odwołania do usługi Azure Stack modułu](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Rozwiązane problemy

- Rozwiązano problem, w którym portalu wykazało, że możliwość utworzenia na podstawie zasad bramy sieci VPN, które nie są obsługiwane w usłudze Azure Stack. Ta opcja została usunięta z portalu.

<!-- 16523695 – IS, ASDK -->
- Rozwiązano problem, w którym po zaktualizowaniu ustawień DNS dla sieci wirtualnej z **użycia usługi Azure Stack DNS** do **niestandardowych serwerów DNS**, wystąpienia nie zostały zaktualizowane o nowe ustawienie.

- <!-- 3235634 – IS, ASDK -->
  Rozwiązano problem z których wdrażanie maszyn wirtualnych o rozmiarach zawierający **v2** sufiks; na przykład **maszyna wirtualna standard_a2_v2 —**, jest to wymagane, określając sufiks jako **maszyna wirtualna standard_a2_v2 —** () małe litery v). Zgodnie z globalnej platformy Azure, możesz teraz użyć **maszyna wirtualna Standard_A2_V2** (wielkie litery V).

<!--  2795678 – IS, ASDK --> 
- Rozwiązano problem, który wygenerował ostrzeżenie, jeśli użyto portalu do tworzenia maszyn wirtualnych (VM) w wersji premium rozmiar maszyny Wirtualnej (DS, Ds_v2, FS i FSv2). Maszyna wirtualna została utworzona na koncie magazynu w warstwie standardowa. Mimo że to nie wpłynął funkcjonalnie, operacje We/Wy lub rozliczeń, ostrzeżenie został rozwiązany.

<!-- 1264761 - IS ASDK -->  
- Rozwiązano problem z **kondycji kontrolera** składnik, który generowania następujące alerty. Alerty można bezpiecznie zignorować:

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


<!-- 3507629 - IS, ASDK --> 
- Rozwiązano problem podczas ustawiania wartości przydziały dysków Managed Disks w ramach [typy limitów przydziału obliczeniowych](azure-stack-quota-types.md#compute-quota-types) 0, jest to równoważne wartość domyślną 2048 GiB. Teraz jest zachowana zero wartości limitu przydziału.

<!-- 2724873 - IS --> 
- Rozwiązano problem, korzystając z polecenia cmdlet programu PowerShell **Start AzsScaleUnitNode** lub **Stop AzsScaleUnitNode** Zarządzanie jednostek skalowania, w których pierwsza próba uruchomienia lub zatrzymania jednostki skalowania może zakończyć się niepowodzeniem.

<!-- 2724961- IS ASDK --> 
- Rozwiązano problem, w którym zarejestrowana **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i utworzyć maszyny Wirtualnej z systemem Windows z włączoną gościa systemu operacyjnego diagnostycznych, ale wykresu procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie wykazała dane metryk. Dane zostaną poprawnie wyświetlone.

- Rozwiązano problem, w której działa **Get AzureStackLog** polecenie cmdlet nie powiodło się po uruchomieniu **AzureStack testu** w tej samej sesji uprzywilejowanych punktu końcowego (program ten). Teraz możesz używać tej samej sesji program ten, który został wykonany **AzureStack testu**.

<!-- bug 3615401, IS -->
- Rozwiązano problem z automatycznych kopii zapasowych, gdzie usługa Harmonogram musieli przejść do stanu wyłączonego nieoczekiwanie. 

<!--2850083, IS ASDK -->
- Usunięte **resetowania bramy** przycisku z portalu usługi Azure Stack, który zgłosił błąd, jeśli został kliknięty przycisk. Usługi Azure Stack, ma wielodostępną bramą, a nie na dedykowanych wystąpieniach maszyn wirtualnych dla każdej dzierżawy bramy sieci VPN, więc został usunięty, aby zapobiec niejasności ten przycisk służy do żadnej funkcji w usłudze Azure Stack. 

<!-- 3209594, IS ASDK -->
- Usunięte **aktywne reguły zabezpieczeń** link z **właściwości sieci** bloku, jak ta funkcja nie jest obsługiwana w usłudze Azure Stack. Utworzenie skojarzenia obecne zapewniła wrażenie, że ta funkcja jest obsługiwana, ale nie działa. Aby złagodzić pomyłek, firma Microsoft usunęła łącze.

<!-- 3139614 | IS -->
- Rozwiązano problem, w którym po aktualizacji do usługi Azure Stack od producenta OEM, **dostępna aktualizacja** powiadomienia nie były wyświetlane w portalu administratora usługi Azure Stack.

## <a name="changes"></a>Zmiany

<!-- 3083238 IS -->
- Usprawnienia zabezpieczeń w ramach tej aktualizacji powoduje wzrost rozmiaru kopii zapasowej roli usługi katalogu. Aby zaktualizować zmiany rozmiaru wskazówki dotyczące lokalizacji magazynu zewnętrznego, zobacz [dokumentacja usługi backup infrastruktury](azure-stack-backup-reference.md#storage-location-sizing). Ta zmiana powoduje dłuższy czas, aby utworzyć kopię zapasową, ze względu na większy rozmiar transferu danych. Ta zmiana ma wpływ na systemach zintegrowanych. 

- Począwszy od stycznia 2019 r można wdrożyć klastrów Kubernetes od usług Active Directory federacyjnego Services (AD FS) zarejestrowany, połączonych sygnatury usługi Azure Stack (wymagany jest dostęp do Internetu). Postępuj zgodnie z instrukcjami [tutaj](azure-stack-solution-template-kubernetes-cluster-add.md) można pobrać nowy element Marketplace usługi Kubernetes. Postępuj zgodnie z instrukcjami [tutaj](user/azure-stack-solution-template-kubernetes-adfs.md) do wdrażania klastra Kubernetes. Należy pamiętać, nowe parametry wskazującą, czy w systemie docelowym jest apletu Dodaj lub usług AD FS zarejestrowany. Jeśli usługi AD FS, nowe pola są dostępne dla wprowadź parametry usługi Key Vault, w których przechowywane są certyfikatem wdrażania.

   Należy pamiętać, że nawet w przypadku obsługi usług AD FS, wdrażanie klastrów Kubernetes wymaga dostępu do Internetu.

- Po zainstalowaniu aktualizacji lub poprawek do usługi Azure Stack, nowe funkcje mogą być wprowadzane, które wymagają nowych uprawnień przyznawanych co najmniej jednej aplikacji tożsamości. Przyznanie tych uprawnień wymaga dostępu administracyjnego do katalogu głównego, a więc go nie może odbywać się automatycznie. Na przykład:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Dostępne jest nowe dokładne Planowanie pojemności usługi Azure Stack. Dzięki aktualizacji 1901 jest teraz limit łącznej liczby maszyn wirtualnych, które mogą być tworzone.  To ograniczenie jest przeznaczone do zastosowania tymczasowego, aby zapobiec niestabilności rozwiązania. Skierowana jest źródłem problemu stabilności w większej liczby maszyn wirtualnych, ale nie ma jeszcze nieustalona określonej osi czasu korygowania. Dzięki aktualizacji 1901 jest teraz na limit serwera 60 maszyn wirtualnych przy ograniczeniu kompleksowe rozwiązania 700.  Na przykład limit maszyny Wirtualnej platformy Azure Stack 8 serwerów będzie 480 (8 * 60).  Dla serwera 12 do 16 rozwiązania usługi Azure Stack limit byłoby 700. Ten limit został utworzony, pamiętając o wszystkie zasoby obliczeniowe zagadnienia dotyczące wydajności takich jak rezerwy odporności i Procesora wirtualnego na fizycznej współczynnika, który operator chcesz zachować na sygnatury. Aby uzyskać więcej informacji zobacz nowa wersja planisty wydajności.  
W przypadku, gdy został osiągnięty limit skalowania maszyn wirtualnych, następujące kody błędów do zwrócenia w wyniku: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- Wersja obliczeniowych interfejsu API został zwiększony do 2017-12-01.

- Tworzenie kopii zapasowych teraz wymaga certyfikatu z kluczem publicznym tylko (. CER) do szyfrowania danych kopii zapasowej. Obsługa kluczy szyfrowania symetrycznego jest przestarzały, począwszy od 1901. Jeśli tworzenie kopii zapasowych jest skonfigurowana przed zaktualizowaniem do 1901, klucze szyfrowania pozostaną w miejscu. Masz co najmniej 2 więcej aktualizacji z poprzednimi wersjami zgodność techniczną, aby zaktualizować ustawienia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [usługi Azure Stack backup najlepszych rozwiązań dotyczących infrastruktury](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń

Ta aktualizacja instaluje następujące aktualizacje zabezpieczeń:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki, lub zobacz artykuły bazy wiedzy Microsoft Knowledge Base [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Podczas uruchamiania [AzureStack testu](azure-stack-diagnostic-test.md), jeśli **AzsInfraRoleSummary** lub **AzsPortalApiSummary** test zakończy się niepowodzeniem, zostanie wyświetlony monit o uruchomienie  **Test AzureStack** z `-Repair` flagi.  Po uruchomieniu tego polecenia, jego wykonanie nie powiodło się następujący komunikat o błędzie:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- Po zastosowaniu 1901 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   - Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Wybierz **dostawców zasobów**, a następnie wybierz **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **AzureStack DiskRP klienta** znajduje się na liście.
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
- Usługa Azure Stack nie obsługuje obecnie dołączania więcej niż 4 interfejsy sieciowe (NIC) do wystąpień maszyn wirtualnych, niezależnie od rozmiaru wystąpienia.

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

Możesz pobrać pakiet aktualizacji usługi Azure Stack 1901 z [tutaj](https://aka.ms/azurestackupdatedownload). 

W scenariuszach na połączonych wdrożenia usługi Azure Stack okresowo sprawdzać bezpiecznego punktu końcowego i automatycznie powiadomienie, jeśli aktualizacja jest dostępna dla chmury. Aby uzyskać więcej informacji, zobacz [zarządzanie aktualizacjami dla usługi Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
