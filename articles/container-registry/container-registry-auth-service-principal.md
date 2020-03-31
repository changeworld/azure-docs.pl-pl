---
title: Uwierzytelnianie za pomocą jednostki usługi
description: Zapewnij dostęp do obrazów w rejestrze kontenerów prywatnych przy użyciu jednostki usługi Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455408"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie rejestru kontenerów platformy Azure z podmiotami zabezpieczeń usługi

Można użyć jednostki usługi Azure Active Directory (Azure `docker push` `pull` AD), aby zapewnić obraz kontenera i dostęp do rejestru kontenerów. Za pomocą jednostki usługi, można zapewnić dostęp do "bezgłowych" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

*Jednostki usługi* Azure AD zapewniają dostęp do zasobów platformy Azure w ramach subskrypcji. Można myśleć o jednostki usługi jako tożsamości użytkownika dla usługi, gdzie "usługa" jest dowolną aplikacją, usługą lub platformą, która musi uzyskać dostęp do zasobów. Można skonfigurować jednostkę usługi z prawami dostępu o zakresie tylko do tych zasobów, które określisz. Następnie skonfiguruj aplikację lub usługę, aby używała poświadczeń jednostki usługi w celu uzyskania dostępu do tych zasobów.

W kontekście usługi Azure Container Registry można utworzyć jednostkę usługi Azure AD z ściąganiem, wypychaniem i ściąganiem lub innymi uprawnieniami do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę, zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto skorzystać z jednostki usługi?

Za pomocą jednostki usługi Azure AD, można zapewnić ograniczony dostęp do rejestru kontenerów prywatnych. Utwórz różne jednostki usługi dla każdej aplikacji lub usług, z których każda ma dostosowane prawa dostępu do rejestru. A ponieważ można uniknąć udostępniania poświadczeń między usługami i aplikacjami, można obrócić poświadczenia lub odwołać dostęp tylko dla jednostki usługi (a tym samym aplikacji), którą wybierzesz.

Na przykład skonfiguruj aplikację sieci web, aby `pull` używać jednostki usługi, która zapewnia tylko dostęp `push` do `pull` obrazu, podczas gdy system kompilacji używa jednostki usługi, która zapewnia jej zarówno i dostęp. Jeśli rozwój aplikacji zmienia ręce, można obrócić jego poświadczenia jednostki usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać jednostki usługi

Należy użyć jednostki usługi, aby zapewnić dostęp do rejestru w **scenariuszach bezgłowy.** Oznacza to, że każda aplikacja, usługa lub skrypt, który musi wypychać lub ściągać obrazy kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany. Przykład:

  * *Ściąganie:* Wdrażanie kontenerów z rejestru do systemów aranżacji, w tym Kubernetes, DC/OS i Docker Swarm. Można również pobierać z rejestrów kontenerów do powiązanych usług platformy Azure, takich jak [Usługa Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Wystąpienia kontenerów platformy Azure,](container-registry-auth-aci.md) [usługa app service,](../app-service/index.yml) [partia,](../batch/index.yml) [sieci szkieletowej usług](/azure/service-fabric/)i inne.

  * *Wypychanie:* Tworzenie obrazów kontenerów i wypychanie ich do rejestru przy użyciu ciągłej integracji i wdrażania rozwiązań, takich jak potoki platformy Azure lub usługi Jenkins.

W przypadku indywidualnego dostępu do rejestru, na przykład podczas ręcznego ściągania obrazu kontenera do stacji roboczej dewelopera, zalecamy użycie własnej [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) zamiast do dostępu do rejestru (na przykład przy użyciu logowania [az acr).][az-acr-login]

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Przykładowe skrypty

Poprzednie przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w usłudze GitHub, a także wersje programu Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Uwierzytelnij się z jednostką usługi

Po uzyskaniu jednostki usługi, której udzielono dostępu do rejestru kontenerów, można skonfigurować jego poświadczenia dostępu do usług i `docker login` aplikacji bez głowy lub wprowadzić je za pomocą polecenia. Wprowadź następujące wartości:

