---
title: Wdrażanie klastra Kubernetes Azure stos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć klaster Kubernetes stos Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823626"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Wdrażanie klastra Kubernetes stos Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!Note]  
> Kubernetes usługi kontenera platformy Azure (ACS) na stosie Azure znajduje się w prywatnej wersji zapoznawczej. Operatorem stosu Azure należy zażądać dostępu do elementu Kubernetes Marketplace niezbędnych do wykonywania instrukcji w tym artykule.

Następujący artykuł wygląda na przy użyciu szablonu usługi Azure Resource Manager rozwiązania do wdrażania i obsługi administracyjnej zasobów dla Kubernetes w jednej, skoordynowanej operacji. Użytkownik zostanie muszą do zbierania wymaganych informacji o instalacji programu Azure stosu Generowanie szablonu, a następnie wdrożyć do chmury.

## <a name="kubernetes-and-containers"></a>Kubernetes i kontenerów

Można zainstalować przy użyciu szablonów usługi Azure Resource Manager wygenerowany przez aparat usługi kontenera platformy Azure (ACS) na stosie Azure Kubernetes. [Kubernetes](https://kubernetes.io) to system typu open source do automatyzacji wdrażania, skalowania i zarządzania aplikacji w kontenerach. A [kontenera](https://www.docker.com/what-container) znajduje się w obrazie, podobne do maszyny Wirtualnej. W przeciwieństwie do maszyny Wirtualnej obrazu kontenera tylko obejmuje zasoby do uruchomienia aplikacji, takich jak kod środowiska uruchomieniowego do wykonania kodu, określonych bibliotek i ustawień.

Możesz użyć Kubernetes do:

- Twórz skalowalna na ogromną skalę, można uaktualnić, aplikacje, które można wdrożyć w sekundach. 
- Upraszcza projektowanie aplikacji i zwiększyć niezawodność jej przez różne aplikacje Helm. [Helm](https://github.com/kubernetes/helm) jest narzędziem open source tworzenia pakietów, które pomaga zainstalować i zarządzanie cyklem życia aplikacji Kubernetes.
- Łatwe monitorowanie i diagnozowanie kondycji aplikacji o skali i uaktualnienia funkcji.

## <a name="prerequisites"></a>Wymagania wstępne 

Aby rozpocząć, upewnij się, masz odpowiednie uprawnienia i czy stosu Azure jest gotowy.

1. Sprawdź, czy aplikacje można tworzyć w dzierżawie usługi Azure Active Directory (Azure AD). Te uprawnienia są wymagane dla wdrożenia Kubernetes.

    Aby uzyskać instrukcje dotyczące sprawdzania uprawnień, zobacz [Sprawdź Azure uprawnień usługi Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Generowanie SSH publicznego i prywatnego pary kluczy do logowania do maszyny Wirtualnej systemu Linux na stosie Azure. Podczas tworzenia klastra należy klucza publicznego.

    Aby uzyskać instrukcje generowania klucza, zobacz [generowania klucza SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Sprawdź, czy masz ważną subskrypcję w portalu dzierżawcy usługi Azure stosu i czy masz wystarczającą ilość publicznego adresu IP adresy można dodać nowej aplikacji.

    Nie można wdrożyć klaster stos Azure **administratora** subskrypcji. Należy użyć subskrypcji użytkownika **. 

## <a name="create-a-service-principal-in-azure-ad"></a>Tworzenie nazwy głównej usługi w usłudze Azure AD

1. Zaloguj się do globalnej [portalu Azure](http://portal.azure.com).
2. Sprawdź, czy użytkownik jest zalogowany przy użyciu dzierżawy usługi Azure AD skojarzonej z wystąpieniem usługi Azure stosu.
3. Tworzenie aplikacji usługi Azure AD.

    a. Wybierz **usługi Azure Active Directory** > **+ rejestracji aplikacji** > **nowej rejestracji aplikacji**.

    b. Wprowadź **nazwa** aplikacji.

    c. Wybierz **aplikacji sieci Web / interfejs API**.

    d. Wprowadź `http://localhost` dla **adres URL logowania**.

    c. Kliknij przycisk **Utwórz**.

4. Zwróć uwagę na **identyfikator aplikacji**. Konieczne będzie identyfikator podczas tworzenia klastra. Identyfikator jest określany jako **identyfikator klienta nazwy głównej usługi**.

5. Wybierz **ustawienia** > **klucze**.

    a. Wprowadź **opis**.

    b. Wybierz **nigdy nie wygasa** dla **Expires**.

    c. Wybierz pozycję **Zapisz**. Upewnij się, należy pamiętać, ciąg klucza. Konieczne będzie ciągu kluczy podczas tworzenia klastra. Klucz jest określany jako **klucz tajny klienta nazwy głównej usługi**.



## <a name="give-the-service-principal-access"></a>Udostępnianie głównej usługi

Nadaj głównych dostępu do usługi do subskrypcji, w którym podmiot zabezpieczeń może utworzyć zasobów.

1.  Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

2. Wybierz **więcej usług** > **subskrypcji użytkownika** > **+ Dodaj**.

3. Wybierz subskrypcję, która zostanie utworzona.

4. Wybierz **(IAM) kontroli dostępu** > Wybierz **+ Dodaj**.

5. Wybierz **właściciela** roli.

6. Wybierz nazwę aplikacji utworzonych dla usługi podmiotu zabezpieczeń. Należy wpisać nazwę w polu wyszukiwania.

7. Kliknij pozycję **Zapisz**.

## <a name="deploy-a-kubernetes-cluster"></a>Wdrażanie klastra Kubernetes

1. Otwórz [portal Azure stosu](https://portal.local.azurestack.external).

2. Wybierz **+ nowy** > **obliczeniowe** > **klastra Kubernetes**. Kliknij przycisk **Utwórz**.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Wybierz **podstawy** w tworzenie Kubernetes klastra.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Wprowadź **nazwa użytkownika administratora maszyny Wirtualnej systemu Linux**. Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.

3. Wprowadź **klucz publiczny SSH** używane na potrzeby autoryzacji do wszystkich maszyn z systemem Linux utworzone jako część klastra Kubernetes i Menedżer DVM.

4. Wprowadź **prefiks DNS profilu wzorca** jest unikatowa w regionie. Musi to być region unikatową nazwę, taką jak `k8s-12345`. Spróbuj wybrać go grupą zasobów nazwy jako najlepsze praktyki.

    > [!Note]  
    > Dla każdego klastra użyj prefiks DNS nowych i unikatowych głównego profilu.

5. Wprowadź **liczba profilu puli agentów**. Liczba zawiera liczby agentów w klastrze. Może być z zakresu od 1 do 4.

6. Wprowadź **ClientId nazwy głównej usługi** jest używany przez dostawcę chmury Kubernetes Azure.

7. Wprowadź **klucz tajny klienta nazwy głównej usługi** utworzonego podczas tworzenia aplikacji głównej usługi.

8. Wprowadź **wersja dostawcy chmury Kubernetes Azure**. To jest wersja dla dostawcy usługi Kubernetes Azure. Azure stosu zwalnia niestandardowej kompilacji Kubernetes dla każdej wersji Azure stosu.

9. Wybierz użytkownika **subskrypcji** identyfikatora.

10. Wprowadź nazwę nowej grupy zasobów lub wybierz istniejącą grupę zasobów. Nazwa zasobu musi być alfanumeryczny i małe litery.

11. Wybierz **lokalizacji** grupy zasobów. Jest to region, wybranym dla instalacji programu Azure stosu.

### <a name="specify-the-azure-stack-settings"></a>Określ ustawienia stosu Azure

1. Wybierz **ustawienia sygnatury Azure stosu**.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Wprowadź **dzierżawy punktu końcowego Arm**. To jest punkt końcowy usługi Azure Resource Manager nawiązać Utwórz grupę zasobów klastra Kubernetes. Należy uzyskać z operatorem stosu Azure zintegrowany system punktu końcowego. Dla usługi Azure stosu Development Kit (ASDK), można użyć `https://management.local.azurestack.external`.

3. Wprowadź **identyfikator dzierżawcy** dla dzierżawy. Jeśli potrzebujesz pomocy w znajdowaniu tej wartości, zobacz [uzyskanie Identyfikatora dzierżawy](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Połączyć się z klastrem

Teraz można przystąpić do nawiązania połączenia z klastrem. Wzorzec znajduje się w grupie zasobów klastra i nosi nazwę `k8s-master-<sequence-of-numbers>`. Podłącz do wzorca za pomocą klienta SSH. We wzorcu, można użyć **kubectl**, Kubernetes klient wiersza polecenia do zarządzania klastrem. Aby uzyskać instrukcje, zobacz [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Można również znaleźć **Helm** Menedżera pakietów jest przydatne w przypadku instalowania i wdrażania aplikacji do klastra. Aby uzyskać instrukcje dotyczące instalowania i używania Helm z klastrem, zobacz [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie klastra Kubernetes w portalu Marketplace, (dla operatora Azure stosu)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
