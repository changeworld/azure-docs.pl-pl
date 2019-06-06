---
title: Resetowanie poświadczeń klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak aktualizacja lub zresetować poświadczenia nazwy głównej usługi dla klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 189bcf2ddc7d301c8100f74e51374abd217a144f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475488"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Aktualizowanie lub zamiana poświadczeń dla jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry usługi AKS są tworzone przy użyciu nazwy głównej usługi czasu wygaśnięcia jeden rok. Jak blisko datę wygaśnięcia można zresetować poświadczenia, które mają rozszerzenie nazwy głównej usługi dla dodatkowy okres czasu. Można również zaktualizować lub obrót poświadczeń jako część zasad zabezpieczeń zdefiniowane. Ten artykuł szczegółowo opisuje sposób aktualizacji tych poświadczeń klastra usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.65 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Zaktualizowanie lub utworzyć nazwę główną usługi

Jeśli chcesz zaktualizować poświadczenia dla klastra usługi AKS możesz:

* Zaktualizuj poświadczenia dla istniejącej jednostki usługi używany przez klaster, lub
* Tworzenie jednostki usługi i zaktualizuj klaster pod kątem używania tych nowych poświadczeń.

Jeśli chcesz utworzyć jednostkę usługi i aktualizowanie klastra AKS, pomiń pozostałe kroki w tej sekcji i w dalszym ciągu [utworzyć nazwę główną usługi](#create-a-service-principal). Jeśli chcesz zaktualizować poświadczenia dla istniejącej jednostki usługi używany przez klaster AKS, wykonaj kroki w tej sekcji.

### <a name="get-the-service-principal-id"></a>Pobierz identyfikator jednostki usługi

Aby zaktualizować poświadczenia dla istniejącej jednostki usługi, Pobierz identyfikator jednostki usługi przy użyciu klastra [az aks show] [ az-aks-show] polecenia. Poniższy przykład pobiera identyfikator dla klastra o nazwie *myAKSCluster* w *myResourceGroup* grupy zasobów. Identyfikator jednostki usługi jest ustawiona w zmiennej o nazwie *SP_ID* do użycia w dodatkowych poleceń.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Zaktualizuj poświadczenia nazwy głównej usługi

Przy użyciu zestawu zmiennej, która zawiera identyfikator jednostki usługi, zresetuj poświadczeń przy użyciu [resetowania az ad sp poświadczeń][az-ad-sp-credential-reset]. Poniższy przykład pozwala wygenerować nowy wpis tajny bezpieczny dla jednostki usługi platformy Azure. Ten nowy wpis tajny bezpieczne jest również przechowywane jako zmienną.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teraz przejdź do [klastra AKS aktualizacji za pomocą nowych poświadczeń](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jeśli wybrano zaktualizować istniejące poświadczenia nazwy głównej usługi w poprzedniej sekcji, Pomiń ten krok. W dalszym ciągu [klastra AKS aktualizacji za pomocą nowych poświadczeń](#update-aks-cluster-with-new-credentials).

Aby utworzyć nazwę główną usługi, a następnie zaktualizować klastra usługi AKS przy użyciu tych nowych poświadczeń, należy użyć [az ad sp create-for-rbac] [ az-ad-sp-create] polecenia. W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości są używane w następnym kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Teraz Zdefiniuj zmienne do usługi głównej identyfikator i klucz tajny klienta przy użyciu danych wyjściowych z własnych [az ad sp create-for-rbac] [ az-ad-sp-create] polecenia, jak pokazano w poniższym przykładzie. *SP_ID* jest Twoja *appId*i *SP_SECRET* jest Twoja *hasło*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Aktualizuj klaster AKS za pomocą nowych poświadczeń

Niezależnie od tego, czy została wybrana opcja zaktualizować poświadczenia dla istniejącej jednostki usługi lub utworzyć nazwę główną usługi, można teraz zaktualizować klastra usługi AKS przy użyciu nowych poświadczeń przy użyciu [poświadczenia aktualizacji az aks] [ az-aks-update-credentials] polecenia. Zmienne *--nazwy głównej usługi* i *— klucz tajny klienta* służą:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Może potrwać kilka minut w przypadku poświadczeń jednostki usługi do zaktualizowania w usłudze AKS.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule jednostka usługi dla samego klastra AKS został zaktualizowany. Aby uzyskać więcej informacji na temat sposobu zarządzania tożsamościami dla obciążeń w ramach klastra, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
