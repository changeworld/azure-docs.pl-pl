---
title: Często zadawane pytania
description: Odpowiedzi na często zadawane pytania związane z usługą Azure Container Registry
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 7452b5dd3c952a13a28566914d2fe513689d4751
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618792"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Często zadawane pytania dotyczące rejestru kontenerów platformy Azure

Ten artykuł dotyczy często zadawanych pytań i znanych problemów dotyczących rejestru kontenerów platformy Azure.

## <a name="resource-management"></a>Zarządzanie zasobami

- [Czy można utworzyć rejestr kontenerów platformy Azure przy użyciu szablonu Menedżera zasobów?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Czy istnieje luka w zabezpieczeniach skanowania obrazów w acr?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak skonfigurować usługi Kubernetes za pomocą rejestru kontenerów platformy Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak uzyskać poświadczenia administratora dla rejestru kontenerów?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak uzyskać poświadczenia administratora w szablonie Menedżera zasobów?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Usuwanie replikacji kończy się niepowodzeniem ze stanem Zakazane, chociaż replikacja zostanie usunięta przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Reguły zapory są aktualizowane pomyślnie, ale nie wchodzą w życie](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Czy mogę utworzyć rejestr kontenerów platformy Azure przy użyciu szablonu Menedżera zasobów?

Tak. Oto [szablon,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) którego można użyć do utworzenia rejestru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Czy istnieje luka w zabezpieczeniach skanowania obrazów w acr?

Tak. Zapoznaj się z dokumentacją [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) i [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak skonfigurować usługi Kubernetes za pomocą rejestru kontenerów platformy Azure?

Zobacz dokumentację [kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) i kroki dla [usługi Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak uzyskać poświadczenia administratora dla rejestru kontenerów?

> [!IMPORTANT]
> Konto użytkownika administratora jest przeznaczone dla pojedynczego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Indywidualna tożsamość jest zalecana dla użytkowników i podmiotów usługi dla scenariuszy bezgłowy. Zobacz [omówienie uwierzytelniania](container-registry-authentication.md).

Przed uzyskaniem poświadczeń administratora upewnij się, że jest włączony administrator rejestru.

Aby uzyskać poświadczenia przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az acr credential show -n myRegistry
```

Korzystanie z programu Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak uzyskać poświadczenia administratora w szablonie Menedżera zasobów?

> [!IMPORTANT]
> Konto użytkownika administratora jest przeznaczone dla pojedynczego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora wielu użytkownikom. Indywidualna tożsamość jest zalecana dla użytkowników i podmiotów usługi dla scenariuszy bezgłowy. Zobacz [omówienie uwierzytelniania](container-registry-authentication.md).

Przed uzyskaniem poświadczeń administratora upewnij się, że jest włączony administrator rejestru.

Aby uzyskać pierwsze hasło:

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Usuwanie replikacji kończy się niepowodzeniem ze stanem Zakazane, chociaż replikacja zostanie usunięta przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell

Błąd jest widoczny, gdy użytkownik ma uprawnienia do rejestru, ale nie ma uprawnień na poziomie czytnika w ramach subskrypcji. Aby rozwiązać ten problem, przypisz użytkownikowi uprawnienia programu Reader do subskrypcji:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Reguły zapory są aktualizowane pomyślnie, ale nie wchodzą w życie

Propagowanie zmian reguł zapory zajmuje trochę czasu. Po zmianie ustawień zapory poczekaj kilka minut przed zweryfikowaniem tej zmiany.


## <a name="registry-operations"></a>Operacje rejestru

- [Jak uzyskać dostęp do interfejsu API HTTP w 2 rejestru platformy Docker?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak usunąć wszystkie manifesty, do których nie odwołuje się żaden znacznik w repozytorium?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Dlaczego użycie przydziału rejestru nie zmniejsza się po usunięciu obrazów?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak sprawdzić poprawność zmian przydziału magazynu?](#how-do-i-validate-storage-quota-changes)
- [Jak uwierzytelnić się w rejestrze podczas uruchamiania interfejsu wiersza polecenia w kontenerze?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Jak włączyć TLS 1.2?](#how-to-enable-tls-12)
- [Czy usługa Azure Container Registry obsługuje zaufanie do zawartości?](#does-azure-container-registry-support-content-trust)
- [Jak udzielić dostępu do obrazów ściągania lub wypychania bez uprawnień do zarządzania zasobem rejestru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak włączyć automatyczną kwarantannę obrazu dla rejestru?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Jak włączyć anonimowy dostęp do ściągania?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak uzyskać dostęp do interfejsu API HTTP w 2 rejestru platformy Docker?

Protokół ACR obsługuje interfejs API HTTP w wersji 2 rejestru platformy Docker. Dostęp do interfejsów API `https://<your registry login server>/v2/`można uzyskać pod adresem . Przykład: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak usunąć wszystkie manifesty, do których nie odwołuje się żaden znacznik w repozytorium?

Jeśli jesteś na bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Dla programu Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Uwaga: Możesz `-y` dodać polecenie delete, aby pominąć potwierdzenie.

Aby uzyskać więcej informacji, zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Dlaczego użycie przydziału rejestru nie zmniejsza się po usunięciu obrazów?

Taka sytuacja może się zdarzyć, jeśli warstwy bazowe są nadal odwoływane przez inne obrazy kontenerów. Jeśli usuniesz obraz bez odwołań, użycie rejestru zostanie zaktualizowane w ciągu kilku minut.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak sprawdzić poprawność zmian przydziału magazynu?

Utwórz obraz z warstwą 1 GB przy użyciu następującego pliku docker. Gwarantuje to, że obraz ma warstwę, która nie jest współużytkowana przez żaden inny obraz w rejestrze.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Tworzenie i wypychanie obrazu do rejestru przy użyciu interfejsu wiersza polecenia platformy docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Powinieneś być w stanie zobaczyć, że użycie magazynu wzrosła w witrynie Azure portal lub można kwerendy użycia przy użyciu interfejsu wiersza polecenia.

```azurecli
az acr show-usage -n myregistry
```

Usuń obraz przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu i sprawdź zaktualizowane użycie w ciągu kilku minut.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak uwierzytelnić się w rejestrze podczas uruchamiania interfejsu wiersza polecenia w kontenerze?

Kontener interfejsu wiersza polecenia platformy Azure należy uruchomić, montując gniazdo platformy Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

W pojemniku `docker`zainstaluj:

```bash
apk --update add docker
```

Następnie uwierzytelnij się w rejestrze:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Jak włączyć TLS 1.2?

Włącz TLS 1.2 przy użyciu dowolnego aktualnego klienta platformy docker (wersja 18.03.0 i nowsze). 

> [!IMPORTANT]
> Od 13 stycznia 2020 r. usługa Azure Container Registry będzie wymagać wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z protokołu TLS 1.2. Obsługa protokołu TLS 1.0 i 1.1 zostanie wycofana.

### <a name="does-azure-container-registry-support-content-trust"></a>Czy usługa Azure Container Registry obsługuje zaufanie do zawartości?

Tak, można użyć zaufanych obrazów w rejestrze kontenerów platformy Azure, ponieważ [notariusz platformy Docker](https://docs.docker.com/notary/getting_started/) został zintegrowany i można go włączyć. Aby uzyskać szczegółowe informacje, zobacz [Zaufanie do zawartości w rejestrze kontenerów platformy Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Gdzie znajduje się plik odcisku palca?

W `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`obszarze :

* Klucze publiczne i certyfikaty wszystkich ról (z `root.json`wyjątkiem ról delegowania) są przechowywane w pliku .
* Klucze publiczne i certyfikaty roli delegowania są przechowywane w pliku `targets.json` JSON jego roli nadrzędnej (na przykład dla `targets/releases` roli).

Zaleca się, aby zweryfikować te klucze publiczne i certyfikaty po ogólnej weryfikacji TUF wykonane przez klienta platformy Docker i notariusza.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak udzielić dostępu do obrazów ściągania lub wypychania bez uprawnień do zarządzania zasobem rejestru?

Usługa ACR obsługuje [role niestandardowe,](container-registry-roles.md) które zapewniają różne poziomy uprawnień. W szczególności `AcrPull` `AcrPush` i role umożliwiają użytkownikom ściąganie i/lub wypychanie obrazów bez uprawnień do zarządzania zasobem rejestru na platformie Azure.

* Azure portal: Twój rejestr -> kontroli dostępu (IAM) -> `AcrPull` Add `AcrPush` (Select lub for the Role).
* Narzędzie CLI platformy Azure: znajdź identyfikator zasobu rejestru, uruchamiając następujące polecenie:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Następnie można przypisać `AcrPull` `AcrPush` lub rolę do użytkownika (w `AcrPull`poniższym przykładzie używa):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Lub przypisz rolę do zasady usługi identyfikowane przez jego identyfikator aplikacji:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Cesjonariusz może następnie uwierzytelniać i uzyskiwać dostęp do obrazów w rejestrze.

* Aby uwierzytelnić się w rejestrze:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Aby wyświetlić listę repozytoriów:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Aby wyciągnąć obraz:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Przy użyciu tylko `AcrPull` lub `AcrPush` roli, cesjonariusz nie ma uprawnień do zarządzania zasobem rejestru na platformie Azure. Na przykład `az acr list` `az acr show -n myRegistry` lub nie będzie wyświetlać rejestru.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak włączyć automatyczną kwarantannę obrazu dla rejestru?

Kwarantanna obrazu jest obecnie funkcją podglądu ACR. Można włączyć tryb kwarantanny rejestru, tak aby tylko te obrazy, które pomyślnie przeszły skanowanie zabezpieczeń były widoczne dla zwykłych użytkowników. Aby uzyskać szczegółowe informacje, zobacz [repozytorium ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Jak włączyć anonimowy dostęp do ściągania?

Konfigurowanie rejestru kontenerów platformy Azure dla dostępu ściągania anonimowego (publicznego) jest obecnie funkcją w wersji zapoznawczej. Aby umożliwić publiczny dostęp, otwórz https://aka.ms/acr/support/create-ticketbilet pomocy technicznej pod adresem . Aby uzyskać szczegółowe informacje, zobacz [Forum opinii platformy Azure](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries).


## <a name="diagnostics-and-health-checks"></a>Diagnostyka i kontrole zdrowia

- [Sprawdź stan zdrowia za pomocą`az acr check-health`](#check-health-with-az-acr-check-health)
- [ściąganie docker kończy się niepowodzeniem z powodu błędu: net/http: żądanie anulowane podczas oczekiwania na połączenie (limit czasu klienta przekroczony podczas oczekiwania na nagłówki)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [wypychanie platformy docker powiedzie się, ale ściąganie platformy docker kończy się niepowodzeniem z powodu błędu: nieautoryzowane: wymagane uwierzytelnianie](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`powiedzie się, ale polecenia platformy docker nie powiedzie się z powodu błędu: nieautoryzowane: wymagane uwierzytelnianie](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Włączanie i odciąganie dzienników debugowania demona docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informacje o uwierzytelnianiu nie są podane w prawidłowym formacie w bezpośrednich wywołaniach interfejsu API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Dlaczego portal Azure nie zawiera listy wszystkich moich repozytoriów lub tagów?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Dlaczego portal Azure nie może pobrać repozytoriów lub tagów?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Dlaczego żądanie ściągnięcia lub wypychania kończy się niepowodzeniem przy niedozwolonym działaniu?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Jak zbierać ślady http w systemie Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Sprawdź stan zdrowia za pomocą`az acr check-health`

Aby rozwiązać typowe problemy ze środowiskiem i [rejestrem, zobacz Sprawdzanie kondycji rejestru kontenerów platformy Azure.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>ściąganie docker kończy się niepowodzeniem z powodu błędu: net/http: żądanie anulowane podczas oczekiwania na połączenie (limit czasu klienta przekroczony podczas oczekiwania na nagłówki)

 - Jeśli ten błąd jest przejściowy problem, a następnie ponowić próbę zakończy się pomyślnie.
 - Jeśli `docker pull` nie powiedzie się w sposób ciągły, może to być problem z demonem platformy Docker. Problem można ogólnie złagodzić, uruchamiając ponownie demona platformy Docker. 
 - Jeśli ten problem będzie nadal widoczny po ponownym uruchomieniu demona platformy Docker, problemem mogą być problemy z łącznością sieciową z komputerem. Aby sprawdzić, czy sieć ogólna na komputerze jest w dobrej kondycji, uruchom następujące polecenie, aby przetestować łączność z punktem końcowym. Minimalna `az acr` wersja zawierająca to polecenie sprawdzania łączności to 2.2.9. Uaktualnij platformę Azure CLI, jeśli używasz starszej wersji.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Zawsze powinien mieć mechanizm ponawiania prób we wszystkich operacjach klienta platformy Docker.

### <a name="docker-pull-is-slow"></a>Ściąganie docker jest powolne
Użyj [tego](http://www.azurespeed.com/Azure/Download) narzędzia, aby przetestować szybkość pobierania w sieci komputera. Jeśli sieć komputera jest powolna, należy rozważyć użycie maszyny Wirtualnej platformy Azure w tym samym regionie co rejestr. Zwykle zapewnia to większą szybkość sieci.

### <a name="docker-push-is-slow"></a>Push platformy Docker jest powolny
Użyj [tego](http://www.azurespeed.com/Azure/Upload) narzędzia, aby przetestować szybkość wysyłania w sieci komputera. Jeśli sieć komputera jest powolna, należy rozważyć użycie maszyny Wirtualnej platformy Azure w tym samym regionie co rejestr. Zwykle zapewnia to większą szybkość sieci.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Wypychanie platformy Docker powiedzie się, ale ściąganie platformy docker kończy się niepowodzeniem z powodu błędu: nieautoryzowane: wymagane uwierzytelnianie

Ten błąd może się zdarzyć w przypadku wersji Red `--signature-verification` Hat demona platformy Docker, gdzie jest domyślnie włączona. Opcje demona platformy Docker dla red hat enterprise linux (RHEL) lub Fedory można sprawdzić, uruchamiając następujące polecenie:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Na przykład serwer Fedory 28 ma następujące opcje demona docker:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

W `--signature-verification=false` razie `docker pull` braku kończy się niepowodzenie z błędem podobnym do:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Aby rozwiązać ten błąd:
1. Dodaj tę `--signature-verification=false` opcję do pliku `/etc/sysconfig/docker`konfiguracyjnego demona platformy Docker . Przykład:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Uruchom ponownie usługę demona platformy Docker, uruchamiając następujące polecenie:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Szczegóły `--signature-verification` można znaleźć, `man dockerd`uruchamiając plik .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login powiedzie się, ale docker kończy się niepowodzeniem z błędem: nieautoryzowane: wymagane uwierzytelnianie

Upewnij się, że używasz na przykład wszystkich `docker push myregistry.azurecr.io/myimage:latest`małych adresów URL serwera, nawet jeśli nazwa `myRegistry`zasobu rejestru jest wielką lub mieszaną literą, na przykład .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Włączanie i odciąganie dzienników debugowania demona platformy Docker    

Zacznij `dockerd` od `debug` opcji. Najpierw utwórz plik konfiguracyjny`/etc/docker/daemon.json`demona platformy Docker ( ), `debug` jeśli nie istnieje, i dodaj opcję:

```json
{    
    "debug": true    
}
```

Następnie uruchom ponownie demona. Na przykład z Ubuntu 14.04:

```bash
sudo service docker restart
```

Szczegółowe informacje można znaleźć w dokumentacji platformy [Docker](https://docs.docker.com/engine/admin/#enable-debugging).    

 * Dzienniki mogą być generowane w różnych lokalizacjach, w zależności od systemu. Na przykład dla Ubuntu 14.04 `/var/log/upstart/docker.log`jest to .    
Szczegółowe informacje można znaleźć w [dokumentacji platformy Docker.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * W przypadku platformy Docker dla systemu Windows dzienniki są generowane w obszarze %LOCALAPPDATA%/docker/. Jednak nie może zawierać wszystkie informacje debugowania jeszcze.    

   Aby uzyskać dostęp do pełnego dziennika demona, może być kilka dodatkowych kroków:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teraz masz dostęp do wszystkich plików maszyny `dockerd`Wirtualnej uruchomione . Dziennik znajduje `/var/log/docker.log`się w pliku .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji

Po udzieleniu nowych uprawnień (nowe role) do jednostki usługi, zmiana może nie zostać wniesienia natychmiastowej. Istnieją dwa możliwe powody:

* Opóźnienie przypisania roli usługi Azure Active Directory. Zwykle jest szybki, ale może to potrwać kilka minut z powodu opóźnienia propagacji.
* Opóźnienie uprawnień na serwerze tokenu usługi ACR. Może to potrwać do 10 minut. Aby ograniczyć, `docker logout` można, a następnie uwierzytelnić ponownie z tym samym użytkownikiem po 1 minucie:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Obecnie usługa ACR nie obsługuje usuwania replikacji domowej przez użytkowników. Obejście polega na uwzględnieniu tworzenia replikacji domowej w szablonie, ale pominąć jego utworzenie, dodając, `"condition": false` jak pokazano poniżej:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informacje o uwierzytelnianiu nie są podane w prawidłowym formacie w bezpośrednich wywołaniach interfejsu API REST

Może wystąpić `InvalidAuthenticationInfo` błąd, zwłaszcza `curl` za pomocą `-L`narzędzia `--location` z opcją , (do naśladowania przekierowań).
Na przykład pobieranie obiektu blob `curl` `-L` przy użyciu opcji i uwierzytelniania podstawowego:

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

Główną przyczyną jest `curl` to, że niektóre implementacje wykonaj przekierowania z nagłówkami z oryginalnego żądania.

Aby rozwiązać ten problem, należy wykonać przekierowania ręcznie bez nagłówków. Wydrukuj nagłówki odpowiedzi `-D -` z `curl` opcją, a `Location` następnie wyodrębnij: nagłówek:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Dlaczego portal Azure nie zawiera listy wszystkich moich repozytoriów lub tagów? 

Jeśli korzystasz z przeglądarki Microsoft Edge/IE, możesz zobaczyć co najwyżej 100 repozytoriów lub tagów. Jeśli w rejestrze znajduje się więcej niż 100 repozytoriów lub tagów, zalecamy użycie przeglądarki Firefox lub Chrome do wyświetlenia ich wszystkich.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Dlaczego portal Azure nie może pobrać repozytoriów lub tagów?

Przeglądarka może nie być w stanie wysłać żądania pobierania repozytoriów lub tagów do serwera. Mogą istnieć różne powody, takie jak:

* Brak łączności sieciowej
* Zapora
* Blokowanie reklam
* Błędy DNS

Skontaktuj się z administratorem sieci lub sprawdź konfigurację sieci i łączność. Spróbuj `az acr check-health -n yourRegistry` uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy środowisko jest w stanie połączyć się z rejestrem kontenerów. Ponadto możesz również wypróbować incognito lub prywatną sesję w przeglądarce, aby uniknąć przestarzałej pamięci podręcznej przeglądarki lub plików cookie.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Dlaczego żądanie ściągnięcia lub wypychania kończy się niepowodzeniem przy niedozwolonym działaniu?

Oto kilka scenariuszy, w których operacje mogą być niedozwolone:
* Rejestry klasyczne nie są już obsługiwane. Uaktualnij do obsługiwanych [jednostek SKU](https://aka.ms/acr/skus) przy użyciu [aktualizacji az acr](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) lub witryny Azure portal.
* Obraz lub repozytorium może być zablokowane, tak aby nie można było go usunąć ani zaktualizować. Można użyć polecenia [az acr show repozytorium,](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) aby wyświetlić bieżące atrybuty.
* Niektóre operacje są niedozwolone, jeśli obraz jest w kwarantannie. Dowiedz się więcej o [kwarantannie](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak zbierać ślady http w systemie Windows?

#### <a name="prerequisites"></a>Wymagania wstępne

- Włącz odszyfrowywanie https w skrzypek:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Włącz program Docker, aby używać serwera proxy za pośrednictwem interfejsu użytkownika platformy Docker:<https://docs.docker.com/docker-for-windows/#proxies>
- Pamiętaj, aby powrócić po zakończeniu.  Docker nie będzie działać z tym włączonym i skrzypek nie działa.

#### <a name="windows-containers"></a>Kontenery systemu Windows

Konfigurowanie serwera proxy platformy Docker na 127.0.0.1:8888

#### <a name="linux-containers"></a>Kontenery systemu Linux

Znajdź adres IP wirtualnego przełącznika docker vm:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurowanie serwera proxy platformy Docker do wyjścia poprzedniego polecenia i portu 8888 (na przykład 10.0.75.1:8888)

## <a name="tasks"></a>Zadania

- [Jak anulować wsadowe przebiegi?](#how-do-i-batch-cancel-runs)
- [Jak dołączyć folder .git do polecenia az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Czy zadania obsługują wyzwalacze GitLab for Source?](#does-tasks-support-gitlab-for-source-triggers)
- [Jaką usługę zarządzania repozytorium git obsługuje zadania?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Jak anulować wsadowe przebiegi?

Następujące polecenia anulują wszystkie uruchomione zadania w określonym rejestrze.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak dołączyć folder .git do polecenia az acr build?

Jeśli przekażesz do `az acr build` polecenia lokalny `.git` folder źródłowy, folder zostanie domyślnie wykluczony z przekazanego pakietu. Można utworzyć `.dockerignore` plik z następującym ustawieniem. Mówi polecenie, aby przywrócić `.git` wszystkie pliki w ramach przekazanego pakietu. 

`!.git/**`

To ustawienie dotyczy również `az acr run` polecenia.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Czy zadania obsługują wyzwalacze GitLab for Source?

Obecnie nie obsługujemy wyzwalaczy GitLab for Source.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Jaką usługę zarządzania repozytorium git obsługuje zadania?

| Usługa Git | Kontekst źródłowy | Ręczna kompilacja | Automatyczne tworzenie za pomocą wyzwalacza zatwierdzania |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Tak | Tak |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Tak | Tak |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Tak | Nie |
| Bitbucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Tak | Nie |

## <a name="run-error-message-troubleshooting"></a>Uruchamianie rozwiązywania problemów z komunikatem o błędach

| Komunikat o błędzie | Przewodnik rozwiązywania problemów |
|---|---|
|Nie skonfigurowano dostępu dla maszyny Wirtualnej, w związku z czym nie znaleziono subskrypcji|Może się tak zdarzyć, jeśli używasz `az login --identity` w zadaniu ACR. Jest to błąd przejściowy i występuje, gdy przypisanie roli tożsamości zarządzanej nie propagowane. Odczekanie kilku sekund przed ponowieniem próby pracy.|

## <a name="cicd-integration"></a>Integracja ciągłej integracji z ciągłą integracją/dyskami

- [Okręg CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Akcje github](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej](container-registry-intro.md) o usłudze Azure Container Registry.
