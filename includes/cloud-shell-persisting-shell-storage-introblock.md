# <a name="persist-files-in-azure-cloud-shell"></a>Utrwalanie plików w usłudze Azure Cloud Shell
Usługa cloud Shell korzysta z usługi Azure File storage, aby utrwalać pliki między sesjami.

## <a name="set-up-a-clouddrive-file-share"></a>Konfigurowanie udziału plików clouddrive
W menu start początkowej usługi Cloud Shell monituje o skojarzyć udział plików nowej lub istniejącej utrwalanie plików między sesjami.

> [!NOTE]
> Udostępnij ten sam udział plików, bash, jak i programu PowerShell. Tylko jednego udziału plików można skojarzyć z automatyczne instalowanie w usłudze Cloud Shell.

### <a name="create-new-storage"></a>Tworzenie nowego magazynu

Gdy korzystasz z podstawowych ustawień i wybierz tylko subskrypcję, usługa Cloud Shell tworzy trzy zasoby w Twoim imieniu w obsługiwanym regionie znajdującym się najbliżej Twojej lokalizacji:
* Grupa zasobów: `cloud-shell-storage-<region>`
* Konto magazynu: `cs<uniqueGuid>`
* Udział plików: `cs-<user>-<domain>-com-<uniqueGuid>`

![Ustawienia subskrypcji](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Udział plików na komputerze instalująca jako `clouddrive` w swojej `$Home` katalogu. Jest to akcja jednorazowa, a udział plików automatycznie instaluje się w kolejnych sesjach. 

> [!NOTE]
> Aby zapewnić bezpieczeństwo każdy użytkownik zainicjować obsługę administracyjną własnego konta magazynu.  Dla kontroli dostępu opartej na rolach (RBAC) użytkownicy muszą prawa dostępu współautora lub powyżej w magazynie konta poziomu.

W powłoce Bash, udziału plików zawiera także obraz 5 GB, który jest tworzony który automatycznie utrzymuje danych w Twojej `$Home` katalogu. 

### <a name="use-existing-resources"></a>Korzystać z istniejących zasobów

Za pomocą zaawansowanych opcji, należy skojarzyć istniejących zasobów. Gdy zostanie wyświetlony monit o konfiguracji magazynu, wybierz pozycję **Pokaż zaawansowane ustawienia** Aby wyświetlić dodatkowe opcje. Menu rozwijane zostały przefiltrowane pod kątem przypisanej regionu usługi Cloud Shell i Magazyn lokalnie nadmiarowy i kont usługi storage geograficznie nadmiarowy.

Udziały plików wyświetlany obraz 5 GB do utrwalenia swoje `$Home` katalogu.

![Ustawienie Grupa zasobów](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Ogranicz tworzenie zasobów przy użyciu zasad zasobów platformy Azure
Konta magazynu, które tworzysz w usłudze Cloud Shell są oznaczane za pomocą `ms-resource-usage:azure-cloud-shell`. Jeśli chcesz uniemożliwić użytkownikom tworzenie kont magazynu w usłudze Cloud Shell, Utwórz [zasady zasobów platformy Azure dla tagów](../articles/azure-policy/json-samples.md) , są wyzwalane przez to określony tag.

## <a name="supported-storage-regions"></a>Obsługiwane regiony
Skojarzonych kont muszą znajdować się w tym samym regionie, co komputer Cloud Shell, na którym jest zainstalowanie, ich do magazynu platformy Azure.

Aby znaleźć swój region przypisany użytkownik może:
* Wyświetl notatki w oknie dialogowym "Zaawansowane miejsca do magazynowania"
* Odnoszą się do nazwy konta magazynu utworzonego dla Ciebie (np: `cloud-shell-storage-westus`)
* Uruchom `env` i Znajdź zmienną `ACC_LOCATION`

Cloud Shell maszyn istnieją w następujących regionach:
|Obszar|Region|
|---|---|
|Ameryki|Wschodnie stany USA, południowo-środkowe stany USA, zachodnie stany USA|
|Europa|Europa Północna, Europa Zachodnia|
|Azja i Pacyfik|Indie środkowe, południowo-Azja Wschodnia|

