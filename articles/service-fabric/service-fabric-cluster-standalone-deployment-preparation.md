---
title: Samodzielne przygotowanie do wdrażania klastra
description: Dokumentacja związana z przygotowaniem środowiska i tworzeniem konfiguracji klastra, która ma być rozważona przed wdrożeniem klastra przeznaczonego do obsługi obciążenia produkcyjnego.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610067"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planowanie i przygotowywanie samodzielnego wdrażania klastra sieci szkieletowej usług

<a id="preparemachines"></a>Przed utworzeniem klastra wykonaj następujące czynności.

## <a name="plan-your-cluster-infrastructure"></a>Planowanie infrastruktury klastra
Masz zamiar utworzyć klaster sieci szkieletowej usług na komputerach, które są "własne", dzięki czemu można zdecydować, jakie rodzaje błędów, które mają klastra przetrwać. Na przykład, czy potrzebujesz oddzielnych linii energetycznych lub połączeń internetowych dostarczanych do tych maszyn? Ponadto należy wziąć pod uwagę fizyczne bezpieczeństwo tych maszyn. Gdzie znajdują się maszyny i kto potrzebuje do nich dostępu? Po podjęcia tych decyzji można logicznie mapować maszyny do różnych domen błędów (zobacz następny krok). Planowanie infrastruktury dla klastrów produkcyjnych jest bardziej zaangażowane niż w przypadku klastrów testowych.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Określanie liczby domen błędów i domen uaktualniania
[ *Domena błędów* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) jest fizyczną jednostką awarii i jest bezpośrednio związana z infrastrukturą fizyczną w centrach danych. Domena błędów składa się ze składników sprzętowych (komputerów, przełączników, sieci i innych), które współużytkują pojedynczy punkt awarii. Chociaż nie ma mapowania 1:1 między domenami błędów i stojakami, luźno mówiąc, każdy stojak można uznać za domenę błędów.

Po określeniu identyfikatorów FD w pliku ClusterConfig.json można wybrać nazwę dla każdego FD. Sieci szkieletowej usług obsługuje hierarchiczne fds, dzięki czemu można odzwierciedlić topologii infrastruktury w nich.  Na przykład prawidłowe są następujące identyfikatory FD:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

*Domena uaktualnienia* (UD) jest logiczną jednostką węzłów. Podczas uaktualnień zaaranżowanych w sieci szkieletowej usług (uaktualnienia aplikacji lub uaktualnienia klastra) wszystkie węzły w UD są likwidowane w celu wykonania uaktualnienia, podczas gdy węzły w innych identyfikatorach UD pozostają dostępne do obsługi żądań. Uaktualnienia oprogramowania układowego wykonywane na komputerach nie są honorowane, więc musisz wykonywać je po jednym komputerze naraz.

Najprostszym sposobem myślenia o tych pojęciach jest uznanie FDs za jednostkę nieplanowanej awarii i UD jako jednostkę planowanej konserwacji.

Po określeniu UD w ClusterConfig.json, można wybrać nazwę dla każdego UD. Na przykład prawidłowe są następujące nazwy:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Niebieski"

Aby uzyskać bardziej szczegółowe informacje na temat identyfikatorów FD i UD, zobacz [Opisywanie klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md).

Klaster w środowisku produkcyjnym powinien obejmować co najmniej trzy dyski FD, aby były obsługiwane w środowisku produkcyjnym, jeśli masz pełną kontrolę nad konserwacją i zarządzaniem węzłami, oznacza to, że jesteś odpowiedzialny za aktualizowanie i wymianę maszyn. W przypadku klastrów działających w środowiskach (czyli wystąpieniach maszyn wirtualnych amazon web services), w których nie masz pełnej kontroli nad komputerami, powinieneś mieć co najmniej pięć dysków FD w klastrze. Każdy FD może mieć jeden lub więcej węzłów. Ma to zapobiec problemom spowodowanym przez uaktualnienia i aktualizacje komputera, które w zależności od ich czasu mogą zakłócać działanie aplikacji i usług w klastrach.

## <a name="determine-the-initial-cluster-size"></a>Określanie początkowego rozmiaru klastra

Ogólnie rzecz biorąc liczba węzłów w klastrze jest określana na podstawie potrzeb biznesowych, czyli liczby usług i kontenerów będzie uruchomionych w klastrze i ile zasobów jest potrzebnych do obsługi obciążeń. W przypadku klastrów produkcyjnych zaleca się posiadanie co najmniej pięciu węzłów w klastrze, obejmujących 5 dysków FD. Jednak jak opisano powyżej, jeśli masz pełną kontrolę nad węzłami i może obejmować trzy FDs, a następnie trzy węzły powinny również wykonać zadanie.

