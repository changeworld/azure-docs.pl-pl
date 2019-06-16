---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125217"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Utrwalanie plików w usłudze Azure Cloud Shell
Usługa cloud Shell korzysta z usługi Azure File storage, aby utrwalać pliki między sesjami. W menu start początkowej usługi Cloud Shell monituje o skojarzyć udział plików nowej lub istniejącej utrwalanie plików między sesjami.

> [!NOTE]
> Udostępnij ten sam udział plików, bash, jak i programu PowerShell. Tylko jednego udziału plików można skojarzyć z automatyczne instalowanie w usłudze Cloud Shell.

## <a name="create-new-storage"></a>Tworzenie nowego magazynu

Gdy korzystasz z podstawowych ustawień i wybierz tylko subskrypcję, usługa Cloud Shell tworzy trzy zasoby w Twoim imieniu w obsługiwanym regionie znajdującym się najbliżej Twojej lokalizacji:
* Grupa zasobów: `cloud-shell-storage-<region>`
* Konto magazynu: `cs<uniqueGuid>`
* Udział plików: `cs-<user>-<domain>-com-<uniqueGuid>`

![Ustawienia subskrypcji](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Udział plików na komputerze instalująca jako `clouddrive` w swojej `$Home` katalogu. Jest to akcja jednorazowa, a udział plików automatycznie instaluje się w kolejnych sesjach. 

> [!NOTE]
> Aby zapewnić bezpieczeństwo każdy użytkownik zainicjować obsługę administracyjną własnego konta magazynu.  Dla kontroli dostępu opartej na rolach (RBAC) użytkownicy muszą prawa dostępu współautora lub powyżej w magazynie konta poziomu.

Udział plików zawiera także obraz 5 GB, który jest tworzony który automatycznie utrzymuje danych w Twojej `$Home` katalogu. Dotyczy to zarówno w przypadku powłoki Bash, jak i programu PowerShell.

## <a name="use-existing-resources"></a>Korzystać z istniejących zasobów

Za pomocą zaawansowanych opcji, należy skojarzyć istniejących zasobów. Podczas wybierania regionu usługi Cloud Shell, musisz wybrać konto magazynu zapasowego wspólnie w tym samym regionie. Na przykład jeśli przypisany regionu zachodnie stany USA, niż należy skojarzyć udział plików, które również znajdują się w regionie zachodnie stany USA.

Gdy zostanie wyświetlony monit o konfiguracji magazynu, wybierz pozycję **Pokaż zaawansowane ustawienia** Aby wyświetlić dodatkowe opcje. Filtr opcje wypełnione magazynu dla magazynu lokalnie nadmiarowego (LRS), Magazyn geograficznie nadmiarowy (GRS) i konta magazynu strefowo nadmiarowego (ZRS). 

> [!NOTE]
> Za pomocą GRS lub ZRS kont magazynu są zalecane w przypadku dodatkową odporność zapasowy udziału plików. Jakiego typu nadmiarowości zależy od tego, cele i preferencji ceny. [Dowiedz się więcej na temat opcji replikacji dla kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Ustawienie Grupa zasobów](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Obsługiwane regiony
Skojarzonych kont muszą znajdować się w tym samym regionie, co komputer Cloud Shell, na którym jest zainstalowanie, ich do magazynu platformy Azure. Można znaleźć usługi bieżącego regionu, możesz uruchomić `env` w powłoce Bash i Znajdź zmienną `ACC_LOCATION`. Udziały plików wyświetlany obraz 5 GB do utrwalenia swoje `$Home` katalogu.

Cloud Shell maszyn istnieją w następujących regionach:

|Obszar|Region|
|---|---|
|Ameryki|Wschodnie stany USA, południowo-środkowe stany USA, zachodnie stany USA|
|Europa|Europa Północna, Europa Zachodnia|
|Azja i Pacyfik|Indie środkowe, południowo-Azja Wschodnia|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Ogranicz tworzenie zasobów przy użyciu zasad zasobów platformy Azure
Konta magazynu, które tworzysz w usłudze Cloud Shell są oznaczane za pomocą `ms-resource-usage:azure-cloud-shell`. Jeśli chcesz uniemożliwić użytkownikom tworzenie kont magazynu w usłudze Cloud Shell, Utwórz [zasady zasobów platformy Azure dla tagów](../articles/azure-policy/json-samples.md) , są wyzwalane przez to określony tag.
