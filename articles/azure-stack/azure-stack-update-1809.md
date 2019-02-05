---
title: Aktualizacja usługi Azure Stack 1809 | Dokumentacja firmy Microsoft
description: Informacje na temat nowości w aktualizacji 1809 dla usługi Azure Stack zintegrowane systemy, w tym znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: a9cf502f169f4a9c4650545b1b37e11cc16a0a95
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694384"
---
# <a name="azure-stack-1809-update"></a>Aktualizacja usługi Azure Stack 1809

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano zawartość pakietu 1809 aktualizacji. Pakiet aktualizacji zawiera ulepszenia, poprawki i znane problemy dotyczące tej wersji usługi Azure Stack. Ten artykuł zawiera również link, aby pobrać aktualizację. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]  
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania

Numerem kompilacji aktualizacji usługi Azure Stack 1809 **1.1809.0.90**.  

### <a name="new-features"></a>Nowe funkcje

Ta aktualizacja obejmuje następujące ulepszenia usługi Azure Stack:

- W tej wersji usługi Azure Stack zintegrowane systemy obsługuje konfiguracje węzłów 4 – 16. Możesz użyć [Planisty wydajności usługi Azure Stack](https://aka.ms/azstackcapacityplanner) pomagają w planowaniu pojemności usługi Azure Stack i konfiguracji.

- <!--  2712869   | IS  ASDK -->  **Klient usługi syslog usługi Azure Stack (ogólnie)** ten klient umożliwia przekazywanie inspekcje, alerty i dzienniki zabezpieczeń związane z infrastruktury Azure Stack w celu syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrzne w stosunku do usługi Azure Stack. Klient usługi syslog obsługuje teraz, określając port, na którym nasłuchuje serwer syslog.

   W tej wersji klienta programu syslog jest ogólnie dostępna, i mogą być używane w środowiskach produkcyjnych.

   Aby uzyskać więcej informacji, zobacz [przekazywania usługi syslog usługi Azure Stack](azure-stack-integrate-security.md).

- Możesz teraz [Przenieś zasób rejestracji](azure-stack-registration.md#move-a-registration-resource) na platformie Azure, między grupami zasobów bez konieczności ponownej rejestracji. Dostawców rozwiązań w chmurze (CSP) również można przenieść zasobu rejestracji między subskrypcjami, tak długo, jak nowym i starym subskrypcje są mapowane na ten sam identyfikator partnera programu CSP. Nie ma to wpływu na istniejące mapowania dzierżawy klienta. 

- Dodano obsługę przypisywanie wielu adresów IP dla każdego interfejsu sieciowego.  Aby uzyskać więcej informacji, zobacz [przypisać wiele adresów IP do maszyn wirtualnych przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- TBD - IS ASDK -->
- W portalu wykres pamięci raportowania bezpłatnie używać pojemności jest dokładne. Możesz teraz bardziej niezawodne przewidzieć liczbę maszyn wirtualnych można utworzyć.

<!-- TBD - IS ASDK --> 
- Rozwiązano problem, w której maszyny wirtualne są tworzone w portalu użytkowników usługi Azure Stack, a portalem wyświetlane niepoprawną liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- Następujące problemy z dysków zarządzanych zostały usunięte w 1809 i również zostały usunięte w 1808 [usługi Azure Stack poprawkę 1.1808.9.117](https://support.microsoft.com/help/4481066/): 

   <!--  2966665 – IS, ASDK --> 
   - Rozwiązany problem, w których dołączający SSD dysków z danymi premium rozmiar maszyn wirtualnych dysku zarządzanego (DS, DSv2, Fs i Fs_V2) nie powiodło się z powodu błędu:  *Nie można zaktualizować dysków maszyny wirtualnej "vmname" Błąd: Żądanie, nie można wykonać operacji, ponieważ typ konta magazynu "Premium_LRS" nie jest obsługiwana dla rozmiaru maszyny Wirtualnej "Standard_DS/Ds_V2/FS/Fs_v2)*. 
   
   - Tworzenie dysku zarządzanego maszyny Wirtualnej przy użyciu **createOption**: **Dołącz** kończy się niepowodzeniem z powodu następującego błędu: *Długie wykonywanie operacji nie powiodło się ze stanem "Niepowodzenie". Dodatkowe informacje: "Wystąpił błąd wewnętrzny wykonania."*
   Kod błędu: InternalExecutionError komunikat o błędzie: Wystąpił błąd wewnętrzny wykonania.
   
   Ten problem został rozwiązany.

- <!-- 2702741 -  IS, ASDK --> Rozwiązano problem, w której publiczne adresy IP, które zostały wdrożone za pomocą dynamicznej alokacji metoda nie jest gwarantowana zostaną zachowane po wystawieniu Zatrzymaj anulowanie. Teraz są zachowywane.

- <!-- 3078022 - IS, ASDK --> Jeśli maszyna wirtualna została zatrzymana z cofniętą alokacją przed 1808 nie można jej ponownie przydzielić po 1808 aktualizacji.  Ten problem został rozwiązany w 1809. Wystąpienia, które zostały w tym stanie i nie może zostać uruchomiony, może zostać uruchomiony w 1809 dzięki tej poprawce. Poprawka zapobiega także ten problem pojawiał.

### <a name="changes"></a>Zmiany

<!-- 2635202 - IS, ASDK -->
- Infrastruktura usługi kopii zapasowej są przenoszone z [sieci publicznych infrastruktury](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-infrastructure-network) do [sieci publicznych adresów VIP](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network). Klienci będą musieli upewnić się, usługa ma dostęp do lokalizacji magazynu kopii zapasowych z sieci publicznych adresów VIP.  

> [!IMPORTANT]  
> Jeśli masz zaporą, która nie zezwala na połączenia z sieci publicznych adresów VIP do serwera plików, ta zmiana spowoduje, że kopie zapasowe infrastruktury, aby zakończyć się niepowodzeniem z "Błąd 53 nie można odnaleźć ścieżki sieciowej." Jest to istotną zmianę, która ma uzasadnione obejście tego problemu. Na podstawie opinii klientów, firmy Microsoft zostaną przywrócone, aby ta zmiana w poprawki. Przejrzyj [wpis w sekcji kroki aktualizacji](#post-update-steps) Aby uzyskać więcej informacji na temat dostępnych poprawek dla 1809. Gdy poprawka jest dostępna, upewnij się zastosować go po aktualizacji do 1809, tylko wtedy, gdy zasad sieci nie zezwalają na sieci publicznych adresów VIP na dostęp do zasobów infrastruktury. w 1811 tej zmiany zostaną zastosowane do wszystkich systemów. Jeśli zastosowano poprawkę w 1809, nie ma żadnych dodatkowych czynności.  

### <a name="common-vulnerabilities-and-exposures"></a>Typowe luki w zabezpieczeniach i zagrożeń

Ta aktualizacja instaluje następujące aktualizacje zabezpieczeń:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420 —](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Aby uzyskać więcej informacji na temat tych luk w zabezpieczeniach kliknij poprzednie linki, lub zobacz artykuły bazy wiedzy Microsoft Knowledge Base [4457131](https://support.microsoft.com/help/4457131) i [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj najnowsze poprawki stosu platformy Azure dla 1808 przed zastosowaniem 1809. Aby uzyskać więcej informacji, zobacz [4481066 KB — Azure Stack poprawkę Azure stosu poprawkę 1.1808.9.117](https://support.microsoft.com/help/4481066/). Firma Microsoft zaleca najnowszych poprawek, dostępnych, minimalna wersja wymagana do zainstalowania 1809 jest 1.1808.5.110.

  > [!TIP]  
  > Subskrybowania następujących *RRS* lub *Atom* źródła danych na bieżąco przy użyciu usługi Azure Stack poprawki:
  > - REKORDY ZASOBÓW: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Po uruchomieniu [AzureStack testu](azure-stack-diagnostic-test.md) po 1809 aktualizacji, wyświetlany jest komunikat ostrzegawczy z kontrolera zarządzania płytą główną (BMC). Można bezpiecznie zignorować to ostrzeżenie.

- <!-- 2468613 - IS --> Podczas instalacji tej aktualizacji, użytkownik może widzieć alerty z tytułem *błędu — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu instalacji tej aktualizacji.

- <!-- 2489559 - IS --> Nie należy próbować tworzyć maszyny wirtualne podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Po zastosowaniu aktualizacji do usługi Azure Stack z producent OEM, **dostępna aktualizacja** powiadomień nie może występować w portalu administratora usługi Azure Stack. Aby zainstalować usługi Microsoft update, Pobierz i zaimportuj go ręcznie wykonując instrukcje znajdujące się w tym miejscu [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Kroki po aktualizacji

> [!Important]  
> Przygotuj wdrożenia usługi Azure Stack na hoście rozszerzenia, który został włączony przez następny pakiet aktualizacji. Przygotowanie systemu przy użyciu poniższych wskazówek, [przygotować się do hosta rozszerzenia dla usługi Azure Stack](azure-stack-extension-host-prepare.md).

Po zainstalowaniu tej aktualizacji Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszego [obsługi zasad](azure-stack-servicing-policy.md).  
- [KB 4481548 — usługi Azure Stack poprawki w usłudze Azure Stack poprawkę 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Poniżej przedstawiono znane problemy po instalacji tej wersji kompilacji.

### <a name="portal"></a>Portal

- Dokumentacja techniczna usługi Azure Stack koncentruje się na najnowszej wersji. Z powodu portalu zmian między wersjami Zobacz podczas przy użyciu portali usługi Azure Stack mogą różnić się od wyświetlanych w dokumentacji.

<!-- 2930718 - IS ASDK --> 
- W portalu administratora przy uzyskiwaniu dostępu do szczegółów dowolnej subskrypcji użytkownika po zamknięciu bloku i klikając **ostatnie**, nie jest wyświetlana Nazwa subskrypcji użytkownika.

<!-- 3060156 - IS ASDK --> 
- W portalach administratora i użytkownika, klikając ustawienia portalu i wybierając **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** nie działa zgodnie z oczekiwaniami. Powiadomienia o błędzie jest wyświetlany. 

<!-- 2930799 - IS ASDK --> 
- W portalach administratora i użytkownika w obszarze **wszystkich usług**, element zawartości **planów ochrony przed atakami DDoS** niepoprawnie znajduje się na liście. Nie jest dostępna w usłudze Azure Stack. Jeśli spróbujesz go utworzyć, zostanie wyświetlony błąd informujący, że portal nie można utworzyć elementu portalu marketplace. 

<!-- 2930820 - IS ASDK --> 
- W portalach administratora i użytkownika jeśli wyszukasz "Docker" jest niepoprawnie zwrócony element. Nie jest dostępna w usłudze Azure Stack. Jeśli spróbujesz go utworzyć, zostanie wyświetlony blok ze wskazaniem błędu. 

<!-- 2967387 – IS, ASDK --> 
- Konto używane do logowania do portalu administratora lub użytkownika usługi Azure Stack jest wyświetlany jako **Niezidentyfikowany użytkownik**. Ten komunikat jest wyświetlany, gdy konto nie ma albo *pierwszy* lub *ostatniego* nazwy. Aby obejść ten problem, należy edytować konto użytkownika, aby podać nazwę pierwszego lub ostatniego. Należy następnie zaloguj i zaloguj ponownie do portalu.  

<!--  2873083 - IS ASDK --> 
-  Kiedy używać portalu, aby utworzyć zestaw skalowania maszyn wirtualnych zestawu (zestawu skalowania maszyn wirtualnych), *rozmiaru wystąpienia* listy rozwijanej nie załadować się poprawnie, gdy używasz programu Internet Explorer. Aby obejść ten problem, należy użyć innej przeglądarki podczas korzystania z portalu do tworzenia zestawu skalowania maszyn wirtualnych.  

<!-- 2931230 – IS  ASDK --> 
- Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

<!--2760466 – IS  ASDK --> 
- Po zainstalowaniu nowego środowiska usługi Azure Stack, z tą wersją, ten alert oznacza to, *wymagana aktywacja* mogą nie być wyświetlane. [Aktywacja](azure-stack-registration.md) jest wymagany, zanim będzie można użyć portalu marketplace syndykacji.  

<!-- TBD - IS ASDK --> 
- Nie można używać dwa typy subskrypcji administracyjne, które zostały wprowadzone w wersji 1804. Typy subskrypcji **pomiaru subskrypcji**, i **subskrypcji zużycie**. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać **domyślny dostawca** typu subskrypcji.

<!-- TBD - IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- TBD - IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


### <a name="health-and-monitoring"></a>Monitorowanie kondycji i

<!-- TBD - IS -->
- Możesz zobaczyć następujące alerty regularnie pojawiają się i następnie znikają w systemie Azure Stack:
   - *Infrastruktura wystąpienie roli jest niedostępne*
   - *Węzeł jednostka skalowania jest w trybie offline*
   
  Uruchom [AzureStack testu](azure-stack-diagnostic-test.md) polecenia cmdlet, aby sprawdzić kondycję wystąpień roli infrastruktury i skalowanie węzłów jednostki. Brak problemów w przypadku wykrycia przez [AzureStack testu](azure-stack-diagnostic-test.md), można zignorować te alerty. W przypadku wykrycia problemu można spróbować uruchomić wystąpienie roli infrastruktury lub węzła przy użyciu portalu administracyjnego lub programu PowerShell.

  Ten problem jest rozwiązany w najnowszej [wersji poprawki 1809](https://support.microsoft.com/help/4481548/), dlatego należy tę poprawkę należy zainstalować, jeśli występują problem. 

<!-- 1264761 - IS ASDK -->  
- Użytkownik może widzieć alerty dla **kondycji kontrolera** składnik, który ma następujące informacje:  

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


<!-- 2812138 | IS --> 
- Można napotkać alert w przypadku **magazynu** składnik, który zawiera następujące informacje:

   - NAZWA: Błąd wewnętrzny komunikacji z usługą Storage  
   - WAŻNOŚĆ: Krytyczny  
   - SKŁADNIK: Magazyn  
   - OPIS: Błąd wewnętrzny komunikacji z usługą Storage wystąpił podczas wysyłania żądań do następujących węzłów.  

    Alert można bezpiecznie zignorować, ale musisz ręcznie zamknąć alert.

<!-- 2368581 - IS, ASDK --> 
- Operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą **błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej**, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń.

### <a name="compute"></a>Wystąpienia obliczeniowe

- Podczas tworzenia [maszyny Wirtualnej serii Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), maszyn wirtualnych D11 14v2 pozwalają tworzyć 4, 8, 16 i dysków z danymi 32 odpowiednio. Jednak w okienku maszyny Wirtualnej Utwórz pokazuje 8, 16, 32 i 64 dyski z danymi.

<!-- 3235634 – IS, ASDK -->
- Aby wdrożyć maszyny wirtualne o rozmiarach zawierający **v2** sufiks; na przykład **maszyna wirtualna standard_a2_v2 —**, określ sufiks jako **maszyna wirtualna standard_a2_v2 —** (v małe litery). Nie używaj **maszyna wirtualna Standard_A2_V2** (wielkie litery V). To działa na platformie Azure globalnych i niezgodności w usłudze Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Jeśli podczas tworzenia nowej maszyny wirtualnej (VM) przy użyciu portalu Azure Stack, wybierz rozmiar maszyny Wirtualnej, w kolumnie USD/miesiąc jest wyświetlany z **Unavailable** wiadomości. Ta kolumna nie powinien pojawić się; Wyświetlanie maszyny Wirtualnej cen kolumna nie jest obsługiwana w usłudze Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Korzystając z [ **AzsPlatformImage Dodaj** polecenia cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), należy użyć **- OsUri** jako konto magazynu, identyfikator URI, gdy dysk jest przekazywany parametr. Jeśli używasz lokalnej ścieżki dysku, polecenie cmdlet kończy się niepowodzeniem z następującym błędem: *Długie wykonywanie operacji nie powiodło się ze stanem "Niepowodzenie"*. 

<!--  2795678 – IS, ASDK --> 
- Korzystając z portalu do tworzenia maszyn wirtualnych (VM) rozmiarów maszyn wirtualnych — wersja premium (DS, Ds_v2, FS i FSv2), maszyna wirtualna jest tworzona na koncie magazynu w warstwie standardowa. Tworzenie konta magazynu w warstwie standardowa nie wpływa na funkcjonalnie, operacje We/Wy, lub rozliczeń. 

   Można bezpiecznie zignorować to ostrzeżenie, który jest wyświetlany komunikat: *Wybrano używać dysku standardowego na na rozmiar obsługujący dyski w warstwie premium. To może mieć wpływ na wydajność systemu operacyjnego i nie jest zalecane. Rozważ użycie magazynu w warstwie premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Środowisko tworzenia maszyny wirtualnej scale set (VMSS) zapewnia opartych na systemie CentOS 7.2 jako opcję wdrażania. Ponieważ ten obraz nie jest dostępna w usłudze Azure Stack, wybierz inny system operacyjny na potrzeby wdrożenia albo skorzystać z szablonu usługi Azure Resource Manager, określając innego obrazu systemu CentOS, który został pobrany przed ich wdrożeniem w portalu Marketplace przez operatora.  

<!-- 2724873 - IS --> 
- Korzystając z polecenia cmdlet programu PowerShell **Start AzsScaleUnitNode** lub **Stop AzsScaleunitNode** Zarządzanie jednostek skalowania, pierwsza próba uruchomienia lub zatrzymania jednostki skalowania może zakończyć się niepowodzeniem. Jeśli polecenie cmdlet nie powiedzie się przy pierwszym uruchomieniu, należy uruchomić polecenie cmdlet po raz drugi. Drugiego przebiegu ma być pomyślnie wykonane do ukończenia tej operacji. 

<!-- TBD - IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  

<!-- 2724961- IS ASDK --> 
- Po dokonaniu rejestracji **Microsoft.Insight** dostawcy zasobów w ustawieniach subskrypcji i tworzenie maszyny Wirtualnej z systemem Windows przy użyciu gościa systemu operacyjnego diagnostycznych włączone, wykres procent użycia procesora CPU na stronie Omówienie maszyny Wirtualnej nie wyświetla danych metryk.

   Aby znaleźć danych metryk, takich jak wykres procent użycia procesora CPU dla maszyny Wirtualnej, przejdź do okna metryk i wyświetlić wszystkie obsługiwane metryki gościa maszyny Wirtualnej Windows.

<!-- 3507629 - IS, ASDK --> 
- Dyski zarządzane tworzy dwa nowe [typy limitów przydziału obliczeniowych](azure-stack-quota-types.md#compute-quota-types) ograniczenie maksymalnej pojemności dysków zarządzanych, które mogą być udostępniane. Domyślnie 2048 GiB jest przydzielany dla każdego typu przydziału dysków zarządzanych. Jednakże można napotkać następujące problemy:

   - Aby przydziały utworzone przed aktualizacją 1808 przydziału dysków Managed Disks wyświetli wartości 0 w portalu administratora, mimo że jest przydzielany 2048 GiB. Można zwiększyć lub zmniejszyć wartość na podstawie Twoich potrzeb rzeczywiste i nowo ustawiony wartości przydziału na zastępuje domyślne GiB 2048.
   - Jeśli zaktualizujesz wartości limitu przydziału na 0 jest równoważna wartość domyślną 2048 GiB. Obejść ten problem należy ustawić wartość limitu przydziału na 1.

<!-- TBD - IS ASDK --> 
- Po zastosowaniu 1809 aktualizacji, można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

   - Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
      1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Kliknij przycisk **dostawców zasobów**, następnie kliknij przycisk **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
      2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **AzureStack DiskRP klienta** roli znajduje się na liście.
   2. Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.

- Maszyny Wirtualnej systemu Ubuntu 18.04 utworzonej z autoryzacją SSH włączone uniemożliwi używanie kluczy SSH do logowania. Obejść ten problem użyj dostęp do maszyny Wirtualnej dla rozszerzenia systemu Linux do zaimplementowania kluczy SSH po zainicjowaniu obsługi administracyjnej, lub korzystać z uwierzytelniania opartego na hasłach.

### <a name="networking"></a>Networking  

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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.


### <a name="usage"></a>Sposób użycia  

<!-- TBD - IS ASDK --> 
- Publiczne dane z licznika użycia adresów IP zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji usługi Azure Stack 1809 z [tutaj](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Kolejne kroki

- Aby przejrzeć zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym, zobacz [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).  
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).  
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).  
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).  
