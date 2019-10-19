---
title: Wyświetl Azure Monitor dla dzienników kontenerów w czasie rzeczywistym | Microsoft Docs
description: W tym artykule opisano widok w czasie rzeczywistym dzienników kontenerów (stdout/stderr) i zdarzenia bez używania polecenia kubectl z Azure Monitor dla kontenerów.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 25cfe10ec192f874d050bca22ce1b85c2d1afbb4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554091"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Jak wyświetlać dzienniki i zdarzenia w czasie rzeczywistym (wersja zapoznawcza)
Azure Monitor dla kontenerów zawiera funkcję, która jest obecnie dostępna w wersji zapoznawczej, która umożliwia wyświetlanie na żywo w usłudze Azure Kubernetes Service (AKS) Logs (stdout/stderr) i zdarzenia bez konieczności uruchamiania poleceń polecenia kubectl. Po wybraniu jednej z tych opcji nowe okienko zostanie wyświetlone poniżej tabeli dane wydajności w **węźle węzły**, **Kontrolery**i **kontenery** . Pokazuje rejestrowanie na żywo i zdarzenia wygenerowane przez aparat kontenera, aby dodatkowo pomóc w rozwiązywaniu problemów w czasie rzeczywistym.

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w tym na platformie Azure (Chiny). Nie jest ona obecnie dostępna w systemie Azure dla instytucji rządowych USA.

>[!NOTE]
>Aby ta funkcja działała, wymagany jest dostęp roli użytkownika do zasobu klastra **usługi Azure Kubernetes** . [Dowiedz się więcej o roli użytkownika klastra usługi Azure Kubernetes](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Dzienniki na żywo obsługują trzy różne metody kontroli dostępu do dzienników:

1. AKS bez włączonej autoryzacji RBAC Kubernetes
2. AKS włączone z autoryzacją RBAC Kubernetes
3. AKS włączone z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD)

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Klaster Kubernetes bez włączonego RBAC
 
Jeśli masz klaster Kubernetes, który nie jest skonfigurowany przy użyciu autoryzacji RBAC Kubernetes lub jest zintegrowany z logowaniem jednokrotnym usługi Azure AD, nie musisz wykonywać tych czynności. Ponieważ autoryzacja Kubernetes korzysta z interfejsu API polecenia, wymagane są uprawnienia tylko do odczytu.

## <a name="kubernetes-rbac-authorization"></a>Autoryzacja Kubernetes RBAC
Jeśli włączono autoryzację Kubernetes RBAC, należy zastosować powiązanie roli klastra. W poniższych przykładowych krokach pokazano, jak skonfigurować powiązanie roli klastra z tego szablonu konfiguracji YAML. 

1. Skopiuj i wklej plik YAML i Zapisz go jako LogReaderRBAC. YAML.  

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

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurowanie AKS z Azure Active Directory

