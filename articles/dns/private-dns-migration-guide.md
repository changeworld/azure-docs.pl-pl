---
title: Migrowanie starszych stref prywatnych dns platformy Azure do nowego modelu zasobów
titleSuffix: Azure DNS
description: Ten przewodnik zawiera instrukcje krok po kroku dotyczące migracji starszych prywatnych stref DNS do najnowszego modelu zasobów
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939352"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrowanie starszych stref prywatnych usługi Azure DNS do nowego modelu zasobów

Podczas publicznej wersji zapoznawczej prywatne strefy DNS zostały utworzone przy użyciu zasobu "dnszones" z właściwością "zoneType" ustawioną na "Private". Takie strefy nie będą obsługiwane po 31 grudnia 2019 r. i muszą zostać przeniesione do modelu zasobów GA, który korzysta z typu zasobu "privateDnsZones" zamiast "dnszones". Proces migracji jest prosty i udostępniliśmy skrypt programu PowerShell do automatyzacji tego procesu. Ten przewodnik zawiera instrukcje krok po kroku, aby przeprowadzić migrację stref prywatnych usługi Azure DNS do nowego modelu zasobów.

Aby dowiedzieć się, dnszones zasobów, które wymagają migracji; wykonaj poniższe polecenie w usłudze Azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zainstalowano najnowszą wersję programu Azure PowerShell. Aby uzyskać więcej informacji na temat programu Azure PowerShell (Az) i jak go zainstalować, odwiedź stronęhttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Upewnij się, że masz zainstalowany moduł Az.PrivateDns dla programu Azure PowerShell. Aby zainstalować ten moduł, otwórz podwyższone okno programu PowerShell (tryb administracyjny) i wprowadź następujące polecenie

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Proces migracji jest w pełni zautomatyzowany i nie powinien powodować żadnych przestojów. Jeśli jednak używasz stref prywatnych usługi Azure DNS (wersja zapoznawcza) w krytycznym środowisku produkcyjnym, należy wykonać następujący proces migracji podczas planowanego przedziału czasu konserwacji. Upewnij się, że nie modyfikujesz konfiguracji ani zestawów rekordów prywatnych stref DNS podczas uruchamiania skryptu migracji.

## <a name="installing-the-script"></a>Instalowanie skryptu

Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień (tryb administracyjny) i uruchom następujące polecenie

```powershell
install-script PrivateDnsMigrationScript
```

Wpisz "A" po wyświetleniu monitu o zainstalowanie skryptu

![Instalowanie skryptu](./media/private-dns-migration-guide/install-migration-script.png)

Można również ręcznie uzyskać najnowszą wersję skryptu programu PowerShell pod adresemhttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Skrypt migracji nie może być uruchamiany w powłoki chmury platformy Azure i musi być wykonywany na maszynie wirtualnej lub komputerze lokalnym podłączonym do Internetu.

## <a name="running-the-script"></a>Uruchamianie skryptu

Wykonaj następujące polecenie, aby uruchomić skrypt

```powershell
PrivateDnsMigrationScript.ps1
```

