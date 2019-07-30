---
title: Wyświetlanie usługi Azure Monitor dla kontenerów dzienników w czasie rzeczywistym | Dokumentacja firmy Microsoft
description: W tym artykule opisano widok w czasie rzeczywistym dzienników kontenerów (stdout/stderr) i zdarzenia bez używania polecenia kubectl z Azure Monitor dla kontenerów.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 968ee4c8bb5d7e09ef3c345c46f6c7b839e0e25a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990033"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Jak wyświetlać dzienniki i zdarzenia w czasie rzeczywistym (wersja zapoznawcza)
Azure Monitor dla kontenerów zawiera funkcję, która jest obecnie dostępna w wersji zapoznawczej, która umożliwia wyświetlanie na żywo w usłudze Azure Kubernetes Service (AKS) Logs (stdout/stderr) i zdarzenia bez konieczności uruchamiania poleceń polecenia kubectl. Po wybraniu jednej z tych opcji nowe okienko zostanie wyświetlone poniżej tabeli dane wydajności w **węźle węzły**, **Kontrolery**i **kontenery** . Pokazuje rejestrowanie na żywo i zdarzenia wygenerowane przez aparat kontenera, aby dodatkowo pomóc w rozwiązywaniu problemów w czasie rzeczywistym.

>[!NOTE]
>Aby ta funkcja działała, wymagany jest dostęp współautora do zasobu klastra.
>

Dzienniki na żywo obsługują trzy różne metody kontroli dostępu do dzienników:

1. AKS, bez autoryzacji Kubernetes RBAC włączone
2. Włączone z autoryzacji RBAC platformy Kubernetes w usłudze AKS
3. AKS włączone z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD)

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Klaster Kubernetes bez RBAC włączone
 
Jeśli masz klaster Kubernetes, nie jest skonfigurowany z autoryzacji Kubernetes RBAC lub zintegrowana z usługą Azure AD logowania jednokrotnego, nie musisz wykonaj następujące kroki. Ponieważ autoryzacja Kubernetes korzysta z interfejsu API polecenia, wymagane są uprawnienia tylko do odczytu.

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
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
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

2. W przypadku konfigurowania go po raz pierwszy należy zastosować powiązanie reguły klastra, uruchamiając następujące polecenie: `kubectl create -f LogReaderRBAC.yaml`. Jeśli wcześniej włączono obsługę podglądu dzienników na żywo przed wprowadzeniem dzienników zdarzeń na żywo, aby zaktualizować konfigurację, uruchom następujące polecenie: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurowanie usługi AKS przy użyciu usługi Azure Active Directory

AKS można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). Jeśli konfigurujesz ją po raz pierwszy, zobacz Integrowanie [Azure Active Directory z usługą Azure Kubernetes](../../aks/azure-ad-integration.md). Podczas wykonywania kroków w celu utworzenia [aplikacji klienckiej](../../aks/azure-ad-integration.md#create-the-client-application)określ następujące elementy:

- **Identyfikator URI przekierowania (opcjonalnie)** : Jest to typ aplikacji **sieci Web** , a podstawowa wartość `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`adresu URL to.
- Po zarejestrowaniu aplikacji na stronie **Przegląd** wybierz pozycję **uwierzytelnianie** w okienku po lewej stronie. Na stronie **uwierzytelnianie** w obszarze **Ustawienia zaawansowane** niejawnie Udziel **tokenów dostępu** i **tokenów identyfikatorów** , a następnie Zapisz zmiany.

>[!NOTE]
>Konfigurowanie uwierzytelniania przy użyciu Azure Active Directory logowania jednokrotnego można wykonać tylko podczas początkowego wdrażania nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego na dla klastra usługi AKS już wdrożone.
  
>[!IMPORTANT]
>W przypadku zmiany konfiguracji usługi Azure AD na potrzeby uwierzytelniania użytkowników przy użyciu zaktualizowanego identyfikatora URI Wyczyść pamięć podręczną przeglądarki, aby upewnić się, że zaktualizowany token uwierzytelniania zostanie pobrany i zastosowany.   

## <a name="view-live-logs-and-events"></a>Wyświetlanie dzienników i zdarzeń na żywo

Zdarzenia dziennika czasu rzeczywistego można wyświetlać w miarę ich generowania przez aparat kontenera z **węzłów**, **kontrolerów**i widoku **kontenerów** . W okienku właściwości wybierz opcję **Wyświetl dane na żywo (wersja zapoznawcza)** , a okienko jest prezentowane poniżej tabeli danych wydajności, w której można wyświetlić dziennik i zdarzenia w strumieniu ciągłym.

![Opcja Wyświetl dzienniki na żywo w okienku właściwości węzła](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Komunikaty dzienników i zdarzeń są ograniczone w zależności od tego, jaki typ zasobów został wybrany w widoku.

| Widok | Typ zasobu | Dziennik lub zdarzenie | Przedstawione dane |
|------|---------------|--------------|----------------|
| Węzły | Węzeł | Wydarzenie | Po wybraniu węzła zdarzenia nie są filtrowane i wyświetlają zdarzenia Kubernetes obejmujące cały klaster. W tytule okienka wyświetlana jest nazwa klastra. |
| Węzły | Zasobnik | Wydarzenie | Gdy wybrane zdarzenia na obszarze są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw pod. | 
| Kontrolery | Zasobnik | Wydarzenie | Gdy wybrane zdarzenia na obszarze są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw pod. |
| Kontrolery | Kontroler | Wydarzenie | Po wybraniu kontrolera zdarzenia są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw kontrolera. |
| Węzły/kontrolery/kontenery | Kontener | Dzienniki | W tytule okienka wyświetlana jest nazwa, pod którą jest zgrupowany kontener. |

Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi AAD, wyświetlany jest monit o uwierzytelnianie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i ukończenia uwierzytelniania za pomocą platformy Azure.  

Po pomyślnym uwierzytelnieniu w dolnej części środkowym okienku pojawi się okienko dziennika na żywo. Jeśli wskaźnik stanu pobierania wyświetlany zielony znacznik wyboru, który znajduje się w prawym okienku, oznacza to, że dane mogą być przywracane.
    
  ![Data pobrania okienka dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na pasku wyszukiwania można filtrować według kluczowych wyrazów, aby wyróżnić ten tekst w dzienniku lub zdarzeniu, a na pasku wyszukiwania po prawej stronie pokazuje, ile wyników jest zgodnych z filtrem.

  ![Przykład filtr w okienku dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki, używając **filtru** pill znalezionego po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pill wyświetla pozycję pod, przestrzeń nazw lub klaster do wybrania.  

Aby zawiesić automatyczne przewijanie i kontrolować zachowanie okienka i umożliwić ręczne przewijanie nowych danych, kliknij opcję **przewijania** . Aby ponownie włączyć Autoprzewijanie, po prostu kliknij opcję **przewijania** ponownie. Możesz również wstrzymywać pobieranie danych dziennika lub zdarzeń, klikając opcję Wstrzymaj i gdy wszystko będzie gotowe do wznowienia, po prostu kliknij pozycję **Odtwórz**.  

![Dzienniki na żywo wstrzymania na żywo widoku](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Możesz przejść do dzienników Azure Monitor, aby wyświetlić historyczne dzienniki kontenerów, wybierając opcję **Wyświetl dzienniki kontenerów** z listy rozwijanej **w obszarze Analiza**.

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
