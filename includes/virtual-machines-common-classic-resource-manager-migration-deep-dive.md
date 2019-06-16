---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167088"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrację zasobów IaaS od modelu wdrażania klasycznego do usługi Azure Resource Manager
Po pierwsze należy zrozumieć różnicę między operacjami płaszczyzny danych i płaszczyzny zarządzania w infrastrukturze jako zasoby usługi (IaaS).

* *Płaszczyzna zarządzania/kontroli* opisuje wywołania, które dochodzą do płaszczyzny zarządzania/kontroli lub interfejs API umożliwiający modyfikowanie zasobów. Operacje, na przykład takie jak utworzenie maszyny wirtualnej, ponowne uruchomienie maszyny wirtualnej i zaktualizowanie maszyny wirtualnej przy użyciu nowej podsieci, powodują wykonanie działań zarządzania uruchomionymi zasobami. Nie wpływają one bezpośrednio na nawiązywanie połączeń z maszynami wirtualnymi.
* *Płaszczyzna danych* (aplikacji) opisano środowisko uruchomieniowe aplikacji i obejmuje interakcje z wystąpieniami, które nie są kierowane za pośrednictwem interfejsu API platformy Azure. Na przykład dostęp do witryny internetowej lub pobranie danych z uruchomionego wystąpienia programu SQL Server lub serwer bazy danych MongoDB są dane interakcje płaszczyzną lub aplikacji. Inne przykłady obejmują kopiowania obiektu blob z konta magazynu i uzyskiwania dostępu do publicznego adresu IP do używania protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH) do maszyny wirtualnej. Te operacje utrzymują działanie aplikacji w ramach usług obliczeniowych, sieciowych i magazynowych.

Płaszczyzna danych jest taka sama, między klasycznego modelu wdrażania i stosów usługi Resource Manager. Różnica polega na tym, że podczas procesu migracji firmy Microsoft tłumaczy reprezentacja zasobów z klasycznego modelu wdrażania do tego w stosie usługi Resource Manager. W rezultacie należy użyć nowych narzędzi, interfejsów API i zestawów SDK do zarządzania zasobami w stosie usługi Resource Manager.

