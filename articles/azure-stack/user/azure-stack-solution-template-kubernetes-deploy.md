---
title: Wdrażanie rozwiązania Kubernetes do usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć rozwiązanie Kubernetes usługi Azure Stack.
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
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 4aa74aebfd9e0ef6c71913c76181a07045aa3ad7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231223"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Wdrażanie rozwiązania Kubernetes do usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej.

Następujący artykuł patrzy na przy użyciu szablonu rozwiązania usługi Azure Resource Manager do wdrażania i przydzielanie zasobów dla rozwiązania Kubernetes w jednej, skoordynowanej operacji. Możesz należy do zebrania wymaganych informacji dotyczących instalacji usługi Azure Stack, Generowanie szablonu, a następnie wdrożyć do chmury. Uwaga szablonu nie jest tego samego zarządzanych usług AKS oferowanych na platformie Azure globalnego.

## <a name="kubernetes-and-containers"></a>Kubernetes i kontenery

Można zainstalować usługi Kubernetes przy użyciu szablonów usługi Azure Resource Manager, generowane przez aparat usługi ACS w usłudze Azure Stack. [Kubernetes](https://kubernetes.io) to system typu open source do automatyzowania wdrażania, skalowania i zarządzania nimi aplikacji w kontenerach. A [kontenera](https://www.docker.com/what-container) jest zawarty w obrazie, podobne do maszyny Wirtualnej. W przeciwieństwie do maszyny Wirtualnej obraz kontenera zawiera tylko zasoby potrzebne do uruchomienia aplikacji, takich jak kod, środowisko uruchomieniowe do wykonania kodu, określonych bibliotek i ustawień.

Rozwiązanie Kubernetes można użyć:

- Twórz wysoce skalowalne, można uaktualnić, aplikacje, które można wdrożyć w kilka sekund. 
- Uproszczenie projektowania aplikacji i zwiększyć jej niezawodność przez różne aplikacje narzędzia Helm. [Polecenie Helm](https://github.com/kubernetes/helm) to narzędzie open source pakietu, które pomaga zainstalować i zarządzanie cyklem życia aplikacji platformy Kubernetes.
- Łatwe monitorowanie i diagnozowanie kondycji aplikacji w skali i uaktualniania funkcji.

Użytkownik zostanie naliczona tylko za wykorzystanie mocy obliczeniowej, wymagane przez węzły obsługi klastra. Aby uzyskać więcej informacji, zobacz [użycie i rozliczenia w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="prerequisites"></a>Wymagania wstępne 

Aby rozpocząć pracę, upewnij się, że masz odpowiednie uprawnienia, i że usługi Azure Stack jest gotowy.

1. Upewnij się, że możesz tworzyć aplikacje w Twojej dzierżawie usługi Azure Active Directory (Azure AD). Te uprawnienia są potrzebne do wdrożenia rozwiązania Kubernetes.

    Aby uzyskać instrukcje dotyczące sprawdzania uprawnień, zobacz [uprawnień Sprawdź usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Generowanie publicznego i prywatnego pary kluczy SSH do logowania do maszyny Wirtualnej systemu Linux w usłudze Azure Stack. Klucz publiczny będą potrzebne podczas tworzenia klastra.

    Aby uzyskać instrukcje dotyczące generowania klucza, zobacz [generowania klucza SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Sprawdź, czy masz ważnej subskrypcji w portalu dzierżawcy usługi Azure Stack, i że masz wystarczająco dużo publiczny adres IP adresów dostępnych do dodania nowych aplikacji.

    Nie można wdrożyć klaster do usługi Azure Stack **administratora** subskrypcji. Należy użyć **użytkownika** subskrypcji. 

1. Jeśli nie masz klastra Kubernetes w portalu marketplace, Porozmawiaj z administratorem usługi Azure Stack.

## <a name="create-a-service-principal-in-azure-ad"></a>Tworzenie jednostki usługi w usłudze Azure AD

1. Zaloguj się do globalnej [witryny Azure portal](http://portal.azure.com).

1. Upewnij się, że możesz zalogować się przy użyciu dzierżawy usługi Azure AD skojarzonego z wystąpieniem usługi Azure Stack. Logowania można przełączać, klikając ikonę filtru na pasku narzędzi platformy Azure.

    ![Wybierz dzierżawę usługi AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Utwórz aplikację usługi Azure AD.

    a. Wybierz **usługi Azure Active Directory** > **+ rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

    b. Wprowadź **nazwa** aplikacji.

    c. Wybierz **aplikacji sieci Web / interfejs API**.

    d. Wprowadź `http://localhost` dla **adres URL logowania**.

    c. Kliknij pozycję **Utwórz**.

1. Zanotuj wartość **Identyfikatora aplikacji**. Identyfikator będą potrzebne podczas tworzenia klastra. Identyfikator jest określany jako **identyfikator klienta jednostki usługi**.

1. Wybierz **ustawienia** > **klucze**.

    a. Wprowadź **opis**.

    b. Wybierz **nigdy nie wygasa** dla **Expires**.

    c. Wybierz pozycję **Zapisz**. Upewnij się, należy pamiętać, ciąg klucza. Ciąg klucza będą potrzebne podczas tworzenia klastra. Klucz jest określany jako **klucz tajny klienta jednostki usługi**.


## <a name="give-the-service-principal-access"></a>Nadaj dostęp do nazwy głównej usługi

Przekazać jednostce usługi dostępu do Twojej subskrypcji, aby podmiot zabezpieczeń można utworzyć zasobów.

1.  Zaloguj się do [portalu Azure Stack](https://portal.local.azurestack.external/).

1. Wybierz **wszystkich usług** > **subskrypcje**.

1. Wybierz subskrypcję, utworzone przez użytkownika operatora dla przy użyciu klastra Kubernetes.

1. Wybierz **kontrola dostępu (IAM)** > Wybierz **+ Dodaj**.

1. Wybierz **Współautor** roli.

1. Wybierz nazwę aplikacji utworzonych dla usługi podmiotu zabezpieczeń. Może być konieczne w polu wyszukiwania wpisz nazwę.

1. Kliknij pozycję **Zapisz**.

## <a name="deploy-a-kubernetes"></a>Wdrażanie usługi Kubernetes

1. Otwórz [portalu Azure Stack](https://portal.local.azurestack.external).

1. Wybierz **+ Utwórz zasób** > **obliczenia** > **klastra Kubernetes**. Kliknij pozycję **Utwórz**.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Podstawy

1. Wybierz **podstawy** w tworzenie klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wybierz swoje **subskrypcji** identyfikatora.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz istniejącą grupę zasobów. Nazwa zasobu musi być alfanumeryczne i małe litery.

1. Wybierz **lokalizacji** grupy zasobów. Jest to region, w wybranym dla instalacji programu Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Ustawienia klastra Kubernetes

1. Wybierz **ustawienia klastra Kubernetes** w tworzenie klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Wprowadź **nazwa użytkownika administratora maszyny Wirtualnej systemu Linux**. Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.

1. Wprowadź **klucz publiczny SSH** używane na potrzeby autoryzacji do wszystkich utworzonych jako część klastra Kubernetes i Menedżer DVM maszyn z systemem Linux.

1. Wprowadź **prefiks DNS profilu wzorzec** jest unikatowa w regionie. Musi to być region unikatową nazwę, taką jak `k8s-12345`. Spróbuj wybrać jej nazwę takie same jak grupy zasobów jako najlepsze rozwiązanie.

    > [!Note]  
    > Dla każdego klastra użyj prefiks DNS nowych i unikatowych głównym profilu.

1. Wybierz **liczba profil puli wzorców Kubernetes**. Liczba zawiera liczbę węzłów w puli wzorca. Może być z zakresu od 1 do 7. Ta wartość powinna być nieparzysta.

1. Wybierz **VMSize głównych maszyn wirtualnych Kubernetes**.

1. Wybierz **liczba profil puli węzłów rozwiązania Kubernetes**. Liczba zawiera liczbę agentów w klastrze. 

1. Wybierz **profil magazynu**. Możesz wybrać **obiektu Blob dysku** lub **dysku zarządzanego**. To ustawienie określa maszyn wirtualnych węzła rozmiar maszyny Wirtualnej z platformy Kubernetes. 

1. Wprowadź **ClientId nazwy głównej usługi** jest on używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta zidentyfikowane jako identyfikator aplikacji podczas Twojej utworzone jednostki usługi.

1. Wprowadź **klucz tajny klienta jednostki usługi** utworzony podczas tworzenia jednostki usługi.

1. Wprowadź **wersja dostawcy chmury Kubernetes Azure**. To jest wersja dostawcy usługi Kubernetes Azure. Usługa Azure Stack zwalnia niestandardowej kompilacji rozwiązania Kubernetes dla każdej wersji usługi Azure Stack.

### <a name="3-summary"></a>3. Podsumowanie

1. Wybierz pozycję podsumowanie. Blok wyświetla komunikat sprawdzania poprawności ustawień konfiguracji klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Przejrzyj ustawienia.

3. Wybierz **OK** do wdrożenia klastra.

> [!TIP]  
>  Jeśli masz pytania dotyczące wdrożenia, możesz Opublikuj swoje pytanie lub zobaczyć, jeśli ktoś ma już odpowiedzi na pytania w [forum poświęcone systemowi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="connect-to-your-cluster"></a>Połącz z klastrem

Teraz można przystąpić do połączenia z klastrem. Wzorzec znajdują się w grupie zasobów klastra i nosi nazwę `k8s-master-<sequence-of-numbers>`. Użyj klienta SSH, aby nawiązać połączenie z wzorcem. We wzorcu, można użyć **kubectl**, klienta wiersza polecenia Kubernetes do zarządzania klastrem. Aby uzyskać instrukcje, zobacz [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Można również znaleźć **Helm** Menedżera pakietów jest przydatne w przypadku instalowania i wdrażania aplikacji do klastra. Aby uzyskać instrukcje dotyczące instalowania i używania narzędzia Helm z klastrem, zobacz [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Kolejne kroki

[Dodaj rozwiązania Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
