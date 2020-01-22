---
title: Często zadawane pytania dotyczące urządzenia Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c9727287160f996059e9c1fb2d1fb5aec5900ab5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292336"
---
# <a name="azure-migrate-appliance-common-questions"></a>Urządzenie Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrateowego. Jeśli po przeczytaniu tego artykułu masz dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Jeśli masz inne pytania, zapoznaj się z następującymi artykułami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate.
- [Pytania](common-questions-discovery-assessment.md) dotyczące funkcji odnajdywania, oceny i wizualizacji zależności.


## <a name="what-is-the-azure-migrate-appliance"></a>Co to jest urządzenie Azure Migrate?

Urządzenie Azure Migrate jest lekkim urządzeniem używanym przez Azure Migrate: Narzędzie do oceny serwera do odnajdywania i oceniania serwerów lokalnych i używanych przez Azure Migrate: Narzędzie migracji serwera do migracji maszyn wirtualnych programu VMware. 

Urządzenie jest wdrażane lokalnie jako maszynę wirtualną lub na komputerze fizycznym. Urządzenie odnajduje maszyny lokalne i ciągle wysyła metadane maszyny i dane wydajności do Azure Migrate. Odnajdowanie urządzeń jest bez agentów. Nic nie jest zainstalowane na odnalezionych maszynach. [Dowiedz się więcej](migrate-appliance.md) o urządzeniu.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak urządzenie nawiązuje połączenie z platformą Azure?

Połączenie może być nawiązywane za pośrednictwem Internetu lub używać usługi Azure ExpressRoute z usługą komunikacji równorzędnej Public/Microsoft.

## <a name="does-appliance-analysis-impact-performance"></a>Czy analiza urządzenia ma wpływ na wydajność?

Lokalne profile urządzeń Azure Migrate są ciągle mierzone w celu mierzenia danych wydajności maszyny wirtualnej. To Profilowanie nie ma prawie wpływu na hosty funkcji Hyper-V/ESXi ani na VMware vCenter Server.

### <a name="can-i-harden-the-appliance-vm"></a>Czy mogę zabezpieczyć maszynę wirtualną urządzenia?

Podczas tworzenia maszyny wirtualnej z urządzeniem przy użyciu pobranego szablonu można dodać do szablonu składniki (na przykład oprogramowanie antywirusowe), o ile pozostawisz reguły komunikacji i zapory wymagane dla urządzenia Azure Migrate.


## <a name="what-network-connectivity-is-needed"></a>Jaka jest łączność sieciowa?

