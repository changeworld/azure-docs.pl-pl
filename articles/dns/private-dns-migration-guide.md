---
title: Migrowanie starszej wersji usługi Azure DNS Private Zones do nowego modelu zasobów
description: Ten przewodnik zawiera instrukcje krok po kroku dotyczące sposobu przeprowadzenia migracji starszych prywatnych stref DNS do najnowszego modelu zasobów
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276097"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrowanie starszej wersji strefami prywatnymi usługi Azure DNS do nowego modelu zasobów

Firma Microsoft dostarczane nowy model zasobu/interfejsu API w przypadku stref prywatnych w usłudze Azure DNS jako część wersji zapoznawczej odświeżania. Odświeżanie (wersja zapoznawcza) zawiera nowe funkcje i usuwa kilka ograniczeń i ograniczenia początkowej publicznej wersji zapoznawczej. Jednak te korzyści nie są dostępne w prywatnych stref DNS, które zostały utworzone przy użyciu starszej wersji interfejsu API. Aby uzyskać korzyści wynikające z nowej wersji, należy przeprowadzić migrację starszych zasobów strefy prywatnej DNS do nowego modelu zasobów. Proces migracji jest prosty i udostępniliśmy skrypt programu PowerShell, aby zautomatyzować ten proces. Ten przewodnik zawiera instrukcje krok po kroku migracji strefami prywatnymi usługi Azure DNS do nowego modelu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zainstalowano najnowszą wersję programu Azure PowerShell. Aby uzyskać więcej informacji na temat programu Azure PowerShell (Az) i jak je zainstalować, odwiedź stronę https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Upewnij się, zostało Az.PrivateDns modułu dla zainstalowany program Azure PowerShell. Aby zainstalować ten moduł, Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień (w trybie administracyjnych), a następnie wprowadź następujące polecenie

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Proces migracji jest w pełni zautomatyzowane i nie powinien powodować żadnych przestojów. Jednak jeśli używasz strefami prywatnymi usługi Azure DNS (wersja zapoznawcza) w środowisku produkcyjnym krytyczne powinien wykonaniu poniżej proces migracji przedziale czasu planowanej konserwacji. Upewnij się, że możesz nie należy modyfikować konfiguracji lub zestawy rekordów prywatnych stref DNS podczas, gdy używasz skryptu migracji.

## <a name="installing-the-script"></a>Skrypt instalacji

Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień (w trybie administracyjnych) i uruchom następujące polecenie

```powershell
install-script PrivateDnsMigrationScript
```

Wprowadź "A", po wyświetleniu monitu o skrypt instalacji

![Skrypt instalacji](./media/private-dns-migration-guide/install-migration-script.png)

Można również ręcznie uzyskać najnowszą wersję skrypt PowerShell pod https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Uruchomienie skryptu

Wykonaj następujące polecenie, aby uruchomić skrypt

```powershell
PrivateDnsMigrationScript.ps1
```

