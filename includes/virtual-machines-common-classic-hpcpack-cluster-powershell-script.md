---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484867"
---
W zależności od środowiska i opcje skryptu można utworzyć cała infrastruktura klastra, w tym sieci wirtualnej platformy Azure, konta magazynu, usługi w chmurze, kontroler domeny, lokalnej lub zdalnej bazy danych SQL, węzeł główny i dodatkowe węzły klastra. Alternatywnie skryptu można już istniejącą infrastrukturę platformy Azure i tworzyć węzły klastra HPC.

Aby uzyskać ogólne informacje o planowaniu klastra pakietu HPC Pack, zobacz [wersja ewaluacyjna produktu i planowanie](https://technet.microsoft.com/library/jj899596.aspx) i [wprowadzenie](https://technet.microsoft.com/library/jj899590.aspx) zawartości w bibliotece TechNet systemu HPC Pack 2012 R2.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**: W usłudze Azure Global lub chińska wersja platformy Azure, można skorzystać z subskrypcji. Limitów subskrypcji wpływa na liczbę i rodzaj węzłów klastra, które można wdrożyć. Aby uzyskać informacje, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../articles/azure-subscription-service-limits.md).
* **Komputer kliencki Windows przy użyciu programu Azure PowerShell 0.8.10 lub nowszej zainstalowany i skonfigurowany**: Zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azureps-cmdlets-docs) Aby uzyskać instrukcje dotyczące instalacji i kroki nawiązać połączenie z subskrypcją platformy Azure.
* **Skryptem wdrażania IaaS pakietu HPC Pack**: Pobierz i Rozpakuj najnowszej wersji skryptu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Sprawdź wersję skryptu, uruchamiając `New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.5.2 skryptu.
* **Plik konfiguracji skryptu**: Utwórz plik XML, używany przez skrypt do konfigurowania klastra HPC. Aby uzyskać informacje i przykłady zobacz sekcje w dalszej części tego artykułu i plik Manual.rtf, który towarzyszy skrypt wdrożenia.

## <a name="syntax"></a>Składnia
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Uruchom skrypt z uprawnieniami administratora.
> 
> 

### <a name="parameters"></a>Parametry
* **ConfigFile**: Określa ścieżkę pliku pliku konfiguracji do opisania klastra HPC. Zobacz więcej informacji na temat pliku konfiguracji, w tym temacie lub w pliku Manual.rtf z folderu zawierającego skrypt.
* **AdminUserName**: Określa nazwę użytkownika. Jeśli lasu domen jest tworzona przy użyciu skryptu, staje się on nazwą użytkownika administratora lokalnego dla wszystkich maszyn wirtualnych i nazwę administratora domeny. Jeśli lasu domeny już istnieje, to określa użytkownika domeny jako nazwa użytkownika administratora lokalnego, aby zainstalować pakiet HPC Pack.
* **AdminPassword**: Określa hasło administratora. Jeśli nie zostanie określony w wierszu polecenia, skrypt monituje o wprowadź hasło.
* **HPCImageName** (opcjonalnie): Określa nazwę obrazu maszyny Wirtualnej pakietu HPC Pack, które są używane do wdrażania klastra HPC. Musi być obrazem firmy Microsoft HPC Pack z witryny Azure Marketplace. Jeśli nie zostanie określony (zazwyczaj zalecane), skrypt wybiera najnowszej opublikowanej [obraz pakietu HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). Najnowszego obrazu jest oparty na systemie Windows Server 2012 R2 Datacenter z HPC Pack 2012 R2 Update 3.
  
  > [!NOTE]
  > Wdrożenie zakończy się niepowodzeniem, jeśli nie określisz prawidłowego obrazu pakietu HPC Pack.
  > 
  > 
* **Plik dziennika** (opcjonalnie): Określa ścieżkę pliku dziennika wdrażania. Jeśli nie zostanie określony, skrypt tworzy plik dziennika w katalogu tymczasowym komputera, uruchamiając skrypt.
* **Wymuś** (opcjonalnie): Pomija wszystkie monity o potwierdzenie.
* **NoCleanOnFailure** (opcjonalnie): Określa, czy nie są usuwane maszyny wirtualne platformy Azure, które nie zostały pomyślnie wdrożone. Ręcznie usunąć te maszyny wirtualne przed ponowne uruchomienie skryptu, aby kontynuować wdrażanie lub wdrożenie może zakończyć się niepowodzeniem.
* **PSSessionSkipCACheck** (opcjonalnie): Dla każdej usługi w chmurze przy użyciu maszyn wirtualnych wdrożonych przy użyciu tego skryptu certyfikat z podpisem własnym jest generowany automatycznie przez platformę Azure, a wszystkie maszyny wirtualne w usłudze w chmurze, użycie tego certyfikatu jako domyślnego certyfikatu Windows Remote Management (WinRM). Aby wdrożyć funkcje HPC na tych maszynach wirtualnych platformy Azure, skrypt domyślnie po tymczasowo zainstaluje te certyfikaty na komputerze lokalnym\\magazynu Zaufane główne urzędy certyfikacji na komputerze klienckim, aby pominąć "zaufany urząd certyfikacji" Błąd zabezpieczeń Podczas wykonywania skryptu. Certyfikaty są usuwane po zakończeniu działania skryptu. Jeśli ten parametr jest określony, certyfikaty nie są zainstalowane na komputerze klienckim i pominąć to ostrzeżenie o zabezpieczeniach.
  
  > [!IMPORTANT]
  > Ten parametr nie jest zalecane w przypadku wdrożeń produkcyjnych.
  > 
  > 

### <a name="example"></a>Przykład
Poniższy przykład obejmuje tworzenie klastra pakietu HPC Pack za pomocą pliku konfiguracji *MyConfigFile.xml*i określa poświadczenia administratora dla instalacji klastra.

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Dodatkowe zagadnienia
* Skrypt można opcjonalnie włączyć przesyłanie zadań za pośrednictwem portalu sieci web pakietu HPC Pack lub interfejsu API REST pakietu HPC.
* Skrypt Opcjonalnie można uruchomić skrypty przed i po konfiguracji w węźle głównym, jeśli chcesz zainstalować dodatkowe oprogramowanie lub skonfigurować inne ustawienia.

## <a name="configuration-file"></a>Plik konfiguracji
Plik konfiguracyjny dla skryptu wdrożenia to plik XML. Plik schematu HPCIaaSClusterConfig.xsd znajduje się w folderze skryptu wdrażania IaaS pakietu HPC Pack. **IaaSClusterConfig** jest głównym elementem pliku konfiguracji, który zawiera elementy potomne, które opisano szczegółowo w pliku Manual.rtf w folderze skryptu wdrażania.

