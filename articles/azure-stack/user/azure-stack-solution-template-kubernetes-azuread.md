---
title: Wdrażanie rozwiązania Kubernetes do usługi Azure Stack przy użyciu usługi Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć rozwiązanie Kubernetes usługi Azure Stack przy użyciu usługi Azure Active Directory (Azure AD).
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
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c207fb866cdd17e4db1796e415c159eb887eef08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243559"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Wdrażanie rozwiązania Kubernetes do usługi Azure Stack przy użyciu usługi Azure Active Directory

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej.

Możesz wykonać kroki opisane w tym artykule, aby wdrożyć i skonfigurować zasoby dla rozwiązania Kubernetes, gdy Twoja usługa zarządzania tożsamościami, w ramach pojedynczej koordynowany operacji przy użyciu usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, upewnij się, że masz odpowiednie uprawnienia, i że usługi Azure Stack jest gotowy.

1. Upewnij się, że możesz tworzyć aplikacje w Twojej dzierżawie usługi Azure Active Directory (Azure AD). Te uprawnienia są potrzebne do wdrożenia rozwiązania Kubernetes.

    Aby uzyskać instrukcje dotyczące sprawdzania uprawnień, zobacz [uprawnień Sprawdź usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Generowanie publicznego i prywatnego pary kluczy SSH do logowania do maszyny Wirtualnej systemu Linux w usłudze Azure Stack. Klucz publiczny będą potrzebne podczas tworzenia klastra.

    Aby uzyskać instrukcje dotyczące generowania klucza, zobacz [generowania klucza SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Sprawdź, czy masz ważnej subskrypcji w portalu dzierżawcy usługi Azure Stack, i że masz wystarczająco dużo publiczny adres IP adresów dostępnych do dodania nowych aplikacji.

    Nie można wdrożyć klaster do usługi Azure Stack **administratora** subskrypcji. Należy użyć **użytkownika** subskrypcji. 

1. Jeśli nie masz klastra Kubernetes w portalu marketplace, skontaktuj się z administratorem usługi Azure Stack.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Skonfiguruj nazwę główną usługi na platformie Azure. Nazwa główna usługi daje aplikacji dostęp do zasobów usługi Azure Stack.

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

1. Wybierz **kontrola dostępu (IAM)** > Wybierz **Dodaj przypisanie roli**.

1. Wybierz **Współautor** roli.

1. Wybierz nazwę aplikacji utworzonych dla usługi podmiotu zabezpieczeń. Może być konieczne w polu wyszukiwania wpisz nazwę.

1. Kliknij pozycję **Zapisz**.

## <a name="deploy-kubernetes"></a>Wdrażanie rozwiązania Kubernetes

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

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Wprowadź **nazwa użytkownika administratora maszyny Wirtualnej systemu Linux**. Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.

1. Wprowadź **klucz publiczny SSH** używane na potrzeby autoryzacji do wszystkich utworzonych jako część klastra Kubernetes i Menedżer DVM maszyn z systemem Linux.

1. Wprowadź **prefiks DNS profilu wzorzec** jest unikatowa w regionie. Musi to być region unikatową nazwę, taką jak `k8s-12345`. Spróbuj wybrać jej nazwę takie same jak grupy zasobów jako najlepsze rozwiązanie.

    > [!Note]  
    > Dla każdego klastra użyj prefiks DNS nowych i unikatowych głównym profilu.

1. Wybierz **liczba profil puli wzorców Kubernetes**. Liczba zawiera liczbę węzłów w puli wzorca. Może być z zakresu od 1 do 7. Ta wartość powinna być nieparzysta.

1. Wybierz **VMSize głównych maszyn wirtualnych Kubernetes**.

1. Wybierz **liczba profil puli węzłów rozwiązania Kubernetes**. Liczba zawiera liczbę agentów w klastrze. 

1. Wybierz **profil magazynu**. Możesz wybrać **obiektu Blob dysku** lub **dysku zarządzanego**. To ustawienie określa maszyn wirtualnych węzła rozmiar maszyny Wirtualnej z platformy Kubernetes. 

1. Wybierz **usługi Azure AD** dla **systemu tożsamości w usłudze Azure Stack** instalacji usługi Azure Stack. 

1. Wprowadź **ClientId nazwy głównej usługi** jest on używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta zidentyfikowane jako identyfikator aplikacji, podczas tworzenia jednostki usługi.

1. Wprowadź **klucz tajny klienta jednostki usługi** utworzony podczas tworzenia jednostki usługi.

1. Wprowadź **wersja dostawcy chmury Kubernetes Azure**. To jest wersja dostawcy usługi Kubernetes Azure. Usługa Azure Stack zwalnia niestandardowej kompilacji rozwiązania Kubernetes dla każdej wersji usługi Azure Stack.

### <a name="3-summary"></a>3. Podsumowanie

1. Wybierz pozycję podsumowanie. Blok wyświetla komunikat sprawdzania poprawności ustawień konfiguracji klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Przejrzyj ustawienia.

3. Wybierz **OK** do wdrożenia klastra.

> [!TIP]  
>  Jeśli masz pytania dotyczące wdrożenia, możesz Opublikuj swoje pytanie lub zobaczyć, jeśli ktoś ma już odpowiedzi na pytania w [forum poświęcone systemowi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Kolejne kroki

[Połącz z klastrem](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)