![Uruchomienie skryptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Wprowadź identyfikator subskrypcji i zaloguj się do platformy Azure

Zostanie wyświetlony monit o podanie Identyfikatora subskrypcji, zawierający prywatnych stref DNS, które zamierzasz migrować. Zostanie wyświetlony monit logowania do konta platformy Azure. Ukończ logowania, czemu skrypt może uzyskiwać dostęp do zasobów prywatnych stref DNS w ramach subskrypcji.

![Logowanie na platformie Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Wybierz stref DNS, które mają zostać zmigrowane

Skrypt za pomocą uzyskać listę wszystkich prywatnych stref DNS w ramach subskrypcji i wyświetlenie monitu o potwierdzenie te, które mają zostać zmigrowane. Wprowadź "A", aby przeprowadzić migrację wszystkich prywatnych stref DNS. Po wykonaniu tego kroku, skrypt utworzy nowy prywatnych stref DNS przy użyciu nowego modelu zasobów i skopiować dane do nowej strefy DSN. Ten krok nie ma wpływu na istniejących prywatnych stref DNS w mimo to.

![Wybierz stref DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Przełączanie rozpoznawania nazw DNS do nowych stref DNS

Po strefy i rekordy zostały skopiowane do nowego modelu zasobu, skrypt wyświetli monit Przełącz rozpoznawania nazw DNS do nowej strefy DNS. Ten krok usuwa skojarzenie między starszych prywatnych stref DNS i sieci wirtualnych. Kiedy odłączone od sieci wirtualnych jest strefa starszej wersji, nowych stref DNS utworzoną w kroku będzie automatycznie przejąć rozpoznawania nazw DNS dla tych sieci wirtualnych.

Wybierz "A", aby przełączyć rozpoznawania nazw DNS dla wszystkich sieci wirtualnych.

![Przełączanie rozpoznawania nazw](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Sprawdź rozpoznawanie nazw DNS

Przed kontynuacją sprawdź, czy rozpoznawanie nazw DNS na stref DNS działa zgodnie z oczekiwaniami. Użytkownik może zalogować się do maszyn wirtualnych platformy azure i problem nslookup zapytania względem zmigrowanych strefy, aby sprawdzić, że rozpoznawanie nazw DNS działa.

![Sprawdzanie rozpoznawania nazw](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Jeśli okaże się, że nie są rozpoznawania zapytań DNS, poczekaj kilka minut i ponów próbę wykonania zapytania. Jeśli zapytania DNS działają w oczekiwany sposób, wprowadź "Y", gdy skrypt monituje o usunięcie sieć wirtualną z prywatnej strefy DNS.

![Upewnij się, rozpoznawanie nazw](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Jeśli z dowolnej przyczyny DNS rozwiązania dla migrowanych stref nie działa zgodnie z oczekiwaniami, wprowadź "n" w powyżej kroku i skrypt spowoduje przełączenie rozpoznawania nazw DNS do strefy starszej wersji. Utwórz bilet pomocy technicznej i możemy pomóc Ci przy migracji stref DNS.

## <a name="cleanup"></a>Czyszczenie

Ten krok spowoduje usunięcie starszych stref DNS i powinny być wykonywane tylko wtedy, gdy masz już pewność, że rozpoznawanie nazw DNS działa zgodnie z oczekiwaniami. Zostanie wyświetlony monit usunąć każdy prywatnej strefy DNS. Po sprawdzeniu, czy rozpoznawanie nazw DNS dla tej strefy działa prawidłowo, wpisz "Y" w każdym wierszu.

![Czyszczenie](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualizuj automatyzacji

Jeśli używasz usługi automation, takie jak szablony, skrypty programu PowerShell lub niestandardowy kod opracowany przy użyciu zestawu SDK, należy zaktualizować automatyzacji na potrzeby nowego modelu zasobów prywatnych stref DNS. Poniżej znajdują się linki do nowych prywatnych DNS interfejsu wiersza polecenia/PS/dokumentacji zestawu SDK.
* [Usługa system DNS Azure prywatnych stref interfejsu API REST](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Strefami prywatnymi usługi Azure DNS interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Strefami prywatnymi usługi Azure DNS programu PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Strefami prywatnymi usługi Azure DNS zestawu SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potrzebujesz więcej pomocy

Utwórz bilet pomocy technicznej, jeśli możesz dodatkowo potrzebujesz pomocy podczas procesu migracji lub z powodu jakiegoś powyższych kroków wymienionych nie działają dla Ciebie. Dołącz plik transkrypcji wygenerowane przez skrypt programu PowerShell przy użyciu biletu pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak utworzyć prywatną strefę w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Przeczytaj o niektórych typowych [scenariusze prywatnej strefy](./private-dns-scenarios.md) , które można realizować ze strefami prywatnymi w usłudze Azure DNS.

* Typowe pytania i odpowiedzi na pytania dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania do prywatnej DNS](./dns-faq-private.md).

* Więcej informacji na temat stref i rekordów DNS, odwiedzając [DNS strefy i rekordy Przegląd](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
