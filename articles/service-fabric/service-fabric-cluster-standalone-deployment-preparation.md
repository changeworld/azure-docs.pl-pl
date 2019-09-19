---
title: Przygotowywania wdrożenia klastra autonomicznego usługi platformy Azure w sieci szkieletowej | Dokumentacja firmy Microsoft
description: Dokumentacja związanych z przygotowywaniem środowiska i tworzenia konfiguracji klastra, należy wziąć pod uwagę przed wdrożeniem klastra przeznaczone do obsługi obciążeń produkcyjnych.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: dad37af030c456f9ba2cd814fa92a7811dce6aa1
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130328"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planowanie i przygotowanie wdrożenia klastra usługi Service Fabric autonomiczny

<a id="preparemachines"></a>Przed utworzeniem klastra wykonaj poniższe czynności.

## <a name="plan-your-cluster-infrastructure"></a>Planowanie infrastruktury klastra
Masz zamiar utworzyć klaster usługi Service Fabric na maszynach, gdy jesteś "właścicielem", można zdecydować, jakiego rodzaju błędów ma klaster umożliwiającą przetrwanie. Na przykład czy potrzebujesz oddzielnych, linie zasilania lub połączenia przez Internet do tych maszyn? Ponadto należy wziąć pod uwagę zabezpieczenia fizyczne maszyn. Maszyny lokalizację i który musi mieć dostęp do nich? Po wprowadzeniu tych decyzji, komputery można mapować logicznie do różnych domen błędów (patrz następny krok). W przypadku klastrów produkcyjnych z planowaniem infrastruktury jest bardziej skomplikowane niż w przypadku klastrów testowych.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Określenie liczby domen błędów i uaktualnień
A [ *domena błędów* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) jest miejscem, awarii i jest bezpośrednio związane z infrastrukturą fizyczną w centrach danych. Domena błędów składa się z składniki sprzętowe (komputery, przełączniki, sieci i inne), które współużytkują pojedynczy punkt awarii. Mimo że nie istnieje żadne mapowanie 1:1 między domenami błędów i stojaki, luźno wypowiedzi, każdy Stojak jest uznawana za domeny błędów.

Po określeniu domenach błędów w ClusterConfig.json można wybrać nazwę każdego FD. Usługa Service Fabric obsługuje hierarchiczne błędów, dzięki czemu może odzwierciedlać topologii infrastruktury w nich.  Na przykład poniższy domenami błędów są prawidłowe:

* "faultDomain": "fd: / Komputer1-Room1/szafa1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/szafa1/PDU1 znajdującego/M1"

*Domeny uaktualnień* (UD) jest jednostki logicznej węzłów. Podczas uaktualniania usługi Service Fabric zorganizowanych (uaktualnienie aplikacji lub uaktualniania klastra) wszystkie węzły w UD przechodzą w dół do uaktualnienia węzłów w innych domenach uaktualniania pozostaną dostępne obsłużyć żądania. Aktualizacje oprogramowania układowego, wykonania na maszynach, nie uznają domenami aktualizacji, więc musisz je jednym komputerze w danym momencie.

Najprostszym sposobem Pomyśl o tych pojęciach jest jednostką nieplanowanych awarii i domenach uaktualniania jednostką planowanej konserwacji należy wziąć pod uwagę domenami błędów.

Po określeniu domenami aktualizacji w ClusterConfig.json można wybrać nazwę każdego UD. Na przykład następujące nazwy są prawidłowe:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": Świetlon