![Diagram, który zawiera różnicę między płaszczyzną zarządzania/kontroli i płaszczyzny danych](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> W przypadku niektórych scenariuszy migracji platforma Azure zatrzymuje maszyny wirtualne, cofa ich przydział i uruchamia je ponownie. Powoduje to krótki przestój płaszczyzny danych.
>

## <a name="the-migration-experience"></a>Środowisko migracji
Przed rozpoczęciem migracji:

* Upewnij się, że zasoby do zmigrowania nie używają żadnych nieobsługiwanych funkcji ani konfiguracji. Zazwyczaj platforma wykrywa te problemy i zgłasza błąd.
* Jeśli masz maszyny wirtualne, które nie znajdują się w sieci wirtualnej, zostaną zatrzymane i w ramach operacji przygotowania z cofniętą alokacją. Jeśli nie chcesz stracić publicznych adresów IP, należy wziąć pod uwagę rezerwowanie adresów IP przed wyzwoleniem operacji przygotowania. W przypadku maszyn wirtualnych w sieci wirtualnej, nie są zatrzymane i z cofniętą alokacją.
* Zaplanuj przeprowadzenie migracji poza godzinami pracy, aby uwzględnić możliwość wystąpienia nieoczekiwanych awarii podczas migracji.
* Pobierz bieżącą konfigurację maszyn wirtualnych przy użyciu programu PowerShell, poleceń interfejsu wiersza polecenia (CLI) lub interfejsów API REST, aby ułatwić walidację po zakończeniu kroku przygotowania.
* Zaktualizuj skrypty automatyzacji i operacjonalizacji oprogramowania do obsługi modelu wdrażania usługi Resource Manager, przed rozpoczęciem migracji. Opcjonalnie możesz wykonać operacje GET, gdy zasoby są w stanie przygotowania.
* Oceń zasady kontroli dostępu opartej na rolach (RBAC), które są skonfigurowane w przypadku zasobów IaaS w klasycznym modelu wdrażania i Zaplanuj czynności po zakończeniu migracji.

Przepływ pracy migracji jest następujący:

![Diagram przedstawiający przepływ pracy migracji](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Czynności opisane w poniższych sekcjach są idempotentne wszystkich. Jeśli masz problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, ponów próbę wykonania przygotowania, przerwania lub zatwierdzenia operacji. Azure spróbuje wykonać akcję ponownie.
>
>

### <a name="validate"></a>Walidacja
Operacja walidacji to pierwszy krok w procesie migracji. Celem tego kroku jest analizowanie stanu zasobów, które chcesz migrować w klasycznym modelu wdrażania. Operacja ocenia, czy zasoby są w stanie migracji (powodzenie lub niepowodzenie).

Wybierz sieć wirtualną lub usługę w chmurze (Jeśli nie jest w sieci wirtualnej), który ma zostać zwalidowana pod kątem migracji. Jeśli zasób nie jest zdolny do migracji, Azure zawiera listę powodów dlaczego.

#### <a name="checks-not-done-in-the-validate-operation"></a>Sprawdza, czy nie są wykonywane w ramach operacji sprawdzania poprawności

Operacja walidacji analizuje tylko stan zasobów w klasycznym modelu wdrażania. Można ją sprawdzić dla wszystkich błędów i nieobsługiwane scenariusze ze względu na różne konfiguracje w klasycznym modelu wdrażania. Nie jest możliwe do wyszukania wszystkich problemów, które stosu usługi Azure Resource Manager może nakładać się na zasobach podczas migracji. Te problemy tylko są sprawdzane, jeśli zasoby poddaje przekształcania w następnym kroku migracji (operacji przygotowania). Poniższa tabela zawiera listę wszystkich problemów, które nie jest sprawdzana w operacji sprawdzania poprawności:


|Sprawdza, czy sieć nie w ramach operacji sprawdzania poprawności|
|-|
|Posiadanie zarówno ER, jak i sieci VPN bramy sieci wirtualnej.|
|Połączenia bramy sieci wirtualnej w stanie odłączonym.|
|Wszystkie obwody ER wstępnie są migrowane do stosu usługi Azure Resource Manager.|
|Przydziału usługi Azure Resource Manager sprawdza, czy zasoby sieciowe. Na przykład: statyczny publiczny adres IP, dynamiczne publiczne adresy IP obciążenia równoważenia, sieciowe grupy zabezpieczeń, tabele tras i interfejsów sieciowych. |
| Wszystkie reguły modułu równoważenia obciążenia są prawidłowe we wdrażanie i sieci wirtualnej. |
| Konflikt prywatnych adresów IP między zatrzymana z cofniętą alokacją z maszynami wirtualnymi w tej samej sieci wirtualnej. |

### <a name="prepare"></a>Przygotowanie
Operacja przygotowania to drugi krok w procesie migracji. Celem tego kroku jest symulowanie przekształcenia zasobów IaaS z klasycznego modelu wdrażania do zasobów usługi Resource Manager. Dodatkowo operacji przygotowania przedstawia informacje o tym side-by-side służących do wizualizacji.

> [!NOTE] 
> Zasobami w klasycznym modelu wdrażania nie są modyfikowane w tym kroku. Jest bezpieczne krok do uruchomienia, jeśli próbujesz opracowywać migracji. 

Możesz wybrać sieć wirtualną lub usługę w chmurze (Jeśli nie jest używana sieć wirtualna) chcesz przygotować się do migracji.

* Jeśli zasób nie jest w stanie zmigrować, platforma Azure zatrzyma proces migracji i wyświetli listę przyczyn niepowodzenia operacji przygotowania.
* Jeśli zasób jest w stanie migracji, Azure zablokuje operacje płaszczyzny zarządzania dla migrowanych zasobów. Na przykład nie będzie można dodać dysku danych do migrowanej maszyny wirtualnej.

Azure następnie rozpocznie migrację metadanych z klasycznego modelu wdrażania do usługi Resource Manager dla migracji zasobów.

Po zakończeniu operacji przygotowania masz zwizualizować zasoby w klasycznym modelu wdrażania i usługi Resource Manager. Dla każdej usługi w chmurze w klasycznym modelu wdrażania platforma Azure tworzy nazwę grupy zasobów zgodnie ze wzorcem `cloud-service-name>-Migrated`.

> [!NOTE]
> Nie jest możliwe wybrać nazwę grupy zasobów tworzonej dla migrowanych zasobów (czyli "-migracji"). Po zakończeniu migracji, jednak służy funkcji przenoszenia usługi Azure Resource Manager do przeniesienia zasobów do dowolnej grupy zasobów, które chcesz. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../articles/resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

Następujące dwa zrzuty ekranu przedstawiają wynik pomyślnie po pomyślnym Operacja przygotowywania. Pierwsza z nich zawiera grupę zasobów, który zawiera oryginalną usługę w chmurze. Drugi pokazuje nowe "-migracji" grupy zasobów, która zawiera równoważne zasoby usługi Azure Resource Manager.

![Zrzut ekranu, który zawiera oryginalną usługę w chmurze](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Zrzut ekranu przedstawiający zasoby usługi Azure Resource Manager w ramach operacji przygotowania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

W tym miejscu przedstawiono tle zasobów po zakończeniu fazy prepare. Pamiętaj, że zasób na płaszczyźnie danych jest taka sama. Jest reprezentowana w płaszczyzna zarządzania (klasycznego modelu wdrażania) i na płaszczyźnie kontroli (Resource Manager).

![Diagram w fazie przygotowania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Maszyny wirtualne, które nie znajdują się w sieci wirtualnej w klasycznym modelu wdrażania zostaną zatrzymane i w tej fazie migracji z cofniętą alokacją.
>

### <a name="check-manual-or-scripted"></a>Sprawdzenie (ręczne lub za pomocą skryptu)
W kroku sprawdzenia istnieje możliwość użycia konfiguracji, który został wcześniej pobrany, aby sprawdzić, czy migracja wydaje się prawidłowe. Alternatywnie możesz zalogować się do portalu i miejscu właściwości i zasoby, aby sprawdzić, czy migracja metadanych wygląda dobrze.

Jeśli migrujesz sieć wirtualną, większość konfiguracji maszyn wirtualnych nie zostanie uruchomiona ponownie. W przypadku aplikacji dla tych maszyn wirtualnych można sprawdzić, czy aplikacja jest nadal uruchomiona.

Możesz przetestować skrypty monitorowania i operacyjne aby zobaczyć, jeśli maszyny wirtualne działają zgodnie z oczekiwaniami, a zaktualizowane skrypty działają poprawnie. Tylko operacje GET są obsługiwane, jeśli zasoby są w stanie przygotowania.

Istnieje nie okno Ustaw czas, przed którym należy zatwierdzić migrację. Ten stan może trwać tak długo, jak chcesz. Jednak płaszczyzna zarządzania jest zablokowana dla tych zasobów do momentu przerwania lub zatwierdzenia.

Jeśli napotkasz jakiekolwiek problemy, zawsze możesz przerwać migrację i wrócić do klasycznego modelu wdrażania. Po powrocie platforma Azure otworzy operacje płaszczyzny zarządzania dla zasobów, dzięki czemu można wznowienie zwykłych operacji dla tych maszyn wirtualnych w klasycznym modelu wdrażania.

### <a name="abort"></a>Przerwij
Jest to krok opcjonalny, jeśli chcesz cofnąć zmiany do klasycznego modelu wdrażania oraz zatrzymania migracji. Ta operacja usuwa metadanych Menedżera zasobów (utworzonym w kroku) dla zasobów. 

![Diagramu kroków przerwania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Nie można wykonać tej operacji po wyzwoleniu operacji zatwierdzenia.     
>

### <a name="commit"></a>Zatwierdzenie
Po zakończeniu walidacji możesz zatwierdzić migrację. Zasoby nie będą już w klasycznym modelu wdrażania i są dostępne tylko w modelu wdrażania usługi Resource Manager. Zmigrowanymi zasobami można zarządzać tylko w nowym portalu.

> [!NOTE]
> Jest to operacja idempotentna. Jeśli nie powiedzie się, spróbuj ponownie wykonać operację. Jeśli nadal nie można zakończyć się niepowodzeniem, Utwórz bilet pomocy technicznej lub wpis z "ClassicIaaSMigration" tagiem na naszych [forum maszyn wirtualnych](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagram przedstawiający krok zatwierdzenia](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Schemat blokowy migracji

Schemat blokowy pokazujący sposób kontynuować migrację, jest następujący:

![Zrzut ekranu przedstawiający kroki migracji](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tłumaczenie z klasycznego modelu wdrażania do zasobów usługi Resource Manager
Reprezentacje Menedżera zasobów, zasobów i klasycznego modelu wdrażania można znaleźć w poniższej tabeli. Inne funkcje i zasoby nie są obecnie obsługiwane.

| Reprezentacja klasyczna | Reprezentacja usługi Resource Manager | Uwagi |
| --- | --- | --- |
| Nazwa usługi w chmurze |Nazwa DNS |Podczas migracji jest tworzona nowa grupa zasobów dla każdej usługi w chmurze przy użyciu wzorca nazewnictwa `<cloudservicename>-migrated`. Ta grupa zasobów zawiera wszystkie zasoby. Nazwa usługi w chmurze stanie się nazwą DNS skojarzoną z publicznym adresem IP. |
| Maszyna wirtualna |Maszyna wirtualna |Właściwości specyficzne dla maszyny wirtualnej są migrowane bez zmian. Niektóre informacje typu osProfile, takie jak nazwa komputera nie są przechowywane w klasycznym modelu wdrażania i pozostaną puste po migracji. |
| Zasoby dysków dołączonych do maszyny wirtualnej |Niejawne dyski dołączone do maszyny wirtualnej |Dyski nie są modelowane jako zasoby najwyższego poziomu w modelu wdrażania usługi Resource Manager. Są one migrowane jako dyski niejawne w ramach maszyny wirtualnej. Tylko dyski dołączone do maszyny wirtualnej są obecnie obsługiwane. Maszyny wirtualne usługi Resource Manager można teraz używać kont magazynu w klasycznym modelu wdrażania, co pozwala dysków, które można łatwo zmigrować bez wykonywania żadnych aktualizacji. |
| Rozszerzenia maszyn wirtualnych |Rozszerzenia maszyn wirtualnych |Wszystkie rozszerzenia zasobów, z wyjątkiem rozszerzeń XML, są migrowane z klasycznego modelu wdrażania. |
| Certyfikaty maszyny wirtualnej |Certyfikaty w usłudze Azure Key Vault |Jeśli usługa w chmurze zawiera certyfikaty usługi, migracji tworzy nowy magazyn kluczy platformy Azure dla usługi w chmurze i certyfikaty są przenoszone do magazynu kluczy. Maszyny wirtualne są aktualizowane, tak aby przywoływały certyfikaty z magazynu kluczy. <br><br> Nie usuwaj magazynu kluczy. Może to spowodować przejście maszyny Wirtualnej w stanie niepowodzenia. |
| Konfiguracja usługi WinRM |Konfiguracja usługi WinRM w ramach elementu osProfile |Konfiguracja usługi Windows Remote Management jest przenoszona bez zmian w ramach migracji. |
| Właściwość Availability-set |Zasób Availability-set | Specyfikacja Availability-set jest właściwością maszyny wirtualnej w klasycznym modelu wdrażania. Zestawy dostępności stają się zasobami najwyższego poziomu w ramach migracji. Następujące konfiguracje nie są obsługiwane: wiele zestawów dostępności dla usługi w chmurze, co najmniej jeden zestaw dostępności razem z maszyną wirtualną poza zestawem dostępności w ramach usługi w chmurze. |
| Konfiguracja sieci na maszynie wirtualnej |Podstawowy interfejs sieciowy |Konfiguracja sieci na maszynie wirtualnej jest reprezentowana jako zasób podstawowego interfejsu sieciowego po migracji. W przypadku maszyn wirtualnych poza siecią wirtualną wewnętrzny adres IP zmieni się podczas migracji. |
| Wiele interfejsów sieciowych na maszynie wirtualnej |Interfejsy sieciowe |Jeśli maszyna wirtualna ma wiele interfejsów sieciowych skojarzonych z nim, każdy interfejs sieciowy stanie się zasobem najwyższego poziomu w ramach migracji, wraz z wszystkich właściwości. |
| Zestaw punktów końcowych z równoważeniem obciążenia |Moduł równoważenia obciążenia |W klasycznym modelu wdrażania do platformy jest przypisany niejawny moduł równoważenia obciążenia dla każdej usługi w chmurze. Podczas migracji jest tworzony nowy zasób modułu równoważenia obciążenia, a zestaw punktów końcowych równoważenia obciążenia staje się regułami modułu równoważenia obciążenia. |
| Reguły NAT dla ruchu przychodzącego |Reguły NAT dla ruchu przychodzącego |Wejściowe punkty końcowe zdefiniowane na maszynie wirtualnej są konwertowane na reguły translacji dla adresu sieciowego ruchu przychodzącego w module równoważenia obciążenia podczas migracji. |
| Adres VIP |Publiczny adres IP z nazwą DNS |Wirtualny adres IP staje się publiczny adres IP i jest skojarzony z modułem równoważenia obciążenia. Wirtualny adres IP można zmigrować tylko pod warunkiem, że ma przypisany wejściowy punkt końcowy. |
| Sieć wirtualna |Sieć wirtualna |Sieć wirtualna jest migrowana razem ze wszystkimi właściwościami do modelu wdrażania usługi Resource Manager. Nowa grupa zasobów jest tworzona za pomocą sufiksu `-migrated`. |
| Zastrzeżone adresy IP |Publiczny adres IP z przydziałem statycznym |Zastrzeżone adresy IP skojarzone z modułem równoważenia obciążenia są migrowane w ramach migrowania usługi w chmurze lub maszyny wirtualnej. Migracja nieskojarzonego zastrzeżonego adresu IP nie jest obecnie obsługiwana. |
| Publiczny adres IP dla maszyny wirtualnej |Publiczny adres IP z przydziałem dynamicznym |Publiczny adres IP skojarzony z maszyną wirtualną jest konwertowany na zasób publicznego adresu IP z przydziałem statycznym. |
| Sieciowe grupy zabezpieczeń |Sieciowe grupy zabezpieczeń |Sieciowa grupa zabezpieczeń skojarzona z podsiecią jest klonowana w ramach migracji do modelu wdrażania usługi Resource Manager. Sieciowa grupa zabezpieczeń w klasycznym modelu wdrażania nie jest usuwana podczas migracji. Jednak operacje płaszczyzny zarządzania dla sieciowej grupy zabezpieczeń są zablokowane, gdy trwa migracja. |
| Serwery DNS |Serwery DNS |Serwery DNS skojarzone z siecią wirtualną lub maszyną wirtualną są migrowane w ramach migracji odpowiadających zasobów razem ze wszystkimi właściwościami. |
| Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika skojarzone z podsiecią są klonowane w ramach migracji do modelu wdrażania usługi Resource Manager. Trasy zdefiniowane przez użytkownika w klasycznym modelu wdrażania nie są usuwane podczas migracji. Operacje płaszczyzny zarządzania dla tras zdefiniowanych przez użytkownika są zablokowane, gdy trwa migracja. |
| Właściwość przekazywania adresu IP w konfiguracji sieci maszyny wirtualnej |Właściwość przekazywania adresu IP dla karty sieciowej |Właściwość przekazywania adresu IP na maszynie wirtualnej jest konwertowana na właściwość interfejsu sieciowego podczas migracji. |
| Moduł równoważenia obciążenia z wieloma adresami IP |Moduł równoważenia obciążenia z wieloma zasobami publicznego adresu IP |Każdy publiczny adres IP skojarzony z modułem równoważenia obciążenia jest konwertowany na zasób publicznego adresu IP i skojarzone z modułem równoważenia obciążenia po zakończeniu migracji. |
| Wewnętrzne nazwy DNS na maszynie wirtualnej |Wewnętrzne nazwy DNS na karcie sieciowej |Podczas migracji wewnętrzne sufiksy DNS dla maszyn wirtualnych są migrowane do właściwości tylko do odczytu o nazwie „InternalDomainNameSuffix” na karcie sieciowej. Sufiks pozostaje niezmieniony po migracji i rozpoznawanie maszyny Wirtualnej powinny nadal działać jak poprzednio. |
| Brama sieci wirtualnej |Brama sieci wirtualnej |Właściwości bramy sieci wirtualnej są migrowane bez zmian. Adres VIP skojarzony z bramą także nie jest zmieniany. |
| Lokacja sieci lokalnej |Brama sieci lokalnej |Właściwości lokacji sieci lokalnej są migrowane bez zmian do nowego zasobu o nazwie Brama sieci lokalnej. Reprezentuje on prefiksy adresów lokalnych i adres IP bramy zdalnej. |
| Odwołania połączeń |Połączenie |Odwołania łączności między bramą a lokacją sieci lokalnej w konfiguracji sieci jest reprezentowana przez nowy zasób o nazwie połączenia. Wszystkie właściwości odwołania łączności w plikach konfiguracji sieci są kopiowane bez zmian do zasobu połączenia. Łączność między sieciami wirtualnymi w klasycznym modelu wdrażania należy przestrzegać dwóch tuneli protokołu IPsec do lokacji sieci lokalnej reprezentujących sieci wirtualne. Ta konfiguracja jest przekształcana na typ sieci do wirtualnego — połączenia sieci wirtualnej w modelu usługi Resource Manager bez bramy sieci lokalnej. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Zmiany automatyzacji i narzędzi po migracji
W ramach migracji zasobów z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager należy zaktualizować istniejące automatyzacje lub narzędzia, aby zapewnić, że będzie on nadal działać po zakończeniu migracji.
