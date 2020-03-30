---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192766"
---
W tym artykule opisano sposób migracji zasobów infrastruktury jako usługi (IaaS) z modeli wdrażania klasycznego do resource managera oraz szczegółowe informacje na temat łączenia zasobów z dwóch modeli wdrażania współistnienia w ramach subskrypcji przy użyciu sieci wirtualnej bramy między lokacjami. Więcej informacji o [funkcjach i korzyściach usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md). 

## <a name="goal-for-migration"></a>Cel migracji
Menedżer zasobów umożliwia wdrażanie złożonych aplikacji za pomocą szablonów, konfiguruje maszyny wirtualne przy użyciu rozszerzeń maszyn wirtualnych i włącza zarządzanie dostępem i tagowanie. Usługa Azure Resource Manager zawiera skalowalne, równoległe wdrażanie maszyn wirtualnych w zestawach dostępności. Nowy model wdrażania zapewnia również niezależne zarządzanie cyklem życia danych obliczeniowych, sieci i pamięci masowej. Na koniec skupiono się na domyślnym włączaniu zabezpieczeń przy wymuszaniu maszyn wirtualnych w sieci wirtualnej.

Prawie wszystkie funkcje z klasycznego modelu wdrażania są obsługiwane dla obliczeń, sieci i magazynu w usłudze Azure Resource Manager. Aby korzystać z nowych możliwości w usłudze Azure Resource Manager, można migrować istniejące wdrożenia z klasycznego modelu wdrażania.

## <a name="supported-resources-for-migration"></a>Obsługiwane zasoby do migracji
Te klasyczne zasoby IaaS są obsługiwane podczas migracji

* Maszyny wirtualne
* Zestawy dostępności
* Konta magazynu
* Sieci wirtualne
* Bramy sieci VPN
* Bramy trasy ekspresowej _(w tej samej subskrypcji co tylko sieć wirtualna)_
* Grupy zabezpieczeń sieci
* Tabele tras
* Zastrzeżone adresy IP

## <a name="supported-scopes-of-migration"></a>Obsługiwane zakresy migracji
Istnieją cztery różne sposoby ukończenia migracji zasobów obliczeniowych, sieciowych i magazynowych:

