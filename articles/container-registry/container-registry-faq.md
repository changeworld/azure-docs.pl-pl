---
title: Usługa Azure Container Registry — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Container Registry
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: beeb4986750e398071e3afb6c1f04663f858cec1
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303573"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Często zadawane pytania dotyczące usługi Azure Container Registry

W tym artykule opisano często zadawane pytania i znane problemy dotyczące usługi Azure Container Registry.

## <a name="resource-management"></a>Zarządzanie zasobami

- [Można utworzyć rejestr kontenerów platformy Azure przy użyciu szablonu usługi Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Czy istnieje luki w zabezpieczeniach, wyszukiwanie obrazów w rejestru Azure container Registry?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak skonfigurować usługi Kubernetes za pomocą usługi Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak uzyskać poświadczenia administratora dla rejestru container registry?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak uzyskać poświadczeń administratora w szablonie usługi Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Usunięcie replikacji nie powiodło się ze stanem zabronione mimo, że replikacja zostaje usunięte przy użyciu wiersza polecenia platformy Azure lub programu Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Można utworzyć usługi Azure Container Registry przy użyciu szablonu usługi Resource Manager?

Tak. Oto [szablonu](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) służące do tworzenia rejestru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Czy istnieje luki w zabezpieczeniach, wyszukiwanie obrazów w rejestru Azure container Registry?

Tak. Zobacz dokumentację z [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) i [Akwamaryna](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak skonfigurować usługi Kubernetes za pomocą usługi Azure Container Registry?

Zobacz dokumentację [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) i kroki pod kątem [usługi Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak uzyskać poświadczenia administratora dla rejestru container registry?

> [!IMPORTANT]
> Konta użytkownika administratora jest przeznaczony dla jednego użytkownika, dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora z wieloma użytkownikami. Indywidualne tożsamości jest zalecana dla użytkowników i nazwy główne usług dla bezobsługowego scenariuszy. Zobacz [omówienie uwierzytelniania](container-registry-authentication.md).

Przed pobraniem poświadczenia administratora, upewnij się, że włączono administratora w rejestrze.

Aby uzyskać poświadczenia za pomocą wiersza polecenia platformy Azure:

```azurecli
az acr credential show -n myRegistry
```

Używanie programu Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak uzyskać poświadczeń administratora w szablonie usługi Resource Manager?

> [!IMPORTANT]
> Konta użytkownika administratora jest przeznaczony dla jednego użytkownika, dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora z wieloma użytkownikami. Indywidualne tożsamości jest zalecana dla użytkowników i nazwy główne usług dla bezobsługowego scenariuszy. Zobacz [omówienie uwierzytelniania](container-registry-authentication.md).

Przed pobraniem poświadczenia administratora, upewnij się, że włączono administratora w rejestrze.

Aby wyświetlić pierwsze hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Aby uzyskać drugi hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Usunięcie replikacji nie powiodło się ze stanem zabronione mimo, że replikacja zostaje usunięte przy użyciu wiersza polecenia platformy Azure lub programu Azure PowerShell

Ten błąd występuje po użytkownik ma uprawnienia do rejestru, ale nie ma uprawnienia na poziomie czytelnika na subskrypcję. Aby rozwiązać ten problem, należy przypisać uprawnienia czytelnika subskrypcji do użytkownika:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Operacje rejestru

- [Jak uzyskać dostęp do platformy Docker Registry HTTP interfejsu API 2?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak usunąć wszystkich manifestów, które nie są przywoływane przez żadnych tagów w repozytorium?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Dlaczego użycie przydziału w rejestrze nie powoduje zmniejszenia po usunięciu obrazów?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak zweryfikować zmiany limitu przydziału magazynu?](#how-do-i-validate-storage-quota-changes)
- [Jak uwierzytelnić za pomocą rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Usługa Azure Container Registry oferuje protokołu TLS 1.2 tylko konfiguracji i jak włączyć protokół TLS 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Usługa Azure Container Registry obsługuje ufaj zawartości?](#does-azure-container-registry-support-content-trust)
- [Jak udzielić dostępu do ściągania lub wypychania obrazów bez zgody, aby zarządzać zasobem rejestru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak włączyć kwarantanny automatyczne obrazu do rejestru](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak uzyskać dostęp do platformy Docker Registry HTTP interfejsu API 2?

Rejestru Azure container Registry obsługuje Docker Registry HTTP interfejsu API w wersji 2. Interfejsy API mogą być wyświetlane w `https://<your registry login server>/v2/`. Przykład: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak usunąć wszystkich manifestów, które nie są przywoływane przez żadnych tagów w repozytorium?

Jeśli w programie bash uruchomionym:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Dla programu Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Uwaga: Możesz dodać `-y` w poleceniu delete, aby pominąć potwierdzenia.

Aby uzyskać więcej informacji, zobacz [usuwanie obrazów kontenerów w usłudze Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Dlaczego użycie przydziału w rejestrze nie powoduje zmniejszenia po usunięciu obrazów?

Ta sytuacja może wystąpić, jeśli niższych warstwach nadal odwołują się inne obrazy kontenera. Jeśli usuniesz obrazu z żadnych odwołań, użycie rejestru aktualizuje się w ciągu kilku minut.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak zweryfikować zmiany limitu przydziału magazynu?

Tworzenie obrazu z warstwą 1GB, przy użyciu następującego pliku platformy docker. Zapewnia to, czy obraz ma warstwy, która nie jest udostępniony przez innego obrazu, w rejestrze.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Skompiluj i Wypchnij obraz do rejestru przy użyciu interfejsu wiersza polecenia platformy docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Powinien być można zobaczyć, że użycie magazynu wydłużył się w witrynie Azure portal lub możesz zbadać użycia za pomocą interfejsu wiersza polecenia.

```bash
az acr show-usage -n myregistry
```

Usuń obraz przy użyciu wiersza polecenia platformy Azure lub portalu i Sprawdź użycie zaktualizowane w ciągu kilku minut.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak uwierzytelnić za pomocą rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?

Musisz uruchomić kontener wiersza polecenia platformy Azure przez zainstalowanie gniazda platformy Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

W kontenerze, należy zainstalować `docker`:

```bash
apk --update add docker
```

Następnie uwierzytelnianie za pomocą rejestru:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Usługa Azure Container Registry oferuje protokołu TLS 1.2 tylko konfiguracji i jak włączyć protokół TLS 1.2?

Tak. Włącz szyfrowanie TLS przy użyciu najnowszych klienta platformy docker (wersja 18.03.0 i nowsze wersje). 

### <a name="does-azure-container-registry-support-content-trust"></a>Usługa Azure Container Registry obsługuje ufaj zawartości?

Tak, możesz użyć zaufanych obrazów w usłudze Azure Container Registry, od [notariusz Docker](https://docs.docker.com/notary/getting_started/) został zintegrowany i można ją włączyć. Aby uzyskać więcej informacji, zobacz [ufaj zawartości w usłudze Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Gdzie znajduje się w pliku o odcisku palca, znajduje się?

W obszarze `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Klucze publiczne i certyfikaty wszystkie role (z wyjątkiem delegowania ról) są przechowywane w `root.json`.
* Klucze publiczne i certyfikaty delegacja roli są przechowywane w pliku JSON z jej nadrzędną rolę (na przykład `targets.json` dla `targets/releases` roli).

Zalecane jest, aby sprawdzić te klucze publiczne i certyfikatów po ogólnej weryfikacji TUF wykonywane przez klienta platformy Docker i notariusz.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak udzielić dostępu do ściągania lub wypychania obrazów bez zgody, aby zarządzać zasobem rejestru?

Rejestru Azure container Registry obsługuje [ról niestandardowych](container-registry-roles.md) dostarczające różnych poziomów uprawnień. W szczególności `AcrPull` i `AcrPush` role umożliwiają użytkownikom do ściągania i/lub wypychania obrazów bez zgody, aby zarządzać zasobem rejestru na platformie Azure.

* Witryna Azure Portal: Rejestr -> Kontrola dostępu (IAM) -> Dodaj (wybierz `AcrPull` lub `AcrPush` roli).
* Interfejs wiersza polecenia platformy Azure: Znajdź identyfikator zasobu rejestru, uruchamiając następujące polecenie:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Możesz przypisać `AcrPull` lub `AcrPush` roli do użytkownika (w poniższym przykładzie użyto `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Można także przypisać rolę do jednostki usługi o identyfikatorze aplikacji:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Osoby przypisanej jest możliwość uwierzytelniania i dostępu do obrazów w rejestrze.

* Do uwierzytelniania do rejestru:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Do listy repozytoriów:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Aby ściągnąć obraz:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Przy użyciu tylko `AcrPull` lub `AcrPush` roli, assignee nie ma uprawnienia do zarządzania zasób rejestru na platformie Azure. Na przykład `az acr list` lub `az acr show -n myRegistry` nie będzie zawierać rejestru.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak włączyć kwarantanny automatyczne obrazu do rejestru?

Obraz kwarantanny jest obecnie w wersji zapoznawczej z rejestru Azure container Registry. Można włączyć tryb kwarantanny, rejestru, tak aby tylko obrazy, które pomyślnie przeszły skanowania zabezpieczeń są widoczne dla użytkowników normalnego. Aby uzyskać więcej informacji, zobacz [repozytorium GitHub usługi ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnostyka

- [polecenie docker pull nie powiodło się z powodu błędu: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (Client.Timeout przekroczony podczas oczekiwania na nagłówki)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push zakończy się pomyślnie, ale polecenie docker pull nie powiodło się z powodu błędu: nieautoryzowanych: wymagane jest uwierzytelnianie](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Włączanie i pobieranie dzienników debugowania demona platformy docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nowe uprawnienia użytkownika może nie obowiązywać natychmiast po zaktualizowaniu](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informacje dotyczące uwierzytelniania nie zostanie podany w prawidłowym formacie na bezpośrednie wywołania interfejsu API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Dlaczego witryna Azure portal nie zawiera wszystkich repozytoriów lub tagi?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Jak zbierać ślady protokołu http na Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>polecenie docker pull nie powiodło się z powodu błędu: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (Client.Timeout przekroczony podczas oczekiwania na nagłówki)

 - Jeśli ten błąd jest przejściowy problem, a następnie ponów próbę zakończy się powodzeniem.
 - Jeśli `docker pull` zakończy się niepowodzeniem, może to być problem z demona platformy docker. Zazwyczaj można wyeliminować problem, ponownie uruchomić demona platformy docker. 
 - Jeśli nadal widzisz ten problem, po ponownym uruchomieniu demona platformy docker, problem może być pewne problemy z połączeniem sieciowym z maszyną. Aby sprawdzić, jeśli ogólny sieci na maszynie jest w dobrej kondycji, spróbuj wykonać polecenie takich jak `ping www.bing.com`.
 - Zawsze należy mechanizm ponawiania prób dla wszystkich operacji klienta platformy docker.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push zakończy się pomyślnie, ale polecenie docker pull nie powiodło się z powodu błędu: nieautoryzowanych: wymagane jest uwierzytelnianie

Ten błąd może wystąpić z wersją firmy Red Hat demona platformy docker, gdzie `--signature-verification` jest domyślnie włączona. Opcje demona platformy docker dla usługi Red Hat Enterprise Linux (RHEL) lub Fedora można sprawdzić, uruchamiając następujące polecenie:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Na przykład serwer 28 Fedora ma następujące opcje demona platformy docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Za pomocą `--signature-verification=false` brakować `docker pull` kończy się niepowodzeniem ze względu na błąd podobny do:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Aby naprawić błąd:
1. Dodaj opcję `--signature-verification=false` do pliku konfiguracji demona platformy docker `/etc/sysconfig/docker`. Na przykład:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Uruchom ponownie usługi demona platformy docker, uruchamiając następujące polecenie:

  ```bash
  sudo systemctl restart docker.service
  ```

Szczegółowe informacje o `--signature-verification` można znaleźć, uruchamiając `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Włączanie i pobieranie dzienników debugowania demona platformy docker  

Rozpocznij `dockerd` z `debug` opcji. Najpierw utwórz plik konfiguracji demona platformy docker (`/etc/docker/daemon.json`) Jeśli nie istnieje i Dodaj `debug` opcji:

```json
{   
    "debug": true   
}
```

Następnie należy ponownie uruchomić demona. Na przykład w przypadku systemu Ubuntu 14.04:

```bash
sudo service docker restart
```

Szczegółowe informacje można znaleźć w [dokumentacja platformy Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Dzienniki mogą być generowane w różnych lokalizacjach, w zależności od używanego systemu. Na przykład Ubuntu 14.04 w nim `/var/log/upstart/docker.log`.   
Zobacz [dokumentacja platformy Docker](https://docs.docker.com/engine/admin/#read-the-logs) Aby uzyskać szczegółowe informacje.    

 * Docker for Windows w obszarze % LOCALAPPDATA%/docker/ są generowane dzienniki. Jednak nie może zawierać wszystkie informacje o debugowaniu jeszcze.   

   Aby uzyskać dostęp do dziennika pełnej demona, może być konieczne kilka dodatkowych kroków:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teraz masz dostęp do wszystkich plików maszyny Wirtualnej z systemem `dockerd`. Dziennik jest na `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nowe uprawnienia użytkownika może nie obowiązywać natychmiast po zaktualizowaniu

Przy udzielaniu nowe uprawnienia (nowe role) do nazwy głównej usługi, zmiana może nie obowiązywać natychmiast. Istnieją dwie możliwe przyczyny:

* Opóźnienie przypisania roli usługi Azure Active Directory. Zwykle jest szybkie, ale może potrwać minut z powodu opóźnienia propagacji.
* Opóźnienie uprawnień na serwerze tokenu usługi ACR. Może to potrwać do 10 minut. Aby rozwiązać problem, można `docker logout` i następnie ponowne uwierzytelnienie przy użyciu tego samego użytkownika po 1 minutę:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Obecnie rejestru Azure container Registry nie obsługuje usuwania replikacji macierzystego przez użytkowników. Obejście polega na obejmują macierzystego replikacji utworzyć w szablonie, ale Pomiń jej tworzenia, dodając `"condition": false` jak pokazano poniżej:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informacje dotyczące uwierzytelniania nie zostanie podany w prawidłowym formacie na bezpośrednie wywołania interfejsu API REST

Możesz napotkać `InvalidAuthenticationInfo` błąd, szczególnie przy użyciu `curl` narzędzia z opcją `-L`, `--location` (aby wykonaj przekierowania).
Na przykład, pobieranie, obiekt blob przy użyciu `curl` z `-L` opcji i uwierzytelnianie podstawowe:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

może spowodować następującą odpowiedź:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Główną przyczyną jest to, że niektóre `curl` implementacje wykonaj przekierowania z nagłówkami oryginalne żądanie.

Aby rozwiązać ten problem, należy wykonać przekierowuje ręcznie bez nagłówków. Drukuj nagłówki odpowiedzi z `-D -` opcji `curl` , a następnie wyodrębnić: `Location` nagłówka:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Dlaczego witryna Azure portal nie zawiera wszystkich repozytoriów lub tagi? 

Jeśli używasz przeglądarki Microsoft Edge widać, co najwyżej 100 repozytoriów lub tagi, na liście. Jeśli rejestr ma więcej niż 100 repozytoriów lub tagi, firma Microsoft zaleca, użyj przeglądarki Firefox lub Chrome przeglądarki, aby je wszystkie wymienić.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak zbierać ślady protokołu http na Windows?

#### <a name="prerequisites"></a>Wymagania wstępne

- Włącz odszyfrowywania protokołu https w narzędziu fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Włącz Docker, aby użyć serwera proxy przy użyciu interfejsu użytkownika platformy Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Pamiętaj przywrócić po zakończeniu.  Dzięki włączeniu tej opcji jest nieodpowiedni platformy docker i nie jest uruchomiony program fiddler.

#### <a name="windows-containers"></a>Kontenery systemu Windows

Skonfiguruj serwer proxy 127.0.0.1:8888 platformy Docker

#### <a name="linux-containers"></a>Kontenery systemu Linux

Znajdź ip platformy Docker przełącznika wirtualnego maszyny wirtualnej:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Skonfiguruj serwer proxy platformy Docker w danych wyjściowych poprzedniego polecenia i port 8888 (na przykład 10.0.75.1:8888)

## <a name="tasks"></a>Zadania

- [Jak usługi batch uruchamia Anuluj](#how-do-i-batch-cancel-runs)
- [Jak dołączyć .git folder az acr build polecenia?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Jak usługi batch uruchamia Anuluj

Następujące polecenia anulować wszystkie uruchomione zadania określonego rejestru.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak dołączyć .git folder az acr build polecenia?

W przypadku przekazania do folderu lokalnego źródła `az acr build` polecenia `.git` folderu jest wykluczony z przekazanym pakiecie domyślnie. Możesz utworzyć `.dockerignore` plików z następującymi ustawieniami. Informuje on polecenie, aby przywrócić wszystkie pliki w `.git` w przekazanym pakiecie. 

```
!.git/**
```

To ustawienie ma zastosowanie także do `az acr run` polecenia.

## <a name="cicd-integration"></a>Integracja ciągła Integracja/ciągłe dostarczanie

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Akcje usługi GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="error-references-for-az-acr-check-health"></a>Błąd odwołania do `az acr check-health`

### <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że ten klient platformy docker dla interfejsu wiersza polecenia nie można odnaleźć, co wyklucza wyszukiwania wersji platformy docker, oceny stanu demona platformy docker i zapewnienie, że można uruchomić polecenia ściągania platformy docker.

*Potencjalne rozwiązania*: Instalowanie klienta platformy docker; Dodawanie ścieżka platformy docker do zmiennych systemowych.

### <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stanu demona platformy docker jest niedostępna lub że go nie można osiągnąć przy użyciu interfejsu wiersza polecenia. Oznacza to, że operacje platformy docker (np. nazwy logowania, ściągnięcia) będzie niedostępne za pośrednictwem interfejsu wiersza polecenia.

*Potencjalne rozwiązania*: Ponownie uruchom demona platformy docker lub sprawdzić, czy jest zainstalowany poprawnie.

### <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie był w stanie uruchomić polecenie `docker --version`.

*Potencjalne rozwiązania*: spróbuj ręcznie uruchomić polecenie, upewnij się, jest zainstalowana najnowsza wersja interfejsu wiersza polecenia i zbadaj komunikat o błędzie.

### <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że interfejsu wiersza polecenia nie był w stanie ściągnąć obraz próbki do środowiska.

*Potencjalne rozwiązania*: Zweryfikuj, że wszystkie składniki niezbędne do ściągania obrazu działają prawidłowo.

### <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć tego klienta helm przez interfejs wiersza polecenia, co wyklucza inne operacje narzędzia helm.

*Potencjalne rozwiązania*: Sprawdź, że narzędzia helm klient jest zainstalowany, a jego ścieżka jest dodawany do zmiennych środowiskowych systemu.

### <a name="helmversionerror"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może ustalić zainstalowaną wersję narzędzia Helm. Może się to zdarzyć, jeśli w wersji wiersza polecenia platformy Azure (lub, jeśli wersja narzędzia helm) używana jest przestarzały.

*Potencjalne rozwiązania*: aktualizacji do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub do wersji zalecane helm; ręcznie uruchom polecenie i zbadaj komunikat o błędzie.

### <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że nazwy DNS dla serwera logowania rejestru danego był za pomocą polecenia ping, ale nie odpowiada, co oznacza, że jest ona niedostępna. Może to oznaczać, że niektóre problemy z łącznością. To także oznaczać, że rejestru nie istnieje, czy użytkownik ma uprawnienia w rejestrze (Aby prawidłowo pobrać jego serwer logowania), lub że rejestru docelowego jest w chmurze innej niż ta, używany w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania*: sprawdzania poprawności łączności; Sprawdź pisownię rejestru i rejestru, że istnieje; Sprawdź, czy użytkownik ma odpowiednie uprawnienia na nim i chmury w rejestrze jest taki sam, który jest używany w interfejsie wiersza polecenia platformy Azure.

### <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Oznacza to, czy punkt końcowy wyzwanie dla danego rejestru odpowiedziała, zgłaszając 403 stan HTTP jest zabronione. Oznacza to, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej z powodu konfiguracji sieci Wirtualnej.

*Potencjalne rozwiązania*: usuwanie reguł sieci Wirtualnej lub dodać bieżący adres IP klienta do listy dozwolonych.

### <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że żądania punktu końcowego rejestru docelowego nie wystawił wyzwanie.

*Potencjalne rozwiązania*: spróbuj ponownie po pewnym czasie. Jeśli błąd będzie się powtarzać, otwórz problem am w https://aka.ms/acr/issues.

### <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwanie rejestru docelowego wystawione żądanie, ale rejestru nie obsługuje logowania usługi AAD.

*Potencjalne rozwiązania*: spróbuj inny sposób rejestrowania w programie, np. poświadczenia administratora. W przypadku, gdy użytkownik chce, aby zalogować się przy użyciu usługi AAD pomocy technicznej, otwórz problem am w https://aka.ms/acr/issues.

### <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Oznacza to, że adres serwera logowania rejestru nie odpowiedział przy użyciu tokenu odświeżania, co oznacza, że nastąpiła odmowa dostępu do rejestru docelowego. Może to nastąpić, jeśli użytkownik nie ma odpowiednich uprawnień w rejestrze lub poświadczenia użytkownika dla wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia w rejestrze; uruchomienie `az login` odświeżania uprawnień, tokenów i poświadczeń.

### <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Oznacza to, że adres serwera logowania rejestru nie odpowiedział przy użyciu tokenu dostępu, co oznacza, że nastąpiła odmowa dostępu do rejestru docelowego. Może to nastąpić, jeśli użytkownik nie ma odpowiednich uprawnień w rejestrze lub poświadczenia użytkownika dla wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia w rejestrze; uruchomienie `az login` odświeżania uprawnień, tokenów i poświadczeń.

### <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Oznacza to, że interfejs wiersza polecenia nie może znaleźć serwera logowania rejestru danego i nie domyślnym sufiksem znaleziono dla bieżącej chmury. Może to nastąpić, jeśli rejestru nie istnieje, jeśli użytkownik ma odpowiednie uprawnienia w rejestrze, jeśli w rejestrze chmury i bieżącej chmury wiersza polecenia platformy Azure nie są zgodne lub wersji wiersza polecenia platformy Azure jest przestarzały.

*Potencjalne rozwiązania*: Sprawdź, że pisownia jest poprawna i czy rejestru istnieje; Sprawdź, jeśli użytkownik ma odpowiednie uprawnienia w rejestrze oraz czy odpowiadają chmury rejestru i środowiska interfejsu wiersza polecenia; zaktualizować wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej](container-registry-intro.md) o usłudze Azure Container Registry.