AKS można skonfigurować do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników. Jeśli konfigurujesz ją po raz pierwszy, zobacz [integrowanie Azure Active Directory z usługą Azure Kubernetes](../../aks/azure-ad-integration.md). Podczas wykonywania kroków w celu utworzenia [aplikacji klienckiej](../../aks/azure-ad-integration.md#create-the-client-application)określ następujące elementy:

-  **Identyfikator URI przekierowania**: należy utworzyć dwa typy aplikacji **sieci Web** . Pierwszą podstawową wartością adresu URL powinna być `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, a druga podstawowa wartość adresu URL powinna być `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Po zarejestrowaniu aplikacji na stronie **Przegląd** wybierz pozycję **uwierzytelnianie** w okienku po lewej stronie. Na stronie **uwierzytelnianie** w obszarze **Ustawienia zaawansowane** niejawnie Udziel **tokenów dostępu** i **tokenów identyfikatorów** , a następnie Zapisz zmiany.

>[!NOTE]
>Jeśli używasz tej funkcji w regionie platformy Azure (Chiny), pierwszą podstawową wartością adresu URL powinna być `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, a druga podstawowa wartość adresu URL powinna być `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

>[!NOTE]
>Konfigurowanie uwierzytelniania przy użyciu Azure Active Directory logowania jednokrotnego można wykonać tylko podczas początkowego wdrażania nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego dla klastra AKS, który już został wdrożony.
  
>[!IMPORTANT]
>W przypadku zmiany konfiguracji usługi Azure AD na potrzeby uwierzytelniania użytkowników przy użyciu zaktualizowanego identyfikatora URI Wyczyść pamięć podręczną przeglądarki, aby upewnić się, że zaktualizowany token uwierzytelniania zostanie pobrany i zastosowany.   

## <a name="view-live-logs-and-events"></a>Wyświetlanie dzienników i zdarzeń na żywo

Zdarzenia dziennika czasu rzeczywistego można wyświetlać w miarę ich generowania przez aparat kontenera z **węzłów**, **kontrolerów**i widoku **kontenerów** . W okienku właściwości wybierz opcję **Wyświetl dane na żywo (wersja zapoznawcza)** , a okienko jest prezentowane poniżej tabeli danych wydajności, w której można wyświetlić dziennik i zdarzenia w strumieniu ciągłym.

![Opcja Wyświetl dzienniki na żywo w okienku właściwości węzła](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Komunikaty dzienników i zdarzeń są ograniczone w zależności od tego, jaki typ zasobów został wybrany w widoku.

| Wyświetl | Typ zasobu | Dziennik lub zdarzenie | Przedstawione dane |
|------|---------------|--------------|----------------|
| Węzły | Węzeł | Wydarzenie | Po wybraniu węzła zdarzenia nie są filtrowane i wyświetlają zdarzenia Kubernetes obejmujące cały klaster. W tytule okienka wyświetlana jest nazwa klastra. |
| Węzły | Pod | Wydarzenie | Gdy wybrane zdarzenia na obszarze są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw pod. | 
| Rejestrowanie | Pod | Wydarzenie | Gdy wybrane zdarzenia na obszarze są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw pod. |
| Rejestrowanie | Kontroler | Wydarzenie | Po wybraniu kontrolera zdarzenia są filtrowane do jego przestrzeni nazw. Tytuł okienka pokazuje przestrzeń nazw kontrolera. |
| Węzły/kontrolery/kontenery | Kontener | Dzienniki | W tytule okienka wyświetlana jest nazwa, pod którą jest zgrupowany kontener. |

Jeśli klaster AKS jest skonfigurowany z logowaniem jednokrotnym przy użyciu usługi AAD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i Ukończ uwierzytelnianie na platformie Azure.  

Po pomyślnym uwierzytelnieniu okienko Live log pojawi się w dolnej części środkowego okienka. Jeśli wskaźnik stanu pobierania zawiera zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że może pobrać dane.
    
  ![Okienko dzienników na żywo pobrało dane](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na pasku wyszukiwania można filtrować według kluczowych wyrazów, aby wyróżnić ten tekst w dzienniku lub zdarzeniu, a na pasku wyszukiwania po prawej stronie pokazuje, ile wyników jest zgodnych z filtrem.

  ![Przykład filtru okienka dzienników dynamicznych](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki, używając **filtru** pill znalezionego po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pill wyświetla pozycję pod, przestrzeń nazw lub klaster do wybrania.  

Aby zawiesić automatyczne przewijanie i kontrolować zachowanie okienka i umożliwić ręczne przewijanie nowych danych, kliknij opcję **przewijania** . Aby ponownie włączyć Autoprzewijanie, po prostu kliknij opcję **przewijania** ponownie. Możesz również wstrzymywać pobieranie danych dziennika lub zdarzeń, klikając opcję **Wstrzymaj** i gdy wszystko będzie gotowe do wznowienia, po prostu kliknij pozycję **Odtwórz**.  

![Okienko dzienników na żywo Wstrzymaj widok na żywo](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Możesz przejść do dzienników Azure Monitor, aby wyświetlić historyczne dzienniki kontenerów, wybierając opcję **Wyświetl dzienniki kontenerów** z listy rozwijanej **w obszarze Analiza**.

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować uczenie się, jak używać Azure Monitor i monitorować inne aspekty klastra AKS, zobacz [Wyświetlanie usługi Azure Kubernetes Service Health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
