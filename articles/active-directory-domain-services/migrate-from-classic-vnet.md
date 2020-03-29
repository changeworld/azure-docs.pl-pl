---
title: Migrowanie usług domenowych usługi Azure AD z klasycznej sieci wirtualnej | Dokumenty firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację istniejącego wystąpienia domeny domeny usług AD AD azure z klasycznego modelu sieci wirtualnej do sieci wirtualnej opartej na Menedżerze zasobów.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: e7caacf23cb489da6f9f85748ae839bc4babff8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917309"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrowanie usług domenowych usługi Azure AD z klasycznego modelu sieci wirtualnej do Menedżera zasobów

Usługi domenowe Active Directory (AD DS) platformy Azure obsługują jednorazowe przejście dla klientów korzystających obecnie z klasycznego modelu sieci wirtualnej do modelu sieci wirtualnej Menedżera zasobów. Domeny zarządzane usługi Azure AD DS korzystające z modelu wdrażania Usługi Resource Manager zapewniają dodatkowe funkcje, takie jak szczegółowe zasady haseł, dzienniki inspekcji i ochrona blokady konta.

W tym artykule opisano korzyści i zagadnienia dotyczące migracji, a następnie kroki wymagane do pomyślnej migracji istniejącego wystąpienia usług Azure AD DS.