* [Migracja maszyn wirtualnych (NIE w sieci wirtualnej)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migracja maszyn wirtualnych (w sieci wirtualnej)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migracja kont magazynu](#migration-of-storage-accounts)
* [Migracja nieprzywiązanych zasobów](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migracja maszyn wirtualnych (NIE w sieci wirtualnej)
W modelu wdrażania Menedżera zasobów zabezpieczenia są domyślnie wymuszane dla aplikacji. Wszystkie maszyny wirtualne muszą znajdować się w sieci wirtualnej w modelu Menedżera zasobów. Platforma Azure uruchamia`Stop`ponownie `Deallocate`( `Start`, i ) maszyn wirtualnych w ramach migracji. Dostępne są dwie opcje dla sieci wirtualnych, do których zostaną przeniesione maszyny wirtualne:

* Można zażądać platformy, aby utworzyć nową sieć wirtualną i migracji maszyny wirtualnej do nowej sieci wirtualnej.
* Maszynę wirtualną można przeprowadzić do istniejącej sieci wirtualnej w Menedżerze zasobów.

> [!NOTE]
> W tym zakresie migracji zarówno operacje płaszczyzny zarządzania, jak i operacje płaszczyzny danych mogą być niedozwolone przez pewien czas podczas migracji.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migracja maszyn wirtualnych (w sieci wirtualnej)
W przypadku większości konfiguracji maszyn wirtualnych tylko metadane są migrowane między modelami wdrażania klasycznego i menedżera zasobów. Podstawowe maszyny wirtualne są uruchomione na tym samym sprzęcie, w tej samej sieci i z tym samym magazynem. Operacje płaszczyzny zarządzania mogą być niedozwolone przez pewien okres czasu podczas migracji. Jednak płaszczyzna danych nadal działa. Oznacza to, że aplikacje działające na maszynach wirtualnych (klasyczne) nie ponoszą przestojów podczas migracji.

Następujące konfiguracje nie są obecnie obsługiwane. Jeśli pomoc techniczna zostanie dodana w przyszłości, niektóre maszyny wirtualne w tej konfiguracji mogą ponieść przestoje (przejdź przez zatrzymanie, cofnięte alokacje i ponowne uruchomienie operacji maszyn wirtualnych).

* Masz więcej niż jeden zestaw dostępności w jednej usłudze w chmurze.
* Masz jeden lub więcej zestawów dostępności i maszyn wirtualnych, które nie są w dostępności ustawionej w jednej usłudze w chmurze.

> [!NOTE]
> W tym zakresie migracji płaszczyzna zarządzania może nie być dozwolona przez pewien czas podczas migracji. W przypadku niektórych konfiguracji, jak opisano wcześniej, występuje przestój płaszczyzny danych.
>

### <a name="migration-of-storage-accounts"></a>Migracja kont magazynu
Aby umożliwić bezproblemową migrację, można wdrożyć maszyny wirtualne Menedżera zasobów na klasycznym koncie magazynu. Dzięki tej możliwości zasoby obliczeniowe i sieciowe mogą i powinny być migrowane niezależnie od kont magazynu. Po migracji za pośrednictwem maszyn wirtualnych i sieci wirtualnej należy przeprowadzić migrację za pośrednictwem kont magazynu, aby zakończyć proces migracji.

Jeśli konto magazynu nie ma żadnych skojarzonych dysków lub danych maszyn wirtualnych i ma tylko obiekty blob, pliki, tabele i kolejki, migracja do usługi Azure Resource Manager może odbywać się jako autonomiczna migracja bez zależności.

> [!NOTE]
> Model wdrażania Menedżera zasobów nie ma koncepcji klasycznych obrazów i dysków. Podczas migracji konta magazynu klasyczne obrazy i dyski nie są widoczne w stosie Menedżera zasobów, ale zapasowe identyfikatory VHD pozostają na koncie magazynu.

Poniższe zrzuty ekranu pokazują, jak uaktualnić konto magazynu klasycznego do konta magazynu usługi Azure Resource Manager przy użyciu witryny Azure portal:
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do swojego konta magazynu.
3. W sekcji **Ustawienia** kliknij pozycję **Migracja do arm**.
4. Kliknij **przycisk Sprawdź poprawność,** aby określić wykonalność migracji.
5. Jeśli sprawdzanie poprawności przebiegnie, kliknij przycisk **Przygotuj,** aby utworzyć zmigrowane konto magazynu.
6. Wpisz **tak,** aby potwierdzić migrację, a następnie kliknij przycisk **Zaślij,** aby zakończyć migrację.

    ![Sprawdzanie poprawności konta magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Przygotuj konto magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizowanie migracji konta magazynu](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migracja nieprzywiązanych zasobów
Konta magazynu bez skojarzonych dysków lub danych maszyn wirtualnych mogą być migrowane niezależnie.

Sieciowe grupy zabezpieczeń, tabele tras & zastrzeżone adresy IP, które nie są dołączone do żadnych maszyn wirtualnych i sieci wirtualnych, mogą być również migrowane niezależnie.

<br>

## <a name="unsupported-features-and-configurations"></a>Nieobsługiwały funkcje i konfiguracje
Niektóre funkcje i konfiguracje nie są obecnie obsługiwane; w poniższych sekcjach opisano nasze zalecenia dotyczące ich.

### <a name="unsupported-features"></a>Nieobsługiwały się funkcje
Następujące funkcje nie są obecnie obsługiwane. Opcjonalnie można usunąć te ustawienia, przeprowadzić migrację maszyn wirtualnych, a następnie ponownie włączyć ustawienia w modelu wdrażania Menedżera zasobów.

| Dostawca zasobów | Funkcja | Zalecenie |
| --- | --- | --- |
| Wystąpienia obliczeniowe | Nieskoczył dysków maszyny wirtualnej. | Obiekty blob VHD za tymi dyskami zostaną zmigrowane podczas migracji konta magazynu |
| Wystąpienia obliczeniowe | Obrazy maszyn wirtualnych. | Obiekty blob VHD za tymi dyskami zostaną zmigrowane podczas migracji konta magazynu |
| Network (Sieć) | Listy ACL punktów końcowych. | Usuń listy ACL punktu końcowego i ponów migrację. |
| Network (Sieć) | Application Gateway | Usuń bramę aplikacji przed rozpoczęciem migracji, a następnie ponownie utworzyć bramę aplikacji po zakończeniu migracji. |
| Network (Sieć) | Sieci wirtualne korzystające z komunikacji równorzędnej sieci wirtualnej. | Migrowanie sieci wirtualnej do Menedżera zasobów, a następnie równorzędne. Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnej](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nieobsługiwane konfiguracje
Następujące konfiguracje nie są obecnie obsługiwane.

| Usługa | Konfigurowanie | Zalecenie |
| --- | --- | --- |
| Resource Manager |Kontrola dostępu oparta na rolach (RBAC) dla zasobów klasycznych |Ponieważ identyfikator URI zasobów jest modyfikowany po migracji, zaleca się zaplanowanie aktualizacji zasad RBAC, które muszą nastąpić po migracji. |
| Wystąpienia obliczeniowe |Wiele podsieci skojarzonych z maszyną wirtualną |Zaktualizuj konfigurację podsieci, aby odwoływać się tylko do jednej podsieci. Może to wymagać usunięcia pomocniczej karty sieciowej (która odnosi się do innej podsieci) z maszyny Wirtualnej i ponownego dołączenia jej po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Maszyny wirtualne, które należą do sieci wirtualnej, ale nie mają przypisanej podsieci jawnej |Opcjonalnie można usunąć maszynę wirtualną. |
| Wystąpienia obliczeniowe |Maszyny wirtualne z alertami, zasady skalowania automatycznego |Migracja przechodzi i te ustawienia są usuwane. Zdecydowanie zaleca się, aby ocenić środowisko przed dokonaniem migracji. Alternatywnie można ponownie skonfigurować ustawienia alertów po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Rozszerzenia maszyn wirtualnych XML (BGInfo 1.*, debuger programu Visual Studio, wdrażanie w sieci Web i zdalne debugowanie) |To nie jest obsługiwane. Zaleca się usunięcie tych rozszerzeń z maszyny wirtualnej w celu kontynuowania migracji lub zostaną one automatycznie usunięte podczas procesu migracji. |
| Wystąpienia obliczeniowe |Diagnostyka rozruchu z pamięcią masową Premium |Wyłącz funkcję diagnostyki rozruchu dla maszyn wirtualnych przed kontynuowaniem migracji. Po zakończeniu migracji można ponownie włączyć diagnostykę rozruchu w stosie Menedżera zasobów. Ponadto obiekty BLOB, które są używane do zrzutu ekranu i dzienników szeregowych powinny zostać usunięte, więc nie są już naliczane opłaty za te obiekty blob. |
| Wystąpienia obliczeniowe | Usługi w chmurze zawierające role sieci Web/procesu roboczego | Obecnie nie jest to obsługiwane. |
| Wystąpienia obliczeniowe | Usługi w chmurze, które zawierają więcej niż jeden zestaw dostępności lub wiele zestawów dostępności. |Obecnie nie jest to obsługiwane. Przed migracją należy przenieść maszyny wirtualne do tej samej dostępności ustawionej. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem usługi Azure Security Center | Usługa Azure Security Center automatycznie instaluje rozszerzenia na maszynach wirtualnych w celu monitorowania ich zabezpieczeń i podnoszenia alertów. Te rozszerzenia zwykle są instalowane automatycznie, jeśli zasady usługi Azure Security Center są włączone w ramach subskrypcji. Aby przeprowadzić migrację maszyn wirtualnych, należy wyłączyć zasady centrum zabezpieczeń w ramach subskrypcji, która spowoduje usunięcie rozszerzenia monitorowania centrum zabezpieczeń z maszyn wirtualnych. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem kopii zapasowej lub migawki | Te rozszerzenia są instalowane na maszynie wirtualnej skonfigurowany z usługą Azure Backup. Chociaż migracja tych maszyn wirtualnych nie jest obsługiwana, postępuj zgodnie ze wskazówkami [w tym miejscu,](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) aby zachować kopie zapasowe, które zostały wykonane przed migracją.  |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem usługi Azure Site Recovery | Te rozszerzenia są instalowane na maszynie wirtualnej skonfigurowaną z usługą Azure Site Recovery. Podczas migracji magazynu używanego z odzyskiwaniem witryny będzie działać, bieżąca replikacja będzie mieć wpływ. Należy wyłączyć i włączyć replikację maszyny Wirtualnej po migracji magazynu. |
| Network (Sieć) |Sieci wirtualne zawierające maszyny wirtualne i role sieci web/procesu roboczego |Obecnie nie jest to obsługiwane. Przed migracją należy przenieść role sieci Web/Proces roboczy do własnej sieci wirtualnej. Po migracji klasycznej sieci wirtualnej zmigrowana sieć wirtualna usługi Azure Resource Manager można równorzędnie z klasyczną siecią wirtualną, aby uzyskać podobną konfigurację jak poprzednio.|
| Network (Sieć) | Obwody klasycznej trasy ekspresowej |Obecnie nie jest to obsługiwane. Te obwody muszą zostać zmigrowane do usługi Azure Resource Manager przed rozpoczęciem migracji IaaS. Aby dowiedzieć się więcej, zobacz [Przenoszenie obwodów usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Sieci wirtualne zawierające środowiska usługi app service |Obecnie nie jest to obsługiwane. |
| Azure HDInsight |Sieci wirtualne zawierające usługi HDInsight |Obecnie nie jest to obsługiwane. |
| Usługi cyklu życia systemu Microsoft Dynamics |Sieci wirtualne zawierające maszyny wirtualne zarządzane przez usługi Dynamics Lifecycle Services |Obecnie nie jest to obsługiwane. |
| Azure AD Domain Services |Sieci wirtualne zawierające usługi domen usługi Azure AD |Obecnie nie jest to obsługiwane. |
| Usługa Azure API Management |Sieci wirtualne zawierające wdrożenia usługi Azure API Management |Obecnie nie jest to obsługiwane. Aby przeprowadzić migrację sieci wirtualnej IaaS, zmień w sieci wirtualnej wdrożenia usługi API Management, która nie jest operacją przestojów. |