![Uruchamianie skryptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Wprowadzanie identyfikatora subskrypcji i logowanie się na platformie Azure

Zostanie wyświetlony monit o wprowadzenie identyfikatora subskrypcji zawierającego prywatne strefy DNS, które mają zostać poddane migracji. Zostaniesz poproszony o zalogowanie się na swoje konto platformy Azure. Wypełnij logowanie, aby skrypt mógł uzyskać dostęp do prywatnych zasobów strefy DNS w ramach subskrypcji.

![Logowanie na platformie Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Wybierz strefy DNS, które chcesz przeprowadzić migrację

Skrypt z pobierz listę wszystkich prywatnych stref DNS w subskrypcji i monituj o potwierdzenie, które z nich chcesz przeprowadzić migrację. Wprowadź "A", aby przeprowadzić migrację wszystkich prywatnych stref DNS. Po wykonaniu tego kroku skrypt utworzy nowe prywatne strefy DNS przy użyciu nowego modelu zasobów i skopiuje dane do nowej strefy DSN. Ten krok nie spowoduje zmiany istniejących prywatnych stref DNS w każdym razie.

![Wybieranie stref DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Przełączanie rozpoznawania DNS do nowych stref DNS

Po skopiowaniu stref i rekordów do nowego modelu zasobów skrypt wyświetli monit o przełączenie rozpoznawania DNS na nowe strefy DNS. Ten krok usuwa skojarzenia między starszymi prywatnymi strefami DNS a sieciami wirtualnymi. Gdy starsza strefa jest odłączona od sieci wirtualnych, nowe strefy DNS utworzone w powyższym kroku automatycznie przejmą rozdzielczość DNS dla tych sieci wirtualnych.

Wybierz "A", aby przełączyć rozdzielczość DNS dla wszystkich sieci wirtualnych.

![Rozpoznawanie nazw przełączania](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Weryfikowanie rozpoznawania systemu DNS

Przed kontynuowaniem sprawdź, czy rozpoznawanie dns w strefach DNS działa zgodnie z oczekiwaniami. Można zalogować się do maszyn wirtualnych platformy Azure i wystawić zapytanie nslookup względem migrowanych stref, aby sprawdzić, czy rozpoznawanie dns działa.

![Weryfikowanie rozpoznawania nazw](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Jeśli okaże się, że kwerendy DNS nie są rozwiązujące, poczekaj kilka minut i ponów próbę kwerend. Jeśli kwerendy DNS działają zgodnie z oczekiwaniami, wprowadź "Y", gdy skrypt wyświetli monit o usunięcie sieci wirtualnej z prywatnej strefy DNS.

![Potwierdź rozpoznawanie nazw](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Jeśli z jakiegokolwiek powodu rozpoznawanie DNS względem migrowanych stref nie działa zgodnie z oczekiwaniami, wprowadź "N" w powyższym kroku, a skrypt przełączy rozdzielczość DNS z powrotem na starsze strefy. Utwórz bilet pomocy technicznej, a my pomożemy Ci w migracji stref DNS.

## <a name="cleanup"></a>Czyszczenie

Ten krok spowoduje usunięcie starszych stref DNS i powinny być wykonywane tylko po sprawdzeniu, że rozpoznawanie DNS działa zgodnie z oczekiwaniami. Zostanie wyświetlony monit o usunięcie każdej prywatnej strefy DNS. Wprowadź "Y" przy każdym wierszu po sprawdzeniu, czy rozpoznawanie nazw DNS dla tych stref działa poprawnie.

![Czyszczenie](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Zaktualizuj swoją automatyzację

Jeśli używasz automatyzacji, w tym szablonów, skryptów programu PowerShell lub kodu niestandardowego opracowanego przy użyciu pakietu SDK, należy zaktualizować automatyzację, aby użyć nowego modelu zasobów dla prywatnych stref DNS. Poniżej znajdują się łącza do nowej prywatnej dokumentacji DNS CLI/PS/SDK.
* [Interfejs API REST stref prywatnych usługi Azure DNS](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Ustawienia wiersza polecenia stref prywatnych usługi Azure DNS](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Strefy prywatne usługi Azure DNS programu PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Zestaw SDK stref prywatnych usługi Azure DNS](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potrzebujesz dalszej pomocy

Utwórz bilet pomocy technicznej, jeśli potrzebujesz dalszej pomocy w procesie migracji lub z jakiegokolwiek powodu powyższe kroki nie działają dla Ciebie. Dołącz plik transkrypcji wygenerowany przez skrypt programu PowerShell z biletem pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](./private-dns-getstarted-cli.md)

* Przeczytaj o niektórych typowych [scenariuszach strefy prywatnej,](./private-dns-scenarios.md) które można zrealizować za pomocą stref prywatnych w usłudze Azure DNS.

* Aby uzyskać typowe pytania i odpowiedzi dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie, jakich można oczekiwać w przypadku niektórych rodzajów operacji, zobacz [często zadawane pytania dotyczące prywatnego DNS](./dns-faq-private.md).

* Dowiedz się więcej o strefach i rekordach DNS, odwiedzając [omówienie stref i rekordów DNS](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