Zapoznaj się z następującymi tematami:
- Ocena VMware przy użyciu urządzenia Azure Migrate: wymagania dostępu do [adresów URL](migrate-appliance.md#url-access) i [portów](migrate-support-matrix-vmware.md#port-access) .
- Migracja bez agenta VMware za pomocą urządzenia Azure Migrate: wymagania dostępu do [adresów URL](migrate-appliance.md#url-access) i [portów](migrate-support-matrix-vmware-migration.md#agentless-ports) .
- Ocena funkcji Hyper-V przy użyciu urządzenia Azure Migrate: wymagania dostępu do [adresów URL](migrate-appliance.md#url-access) i [portów](migrate-support-matrix-hyper-v.md#port-access) .


## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Przejrzyj zebrane dane:

- [Dane wydajności](migrate-appliance.md#collected-performance-data-vmware) i [metadanych](migrate-appliance.md#collected-metadata-vmware)maszyny wirtualnej VMware.
- Dane i [metadane](migrate-appliance.md#collected-metadata-hyper-v) [wydajności](migrate-appliance.md#collected-performance-data-hyper-v) maszyny wirtualnej funkcji Hyper-V.


## <a name="how-is-data-stored"></a>W jaki sposób przechowywane są dane?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure wybranej podczas tworzenia projektu migracji. 

- Dane są bezpiecznie przechowywane w subskrypcji firmy Microsoft i usuwane po usunięciu projektu Azure Migrate.
- Jeśli używasz [wizualizacji zależności](concepts-dependency-visualization.md), zebrane dane są przechowywane w Stany Zjednoczone w obszarze roboczym log Analytics utworzonym w ramach subskrypcji platformy Azure. Te dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Ile danych jest przekazywanych do ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate zależy od liczby parametrów. Aby zapewnić zrozumienie woluminu, projekt Azure Migrate zawierający 10 maszyn (z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB dziennie. Ta wartość jest przybliżona i zmienia się w zależności od liczby punktów danych dla kart sieciowych i dysków. Zwiększenie ilości wysyłanych danych jest nieliniowe, jeśli istnieje wzrost liczby maszyn, kart sieciowych lub dysków.

## <a name="is-data-encrypted-at-restin-transit"></a>Czy dane są szyfrowane w czasie spoczynku/w trakcie przesyłania?

Tak, dla obu.

- Metadane są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem Internetu za pośrednictwem protokołu HTTPS.
- Metadane są przechowywane w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) , a w [usłudze Azure Blob Storage](../storage/common/storage-service-encryption.md)w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w stanie spoczynku.
- Dane dotyczące analizy zależności są również szyfrowane w trakcie przesyłania (Secure HTTPS). Jest ona przechowywana w obszarze roboczym Log Analytics w ramach subskrypcji. Jest on także szyfrowany w spoczynku.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak urządzenie nawiązuje połączenie z vCenter Server?

1. Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia.
2. Urządzenie używa programu VMware PowerCLI do wysyłania zapytań do vCenter Server, aby zbierać metadane dotyczące maszyn wirtualnych zarządzanych przez vCenter Server.
3. Urządzenie zbiera dane konfiguracyjne o maszynach wirtualnych (rdzenie, pamięć, dyski, karty sieciowe) i historię wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca.
4. Zebrane metadane są przeznaczone do Azure Migrate: Ocena serwera (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Czy mogę podłączyć urządzenie do wielu serwerów vCenter?

Nie. Istnieje mapowanie jeden do jednego między urządzeniem i vCenter Server. Aby odnajdywać maszyny wirtualne w wielu wystąpieniach vCenter Server, należy wdrożyć wiele urządzeń.

### <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych lub serwerów można wykryć przy użyciu urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware, do 5 000 maszyn wirtualnych funkcji Hyper-V, a nawet do 250 serwerów za pomocą jednego urządzenia. Jeśli masz więcej maszyn w środowisku lokalnym, zapoznaj się z tematem skalowanie [funkcji Hyper-V](scale-hyper-v-assessment.md), programu [VMware](scale-vmware-assessment.md) i oceny [fizycznej](scale-physical-assessment.md) .

## <a name="can-i-delete-an-appliance"></a>Czy mogę usunąć urządzenie?

Obecnie Usuwanie urządzenia z projektu nie jest obsługiwane.

- Jedynym sposobem usunięcia urządzenia jest usunięcie grupy zasobów zawierającej projekt Azure Migrate skojarzony z urządzeniem.
- Jednak usunięcie grupy zasobów spowoduje również usunięcie innych zarejestrowanych urządzeń, wykrytego spisu, ocen i wszystkich innych składników platformy Azure skojarzonych z projektem w grupie zasobów.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Czy mogę użyć urządzenia z inną subskrypcją/projektem?

Po użyciu urządzenia do zainicjowania odnajdywania nie można zmienić jego konfiguracji z inną subskrypcją platformy Azure lub w innym projekcie Azure Migrate. Nie można również odnajdywać maszyn wirtualnych na innym vCenter Server. Skonfiguruj nowe urządzenie dla tych zadań.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Czy mogę skonfigurować urządzenie na maszynie wirtualnej platformy Azure?
Obecnie nie jest obsługiwane. 

## <a name="can-i-discover-on-an-esxi-host"></a>Czy mogę odnaleźć na hoście ESXi?
Nie potrzebujesz vCenter Server do odnajdywania maszyn wirtualnych VMware.

## <a name="how-do-i-update-the-appliance"></a>Jak mogę zaktualizować urządzenie?

Domyślnie urządzenie i jego zainstalowanych agentów są automatycznie aktualizowane. Urządzenie sprawdza dostępność aktualizacji co 24 godziny. Jeśli podczas procesu aktualizacji wystąpią jakiekolwiek błędy, proces ponawiania prób. Aktualizacje automatyczne aktualizują tylko agentów urządzeń i urządzeń. System operacyjny nie został zaktualizowany. Aby zapewnić aktualność systemu operacyjnego, użyj aktualizacji firmy Microsoft.

## <a name="can-i-check-agent-health"></a>Czy mogę sprawdzić kondycję agentów?

W portalu przejdź do strony **kondycja agenta** w narzędziu do oceny serwera lub migracji serwera. W tym miejscu można sprawdzić stan połączenia między agentami odnajdywania i oceny na urządzeniu i na platformie Azure.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
