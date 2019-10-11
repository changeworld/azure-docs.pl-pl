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
ms.openlocfilehash: 16ad37eaa50f0c3825d131338cc4a0abdc369978
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262868"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie Azure Container Registry przy użyciu jednostek usługi

Za pomocą jednostki usługi Azure Active Directory (Azure AD) można zapewnić obraz kontenera `docker push` i `pull` dostęp do rejestru kontenerów. Za pomocą nazwy głównej usługi można zapewnić dostęp do "bezobsługowego" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

Jednostki *usługi* Azure AD zapewniają dostęp do zasobów platformy Azure w ramach Twojej subskrypcji. Nazwę główną usługi można traktować jako tożsamość użytkownika usługi, gdzie "usługa" to dowolna aplikacja, usługa lub platforma, która musi uzyskać dostęp do zasobów. Można skonfigurować jednostkę usługi z prawami dostępu w zakresie tylko do określonych zasobów. Następnie skonfiguruj aplikację lub usługę tak, aby korzystała z poświadczeń jednostki usługi w celu uzyskania dostępu do tych zasobów.

W kontekście Azure Container Registry można utworzyć jednostkę usługi Azure AD za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Za pomocą nazwy głównej usługi Azure AD można zapewnić dostęp z zakresu do prywatnego rejestru kontenerów. Utwórz różne jednostki usługi dla każdej aplikacji lub usług, z których każdy może mieć dostosowane prawa dostępu do rejestru. Ponadto, ponieważ możesz uniknąć udostępniania poświadczeń między usługami i aplikacjami, możesz obrócić poświadczenia lub odwołać dostęp tylko do nazwy głównej usługi (i w związku z tym aplikacji).

Na przykład skonfiguruj aplikację sieci Web tak, aby korzystała z jednostki usługi, która udostępnia ją @no__t tylko do odczytu, podczas gdy system kompilacji używa jednostki usługi, która udostępnia ją z dostępem `push` i `pull`. Jeśli opracowywanie aplikacji zmienia się, możesz obrócić swoje poświadczenia zasad usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać nazwy głównej usługi

Aby zapewnić dostęp do rejestru w **scenariuszach bezobsługowym**, należy użyć nazwy głównej usługi. Oznacza to, że dowolna aplikacja, usługa lub skrypt, który musi wypchnąć lub ściągnąć obrazy kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany. Na przykład:

  * *Ściąganie*: wdrażanie kontenerów z rejestru w systemach aranżacji, takich jak KUBERNETES, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów do powiązanych usług platformy Azure, takich jak [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)i innych.

  * *Wypychanie*: Tworzenie obrazów kontenerów i wypychanie ich do rejestru przy użyciu rozwiązań ciągłej integracji i wdrażania, takich jak Azure Pipelines lub Jenkins.

W przypadku indywidualnego dostępu do rejestru, takiego jak ręczne ściąganie obrazu kontenera do stacji roboczej deweloperskiej, zalecamy użycie własnej [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) zamiast dostępu do rejestru (na przykład za pomocą polecenia [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Przykładowe skrypty

Powyższe przykładowe skrypty dla interfejsu wiersza polecenia platformy Azure można znaleźć w witrynie GitHub, a także wersje dla Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

Gdy masz nazwę główną usługi, której udzielono dostępu do rejestru kontenerów, możesz skonfigurować jej poświadczenia w taki sposób, aby miały dostęp do "bezobsługowego" usług i aplikacji, lub wprowadzić je przy użyciu `docker login` polecenia. Wprowadź następujące wartości:

* **Nazwa użytkownika** — identyfikator aplikacji głównej usługi (NAZYWANY także *identyfikatorem klienta*)
* **Hasło** — hasło główne usługi (nazywane także *kluczem tajnym klienta*)

Każda wartość jest identyfikatorem GUID formularza `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Możesz ponownie wygenerować hasło jednostki usługi, uruchamiając polecenie [AZ AD Sp Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Używanie poświadczeń z usługami platformy Azure

Poświadczeń jednostki usługi można użyć z dowolnej usługi platformy Azure, która może być uwierzytelniana w usłudze Azure Container Registry.  Użyj poświadczeń nazwy głównej usługi zamiast poświadczeń administratora rejestru dla różnych scenariuszy.

Na przykład Użyj poświadczeń w celu ściągnięcia obrazu z rejestru kontenerów platformy Azure w celu [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Użyj z logowaniem Docker

Możesz również uruchomić `docker login` przy użyciu nazwy głównej usługi. W poniższym przykładzie identyfikator aplikacji głównej usługi jest przesyłany w zmiennej środowiskowej `$SP_APP_ID` i hasła w zmiennej `$SP_PASSWD`. Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi zarządzania poświadczeniami platformy Docker, zobacz informacje dotyczące polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

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
