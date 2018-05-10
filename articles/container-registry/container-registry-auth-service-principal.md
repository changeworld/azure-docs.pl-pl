---
title: Azure rejestru kontenera uwierzytelniania za pomocą nazwy główne usług
description: Dowiedz się, jak zapewnić dostęp do obrazów w rejestrze kontener prywatnego przy użyciu nazwy głównej usługi Azure Active Directory.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: 16af83522dd55744c485f6dd3696481e16da1b22
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure rejestru kontenera uwierzytelniania za pomocą nazwy główne usług

Nazwy głównej usługi Azure Active Directory (Azure AD) służy do zapewnienia kontener obrazu `docker push` i `pull` dostępu do rejestru kontenera. Przy użyciu nazwy głównej usługi, można zapewnić dostęp do usługi "bezobsługowe" i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest nazwy głównej usługi?

Usługi Azure AD *usługi podmiotów* zapewniają dostęp do zasobów platformy Azure w ramach subskrypcji. Można potraktować usługi głównej jako tożsamość użytkownika dla usługi, "Usługa" w przypadku dowolnej aplikacji, usługi lub platformy, które wymagają dostępu do zasobów. Nazwy głównej usługi można skonfigurować uprawnienia dostępu tylko do tych zasobów, które określisz zakresu. Następnie można skonfigurować aplikacji lub usługi, aby użyć nazwy głównej usługi poświadczeń dostępu do tych zasobów.

W kontekście rejestru kontenera platformy Azure można utworzyć usługi Azure AD nazwy głównej usługi z uprawnieniami ściągania, wypychania i ściągania lub właściciela do prywatnego rejestru Docker na platformie Azure.

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Przy użyciu nazwy głównej usługi Azure AD, można skonfigurować rejestru Kontener prywatny dostęp w zakresie. Można utworzyć jednostki innej usługi dla poszczególnych aplikacji lub usług, a każda z prawami dostępu dopasowane do rejestru. I ponieważ poświadczeń między usług i aplikacji do udostępniania można uniknąć, można obrócić poświadczeń lub odwołać dostęp do nazwy głównej usługi (i w związku z tym aplikacji), możesz wybrać.

Na przykład aplikacja sieci web można użyć nazwy głównej usługi, która dostarcza obrazu `pull` dostęp tylko wtedy, gdy system kompilacji można użyć nazwy głównej usługi, która dostarcza ona zarówno `push` i `pull` dostępu. W przypadku rozwoju aplikacji zmiany ręce, można obracać jego poświadczenia zasady usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy należy używać nazwy głównej usługi

Nazwy głównej usługi należy używać w celu zapewnienia dostępu do rejestru w **bezobsługowe scenariusze**. Oznacza to, że żadnych aplikacji, usługi lub skrypt, który musi wypychania lub ściągania kontener obrazów w trybie nienadzorowanym automatycznych lub w inny sposób.

Indywidualnej operacji dostępu do rejestru, takie jak kiedy należy ręcznie wyciągnąć obrazu kontenera deweloperskiej stacji roboczej, należy zamiast tego używać własnych [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) dla dostępu do rejestru (na przykład z [az acr logowania][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty

Poprzednie przykładowe skrypty można znaleźć dla wiersza polecenia platformy Azure w serwisie GitHub, jak dobrze wersji dla programu Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu nazwy głównej usługi że zostały przyznane dostępu do rejestru kontenera, można użyć jego poświadczeń w aplikacji i usług do interakcji z rejestru.

Podczas konfigurowania poszczególnych aplikacji, aby użyć poświadczenia główne usługi wykracza poza zakres tego artykułu, można znaleźć instrukcje dla niektórych platform w tym miejscu i określonych usług:

* [Uwierzytelniania za pomocą rejestru kontenera platformy Azure z usługi Azure Kubernetes (AKS)](container-registry-auth-aks.md)
* [Uwierzytelniania za pomocą rejestru kontenera platformy Azure z wystąpień kontenera platformy Azure (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login