Klastry testowe z uruchomionymi obciążeniami stanowymi powinny mieć trzy węzły, podczas gdy klastry testowe uruchamiane tylko z obciążeniami bezstanowymi potrzebują tylko jednego węzła. Należy również zauważyć, że do celów programisty, można mieć więcej niż jeden węzeł na danym komputerze. Jednak w środowisku produkcyjnym sieć szkieletowa usług obsługuje tylko jeden węzeł na maszynę fizyczną lub wirtualną.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Przygotowanie maszyn, które będą służyć jako węzły

Oto kilka zalecanych specyfikacji dla każdego komputera, które chcesz dodać do klastra:

* Co najmniej 16 GB pamięci RAM
* Co najmniej 40 GB wolnego miejsca na dysku
* 4-rdzeniowy lub większy procesor
* Łączność z bezpieczną siecią lub sieciami dla wszystkich maszyn
* Zainstalowany system operacyjny Windows Server (prawidłowe wersje: 2012 R2, 2016, 1709 lub 1803). Usługa Sieci szkieletowej w wersji 6.4.654.9590 i nowszych obsługuje również serwery 2019 i 1809.
* [.NET Framework 4.5.1 lub nowsza](https://www.microsoft.com/download/details.aspx?id=40773), pełna instalacja
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* [Usługa RemoteRegistry](https://technet.microsoft.com/library/cc754820) powinna działać na wszystkich komputerach
* Dysk instalacyjny sieci szkieletowej usługi musi być systemem plików NTFS

Administrator klastra wdrażający i konfigurujący klaster musi mieć [uprawnienia administratora](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) na każdym z komputerów. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Pobieranie samodzielnego pakietu sieci szkieletowej usług dla systemu Windows Server
[Pobierz łącze — pakiet autonomiczny sieci szkieletowej usług — windows server](https://go.microsoft.com/fwlink/?LinkId=730690) i rozpakuj pakiet, albo na komputerze wdrożeniowym, który nie jest częścią klastra, albo na jednym z komputerów, które będą częścią klastra.

## <a name="modify-cluster-configuration"></a>Modyfikowanie konfiguracji klastra
Aby utworzyć klaster autonomiczny, należy utworzyć autonomiczny plik clusterconfig.json konfiguracji klastra, który opisuje specyfikację klastra. Plik konfiguracyjny można oprzeć na szablonach znalezionych pod poniższym linkiem. <br>
[Konfiguracje klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Aby uzyskać szczegółowe informacje na temat sekcji w tym pliku, zobacz [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md).

Otwórz jeden z plików ClusterConfig.json z pobranego pakietu i zmodyfikuj następujące ustawienia:

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| **Typy węzłów** |Typy węzłów umożliwiają oddzielenie węzłów klastra na różne grupy. Klaster musi mieć co najmniej jeden typ węzła. Wszystkie węzły w grupie mają następujące wspólne cechy: <br> **Nazwa** — jest to nazwa typu węzła. <br>**Porty punktu końcowego** — są to różne nazwane punkty końcowe (porty), które są skojarzone z tym typem węzła. Można użyć dowolnego numeru portu, który chcesz, tak długo, jak nie są one w konflikcie z niczym innym w tym manifeście i nie są już używane przez inną aplikację działającą na komputerze/maszynie wirtualnej. <br> **Właściwości umieszczania** — opisują właściwości dla tego typu węzła, które są używane jako ograniczenia miejsca docelowego dla usług systemowych lub usług. Właściwości te są zdefiniowane przez użytkownika pary klucz/wartość, które zapewniają dodatkowe dane meta dla danego węzła. Przykładami właściwości węzła jest to, czy węzeł ma dysk twardy lub kartę graficzną, liczbę wrzecion w jego dysku twardym, rdzenie i inne właściwości fizyczne. <br> **Pojemności** — pojemności węzłów definiują nazwę i ilość określonego zasobu, który jest dostępny dla określonego węzła do użycia. Na przykład węzeł może zdefiniować, że ma pojemność dla metryki o nazwie "MemoryInMb" i że ma 2048 MB dostępne domyślnie. Te pojemności są używane w czasie wykonywania, aby upewnić się, że usługi, które wymagają określonych ilości zasobów są umieszczane w węzłach, które mają te zasoby dostępne w wymaganych ilościach.<br>**IsPrimary** — jeśli masz więcej niż jeden NodeType zdefiniowane upewnij się, że tylko jeden jest ustawiony na podstawowy z *wartością true*, czyli gdzie działają usługi systemowe. Wszystkie inne typy węzłów powinny być ustawione na wartość *false* |
| **Węzły** |Są to szczegóły dla każdego z węzłów, które są częścią klastra (typ węzła, nazwa węzła, adres IP, domena błędów i domena uaktualnienia węzła). Maszyny, na których ma zostać utworzony klaster, muszą być wymienione w tym miejscu z ich adresami IP. <br> Jeśli używasz tego samego adresu IP dla wszystkich węzłów, tworzony jest klaster jednokojowy, którego można użyć do celów testowych. Nie należy używać klastrów jednokojowych do wdrażania obciążeń produkcyjnych. |

Po skonfigurowaniu konfiguracji klastra do środowiska można ją przetestować w środowisku klastra (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Konfigurowanie środowiska

Gdy administrator klastra konfiguruje autonomiczny klaster sieci szkieletowej usług, środowisko musi być skonfigurowane z następującymi kryteriami: <br>
1. Użytkownik tworzący klaster powinien mieć uprawnienia zabezpieczeń na poziomie administratora do wszystkich komputerów, które są wymienione jako węzły w pliku konfiguracji klastra.
2. Maszyna, z której tworzony jest klaster, a także każdy węzeł klastra musi:
   * Odinstalowywanie SDK sieci szkieletowej usług
   * Odinstalowanie środowiska uruchomieniowego sieci szkieletowej usług
   * Mieć włączoną usługę Zapory systemu Windows (mpssvc)
   * Włączenie usługi rejestru zdalnego (rejestru zdalnego)
   * Włącz udostępnianie plików (SMB)
   * Otwarte niezbędne porty w oparciu o porty konfiguracji klastra
   * Mają otwarte niezbędne porty dla usługi Windows SMB i rejestr zdalny: 135, 137, 138, 139 i 445
   * Łączność sieciowa ze sobą
3. Żadna z maszyn węzła klastra nie powinna być kontrolerem domeny.
4. Jeśli klaster, który ma zostać wdrożony, jest bezpiecznym klastrem, sprawdź poprawność niezbędnych wymagań wstępnych zabezpieczeń i są poprawnie skonfigurowane względem konfiguracji.
5. Jeśli maszyny klastrowe nie są dostępne dla Internetu, w konfiguracji klastra należy ustawić następujące ustawienia:
   * Wyłącz telemetrię: W obszarze *właściwości* zestaw *"enableTelemetry": false*
   * Wyłącz automatyczne pobieranie wersji sieci szkieletowej & powiadomienia, że bieżąca wersja klastra zbliża się do końca pomocy technicznej: W obszarze *właściwości* zestaw *"fabricClusterAutoupgradeEnabled": false*
   * Alternatywnie, jeśli dostęp do Internetu w sieci jest ograniczony do domen z białą notą, do automatycznego uaktualnienia wymagane są poniższe domeny: go.microsoft.com download.microsoft.com

6. Ustaw odpowiednie wykluczenia antywirusowe sieci szkieletowej usług:

| **Katalogi wykluczonych z programu antywirusowego** |
| --- |
| Pliki programów\Sieci szkieletowej usług firmy Microsoft |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Procesy wykluczone z programu antywirusowego** |
| --- |
| Plik Fabric.exe |
| Plik FabricHost.exe |
| Program FabricInstallerService.exe |
| Plik FabricSetup.exe |
| Plik FabricDeployer.exe |
| Plik ImageBuilder.exe |
| Plik FabricGateway.exe |
| Plik FabricDCA.exe |
| Plik FabricFAS.exe |
| Plik FabricUOS.exe |
| Plik FabricRM.exe |
| PlikStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Sprawdzanie poprawności środowiska przy użyciu skryptu konfiguracji testowej
Skrypt TestConfiguration.ps1 można znaleźć w pakiecie autonomicznym. Jest on używany jako analizator najlepszych rozwiązań do sprawdzania poprawności niektórych z powyższych kryteriów i powinny być używane jako sprawdzanie poczytalności, aby sprawdzić, czy klaster można wdrożyć w danym środowisku. Jeśli wystąpi błąd, zapoznaj się z listą w obszarze [Ustawienia środowiska](service-fabric-cluster-standalone-deployment-preparation.md) w celu rozwiązania problemu.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w pliku konfiguracji klastra. Komputer, na który jest uruchamiany ten skrypt, nie musi być częścią klastra.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Obecnie ten moduł testowania konfiguracji nie sprawdza poprawności konfiguracji zabezpieczeń, więc musi to być wykonane niezależnie.

> [!NOTE]
> Nieustannie wprowadzamy ulepszenia, aby ten moduł był bardziej wytrzymały, więc jeśli istnieje uszkodzony lub brakujący przypadek, który, jak uważasz, nie jest obecnie złapany przez TestConfiguration, poinformuj nas o tym za pośrednictwem naszych [kanałów wsparcia.](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)
>
>

## <a name="next-steps"></a>Następne kroki
* [Tworzenie klastra autonomicznego działającego w systemie Windows Server](service-fabric-cluster-creation-for-windows-server.md)
