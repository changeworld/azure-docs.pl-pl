# <a name="persist-files-in-azure-cloud-shell"></a>Utrwalanie plików w powłoce chmury Azure
Powłoka chmury wykorzystuje magazyn plików Azure, aby zachować pliki między sesjami.

## <a name="set-up-a-clouddrive-file-share"></a>Konfigurowanie udziału plików clouddrive
Na początkowej start powłoki chmury monituje o skojarzyć udział plików nowego lub istniejącego zachowywane między sesjami plików.

> [!NOTE]
> Bash i programu PowerShell używają tego samego udziału plików. Tylko jeden plik udziału można skojarzyć z automatyczną instalację w powłoce chmury.

### <a name="create-new-storage"></a>Tworzenie nowego magazynu

Po za pomocą podstawowych ustawień i wybierz tylko subskrypcję, powłoki chmury tworzy trzy zasoby w Twoim imieniu w obsługiwanym regionie, najbliższą możesz:
* Grupa zasobów: `cloud-shell-storage-<region>`
* Konto magazynu: `cs<uniqueGuid>`
* Udziału plików: `cs-<user>-<domain>-com-<uniqueGuid>`

![Ustawienia subskrypcji](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Instaluje udziału plików jako `clouddrive` w Twojej `$Home` katalogu. To działanie jednorazowe i udziału plików automatycznie instaluje się w kolejnych sesjach. 

> [!NOTE]
> Dla bezpieczeństwa każdy użytkownik powinien obsługiwać własne magazyn.  Do kontroli dostępu opartej na rolach (RBAC), użytkownicy muszą mieć dostęp współautora lub nowszej.

W Bash, udziału plików zawiera także obraz 5 GB, który jest tworzony której automatycznie będzie się powtarzał danych w sieci `$Home` katalogu. 

### <a name="use-existing-resources"></a>Korzystać z istniejących zasobów

Przy użyciu opcji zaawansowanych, można skojarzyć istniejących zasobów. Gdy zostanie wyświetlony monit konfiguracji magazynu, wybierz **Pokaż zaawansowane ustawienia** Aby wyświetlić dodatkowe opcje. Menu rozwijane są filtrowane pod kątem przypisanej regionu powłoki chmury oraz Magazyn lokalnie nadmiarowy i kont magazynu geograficznie nadmiarowego.

W Bash, istniejących udziałów plików wyświetlany obraz 5 GB utworzone automatycznie utrwalić Twojej `$Home` katalogu.

![Ustawienia grupy zasobów](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Ogranicz tworzenie zasobów przy użyciu zasad zasobów platformy Azure
Konta magazynu, utworzone w chmurze powłoki są oznaczane `ms-resource-usage:azure-cloud-shell`. Jeśli chcesz uniemożliwić użytkownikom tworzenie kont magazynu w chmurze powłoki, Utwórz [zasad zasobów platformy Azure dla tagów](../articles/azure-policy/json-samples.md) wyzwolenia przy tym konkretnym elementem tag.

## <a name="supported-storage-regions"></a>Regiony obsługiwanego magazynu
Skojarzone konta musi znajdować się w tym samym regionie co maszyny powłoki w chmurze, która jest instalowanie, ich do magazynu Azure.

Aby znaleźć przypisaną regionu, użytkownik może:
* Umożliwia wyświetlenie notatki w oknie dialogowym "Zaawansowane magazynu ustawienia"
* Odwołuje się do nazwy konta magazynu utworzone (przykład: `cloud-shell-storage-westus`)
* Uruchom `env` i Znajdź zmiennej `ACC_LOCATION`

Komputer powłoki chmura istnieje w następujących regionach:
|Obszar|Region|
|---|---|
|Ameryki|Wschodnie stany USA, południowo środkowe stany USA, zachodnie stany USA|
|Europa|Europa Północna, Europa Zachodnia|
|Azja i Pacyfik|Indie centralnej, południowo-Azja.|

