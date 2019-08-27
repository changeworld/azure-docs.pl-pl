---
title: Azure Container Registry uwierzytelniania przy użyciu nazwy głównej usługi
description: Zapewnianie dostępu do obrazów w prywatnym rejestrze kontenera przy użyciu nazwy głównej usługi Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032370"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie Azure Container Registry przy użyciu jednostek usługi

Aby zapewnić obraz `docker push` kontenera i dostęp do rejestru kontenerów, `pull` można użyć jednostki usługi Azure Active Directory (Azure AD). Za pomocą nazwy głównej usługi można zapewnić dostęp do "bezobsługowego" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

Jednostki *usługi* Azure AD zapewniają dostęp do zasobów platformy Azure w ramach Twojej subskrypcji. Nazwę główną usługi można traktować jako tożsamość użytkownika usługi, gdzie "usługa" to dowolna aplikacja, usługa lub platforma, która musi uzyskać dostęp do zasobów. Można skonfigurować jednostkę usługi z prawami dostępu w zakresie tylko do określonych zasobów. Następnie skonfiguruj aplikację lub usługę tak, aby korzystała z poświadczeń jednostki usługi w celu uzyskania dostępu do tych zasobów.

W kontekście Azure Container Registry można utworzyć jednostkę usługi Azure AD za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Za pomocą nazwy głównej usługi Azure AD można zapewnić dostęp z zakresu do prywatnego rejestru kontenerów. Utwórz różne jednostki usługi dla każdej aplikacji lub usług, z których każdy może mieć dostosowane prawa dostępu do rejestru. Ponadto, ponieważ możesz uniknąć udostępniania poświadczeń między usługami i aplikacjami, możesz obrócić poświadczenia lub odwołać dostęp tylko do nazwy głównej usługi (i w związku z tym aplikacji).

Na przykład skonfiguruj aplikację sieci Web tak, aby korzystała z jednostki usługi, która udostępnia `pull` ją tylko z dostępem do obrazu, podczas gdy system kompilacji używa jednostki usługi, która `push` udostępnia `pull` ją jednocześnie i dostęp. Jeśli opracowywanie aplikacji zmienia się, możesz obrócić swoje poświadczenia zasad usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać nazwy głównej usługi

Aby zapewnić dostęp do rejestru w **scenariuszach**bezobsługowym, należy użyć nazwy głównej usługi. Oznacza to, że dowolna aplikacja, usługa lub skrypt, który musi wypchnąć lub ściągnąć obrazy kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany. Przykład:

  * *Ściąganie*: Wdrażaj kontenery z rejestru w systemach aranżacji, takich jak Kubernetes, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów do powiązanych usług platformy Azure, takich jak [Azure Kubernetes Service (AKS)](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)i innych.

  * *Wypchnij*: Tworzenie obrazów kontenerów i wypychanie ich do rejestru przy użyciu rozwiązań ciągłej integracji i wdrażania, takich jak Azure Pipelines lub Jenkins.

W przypadku indywidualnego dostępu do rejestru, takiego jak ręczne ściąganie obrazu kontenera do stacji roboczej deweloperskiej, zalecamy użycie własnej [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) zamiast dostępu do rejestru (na przykład za pomocą polecenia [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Przykładowe skrypty

Powyższe przykładowe skrypty dla interfejsu wiersza polecenia platformy Azure można znaleźć w witrynie GitHub, a także wersje dla Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

Gdy masz nazwę główną usługi, której udzielono dostępu do rejestru kontenerów, możesz skonfigurować jej poświadczenia w celu uzyskania dostępu do "bezobsługowego" usług i aplikacji albo wprowadzić je za pomocą `docker login` polecenia. Użyj następujących wartości:

* **Nazwa użytkownika** — identyfikator aplikacji głównej usługi (NAZYWANY także *identyfikatorem klienta*)
* **Hasło** — hasło główne usługi (nazywane także *kluczem tajnym klienta*)

Każda wartość jest identyfikatorem GUID formularza `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Możesz ponownie wygenerować hasło jednostki usługi, uruchamiając polecenie [AZ AD Sp Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Używanie poświadczeń z usługami platformy Azure

Poświadczeń jednostki usługi można użyć z dowolnej usługi platformy Azure, która może być uwierzytelniana w usłudze Azure Container Registry. Przykłady:

* [Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Uwierzytelnianie za pomocą Azure Container Registry z Azure Container Instances (ACI)](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Użyj z logowaniem Docker

Można również uruchomić `docker login` przy użyciu nazwy głównej usługi. W poniższym przykładzie identyfikator aplikacji głównej usługi jest przekazaniem w zmiennej `$SP_APP_ID`środowiskowej i hasłem w zmiennej. `$SP_PASSWD` Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi zarządzania poświadczeniami platformy Docker, zobacz informacje dotyczące polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po zalogowaniu się program Docker buforuje poświadczenia.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [omówieniem uwierzytelniania](container-registry-authentication.md) , aby poznać inne scenariusze uwierzytelniania przy użyciu usługi Azure Container Registry.

* Przykład użycia magazynu kluczy Azure do przechowywania i pobierania poświadczeń jednostki usługi dla rejestru kontenerów można znaleźć w samouczku, aby [skompilować i wdrożyć obraz kontenera za pomocą zadań ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