Aby uzyskać szczegółowe informacje na temat błędów i domenami aktualizacji, zobacz [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Klastra w środowisku produkcyjnym powinien obejmować co najmniej trzech domenach błędów, aby mogły być obsługiwane w środowisku produkcyjnym, jeśli masz pełną kontrolę nad obsługi i zarządzanie węzłami, oznacza to, że użytkownik jest odpowiedzialny aktualizacji i zastępując maszyn. W przypadku klastrów działających w środowiskach (czyli wystąpienia Amazon Web Services w maszynie Wirtualnej), w których nie masz pełną kontrolę nad maszynami powinien mieć co najmniej 5 domenami błędów w klastrze. Każdy FD może mieć co najmniej jeden węzeł. Ma to zapobiec występowaniu problemów spowodowanych maszyny uaktualnienia i aktualizacje, które mogą zakłócać uruchamianie aplikacji i usług w klastrach w zależności od ich czas trwania.

## <a name="determine-the-initial-cluster-size"></a>Określ rozmiar klastra

Ogólnie rzecz biorąc liczbę węzłów w klastrze jest określana w oparciu o Twoje potrzeby biznesowe, które jest, jak wiele usług i kontenery będą działać w klastrze i ile zasobów należy utrzymywanie obciążeń. W przypadku klastrów produkcyjnych zalecamy posiadanie co najmniej pięciu węzłów w klastrze, obejmujące 5 domenami błędów. Jednak zgodnie z powyższym opisem, jeśli mają pełną kontrolę nad węzły i mogą znajdować się na trzech domenami błędów następnie trzy węzły należy również wykonać zadanie.

Klastrów testowych obciążeń stanowych powinien mieć trzy węzły klastrów testowych tylko uruchamianie tylko w przypadku obciążeń bezstanowych powinny jeden węzeł. Należy również zauważyć, że w celach programistycznych więcej niż jeden węzeł może mieć na danym komputerze. W środowisku produkcyjnym, Usługa Service Fabric obsługuje tylko jeden węzeł na maszynie fizycznej lub wirtualnej.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Przygotowywanie komputerów, które będzie służyć jako węzły

Poniżej przedstawiono niektóre specyfikacje zalecane dla każdego komputera, który chcesz dodać do klastra:

* Co najmniej 16 GB pamięci RAM
* Co najmniej 40 GB dostępnego miejsca na dysku
* 4 rdzenie lub większa procesora CPU
* Łączność z bezpiecznej sieci lub sieci dla wszystkich maszyn
* Zainstalowany system operacyjny Windows Server (prawidłowe wersje: 2012 R2, 2016, 1709 lub 1803). Service Fabric w wersji 6.4.654.9590 i nowszych również obsługuje serwer 2019 i 1809.
* [.NET framework 4.5.1 lub nowszej](https://www.microsoft.com/download/details.aspx?id=40773), pełna instalacja
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* [Usługi RemoteRegistry](https://technet.microsoft.com/library/cc754820) powinna być uruchomiona na wszystkich komputerach
* Dysk instalacji Service Fabric musi mieć system plików NTFS

Administrator klastra, wdrażanie i konfigurowanie klastra musi mieć [uprawnień administratora](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) na poszczególnych maszynach. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Pobieranie pakietu autonomicznego usługi Service Fabric dla systemu Windows Server
[Pobierz Link - pakietu autonomicznego usługi Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) i Rozpakuj pakiet maszyną wdrożenia, który nie jest częścią klastra lub do jednej z maszyn, które będzie częścią klastra.

## <a name="modify-cluster-configuration"></a>Modyfikowanie konfiguracji klastra
Tworzenie klastra autonomicznego należy utworzyć autonomiczny klaster ClusterConfig.json pliku konfiguracji, który opisuje specyfikacji klastra. Można utworzyć pliku konfiguracji na tych szablonach adrese Poniższy link. <br>
[Konfiguracje klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Aby uzyskać szczegółowe informacje na sekcje, w tym pliku, zobacz [ustawienia konfiguracji dla autonomicznego klastra Windows](service-fabric-cluster-manifest.md).

Otwórz jeden z plików ClusterConfig.json z pakietu, które zostały pobrane i zmodyfikować następujące ustawienia:

| **Ustawienia konfiguracji** | **Opis** |
| --- | --- |
| **Elementy NodeType** |Typy węzłów umożliwiają oddzielenie węzły klastra w różnych grupach. Klaster musi mieć co najmniej jeden element NodeType. Wszystkie węzły w grupie mają następujące cechy wspólne: <br> **Nazwa** — jest to nazwa typu węzła. <br>**Porty punktów końcowych** — te mają różne nazwy punktów końcowych (porty), które są skojarzone z tym typem węzła. Można użyć dowolnego numeru portu, chcesz, tak długo, jak nie powodują konfliktów z niczego więcej w tym manifeście i nie są już używane przez wszystkie inne aplikacje uruchomione na maszynie/na maszynie Wirtualnej. <br> **Właściwości umieszczania** — te opisują właściwości dla tego typu węzła używanego jako ograniczeniami dotyczącymi umieszczania dla usług systemu lub usługi. Te właściwości to pary klucz/wartość zdefiniowanych przez użytkownika, które zapewniają dodatkowe metadane dla danego węzła. Przykłady — właściwości węzła byłaby, czy węzeł zawiera dysk twardy lub karty graficznej, od liczby dysków w jego dysku twardym, rdzeni i inne właściwości fizyczne. <br> **Pojemności** — wydajność węzłów zdefiniować nazwę i sumę określonego zasobu, określony węzeł jest dostępne do użycia. Na przykład węzeł mogą określić, czy ma ona wydajności dla metryki o nazwie "MemoryInMb" i ma dostępne 2048 MB, domyślnie. Te możliwości są używane w czasie wykonywania, aby upewnić się, że usługi, które wymagają określonej ilości zasobów są umieszczane w węzły, które mają te zasoby, które są dostępne w ilości wymaganych.<br>**IsPrimary** — Jeśli masz więcej niż jeden element NodeType zdefiniowane upewnij się, że tylko jedna jest ustawiona na podstawowej z wartością *true*, czyli, gdzie system usług przebiegu. Wszystkie pozostałe typy węzła powinna być równa wartości *false* |
| **Węzły** |Oto szczegóły dla każdego z węzłów, które są częścią klastra (typ węzła, nazwa węzła, adres IP, domeny błędów i domeny uaktualnień węzła). Komputery mają klastra, który ma zostać utworzony na potrzeby pojawić się tutaj za pomocą ich adresów IP. <br> Jeśli używasz tego samego adresu IP dla wszystkich węzłów klastra jednopunktowe tworzona jest, które służy do celów testowych. Nie używaj klastrów jednopunktowe wdrażania obciążeń produkcyjnych. |

Po konfiguracji klastra miał wszystkie ustawienia skonfigurowane w środowisku, może zostać przetestowany przeciwko środowisku klastra (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Konfiguracja środowiska

Gdy administrator klastra umożliwia skonfigurowanie klastra autonomicznego usługi Service Fabric, środowisko musi można skonfigurować przy użyciu następujących kryteriów: <br>
1. Użytkownik tworzący klaster powinien mieć uprawnienia zabezpieczeń na poziomie administratora do wszystkich maszyn, które są wymienione jako węzły w pliku konfiguracji klastra.
2. Komputer, z którego został utworzony klaster, a także każdego komputera węzła klastra musi:
   * Odinstalowano zestaw SDK usługi Service Fabric
   * Środowiska uruchomieniowego usługi Service Fabric odinstalowane 
   * Czy włączono usługę Windows zapory (mpssvc)
   * Czy włączono usługę Rejestr zdalny (Rejestr zdalny)
   * Plik włączenia udostępniania serwera (SMB)
   * Masz wymagane porty otwarte, oparta na portach konfiguracji klastra
   * Należy otworzyć wymagane porty dla usługi Windows SMB i rejestru zdalnego: 135, 137, 138, 139 i 445
   * Mieć łączność sieciową ze sobą
3. Żadna z maszyn węzeł klastra powinien być kontrolerem domeny.
4. Jeśli można wdrożyć klaster jest zabezpieczony klaster, sprawdź poprawność niezbędne wymagania wstępne znajdują się w miejscu i są poprawnie skonfigurowane dla konfiguracji zabezpieczeń.
5. Jeśli na komputerach klastra nie są dostępne za pośrednictwem Internetu, należy ustawić następującą w konfiguracji klastra:
   * Wyłącz telemetrię: W obszarze *Właściwości* ustaw wartość *"enableTelemetry": false*
   * Wyłącz pobieranie automatycznej wersji sieci szkieletowej & powiadomienia, że aktualna wersja klastra zbliża się do końca wsparcia: W obszarze *Właściwości* ustaw wartość *"fabricClusterAutoupgradeEnabled": false*
   * Alternatywnie dostęp do sieci internet jest ograniczona do biały na liście domen, domen poniżej są wymagane dla automatyczne uaktualnianie: witrynie download.microsoft.com go.microsoft.com

6. Ustaw odpowiednie wyjątki programu antywirusowego usługi Service Fabric:

| **Antywirusowe wykluczone katalogi** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Antywirusowe wykluczone procesy** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Weryfikowanie środowiska za pomocą skryptu TestConfiguration
Skrypt TestConfiguration.ps1 można znaleźć w pakiecie autonomicznym. On używany jako Analizator najlepszych rozwiązań do weryfikowania niektórych z powyższych kryteriów i powinny służyć jako wyboru poprawnością można sprawdzić, czy można wdrożyć klaster w danym środowisku. Jeśli istnieje jakikolwiek błąd, można znaleźć na liście w obszarze [konfiguracji środowiska](service-fabric-cluster-standalone-deployment-preparation.md) do rozwiązywania problemów. 

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w pliku konfiguracji klastra. Komputer, na którym uruchamiany jest skrypt nie musi być częścią klastra.

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

Obecnie tego modułu do testowania konfiguracji nie sprawdza poprawności konfiguracji zabezpieczeń, więc to odbywać się niezależnie.  

> [!NOTE]
> Nieustannie wprowadzamy ulepszenia się ten moduł działał on bardziej niezawodnie, więc w przypadku przypadek uszkodzone lub brakuje który uważasz, że nie jest aktualnie przechwycony przez TestConfiguration, Daj nam znać za pośrednictwem naszego [obsługuje kanałów](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie klastra autonomicznego w systemie Windows Server](service-fabric-cluster-creation-for-windows-server.md)
