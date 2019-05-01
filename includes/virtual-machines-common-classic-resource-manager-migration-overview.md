---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: f60b5421f2bc66cf09ede4178ce18e2394030264
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64929429"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Obsługiwane platformy migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager
W tym artykule opisano sposób migracji infrastruktury jako zasoby usługi (IaaS) z klasycznego modelu wdrażania usługi Resource Manager i szczegółowe informacje, jak połączyć zasoby z dwa modele wdrażania, które współistnieć w subskrypcji przy użyciu sieci wirtualnej bramy lokacja lokacja. Przeczytaj więcej o [funkcje usługi Azure Resource Manager i korzyści](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Cel dla migracji
Resource Manager pozwala wdrażać złożone aplikacje za pomocą szablonów, konfiguruje maszyny wirtualne przy użyciu rozszerzeń maszyn wirtualnych i wprowadza zarządzanie dostępem oraz tagowanie. Usługa Azure Resource Manager obejmuje skalowalne, równoległe wdrażanie dla maszyn wirtualnych w zestawach dostępności. Nowy model wdrożenia zawiera również zarządzanie cyklem życia wystąpień obliczeniowych, sieci i magazynu niezależnie. Na koniec jest szczególnym włączania rozszerzeń zabezpieczeń domyślnie z wymuszaniem maszyn wirtualnych w sieci wirtualnej.

Prawie wszystkie funkcje dostępne w klasycznym modelu wdrażania są obsługiwane zasobów obliczeniowych, sieci i magazynu w ramach usługi Azure Resource Manager. Aby korzystać z nowych funkcji w usłudze Azure Resource Manager, można migrować istniejące wdrożenia z klasycznego modelu wdrażania.

## <a name="supported-resources-for-migration"></a>Zasoby obsługiwane dla migracji
Te klasycznych zasobów IaaS są obsługiwane podczas migracji

* Maszyny wirtualne
* Zestawy dostępności
* Usługi Cloud Services i Virtual Machines
* Konta magazynu
* Sieci wirtualne
* Bramy sieci VPN
* Express Route bram _(w tej samej subskrypcji co sieć wirtualną tylko)_
* Grupy zabezpieczeń sieci
* Tabele tras
* Zastrzeżone adresy IP

## <a name="supported-scopes-of-migration"></a>Obsługiwane zakresy migracji
Istnieją cztery różne sposoby, aby ukończyć migrację zasobów obliczeniowych, sieci i magazynu:

* [Migracja maszyn wirtualnych (nie w sieci wirtualnej)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migracja maszyn wirtualnych (w sieci wirtualnej)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migracja kont magazynu](#migration-of-storage-accounts)
* [Migracja zasobów niedołączone](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migracja maszyn wirtualnych (nie w sieci wirtualnej)
W modelu wdrażania usługi Resource Manager zabezpieczenia są domyślnie wymuszane dla aplikacji. Wszystkie maszyny wirtualne muszą znajdować się w sieci wirtualnej w modelu usługi Resource Manager. Ponowne uruchomienie platformy Azure (`Stop`, `Deallocate`, i `Start`) maszyn wirtualnych w ramach migracji. Masz dwie opcje dla sieci wirtualnych, które maszyny wirtualne, które będą migrowane do:

* Możesz poprosić platformy, Utwórz nową sieć wirtualną i przeprowadzić migrację maszyny wirtualnej, do nowej sieci wirtualnej.
* Należy przeprowadzić migrację maszyny wirtualnej w istniejącej sieci wirtualnej w usłudze Resource Manager.

> [!NOTE]
> W tym zakresie migracji operacje płaszczyzny zarządzania i operacje płaszczyzny danych może być zablokowany przez okres czasu, podczas migracji.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migracja maszyn wirtualnych (w sieci wirtualnej)
W przypadku większości konfiguracji maszyny Wirtualnej trafiają tam jedynie metadane jest Migrowanie między modelami wdrażania klasycznego i usługi Resource Manager. Podstawowych maszyn wirtualnych są uruchomione na tym samym sprzęcie, w tej samej sieci, a za pomocą tego samego magazynu. Operacje płaszczyzny zarządzania może być zablokowany przez pewien czas podczas migracji. Jednak płaszczyzna danych nadal działa. Oznacza to Twoich aplikacji uruchomionych na maszyny wirtualne (klasyczne) nie są naliczane przestój podczas migracji.

Następujące konfiguracje nie są obecnie obsługiwane. Jeśli zostanie dodana jego obsługa w przyszłości niektóre maszyny wirtualne w tej konfiguracji może pociągnąć za sobą przestojów (Przejdź przez wyłącznik, cofnięcie przydziału i ponownego uruchamiania działania maszyny Wirtualnej).

* Masz więcej niż jeden zestaw dostępności w usługa w chmurze.
* Masz jeden lub więcej zestawów dostępności, jak i maszyny wirtualne, które nie znajdują się w zestaw dostępności w usługa w chmurze.

> [!NOTE]
> W tym zakresie migracji płaszczyzny zarządzania może być zablokowany przez okres czasu, podczas migracji. W przypadku niektórych konfiguracji opisanych wcześniej występuje przestój płaszczyzny danych.
>

### <a name="migration-of-storage-accounts"></a>Migracja kont magazynu
Umożliwienie płynnej migracji, można wdrożyć maszyny wirtualne usługi Resource Manager w klasycznego konta magazynu. Dzięki tej możliwości obliczeniowych i zasobów sieciowych można i powinny być migrowane niezależnie od konta magazynu. Po migrację maszyn wirtualnych i sieci wirtualnej, należy wykonać migrację konta magazynu, aby ukończyć proces migracji.

Jeśli konto magazynu nie ma żadnych skojarzone dyski lub dane maszyn wirtualnych i zawiera tylko obiekty BLOB, pliki, tabele i kolejki migracji do usługi Azure Resource Manager można zrobić jako migracja autonomiczna bez zależności.

> [!NOTE]
> Model wdrażania usługi Resource Manager nie ma koncepcji Classic obrazów i dysków. Gdy konto magazynu jest migrowane, klasyczne obrazów i dysków nie są widoczne w stosie usługi Resource Manager, ale zapasowy wirtualne dyski twarde pozostaną na koncie magazynu.

Poniższych zrzutach ekranu przedstawiono sposób uaktualniania klasyczne konta magazynu do konta magazynu usługi Azure Resource Manager przy użyciu witryny Azure portal:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do swojego konta magazynu.
3. W **ustawienia** kliknij **Przeprowadź migrację do ARM**.
4. Kliknij pozycję **weryfikacji** oceniać możliwości mieszania migracji.
5. Jeśli weryfikacja zakończy się pomyślnie, kliknij polecenie **przygotowanie** do utworzenia konta magazynu zmigrowane.
6. Typ **tak** potwierdzić migrację, a następnie kliknij przycisk **zatwierdzić** na zakończenie migracji.

    ![Sprawdzanie poprawności konta magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Przygotowywanie konta magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Zakończ migrację konta magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migracja zasobów niedołączone
Konta magazynu bez skojarzone dyski i maszyny wirtualne, dane można migrować niezależnie.

Sieciowe grupy zabezpieczeń, tabele tras i zastrzeżone adresy IP, które nie są dołączone do wszystkich maszyn wirtualnych i sieci wirtualne można również można migrować niezależnie.

<br>

## <a name="unsupported-features-and-configurations"></a>Nieobsługiwane funkcje i konfiguracji
Niektóre funkcje i konfiguracje nie są obecnie obsługiwane; w poniższych sekcjach opisano Nasze zalecenia dotyczące, wokół nich.

### <a name="unsupported-features"></a>Nieobsługiwane funkcje
Następujące funkcje nie są obecnie obsługiwane. Możesz opcjonalnie usunąć te ustawienia, migracji maszyn wirtualnych i ponownie włączyć ustawienia w modelu wdrażania usługi Resource Manager.

| Dostawca zasobów | Cecha | Zalecenie |
| --- | --- | --- |
| Wystąpienia obliczeniowe | Disks nieskojarzone maszyny wirtualnej. | Obiekty BLOB dysków VHD za te dyski będą migrowani, po zakończeniu migracji konta magazynu |
| Wystąpienia obliczeniowe | Obrazy maszyn wirtualnych. | Obiekty BLOB dysków VHD za te dyski będą migrowani, po zakończeniu migracji konta magazynu |
| Sieć | Listy ACL punktu końcowego. | Usuń listy ACL punktu końcowego, a następnie ponów próbę migracji. |
| Sieć | Application Gateway | Usuwanie bramy aplikacji przed rozpoczęciem migracji, a następnie ponownie bramę Application Gateway, po ukończeniu migracji. |
| Sieć | Sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych. | Migracja sieci wirtualnej do usługi Resource Manager, a następnie komunikacji równorzędnej. Dowiedz się więcej o [komunikacja równorzędna sieci wirtualnych](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nieobsługiwane konfiguracje
Następujące konfiguracje nie są obecnie obsługiwane.

| Usługa | Konfigurowanie | Zalecenie |
| --- | --- | --- |
| Resource Manager |Oparta na rolach kontrola dostępu (RBAC) dla zasobów klasycznych |Ponieważ identyfikator URI zasobów został zmodyfikowany po migracji, zaleca się zaplanowanie aktualizacji zasad RBAC, które należy wykonać po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Wiele podsieci skojarzonych z maszyną Wirtualną |Zaktualizuj konfigurację podsieci, aby odwoływać się tylko w jednej podsieci. Może to wymagać usunięcia pomocniczej karty Sieciowej (do którego odwołuje się do innej podsieci) z maszyny Wirtualnej i dołączyć go ponownie po ukończeniu migracji. |
| Wystąpienia obliczeniowe |Maszyny wirtualne, które należą do sieci wirtualnej, ale nie mają jawnych podsieci przypisane |Opcjonalnie można usunąć maszyny Wirtualnej. |
| Wystąpienia obliczeniowe |Maszyny wirtualne, które mają alerty, zasady automatycznego skalowania |Migracja przechodzi przez, a te ustawienia są usuwane. Zdecydowanie zaleca się, oceń swoje środowisko, przed wykonaniem migracji. Alternatywnie po zakończeniu migracji można ponownie skonfigurować ustawienia alertów. |
| Wystąpienia obliczeniowe |Rozszerzenia maszyny Wirtualnej XML (BGInfo 1.*, debuger programu Visual Studio, narzędzia Web Deploy i zdalne debugowanie) |Jest to nieobsługiwane. Zalecane jest, aby usunąć te rozszerzenia z maszyny wirtualnej, aby kontynuować migrację, lub zostaną usunięte automatycznie podczas procesu migracji. |
| Wystąpienia obliczeniowe |Diagnostyka rozruchu dzięki usłudze Premium storage |Wyłącz funkcję diagnostyki rozruchu dla maszyn wirtualnych przed kontynuowaniem migracji. Po zakończeniu migracji, można ponownie włączyć diagnostykę rozruchu w stosie usługi Resource Manager. Ponadto obiekty BLOB, które są używane dla dzienników szeregowej i zrzutu ekranu powinny zostać usunięte, więc nie są już naliczane opłaty dla tych obiektów blob. |
| Wystąpienia obliczeniowe | Usługi w chmurze, które zawierają ról sieć web/proces roboczy | To nie jest obecnie obsługiwane. |
| Wystąpienia obliczeniowe | Usługi w chmurze, które zawierają więcej niż jeden dostępności zestawu lub zestawów dostępności wielu. |To nie jest obecnie obsługiwane. Przenieś maszyny wirtualne do tego samego zestawu przed migracją dostępności. |
| Wystąpienia obliczeniowe | Maszyny Wirtualnej przy użyciu rozszerzenia usługi Azure Security Center | Usługa Azure Security Center automatycznie instaluje rozszerzenia na maszynach wirtualnych na monitorowanie ich zabezpieczeń i zgłaszające alerty. Rozszerzenia te zwykle uzyskać zainstalowana automatycznie, jeśli zasady usługi Azure Security Center są włączone w ramach subskrypcji. Aby przeprowadzić migrację maszyn wirtualnych, Wyłącz zasady Centrum zabezpieczeń w ramach subskrypcji, co spowoduje usunięcie monitorowania rozszerzenia z maszyn wirtualnych usługi Security Center. |
| Wystąpienia obliczeniowe | Maszyna wirtualna ma rozszerzenie kopii zapasowej lub migawki | Te rozszerzenia są zainstalowane na maszynie wirtualnej skonfigurowanej przy użyciu usługi Azure Backup. Podczas migracji te maszyny wirtualne nie jest obsługiwane, zgodnie z wytycznymi [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) można przechowywać kopie zapasowe, które zostały pobrane przed migracją.  |
| Sieć |Sieci wirtualne, które zawierają maszyny wirtualne oraz role sieci web/procesu roboczego |To nie jest obecnie obsługiwane. Przed migracją Przenieś role sieć Web/proces roboczy do ich własnych sieci wirtualnej. Po zmigrowaniu klasycznej sieci wirtualnej, zmigrowanych Azure Resource Manager Virtual Network można vnetb klasycznej sieci wirtualnej w celu osiągnięcia podobnej konfiguracji, tak jak poprzednio.|
| Sieć | Klasyczne obwodów Expressroute |To nie jest obecnie obsługiwane. Te obwodów należy migrować do usługi Azure Resource Manager przed rozpoczęciem migracji IaaS. Aby dowiedzieć się więcej, zobacz [obwodów przenoszenia usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Sieci wirtualne, które zawierają środowisk usługi App Service |To nie jest obecnie obsługiwane. |
| Azure HDInsight |Sieci wirtualne, które zawierają usługi HDInsight |To nie jest obecnie obsługiwane. |
| Microsoft Dynamics Lifecycle Services |Sieci wirtualne, które zawierają maszyny wirtualne, które są zarządzane przez program Dynamics Lifecycle Services |To nie jest obecnie obsługiwane. |
| Azure AD Domain Services |Sieci wirtualne, które zawierają usług domenowych Azure AD |To nie jest obecnie obsługiwane. |
| Azure RemoteApp |Sieci wirtualne, które zawierają wdrożenia usługi Azure RemoteApp |To nie jest obecnie obsługiwane. |
| Usługa Azure API Management |Sieci wirtualne, które zawierają wdrożenia usługi Azure API Management |To nie jest obecnie obsługiwane. Aby przeprowadzić migrację sieci Wirtualnej IaaS, należy zmienić sieci Wirtualnej, wdrożenia usługi API Management jest operacją nie przestojów. |