> [!NOTE]
> W 2017 r. usługi domenowe usługi ad usługi Azure AD stały się dostępne do hosta w sieci usługi Azure Resource Manager. Od tego czasu udało nam się zbudować bezpieczniejszą usługę przy użyciu nowoczesnych możliwości usługi Azure Resource Manager. Ponieważ wdrożenia usługi Azure Resource Manager w pełni zastępują klasyczne wdrożenia, klasyczne wdrożenia sieci wirtualnej usługi Azure AD DS zostaną wycofane 1 marca 2023 r.
>
> Aby uzyskać więcej informacji, zobacz [oficjalne zawiadomienie o umoriwanie](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Omówienie procesu migracji

Proces migracji trwa istniejące wystąpienie usług Azure AD DS, które działa w klasycznej sieci wirtualnej i przenosi go do istniejącej sieci wirtualnej Menedżera zasobów. Migracja jest wykonywana przy użyciu programu PowerShell i ma dwa główne etapy wykonywania - *przygotowanie* i *migrację.*

![Omówienie procesu migracji usług Ad DS](media/migrate-from-classic-vnet/migration-overview.png)

Na etapie *przygotowania* usługi Azure AD DS wykonuje kopię zapasową domeny, aby uzyskać najnowszą migawkę użytkowników, grup i haseł zsynchronizowanych z domeną zarządzaną. Synchronizacja jest następnie wyłączona, a usługa w chmurze obsługująca domenę zarządzaną usługą Azure AD DS jest usuwana. Na etapie przygotowania domena zarządzana usługą Azure AD DS nie może uwierzytelnić użytkowników.

![Etap przygotowania do migracji usług Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Na etapie *migracji* podstawowe dyski wirtualne dla kontrolerów domeny z klasycznej domeny zarządzanej usługi Azure AD DS są kopiowane w celu utworzenia maszyn wirtualnych przy użyciu modelu wdrażania Menedżera zasobów. Domena zarządzana usługą Azure AD DS jest następnie odtworzona, co obejmuje konfigurację LDAPS i DNS. Synchronizacja z usługą Azure AD jest ponownie uruchamiana, a certyfikaty LDAP są przywracane. Nie ma potrzeby ponownego dołączania do żadnych komputerów do domeny zarządzanej usług Azure AD DS — nadal są one przyłączane do domeny zarządzanej i uruchamiane bez zmian.

![Migracja usług Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Korzyści z migracji

Podczas przenoszenia domeny zarządzanej usługi Azure AD DS przy użyciu tego procesu migracji, można uniknąć konieczności ponownego dołączania maszyn do domeny zarządzanej lub usunąć wystąpienie usług Azure AD DS i utworzyć go od podstaw. Maszyny wirtualne nadal są dołączane do domeny zarządzanej usług Azure AD DS na końcu procesu migracji.

Po migracji usługa Azure AD DS udostępnia wiele funkcji, które są dostępne tylko dla domen przy użyciu sieci wirtualnych Usługi Resource Manager, takich jak:

* Obsługa zasad haseł szczegółowe.
* Ochrona blokady konta USŁUGI AD.
* Powiadomienia e-mail alertów w domenie zarządzanej usług Azure AD DS.
* Dzienniki inspekcji przy użyciu usługi Azure Monitor.
* Integracja z usługą Azure Files
* Integracja z hd insights

Domeny zarządzane usługi Azure AD DS korzystające z sieci wirtualnej Usługi Resource Manager pomagają być na bieżąco z najnowszymi nowymi funkcjami. Obsługa usług Azure AD DS przy użyciu klasycznych sieci wirtualnych ma być przestarzała w przyszłości.

## <a name="example-scenarios-for-migration"></a>Przykładowe scenariusze migracji

Niektóre typowe scenariusze migracji domeny zarządzanej usług Azure AD DS obejmują następujące przykłady.

> [!NOTE]
> Nie konwertuj klasycznej sieci wirtualnej, dopóki nie potwierdzę pomyślnej migracji. Konwertowanie sieci wirtualnej powoduje usunięcie opcji wycofania lub przywrócenia domeny zarządzanej usług Azure AD DS w przypadku jakichkolwiek problemów podczas etapów migracji i weryfikacji.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrowanie usług Azure AD DS do istniejącej sieci wirtualnej Usługi Resource Manager (zalecane)

Typowym scenariuszem jest, gdy już przeniesione inne istniejące zasoby klasyczne do modelu wdrażania Menedżera zasobów i sieci wirtualnej. Komunikacja równorzędna jest następnie używana z sieci wirtualnej Menedżera zasobów do klasycznej sieci wirtualnej, która kontynuuje uruchamianie usług Azure AD DS. Takie podejście umożliwia aplikacjom i usługom Resource Manager korzystanie z funkcji uwierzytelniania i zarządzania domeny zarządzanej usługą Azure AD DS w klasycznej sieci wirtualnej. Po migracji wszystkie zasoby są uruchamiane przy użyciu modelu wdrażania Menedżera zasobów i sieci wirtualnej.

![Migrowanie usług Azure AD DS do istniejącej sieci wirtualnej usługi Resource Manager](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Kroki wysokiego poziomu związane z tym przykładowym scenariuszem migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub komunikację równorzędną sieci wirtualnej skonfigurowaną w klasycznej sieci wirtualnej.
1. Migrowanie domeny zarządzanej usług Azure AD DS przy użyciu kroków opisanych w tym artykule.
1. Przetestuj i potwierdź pomyślną migrację, a następnie usuń klasyczną sieć wirtualną.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrowanie wielu zasobów, w tym usług Azure AD DS

W tym przykładzie można przeprowadzić migrację usług Azure AD DS i innych skojarzonych zasobów z klasycznego modelu wdrażania do modelu wdrażania Menedżera zasobów. Jeśli niektóre zasoby nadal działać w klasycznej sieci wirtualnej obok domeny zarządzanej usługi Azure AD DS, wszystkie one mogą korzystać z migracji do modelu wdrażania Usługi Resource Manager.

![Migrowanie wielu zasobów do modelu wdrażania Menedżera zasobów](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Kroki wysokiego poziomu związane z tym przykładowym scenariuszem migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub komunikację równorzędną sieci wirtualnej skonfigurowaną w klasycznej sieci wirtualnej.
1. Migrowanie domeny zarządzanej usług Azure AD DS przy użyciu kroków opisanych w tym artykule.
1. Konfigurowanie komunikacji równorzędnej sieci wirtualnej między siecią klasyczną a siecią Menedżera zasobów.
1. Przetestuj i potwierdź pomyślną migrację.
1. [Przenieś dodatkowe zasoby klasyczne, takie jak maszyny wirtualne][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrowanie usług Azure AD DS, ale przechowywanie innych zasobów w klasycznej sieci wirtualnej

W tym przykładzie scenariusza masz minimalną ilość przestojów w jednej sesji. Migracja usług Azure AD DS tylko do sieci wirtualnej Usługi Resource Manager i zachować istniejące zasoby w modelu wdrażania klasycznego i sieci wirtualnej. W następującym okresie konserwacji można przeprowadzić migrację dodatkowych zasobów z klasycznego modelu wdrażania i sieci wirtualnej zgodnie z potrzebami.

![Migrowanie tylko usług Azure AD DS do modelu wdrażania usługi Resource Manager](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Kroki wysokiego poziomu związane z tym przykładowym scenariuszem migracji obejmują następujące części:

1. Usuń istniejące bramy sieci VPN lub komunikację równorzędną sieci wirtualnej skonfigurowaną w klasycznej sieci wirtualnej.
1. Migrowanie domeny zarządzanej usług Azure AD DS przy użyciu kroków opisanych w tym artykule.
1. Skonfiguruj komunikację równorzędną sieci wirtualnej między klasyczną siecią wirtualną a nową siecią wirtualną Menedżera zasobów.
1. Później należy w razie potrzeby [przeprowadzić migrację dodatkowych zasobów][migrate-iaas] z klasycznej sieci wirtualnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Podczas przygotowywania, a następnie migracji domeny zarządzanej usług Azure AD DS, istnieją pewne zagadnienia dotyczące dostępności usług uwierzytelniania i zarządzania. Domena zarządzana usługą Azure AD DS jest niedostępna przez pewien czas podczas migracji. Aplikacje i usługi, które opierają się na usługach Azure AD DS doświadczenie przestojów podczas migracji.

> [!IMPORTANT]
> Przeczytaj cały ten artykuł migracji i wskazówki przed rozpoczęciem procesu migracji. Proces migracji wpływa na dostępność kontrolerów domeny usług Azure AD DS przez okres czasu. Użytkownicy, usługi i aplikacje nie mogą uwierzytelniać się względem domeny zarządzanej podczas procesu migracji.

### <a name="ip-addresses"></a>Adresy IP

Adresy IP kontrolera domeny dla domeny zarządzanej usług Azure AD DS zmieniają się po migracji. Ta zmiana obejmuje publiczny adres IP dla bezpiecznego punktu końcowego LDAP. Nowe adresy IP znajdują się w zakresie adresów nowej podsieci w sieci wirtualnej Menedżera zasobów.

W przypadku wycofywania adresy IP mogą ulec zmianie po wycofaniu.

Usługi Azure AD DS zazwyczaj używa pierwszych dwóch dostępnych adresów IP w zakresie adresów, ale nie jest to gwarantowane. Obecnie nie można określić adresów IP, które mają być używane po migracji.

### <a name="downtime"></a>Downtime (Przestoje)

Proces migracji obejmuje kontrolery domeny w trybie offline przez pewien czas. Kontrolery domeny są niedostępne, gdy usługi Azure AD DS są migrowane do modelu wdrażania usługi Resource Manager i sieci wirtualnej. Średnio czas przestoju wynosi około 1 do 3 godzin. Okres ten trwa od momentu przesunięciu kontrolerów domeny w tryb offline do momentu, gdy pierwszy kontroler domeny powróci do trybu online. Ta średnia nie obejmuje czasu, jaki zajmuje replikowanie drugiego kontrolera domeny ani czasu, jaki może zająć migracja dodatkowych zasobów do modelu wdrażania Menedżera zasobów.

### <a name="account-lockout"></a>Blokada konta

Domeny zarządzane usługi Azure AD DS, które są uruchamiane w klasycznych sieciach wirtualnych, nie mają zasad blokady konta usługi AD. Jeśli maszyny wirtualne są narażone na działanie Internetu, osoby atakujące mogą używać metod rozpylania haseł, aby wymusić drogę do kont. Nie ma zasad blokady konta, aby zatrzymać te próby. W przypadku domen zarządzanych usług Azure AD DS korzystających z modelu wdrażania usługi Resource Manager i sieci wirtualnych zasady blokady konta usługi AD chronią przed tymi atakami z użyciem sprayu hasłem.

Domyślnie 5 złych prób hasła w ciągu 2 minut blokuje konto na 30 minut.

Zablokowane konto nie może służyć do logowania, co może zakłócać możliwość zarządzania domeną zarządzaną usługą Azure AD DS lub aplikacjami zarządzanymi przez konto. Po migracji domeny zarządzanej usług Ad DS usługi Azure AD DS konta mogą wystąpić, co wydaje się stałą blokadą z powodu powtarzających się nieudanych prób logowania. Dwa typowe scenariusze po migracji są następujące:

* Konto usługi, które używa wygasłego hasła.
    * Konto usługi wielokrotnie próbuje zalogować się przy za pomocą wygasłego hasła, które blokuje konto. Aby rozwiązać ten problem, zlokalizuj aplikację lub maszynę wirtualną z wygasłymi poświadczeniami i zaktualizuj hasło.
* Złośliwa jednostka używa prób wymuszenia użycia, aby zalogować się na kontach.
    * Gdy maszyny wirtualne są narażone na działanie Internetu, osoby atakujące często próbują typowych kombinacji nazwy użytkownika i hasła podczas próby podpisania. Te powtarzające się nieudane próby logowania można zablokować konta. Nie zaleca się używania kont administratorów o nazwach ogólnych, takich jak *administrator* lub *administrator,* na przykład w celu zminimalizowania blokowania kont administracyjnych.
    * Zminimalizuj liczbę maszyn wirtualnych, które są udostępniane w Internecie. Za pomocą [usługi Azure Bastion][azure-bastion] można bezpiecznie łączyć się z maszynami wirtualnymi przy użyciu witryny Azure portal.

Jeśli podejrzewasz, że niektóre konta mogą zostać zablokowane po migracji, końcowe kroki migracji określają sposób włączania inspekcji lub zmiany ustawień zasad haseł.

### <a name="roll-back-and-restore"></a>Cofanie i przywracanie

Jeśli migracja nie powiedzie się, istnieje proces wycofywania lub przywracania domeny zarządzanej usług Azure AD DS. Wycofywanie jest opcja samoobsługi, aby natychmiast zwrócić stan domeny zarządzanej do przed próbą migracji. Inżynierowie pomocy technicznej platformy Azure mogą również przywrócić domenę zarządzaną z kopii zapasowej w ostateczności. Aby uzyskać więcej informacji, zobacz [jak wycofać lub przywrócić migrację po awarii](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Ograniczenia dotyczące dostępnych sieci wirtualnych

Istnieją pewne ograniczenia dotyczące sieci wirtualnych, do których można przeprowadzić migrację domeny zarządzanej usług Ad DS. Docelowa sieć wirtualna Menedżera zasobów musi spełniać następujące wymagania:

* Sieć wirtualna Usługi Resource Manager musi znajdować się w tej samej subskrypcji platformy Azure, co klasyczna sieć wirtualna, w których obecnie wdrażana jest usługa Azure AD DS.
* Sieć wirtualna Menedżera zasobów musi znajdować się w tym samym regionie co klasyczna sieć wirtualna, w których obecnie wdrażana jest usługa Azure AD DS.
* Podsieć sieci wirtualnej Menedżera zasobów powinna mieć co najmniej 3-5 dostępnych adresów IP.
* Podsieć sieci wirtualnej Usługi Resource Manager powinna być dedykowaną podsiecią dla usług Azure AD DS i nie powinna obsługiwać żadnych innych obciążeń.

Aby uzyskać więcej informacji na temat wymagań dotyczących sieci [wirtualnej, zobacz Zagadnienia dotyczące projektowania sieci wirtualnej i opcje konfiguracji][network-considerations].

## <a name="migration-steps"></a>Kroki migracji

Migracja do modelu wdrażania Menedżera zasobów i sieci wirtualnej jest podzielona na 5 głównych kroków:

| Krok    | Wykonywane przez  | Szacowany czas  | Downtime (Przestoje)  | Wycofać/przywrócić? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Krok 1 - Aktualizacja i zlokalizowanie nowej sieci wirtualnej](#update-and-verify-virtual-network-settings) | Portal Azure | 15 minut | Nie są wymagane przestoje | Nie dotyczy |
| [Krok 2 — przygotowanie domeny zarządzanej usług Ad DS do migracji](#prepare-the-managed-domain-for-migration) | PowerShell | Średnio 15 – 30 minut | Przestoje usług Azure AD DS rozpoczyna się po zakończeniu tego polecenia. | Wycofaj i przywróć dostępne. |
| [Krok 3 — Przenoszenie domeny zarządzanej usług Azure AD DS do istniejącej sieci wirtualnej](#migrate-the-managed-domain) | PowerShell | Średnio 1 – 3 godziny | Jeden kontroler domeny jest dostępny po zakończeniu tego polecenia, czas przestoju kończy się. | W przypadku awarii dostępne są zarówno wycofywanie (samoobsługa), jak i przywracanie. |
| [Krok 4 - Testowanie i oczekiwanie na replikę kontrolera domeny](#test-and-verify-connectivity-after-the-migration)| Program PowerShell i witryna Azure portal | 1 godzina lub więcej, w zależności od liczby testów | Oba kontrolery domeny są dostępne i powinny działać normalnie. | Nie dotyczy. Po pomyślnej migracji pierwszej maszyny Wirtualnej nie ma opcji wycofywania lub przywracania. |
| [Krok 5 - Opcjonalne kroki konfiguracji](#optional-post-migration-configuration-steps) | Portal Azure i maszyny wirtualne | Nie dotyczy | Nie są wymagane przestoje | Nie dotyczy |

> [!IMPORTANT]
> Aby uniknąć dodatkowych przestojów, przeczytaj cały ten artykuł migracji i wskazówki przed rozpoczęciem procesu migracji. Proces migracji wpływa na dostępność kontrolerów domeny usług Azure AD DS przez pewien czas. Użytkownicy, usługi i aplikacje nie mogą uwierzytelniać się względem domeny zarządzanej podczas procesu migracji.

## <a name="update-and-verify-virtual-network-settings"></a>Aktualizowanie i weryfikowanie ustawień sieci wirtualnej

Przed rozpoczęciem procesu migracji należy wykonać następujące początkowe kontrole i aktualizacje. Te kroki mogą się zdarzyć w dowolnym momencie przed migracją i nie mają wpływu na działanie domeny zarządzanej usług Azure AD DS.

1. Zaktualizuj lokalne środowisko programu Azure PowerShell do najnowszej wersji. Aby wykonać kroki migracji, potrzebujesz co najmniej wersji *2.3.2*.

    Aby uzyskać informacje dotyczące sprawdzania i aktualizowania wersji programu PowerShell, zobacz [omówienie programu Azure PowerShell][azure-powershell].

1. Utwórz lub wybierz istniejącą sieć wirtualną Menedżera zasobów.

    Upewnij się, że ustawienia sieciowe nie blokują niezbędnych portów wymaganych dla usług Azure AD DS. Porty muszą być otwarte zarówno w klasycznej sieci wirtualnej, jak i w sieci wirtualnej Menedżera zasobów. Ustawienia te obejmują tabele tras (chociaż nie zaleca się używania tabel tras) i sieciowe grupy zabezpieczeń.

    Aby wyświetlić wymagane porty, zobacz [Sieciowe grupy zabezpieczeń i wymagane porty][network-ports]. Aby zminimalizować problemy z komunikacją sieciową, zaleca się odczekanie i stosowanie tabeli zabezpieczeń sieci lub marszruty do sieci wirtualnej Menedżera zasobów po pomyślnym zakończeniu migracji.

    Zanotuj tę docelową grupę zasobów, docelową sieć wirtualną i docelową podsieć sieci wirtualnej. Te nazwy zasobów są używane podczas procesu migracji.

1. Sprawdź kondycję domeny zarządzanej usług Azure AD DS w witrynie Azure portal. Jeśli masz jakieś alerty dla domeny zarządzanej, rozwiąż je przed rozpoczęciem procesu migracji.
1. Opcjonalnie, jeśli planujesz przenieść inne zasoby do modelu wdrażania Menedżera zasobów i sieci wirtualnej, upewnij się, że te zasoby mogą być migrowane. Aby uzyskać więcej informacji, zobacz [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do Menedżera zasobów][migrate-iaas].

    > [!NOTE]
    > Nie konwertuj klasycznej sieci wirtualnej na sieć wirtualną Menedżera zasobów. Jeśli to zrobisz, nie ma opcji, aby wycofać lub przywrócić domenę zarządzaną usługą Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Przygotowanie domeny zarządzanej do migracji

Program Azure PowerShell służy do przygotowania domeny zarządzanej usług Azure AD DS do migracji. Te kroki obejmują wykonywanie kopii zapasowej, wstrzymywanie synchronizacji i usuwanie usługi w chmurze obsługującej usługi Azure AD DS. Po zakończeniu tego kroku usługi Azure AD DS jest przesuń w tryb offline na okres czasu. Jeśli krok przygotowania nie powiedzie się, można [przywrócić do poprzedniego stanu](#roll-back).

Aby przygotować domenę zarządzana usługą Azure AD DS do migracji, wykonaj następujące kroki:

1. Zainstaluj `Migrate-Aaads` skrypt z [Galerii programu PowerShell][powershell-script]. Ten skrypt migracji programu PowerShell jest podpisany cyfrowo przez zespół inżynierów usługi Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Utwórz zmienną do przechowywania poświadczeń dla skryptu migracji przy użyciu polecenia cmdlet [Get-Credential.][get-credential]

    Konto użytkownika, które określisz, wymaga uprawnień *administratora globalnego* w dzierżawie usługi Azure AD, aby włączyć usługi Azure AD DS, a następnie uprawnienia *współautora* w subskrypcji platformy Azure, aby utworzyć wymagane zasoby usługi Azure AD DS.

    Po wyświetleniu monitu wprowadź odpowiednie konto użytkownika i hasło:

    ```powershell
    $creds = Get-Credential
    ```

1. Teraz uruchom `Migrate-Aadds` polecenie cmdlet przy użyciu parametru *-Prepare.* Podaj *-ManagedDomainFqdn* dla własnej domeny zarządzanej usług Azure AD DS, takiej jak *aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrowanie domeny zarządzanej

Po przygotowaniu i utworzeniu kopii zapasowej domeny usług Ad DS można przeprowadzić migrację. Ten krok odtwarza maszyny wirtualne kontrolera domeny usług domenowych usługi Azure AD przy użyciu modelu wdrażania Menedżera zasobów. Ten krok może potrwać od 1 do 3 godzin.

Uruchom `Migrate-Aadds` polecenie cmdlet przy użyciu parametru *-Commit.* Podaj *-ManagedDomainFqdn* dla własnej domeny zarządzanej usługi Azure AD DS przygotowanej w poprzedniej sekcji, na przykład *aaddscontoso.com:*

Określ docelową grupę zasobów zawierającą sieć wirtualną, do której chcesz przeprowadzić migrację usług Azure AD DS, na przykład *myResourceGroup*. Podaj docelową sieć wirtualną, taką jak *myVnet,* i podsieć, taką jak *DomainServices*.

Po uruchomieniu tego polecenia nie można wycofać:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Po sprawdzeniu poprawności domeny zarządzanej do migracji wprowadź *Y,* aby rozpocząć proces migracji.

> [!IMPORTANT]
> Nie konwertuj klasycznej sieci wirtualnej na sieć wirtualną Menedżera zasobów podczas procesu migracji. Jeśli konwertujesz sieć wirtualną, nie można następnie wycofać ani przywrócić domeny zarządzanej usługi Azure AD DS, ponieważ oryginalna sieć wirtualna już nie istnieje.

Co dwie minuty podczas procesu migracji wskaźnik postępu zgłasza bieżący stan, jak pokazano w poniższym przykładzie danych wyjściowych:

![Wskaźnik postępu migracji usług Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Proces migracji jest nadal uruchamiany, nawet po zamknięciu skryptu programu PowerShell. W witrynie Azure portal stan domeny zarządzanej zgłasza się jako *Migrowanie*.

Po pomyślnym zakończeniu migracji można wyświetlić adres IP pierwszego kontrolera domeny w witrynie Azure portal lub za pośrednictwem programu Azure PowerShell. Pokazano również oszacowanie czasu na drugi dostępny kontroler domeny.

Na tym etapie można opcjonalnie przenieść inne istniejące zasoby z klasycznego modelu wdrażania i sieci wirtualnej. Lub można zachować zasoby w klasycznym modelu wdrażania i równorzędnych sieci wirtualnych do siebie po zakończeniu migracji usług AD DS.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testowanie i weryfikowanie łączności po migracji

Pomyślne wdrożenie drugiego kontrolera domeny może zająć trochę czasu i będzie dostępne do użycia w domenie zarządzanej usług Azure AD DS.

W modelu wdrażania usługi Resource Manager zasoby sieciowe dla domeny zarządzanej usługi Azure AD DS są wyświetlane w witrynie Azure portal lub usłudze Azure PowerShell. Aby dowiedzieć się więcej o tym, czym są i do których służą te zasoby sieciowe, zobacz [Zasoby sieciowe używane przez usługi Azure AD DS][network-resources].

Jeśli dostępny jest co najmniej jeden kontroler domeny, wykonaj następujące kroki konfiguracji łączności sieciowej z maszynami wirtualnymi:

* **Aktualizowanie ustawień serwera DNS** Aby umożliwić innym zasobom w sieci wirtualnej Menedżera zasobów rozpoznawanie i używanie domeny zarządzanej usług Azure AD DS, należy zaktualizować ustawienia DNS przy użyciu adresów IP nowych kontrolerów domeny. Portal Azure może automatycznie skonfigurować te ustawienia dla Ciebie. Aby dowiedzieć się więcej o konfigurowaniu sieci wirtualnej Menedżera zasobów, zobacz [Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure][update-dns].
* **Uruchom ponownie maszyny wirtualne przyłączone do domeny** — w miarę zmiany adresów IP serwera DNS kontrolerów domeny usług Ad DS uruchom ponownie wszystkie maszyny wirtualne przyłączone do domeny, aby następnie użyć nowych ustawień serwera DNS. Jeśli aplikacje lub maszyny wirtualne mają ręcznie skonfigurowane ustawienia DNS, ręcznie zaktualizuj je za pomocą nowych adresów IP serwera DNS kontrolerów domeny wyświetlanych w witrynie Azure portal.

Teraz przetestuj połączenie sieci wirtualnej i rozpoznawanie nazw. Na maszynie wirtualnej, która jest podłączona do sieci wirtualnej Menedżera zasobów lub w równorzędnie, spróbuj wykonać następujące testy komunikacji sieciowej:

1. Sprawdź, czy możesz pingować adres IP jednego z kontrolerów domeny, np.`ping 10.1.0.4`
    * Adresy IP kontrolerów domeny są wyświetlane na stronie **Właściwości** domeny zarządzanej usług Azure AD DS w witrynie Azure portal.
1. Sprawdź rozpoznawanie nazw domeny zarządzanej, na przykład`nslookup aaddscontoso.com`
    * Określ nazwę DNS własnej domeny zarządzanej usługi Azure AD DS, aby sprawdzić, czy ustawienia DNS są poprawne i zostaną rozwiązane.

Drugi kontroler domeny powinien być dostępny 1-2 godziny po zakończeniu polecenia cmdlet migracji. Aby sprawdzić, czy drugi kontroler domeny jest dostępny, zajrzyj na stronę **Właściwości** domeny zarządzanej usług Azure AD DS w witrynie Azure portal. Jeśli pokazane są dwa adresy IP, drugi kontroler domeny jest gotowy.

## <a name="optional-post-migration-configuration-steps"></a>Opcjonalne kroki konfiguracji po migracji

Po pomyślnym zakończeniu procesu migracji niektóre opcjonalne kroki konfiguracji obejmują włączenie dzienników inspekcji lub powiadomień e-mail lub zaktualizowanie zasad haseł ziarnistych.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Subskrybuj dzienniki inspekcji przy użyciu usługi Azure Monitor

Usługi Azure AD DS udostępnia dzienniki inspekcji, aby ułatwić rozwiązywanie problemów i wyświetlanie zdarzeń na kontrolerach domeny. Aby uzyskać więcej informacji, zobacz [Włączanie i używanie dzienników inspekcji][security-audits].

Szablony służy do monitorowania ważnych informacji ujawnionych w dziennikach. Na przykład szablon skoroszytu dziennika inspekcji może monitorować możliwe blokady konta w domenie zarządzanej usług Azure AD DS.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Konfigurowanie powiadomień e-mail usług domenowych usługi Azure AD

Aby otrzymywać powiadomienia o wykryciu problemu w domenie zarządzanej usług Azure AD DS, zaktualizuj ustawienia powiadomień e-mail w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień powiadomień][notifications].

### <a name="update-fine-grained-password-policy"></a>Aktualizowanie zasad haseł precyzyjnych

W razie potrzeby można zaktualizować zasady haseł szczegółowe, aby były mniej restrykcyjne niż konfiguracja domyślna. Dzienniki inspekcji można użyć, aby ustalić, czy mniej restrykcyjne ustawienie ma sens, a następnie skonfigurować zasady w razie potrzeby. Aby przejrzeć i zaktualizować ustawienia zasad dla kont, które są wielokrotnie blokowane po migracji, należy wykonać następujące kroki wysokiego poziomu:

1. [Skonfiguruj zasady haseł][password-policy] dla mniejszej liczby ograniczeń w domenie zarządzanej usług Azure AD DS i obserwuj zdarzenia w dziennikach inspekcji.
1. Jeśli dowolne konta usługi używają wygasłych haseł zidentyfikowanych w dziennikach inspekcji, zaktualizuj te konta za pomocą poprawnego hasła.
1. Jeśli maszyna wirtualna jest narażona na działanie Internetu, przejrzyj nazwy kont ogólnych, takich jak *administrator,* *użytkownik*lub *gość* z próbami wysokiego logowania. Jeśli to możliwe, zaktualizuj te maszyny wirtualne, aby używały mniej ogólnie nazwanych kont.
1. Użyj śledzenia sieci na maszynie Wirtualnej, aby zlokalizować źródło ataków i zablokować te adresy IP przed próbą logowania.
1. W przypadku minimalnych problemów z blokadą należy zaktualizować zasady haseł szczegółowe, aby były tak restrykcyjne, jak to konieczne.

### <a name="creating-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Usługi Azure AD DS potrzebuje sieciowej grupy zabezpieczeń, aby zabezpieczyć porty potrzebne do domeny zarządzanej i zablokować cały inny ruch przychodzący. Ta sieciowa grupa zabezpieczeń działa jako dodatkowa warstwa ochrony, aby zablokować dostęp do domeny zarządzanej i nie jest tworzona automatycznie. Aby utworzyć sieciową grupę zabezpieczeń i otworzyć wymagane porty, przejrzyj następujące kroki:

1. W witrynie Azure portal wybierz zasób usług Azure AD DS. Na stronie przegląd jest wyświetlany przycisk do tworzenia sieciowej grupy zabezpieczeń, jeśli nie ma żadnego skojarzonego z usługami domenowymi usługi Azure AD.
1. Jeśli używasz bezpiecznego protokołu LDAP, dodaj regułę do sieciowej grupy zabezpieczeń, aby zezwolić na ruch przychodzący dla portu *TCP* *636*. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Wycofywanie i przywracanie z migracji

Do pewnego momentu w procesie migracji można wycofać lub przywrócić domenę zarządzaną usługą Azure AD DS.

### <a name="roll-back"></a>Wycofać

Jeśli wystąpi błąd po uruchomieniu polecenia cmdlet programu PowerShell, aby przygotować się do migracji w kroku 2 lub dla samej migracji w kroku 3, domena zarządzana usługą Azure AD DS może przywrócić oryginalną konfigurację. To wycofanie wymaga oryginalnej sieci wirtualnej Classic. Należy zauważyć, że adresy IP mogą ulec zmianie po wycofywaniu.

Uruchom `Migrate-Aadds` polecenie cmdlet przy użyciu parametru *-Abort.* Podaj *-ManagedDomainFqdn* dla własnej domeny zarządzanej usługi Azure AD DS przygotowanej w poprzedniej sekcji, takiej jak *aaddscontoso.com,* oraz klasyczna nazwa sieci wirtualnej, na przykład *myClassicVnet:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Przywracanie

W ostateczności usługi domenowe usługi azure ad można przywrócić z ostatniej dostępnej kopii zapasowej. Kopia zapasowa jest podejmowana w kroku 1 migracji, aby upewnić się, że najnowsza kopia zapasowa jest dostępna. Ta kopia zapasowa jest przechowywana przez 30 dni.

Aby przywrócić domenę zarządzaną usługą Azure AD DS z kopii zapasowej, [otwórz zgłoszenie sprawy pomocy technicznej za pomocą witryny Azure portal][azure-support]. Podaj identyfikator katalogu, nazwę domeny i powód przywracania. Proces pomocy technicznej i przywracania może potrwać wiele dni.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy po migracji do modelu wdrażania Menedżera zasobów, przejrzyj niektóre z następujących typowych obszarów rozwiązywania problemów:

* [Rozwiązywanie problemów z przyłączaniem do domeny][troubleshoot-domain-join]
* [Rozwiązywanie problemów z blokadą konta][troubleshoot-account-lockout]
* [Rozwiązywanie problemów z logowaniem do konta][troubleshoot-sign-in]
* [Rozwiązywanie problemów z bezpieczną łącznością LDAP][tshoot-ldaps]

## <a name="next-steps"></a>Następne kroki

Po migracji domeny zarządzanej usług Azure AD DS do modelu wdrażania Menedżera zasobów [utwórz maszynę wirtualną systemu Windows i dołącz do domeny do domeny,][join-windows] a następnie [zainstaluj narzędzia do zarządzania][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