* **Nazwa użytkownika** — identyfikator głównej aplikacji usługi (nazywany także *identyfikatorem klienta)*
* **Hasło** - hasło głównej usługi (nazywane również *kluczem tajnym klienta)*

Każda wartość jest identyfikatorem `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID formularza . 

> [!TIP]
> Hasło jednostki usługi można ponownie wygenerować, uruchamiając polecenie [resetowania poświadczeń az ad sp.](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
>

### <a name="use-credentials-with-azure-services"></a>Używanie poświadczeń z usługami platformy Azure

Można użyć poświadczeń jednostki usługi z dowolnej usługi platformy Azure, która uwierzytelnia się za pomocą rejestru kontenerów platformy Azure.  Użyj poświadczeń jednostki usługi zamiast poświadczeń administratora rejestru dla różnych scenariuszy.

Na przykład użyj poświadczeń, aby wyciągnąć obraz z rejestru kontenerów platformy Azure do [wystąpień kontenera platformy Azure.](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Używanie z logowaniem docker

Można uruchomić `docker login` przy użyciu jednostki usługi. W poniższym przykładzie identyfikator aplikacji głównej usługi jest `$SP_APP_ID`przekazywany w `$SP_PASSWD`zmiennej środowiskowej, a hasło w zmiennej . Aby uzyskać najlepsze rozwiązania dotyczące zarządzania poświadczeniami platformy Docker, zobacz odwołanie do polecenia [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po zalogowaniu docker buforuje poświadczenia.

### <a name="use-with-certificate"></a>Używanie z certyfikatem

Jeśli dodano certyfikat do jednostki usługi, można zalogować się do interfejsu wiersza polecenia platformy Azure za pomocą uwierzytelniania opartego na certyfikatach, a następnie użyć polecenia [logowania az acr,][az-acr-login] aby uzyskać dostęp do rejestru. Użycie certyfikatu jako klucza tajnego zamiast hasła zapewnia dodatkowe zabezpieczenia podczas korzystania z interfejsu wiersza polecenia. 

Certyfikat z podpisem własnym można utworzyć podczas [tworzenia jednostki usługi](/cli/azure/create-an-azure-service-principal-azure-cli). Lub dodaj jeden lub więcej certyfikatów do istniejącej jednostki usługi. Jeśli na przykład użyjesz jednego ze skryptów w tym artykule do utworzenia lub zaktualizowania jednostki usługi z uprawnieniami do ściągania lub wypychania obrazów z rejestru, dodaj certyfikat za pomocą polecenia [resetowania poświadczeń az ad sp.][az-ad-sp-credential-reset]

Aby użyć jednostki usługi z certyfikatem do [logowania się do interfejsu wiersza polecenia platformy Azure,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)certyfikat musi być w formacie PEM i zawierać klucz prywatny. Jeśli certyfikat nie jest w wymaganym formacie, użyj `openssl` narzędzia, takiego jak konwertowanie. Po uruchomieniu [az logowania][az-login] do logowania się do interfejsu wiersza polecenia przy użyciu jednostki usługi, również podać identyfikator aplikacji jednostki usługi i identyfikator dzierżawy usługi Active Directory. Poniższy przykład pokazuje te wartości jako zmienne środowiskowe:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Następnie uruchom [az acr zaloguj się,][az-acr-login] aby uwierzytelnić się w rejestrze:

```azurecli
az acr login --name myregistry
```

Wierszka polecenia używa tokenu `az login` utworzonego podczas pracy w celu uwierzytelnienia sesji w rejestrze.

## <a name="next-steps"></a>Następne kroki

* Zobacz [omówienie uwierzytelniania](container-registry-authentication.md) dla innych scenariuszy do uwierzytelniania za pomocą rejestru kontenerów platformy Azure.

* Na przykład przy użyciu magazynu kluczy platformy Azure do przechowywania i pobierania poświadczeń głównej usługi dla rejestru kontenerów, zobacz samouczek do [tworzenia i wdrażania obrazu kontenera przy użyciu zadań usługi ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
