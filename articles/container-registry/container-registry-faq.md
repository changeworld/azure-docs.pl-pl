---
title: Azure Container Registry — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Container Registry
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: cbdf4f203204df2d26eb7c908167c1e1ca10d0ab
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264321"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Często zadawane pytania dotyczące Azure Container Registry

W tym artykule opisano często zadawane pytania i znane problemy dotyczące Azure Container Registry.

## <a name="resource-management"></a>Zarządzanie zasobami

- [Czy mogę utworzyć usługę Azure Container Registry przy użyciu szablonu Menedżer zasobów?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Czy istnieją luki w zabezpieczeniach dotyczące skanowania obrazów w ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak mogę skonfigurować Kubernetes z Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak mogę uzyskać poświadczenia administratora dla rejestru kontenerów?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak mogę uzyskać poświadczenia administratora w szablonie Menedżer zasobów?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Usuwanie replikacji kończy się niepowodzeniem z niedozwolonym stanem, chociaż replikacja zostanie usunięta przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Reguły zapory zostały pomyślnie zaktualizowane, ale nie zaczną obowiązywać](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Czy można utworzyć Azure Container Registry przy użyciu szablonu Menedżer zasobów?

Tak. Oto [szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) , którego można użyć do utworzenia rejestru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Czy istnieją luki w zabezpieczeniach dotyczące skanowania obrazów w ACR?

Tak. Zapoznaj się z dokumentacją z [TwistLock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) i [akwamaryna](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak mogę skonfigurować Kubernetes z Azure Container Registry?

Zapoznaj się z dokumentacją dotyczącą [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) i kroków [usługi Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak mogę uzyskać poświadczenia administratora dla rejestru kontenerów?

> [!IMPORTANT]
> Konto użytkownika Administrator służy do uzyskiwania dostępu do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Indywidualna tożsamość jest zalecana dla użytkowników i jednostek usługi dla scenariuszy bezobsługowych. Zobacz [Omówienie uwierzytelniania](container-registry-authentication.md).

Przed pobraniem poświadczeń administratora upewnij się, że administrator rejestru jest włączony.

Aby uzyskać poświadczenia przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az acr credential show -n myRegistry
```

Przy użyciu programu Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak mogę uzyskać poświadczenia administratora w szablonie Menedżer zasobów?

> [!IMPORTANT]
> Konto użytkownika Administrator służy do uzyskiwania dostępu do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Indywidualna tożsamość jest zalecana dla użytkowników i jednostek usługi dla scenariuszy bezobsługowych. Zobacz [Omówienie uwierzytelniania](container-registry-authentication.md).

Przed pobraniem poświadczeń administratora upewnij się, że administrator rejestru jest włączony.

Aby pobrać pierwsze hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Aby uzyskać drugie hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Usuwanie replikacji kończy się niepowodzeniem z niedozwolonym stanem, chociaż replikacja zostanie usunięta przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

Ten błąd jest widoczny, gdy użytkownik ma uprawnienia do rejestru, ale nie ma uprawnień na poziomie czytnika do subskrypcji. Aby rozwiązać ten problem, należy przypisać uprawnienia czytelnika do użytkownika:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Reguły zapory zostały pomyślnie zaktualizowane, ale nie zaczną obowiązywać

Propagowanie zmian reguł zapory zajmuje trochę czasu. Po zmianie ustawień zapory poczekaj kilka minut przed zweryfikowaniem tej zmiany.


## <a name="registry-operations"></a>Operacje rejestru

- [Jak mogę dostęp do usługi Docker Registry HTTP API v2?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak mogę usunąć wszystkie manifesty, do których nie odwołują się żadne Tagi w repozytorium?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Dlaczego użycie limitu przydziału rejestru nie jest zmniejszane po usunięciu obrazów?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak mogę sprawdzić poprawność zmian przydziału magazynu?](#how-do-i-validate-storage-quota-changes)
- [Jak mogę uwierzytelniać się przy użyciu mojego rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Czy Azure Container Registry oferować konfigurację tylko TLS v 1.2 i jak włączyć protokół TLS w wersji 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Czy Azure Container Registry obsługuje zaufanie zawartości?](#does-azure-container-registry-support-content-trust)
- [Jak mogę udzielić dostępu do obrazów ściągania lub wypychania bez zezwolenia na zarządzanie zasobem rejestru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak mogę włączyć automatycznej kwarantanny obrazu dla rejestru](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak mogę dostęp do usługi Docker Registry HTTP API v2?

ACR obsługuje protokół HTTP API v2 usługi Docker. Dostęp do interfejsów API można uzyskać pod adresem `https://<your registry login server>/v2/`. Przykład: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak mogę usunąć wszystkie manifesty, do których nie odwołują się żadne Tagi w repozytorium?

Jeśli jesteś w systemie bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Dla programu PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Uwaga: aby pominąć potwierdzenie, można dodać `-y` w poleceniu Delete.

Aby uzyskać więcej informacji, zobacz [usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Dlaczego użycie limitu przydziału rejestru nie jest zmniejszane po usunięciu obrazów?

Taka sytuacja może wystąpić, jeśli nadal istnieją odwołania do warstw bazowych innych obrazów kontenerów. Po usunięciu obrazu bez odwołań w ciągu kilku minut są aktualizowane informacje o użyciu rejestru.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak mogę sprawdzić poprawność zmian przydziału magazynu?

Utwórz obraz z warstwą 1 GB przy użyciu następującego pliku platformy Docker. Dzięki temu obraz ma warstwę, która nie jest udostępniana przez żadne inne obrazy w rejestrze.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Kompiluj i wypchnij obraz do rejestru przy użyciu interfejsu wiersza polecenia platformy Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Powinien być w stanie zobaczyć, że użycie magazynu zostało zwiększone w Azure Portal lub można użyć zapytania przy użyciu interfejsu wiersza polecenia.

```bash
az acr show-usage -n myregistry
```

Usuń obraz przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu i sprawdź zaktualizowane użycie w ciągu kilku minut.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak mogę uwierzytelniać się przy użyciu mojego rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?

Musisz uruchomić kontener interfejsu wiersza polecenia platformy Azure, instalując gniazdo Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

W kontenerze Zainstaluj `docker`:

```bash
apk --update add docker
```

Następnie Uwierzytelnij się w rejestrze:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Czy Azure Container Registry oferować konfigurację tylko TLS v 1.2 i jak włączyć protokół TLS w wersji 1.2?

Tak. Włącz protokół TLS przy użyciu dowolnego ostatniego klienta platformy Docker (wersja 18.03.0 lub nowsza). 

### <a name="does-azure-container-registry-support-content-trust"></a>Czy Azure Container Registry obsługuje zaufanie zawartości?

Tak, możesz używać zaufanych obrazów w Azure Container Registry, ponieważ [notariusz platformy Docker](https://docs.docker.com/notary/getting_started/) został zintegrowany i można go włączyć. Aby uzyskać szczegółowe informacje, zobacz [zaufanie zawartości w Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Gdzie znajduje się plik dla odcisku palca?

W obszarze `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Klucze publiczne i certyfikaty wszystkich ról (z wyjątkiem ról delegowania) są przechowywane w `root.json`.
* Klucze publiczne i certyfikaty roli delegowania są przechowywane w pliku JSON jego roli nadrzędnej (na przykład `targets.json` dla roli `targets/releases`).

Zalecane jest zweryfikowanie tych kluczy publicznych i certyfikatów po ogólnej weryfikacji TUF wykonywanej przez klienta platformy Docker i notariusza.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak mogę udzielić dostępu do obrazów ściągania lub wypychania bez zezwolenia na zarządzanie zasobem rejestru?

Program ACR obsługuje [role niestandardowe](container-registry-roles.md) , które zapewniają różne poziomy uprawnień. W odniesieniu do `AcrPull` i `AcrPush` role umożliwiają użytkownikom ściąganie i/lub wypychanie obrazów bez uprawnień do zarządzania zasobami rejestru na platformie Azure.

* Azure Portal: > rejestru Access Control (IAM) — > Dodaj (wybierz `AcrPull` lub `AcrPush` dla roli).
* Interfejs wiersza polecenia platformy Azure: Znajdź identyfikator zasobu rejestru, uruchamiając następujące polecenie:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Następnie można przypisać rolę `AcrPull` lub `AcrPush` do użytkownika (w poniższym przykładzie jest stosowana `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Lub Przypisz rolę do zasady usługi identyfikowanej za pomocą identyfikatora aplikacji:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Przystawka jest następnie w stanie uwierzytelniać obrazy w rejestrze i uzyskiwać do nich dostęp.

* Aby uwierzytelnić się w rejestrze:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Aby wyświetlić listę repozytoriów:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Aby ściągnąć obraz:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Korzystając z tylko roli `AcrPull` lub `AcrPush`, osoba przydzielona nie ma uprawnień do zarządzania zasobem rejestru na platformie Azure. Na przykład `az acr list` lub `az acr show -n myRegistry` nie będzie wyświetlał rejestru.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak mogę włączyć automatyczne kwarantanny obrazu dla rejestru?

Kwarantanna obrazu jest obecnie funkcją w wersji zapoznawczej ACR. Można włączyć tryb kwarantanny rejestru, aby były widoczne tylko te obrazy, które pomyślnie przekazały skanowanie zabezpieczeń. Aby uzyskać szczegółowe informacje, zobacz [repozytorium GitHub ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Testy diagnostyczne i kondycji

- [Sprawdzanie kondycji przy użyciu `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull nie powiodło się z powodu błędu: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (klient. limit czasu został przekroczony w oczekiwaniu na nagłówki)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [wypychanie Docker kończy się powodzeniem, ale docker pull nie powiodło się z powodu błędu: Brak autoryzacji: wymagane jest uwierzytelnianie](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Włączanie i pobieranie dzienników debugowania demona platformy Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informacje o uwierzytelnianiu nie są nadawane w poprawnym formacie dla wywołań interfejsu API bezpośredniego REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Dlaczego w Azure Portal nie są wystawiane wszystkie repozytoria lub Tagi?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Dlaczego Azure Portal nie można pobrać repozytoriów lub tagów?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Jak mogę zbierać ślady http w systemie Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Sprawdzanie kondycji przy użyciu `az acr check-health`

Aby rozwiązać problemy ze wspólnym środowiskiem i rejestrem, zobacz [Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull nie powiodło się z powodu błędu: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (klient. limit czasu został przekroczony w oczekiwaniu na nagłówki)

 - Jeśli ten błąd jest przejściowy, a następnie ponów próbę.
 - Jeśli `docker pull` nie powiedzie się w sposób ciągły, może wystąpić problem z demonem Docker. Problem można ogólnie wyeliminować przez ponowne uruchomienie demona platformy Docker. 
 - Jeśli ten problem będzie nadal występować po ponownym uruchomieniu demona platformy Docker, problem może być przyczyną niektórych problemów z łącznością sieciową z maszyną. Aby sprawdzić, czy sieć ogólna na komputerze jest w dobrej kondycji, uruchom następujące polecenie, aby przetestować łączność z punktem końcowym. Minimalna wersja `az acr`, która zawiera to polecenie kontroli łączności, to 2.2.9. Uaktualnij interfejs wiersza polecenia platformy Azure, jeśli używasz starszej wersji.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Należy zawsze mieć mechanizm ponawiania prób dla wszystkich operacji klienta platformy Docker.

### <a name="docker-pull-is-slow"></a>Wypychanie platformy Docker jest powolne
Użyj [tego](http://www.azurespeed.com/Azure/Download) narzędzia, aby przetestować szybkość pobierania sieci maszyny. W przypadku powolnej sieci komputerowej należy rozważyć użycie maszyny wirtualnej platformy Azure w tym samym regionie, w którym znajduje się rejestr. Zwykle zapewnia to szybszą szybkość działania sieci.

### <a name="docker-push-is-slow"></a>Wypychanie Docker jest powolne
[To](http://www.azurespeed.com/Azure/Upload) narzędzie służy do testowania szybkości przekazywania w sieci maszynowej. W przypadku powolnej sieci komputerowej należy rozważyć użycie maszyny wirtualnej platformy Azure w tym samym regionie, w którym znajduje się rejestr. Zwykle zapewnia to szybszą szybkość działania sieci.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Wypychanie Docker kończy się powodzeniem, ale docker pull nie powiodło się z powodu błędu: Brak autoryzacji: wymagane jest uwierzytelnianie

Ten błąd może wystąpić z wersją Red Hat demona Docker, gdzie `--signature-verification` jest domyślnie włączona. Opcje demona platformy Docker dla Red Hat Enterprise Linux (RHEL) lub Fedora można sprawdzić, uruchamiając następujące polecenie:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Na przykład Fedora 28 Server ma następujące opcje demona platformy Docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

W przypadku braku `--signature-verification=false` `docker pull` kończy się niepowodzeniem z powodu błędu podobnego do:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Aby rozwiązać ten problem:
1. Dodaj opcję `--signature-verification=false` do pliku konfiguracji demona platformy Docker `/etc/sysconfig/docker`. Na przykład:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Uruchom ponownie usługę Docker demona, uruchamiając następujące polecenie:

  ```bash
  sudo systemctl restart docker.service
  ```

Szczegóły `--signature-verification` można znaleźć, uruchamiając `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Włączanie i pobieranie dzienników debugowania demona platformy Docker  

Rozpocznij `dockerd` z opcją `debug`. Najpierw utwórz plik konfiguracji demona platformy Docker (`/etc/docker/daemon.json`), jeśli nie istnieje, i Dodaj opcję `debug`:

```json
{   
    "debug": true   
}
```

Następnie ponownie uruchom demona. Na przykład z Ubuntu 14,04:

```bash
sudo service docker restart
```

Szczegółowe informacje znajdują się w [dokumentacji platformy Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Dzienniki mogą być generowane w różnych lokalizacjach, w zależności od systemu. Na przykład dla Ubuntu 14,04 jest `/var/log/upstart/docker.log`.   
Szczegóły można znaleźć w [dokumentacji platformy Docker](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * W przypadku Docker for Windows dzienniki są generowane w lokalizacji% LOCALAPPDATA%/Docker/. Jednak może nie zawierać jeszcze wszystkich informacji o debugowaniu.   

   Aby można było uzyskać dostęp do dziennika pełnego demona, może być konieczne wykonanie pewnych dodatkowych czynności:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teraz masz dostęp do wszystkich plików maszyny wirtualnej z uruchomioną `dockerd`. Dziennik ma `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji

Gdy przyznasz nowe uprawnienia (nowe role) do jednostki usługi, zmiana może nie zaczyna obowiązywać natychmiast. Istnieją dwie możliwe przyczyny:

* Opóźnienie przypisania roli Azure Active Directory. Zwykle jest to szybkie, ale może to potrwać kilka minut z powodu opóźnienia propagacji.
* Opóźnienie uprawnień na serwerze tokenów ACR. Może to potrwać do 10 minut. Aby zmniejszyć ryzyko, można `docker logout`, a następnie ponownie uwierzytelnić się przy użyciu tego samego użytkownika po 1 minucie:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Obecnie ACR nie obsługuje usuwania replikacji domowej przez użytkowników. Obejście polega na uwzględnieniu tworzenia w szablonie replikacji domowej, ale pominięcia jej tworzenia przez dodanie `"condition": false`, jak pokazano poniżej:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informacje o uwierzytelnianiu nie są nadawane w poprawnym formacie dla wywołań interfejsu API bezpośredniego REST

Może wystąpić błąd `InvalidAuthenticationInfo`, zwłaszcza przy użyciu narzędzia `curl` z opcją `-L`, `--location` (aby wykonać przekierowania).
Na przykład pobieranie obiektu BLOB przy użyciu `curl` z opcją `-L` i uwierzytelnianie podstawowe:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

może skutkować następującą odpowiedzią:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Główną przyczyną jest to, że niektóre implementacje `curl` są zgodne z nagłówkami z oryginalnego żądania.

Aby rozwiązać ten problem, należy ponownie wykonać przekierowania ręcznie bez nagłówków. Wydrukuj nagłówki odpowiedzi z opcją `-D -` `curl`, a następnie wyodrębnij: nagłówek `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Dlaczego w Azure Portal nie są wystawiane wszystkie repozytoria lub Tagi? 

Jeśli używasz przeglądarki Microsoft Edge/IE, możesz zobaczyć maksymalnie 100 repozytoriów lub tagów. Jeśli Rejestr zawiera więcej niż 100 repozytoriów lub tagów, zalecamy użycie przeglądarki Firefox lub Chrome, aby wyświetlić listę wszystkich elementów.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Dlaczego Azure Portal nie można pobrać repozytoriów lub tagów?

Przeglądarka może nie być w stanie wysłać żądania pobrania repozytoriów lub tagów na serwer. Mogą istnieć różne przyczyny, takie jak:

* Brak łączności sieciowej
* Zapora
* Bloki usługi AD
* Błędy DNS

Skontaktuj się z administratorem sieci lub Sprawdź konfigurację sieci i łączność. Ponadto można również wypróbować sesję incognito lub prywatną w przeglądarce, aby uniknąć nieodświeżonej pamięci podręcznej lub plików cookie przeglądarki.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak mogę zbierać ślady http w systemie Windows?

#### <a name="prerequisites"></a>Wymagania wstępne

- Włącz odszyfrowywanie https w programu Fiddler: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Włącz platformę Docker, aby użyć serwera proxy za pomocą interfejsu użytkownika platformy Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Pamiętaj, aby przywrócić po zakończeniu.  Platforma Docker nie będzie działać z tym włączonym i programu Fiddler nie jest uruchomiona.

#### <a name="windows-containers"></a>Kontenery systemu Windows

Konfigurowanie serwera proxy platformy Docker na adres 127.0.0.1:8888

#### <a name="linux-containers"></a>Kontenery systemu Linux

Znajdź adres IP przełącznika wirtualnego Docker VM:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Skonfiguruj serwer proxy platformy Docker do danych wyjściowych poprzedniego polecenia i portu 8888 (na przykład 10.0.75.1:8888)

## <a name="tasks"></a>Zadania

- [Jak mogę przebiegów zadań wsadowych?](#how-do-i-batch-cancel-runs)
- [Jak mogę uwzględnić folder. git w polecenia AZ ACR Build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Jak mogę przebiegów zadań wsadowych?

Następujące polecenia powodują anulowanie wszystkich uruchomionych zadań w określonym rejestrze.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak mogę uwzględnić folder. git w polecenia AZ ACR Build?

W przypadku przekazania lokalnego folderu źródłowego do polecenia `az acr build` folder `.git` jest domyślnie wykluczony z przekazanego pakietu. Plik `.dockerignore` można utworzyć przy użyciu następującego ustawienia. Instruuje polecenie, aby przywrócić wszystkie pliki w obszarze `.git` w przekazanym pakiecie. 

```
!.git/**
```

To ustawienie dotyczy również polecenia `az acr run`.

## <a name="cicd-integration"></a>Integracja ciągłej integracji/ciągłego wdrażania

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Akcje GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej](container-registry-intro.md) o Azure Container Registry.
