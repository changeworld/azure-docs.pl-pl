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
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309957"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie Azure Container Registry przy użyciu jednostek usługi

Aby zapewnić obraz `docker push` kontenera i dostęp do rejestru kontenerów, `pull` można użyć jednostki usługi Azure Active Directory (Azure AD). Za pomocą nazwy głównej usługi można zapewnić dostęp do "bezobsługowego" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

Jednostki *usługi* Azure AD zapewniają dostęp do zasobów platformy Azure w ramach Twojej subskrypcji. Nazwę główną usługi można traktować jako tożsamość użytkownika usługi, gdzie "usługa" to dowolna aplikacja, usługa lub platforma, która wymaga dostępu do zasobów. Można skonfigurować jednostkę usługi z prawami dostępu w zakresie tylko do określonych zasobów. Następnie skonfiguruj aplikację lub usługę tak, aby korzystała z poświadczeń jednostki usługi w celu uzyskania dostępu do tych zasobów.

W kontekście Azure Container Registry można utworzyć jednostkę usługi Azure AD za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Za pomocą nazwy głównej usługi Azure AD można zapewnić dostęp z zakresu do prywatnego rejestru kontenerów. Można utworzyć różne jednostki usługi dla każdej aplikacji lub usług, z których każdy ma dostosowane prawa dostępu do rejestru. Ponadto, ponieważ możesz uniknąć udostępniania poświadczeń między usługami i aplikacjami, możesz obrócić poświadczenia lub odwołać dostęp tylko do nazwy głównej usługi (i w związku z tym aplikacji).

Na przykład aplikacja sieci Web może korzystać z jednostki usługi, która udostępnia ją tylko z `pull` dostępem do obrazu, podczas gdy system kompilacji może korzystać z jednostki usługi, która udostępnia ją `push` zarówno `pull` z programem, jak i dostępem. Jeśli opracowywanie aplikacji zmienia się, możesz obrócić swoje poświadczenia zasad usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać nazwy głównej usługi

Aby zapewnić dostęp do rejestru w **scenariuszach**bezobsługowym, należy użyć nazwy głównej usługi. Oznacza to, że dowolna aplikacja, usługa lub skrypt, który musi wypchnąć lub ściągnąć obrazy kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany.

Aby uzyskać dostęp do rejestru, na przykład podczas ręcznego ściągania obrazu kontenera do stacji roboczej deweloperskiej, należy zamiast tego użyć własnej [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) na potrzeby dostępu do rejestru (na przykład za pomocą polecenia [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty

Powyższe przykładowe skrypty dla interfejsu wiersza polecenia platformy Azure można znaleźć w witrynie GitHub, jak również wersje Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Kolejne kroki

Gdy masz nazwę główną usługi, której udzielono dostępu do rejestru kontenerów, możesz użyć jej poświadczeń w aplikacjach i usługach do interakcji z rejestrem samoobsługowego. Poświadczeń jednostki usługi można użyć z dowolnej usługi platformy Azure, która może być uwierzytelniana w usłudze Azure Container Registry. Przykłady:

* [Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Uwierzytelnianie za pomocą Azure Container Registry z Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
