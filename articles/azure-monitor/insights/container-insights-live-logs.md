---
title: Wyświetlanie usługi Azure Monitor dla kontenerów dzienników w czasie rzeczywistym | Dokumentacja firmy Microsoft
description: W tym artykule opisano widok czasie rzeczywistym dzienniki z kontenerów (stdout/stderr) bez używania narzędzia kubectl za pomocą usługi Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904750"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Jak wyświetlić kontenera dzienników czasu rzeczywistego z usługą Azure Monitor dla kontenerów (wersja zapoznawcza)
Ta funkcja, która jest obecnie dostępna w wersji zapoznawczej, zapewnia wgląd w czasie rzeczywistym w dzienników kontenera usługi Azure Kubernetes Service (AKS) (stdout/stderr) bez konieczności uruchamiania poleceń kubectl. Po wybraniu tej opcji, zostanie wyświetlone nowe okienko pod tabelą danych wydajności kontenerów na **kontenery** widoku.  Przedstawia on na żywo rejestrowania generowane przez aparat container ułatwiających dalsze rozwiązywanie problemów w czasie rzeczywistym. **Współautor** dostęp do zasobu klastra jest wymagany dla tej funkcji do pracy.

Na żywo dzienniki obsługuje trzy różne metody kontroli dostępu do dzienników:

1. AKS, bez autoryzacji Kubernetes RBAC włączone 
2. Włączone z autoryzacji RBAC platformy Kubernetes w usłudze AKS
3. AKS, włączone za pomocą usługi Azure Active Directory (AD) SAML na podstawie logowania jednokrotnego 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Klaster Kubernetes bez RBAC włączone
 
Jeśli masz klaster Kubernetes, nie jest skonfigurowany z autoryzacji Kubernetes RBAC lub zintegrowana z usługą Azure AD logowania jednokrotnego, nie musisz wykonaj następujące kroki. Ponieważ autoryzacji Kubernetes korzysta z interfejsu api rozwiązania kubernetes, uprawnienia tylko do odczytu są wymagane.

## <a name="kubernetes-rbac-authorization"></a>Autoryzacji RBAC usługi Kubernetes
Po włączeniu autoryzacji RBAC platformy Kubernetes, należy zastosować powiązania roli klastra. Krokach w poniższym przykładzie pokazano sposób konfigurowania powiązania rolę klastra za pomocą tego szablonu konfiguracji yaml.   

1. Skopiuj i wklej plik yaml i zapisz go jako LogReaderRBAC.yaml.  

   ```
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRole 
   metadata: 
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""] 
        resources: ["pods/log"] 
        verbs: ["get"] 
   --- 
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRoleBinding 
   metadata: 
      name: containerHealth-read-logs-global 
   roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
   subjects: 
      - kind: User 
        name: clusterUser 
        apiGroup: rbac.authorization.k8s.io 
   ```

2. Utwórz powiązanie reguły klastra, uruchamiając następujące polecenie: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurowanie usługi AKS przy użyciu usługi Azure Active Directory
AKS można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). Jeśli konfigurujesz to po raz pierwszy, zobacz [integracji usługi Azure Active Directory z usługą Azure Kubernetes Service](../../aks/aad-integration.md). Podczas wykonywania czynności, aby utworzyć [aplikacja kliencka](../../aks/aad-integration.md#create-client-application) i określ **identyfikator URI przekierowania**, należy dodać inny identyfikator URI do listy `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>Konfigurowanie uwierzytelniania za pomocą usługi Azure Active Directory dla logowania jednokrotnego można wykonywać tylko podczas wstępnej wdrożenia nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego na dla klastra usługi AKS już wdrożone.  
> 

## <a name="view-live-logs"></a>Wyświetl dzienniki na żywo
Kiedy przeglądasz **kontenery**, możesz **dzienniki kontenera widoku** lub **wyświetlić dzienniki na żywo kontenera**.  Po wybraniu **dzienniki na żywo kontenera widoku**, nowe okienko zostanie wyświetlone poniżej tabeli danych wydajności kontenerów i na żywo rejestrowanie przedstawiający, generowane przez aparat container ułatwiających dalsze rozwiązywanie problemów w czasie rzeczywistym.  
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Z **Microsoft Azure** menu, wybierz opcję **Monitor** , a następnie wybierz **kontenery**.  
3. Wybierz kontener z listy w obszarze **monitorowania kontenerów** widoku.  
4. Wybierz **kontenery** widoku i na panelu Właściwości dla wybranego kontenera łącze **wyświetlić dzienniki na żywo kontenera** znajduje się na liście.  
5. Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi AAD, wyświetlany jest monit o uwierzytelnianie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i ukończenia uwierzytelniania za pomocą platformy Azure.  

Po pomyślnym uwierzytelnieniu w dolnej części środkowym okienku pojawi się okienko dziennika na żywo. Jeśli wskaźnik stanu pobierania wyświetlany zielony znacznik wyboru, który znajduje się w prawym okienku, oznacza to, że dane mogą być przywracane.
    
  ![Data pobrania okienka dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na pasku wyszukiwania można filtrować według słowa kluczowego, aby zaznaczyć ten tekst w dzienniku.   

  ![Przykład filtr w okienku dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Aby wstrzymać automatyczne przewijanie i kontrolowania zachowania okienka umożliwiają ręczne przewijanie przez nowe dane dziennika, przeczytaj, kliknij **przewijania** opcji.  Aby ponownie włączyć automatyczne przewijanie, wystarczy kliknąć łącze **przewijania** opcji ponownie.  Pobieranie danych dziennika można również wstrzymać, klikając **wstrzymać** opcji, a gdy wszystko jest gotowe do wznowienia, wystarczy kliknąć **Odtwórz**.  

![Dzienniki na żywo wstrzymania na żywo widoku](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Kolejne kroki
Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
