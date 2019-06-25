---
title: Wyświetlanie usługi Azure Monitor dla kontenerów dzienników w czasie rzeczywistym | Dokumentacja firmy Microsoft
description: W tym artykule opisano widok w czasie rzeczywistym dzienniki z kontenerów (stdout/stderr) oraz zdarzenia bez używania narzędzia kubectl za pomocą usługi Azure Monitor dla kontenerów.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 7fd9248fd38054b7f0e1fad2888d8b0d4cf2e60c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274227"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Sposób wyświetlania dzienników i zdarzeń w czasie rzeczywistym (wersja zapoznawcza)
Usługa Azure Monitor dla kontenerów zawiera funkcję, która jest obecnie dostępna w wersji zapoznawczej, która zawiera widok na żywo w sieci Web i zdarzenia dzienników kontenera usługi Azure Kubernetes Service (AKS) (stdout/stderr) bez konieczności uruchamiania poleceń kubectl. Gdy wybierzesz dowolną opcję, zostanie wyświetlone nowe okienko pod tabelą danych wydajności w **węzłów**, **kontrolerów**, i **kontenery** widoku. Pokazuje rejestrowanie na żywo i zdarzenia generowane przez aparat container ułatwiających dalsze rozwiązywanie problemów w czasie rzeczywistym.

>[!NOTE]
>**Współautor** dostęp do zasobu klastra jest wymagany dla tej funkcji do pracy.
>

Dzienniki na żywo obsługuje trzy różne metody kontroli dostępu do dzienników:

1. AKS, bez autoryzacji Kubernetes RBAC włączone
2. Włączone z autoryzacji RBAC platformy Kubernetes w usłudze AKS
3. Włączone za pomocą usługi Azure Active Directory (AD) opartej na SAML logowania jednokrotnego w usłudze AKS

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

2. Jeśli konfigurujesz je po raz pierwszy, utworzyć powiązanie reguły klastra, uruchamiając następujące polecenie: `kubectl create -f LogReaderRBAC.yaml`. Jeśli wcześniej włączony pomocy technicznej dla dzienniki na żywo w wersji zapoznawczej przed wprowadziliśmy dzienniki zdarzeń na żywo, aby zaktualizować konfigurację, uruchom następujące polecenie: `kubectl apply -f LogReaderRBAC.yml`.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurowanie usługi AKS przy użyciu usługi Azure Active Directory

AKS można skonfigurować do uwierzytelniania użytkowników usługi Azure Active Directory (AD). Jeśli konfigurujesz je po raz pierwszy, zobacz [integracji usługi Azure Active Directory z usługą Azure Kubernetes Service](../../aks/azure-ad-integration.md). Podczas wykonywania czynności, aby utworzyć [aplikacja kliencka](../../aks/azure-ad-integration.md#create-the-client-application), podaj następujące informacje:

- **(Opcjonalnie) identyfikator URI przekierowania**: Jest to **Web** typu aplikacji i wartości bazowej adres URL powinien być `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Po zarejestrowaniu aplikacji z **Przegląd** stronie wybierz **uwierzytelniania** z okienka po lewej stronie. Na **uwierzytelniania** w obszarze **Zaawansowane ustawienia** niejawnie udzielić **tokeny dostępu** i **tokeny Identyfikatora** , a następnie zapisz swoje zmiany.

>[!NOTE]
>Konfigurowanie uwierzytelniania za pomocą usługi Azure Active Directory dla logowania jednokrotnego można wykonywać tylko podczas początkowego wdrożenia nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego na dla klastra usługi AKS już wdrożone.
  
>[!IMPORTANT]
>W przypadku zmiany konfiguracji usługi Azure AD do uwierzytelniania użytkowników za pomocą zaktualizowanego identyfikatora URI należy wyczyścić pamięć podręczną w przeglądarce, aby upewnić się, token uwierzytelniania zaktualizowane zostaną pobrane i zastosowane.   

## <a name="view-live-logs-and-events"></a>Wyświetl dzienniki na żywo i zdarzenia

Można wyświetlać zdarzenia dziennika w czasie rzeczywistym, wygenerowane przez aparat kontenera z **węzłów**, **kontrolerów**, i **kontenery** widoku. W okienku właściwości wybierz **wyświetlać dane na żywo (wersja zapoznawcza)** opcji i okienko znajduje się poniżej w tabeli danych wydajności, gdzie można przejrzeć dziennik i zdarzenia w sposób ciągły.

![Opcja dzienniki na żywo widok okienka właściwości węzła](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Komunikaty dzienników i zdarzeń są ograniczone, oparte na typ zasobu jest wybrany w widoku.

| Widok | Typ zasobu | Dziennik lub zdarzenie | Dane prezentowane |
|------|---------------|--------------|----------------|
| Węzły | Węzeł | Wydarzenie | Po wybraniu węzła zdarzenia nie są filtrowane i Pokaż całego klastra Kubernetes zdarzenia. Tytuł okienko zawiera nazwę klastra. |
| Węzły | Zasobnik | Wydarzenie | Po wybraniu zasobnik zdarzenia są filtrowane, aby jego przestrzeń nazw. Tytuł okienko zawiera przestrzeń nazw zasobnik. | 
| Kontrolery | Zasobnik | Wydarzenie | Po wybraniu zasobnik zdarzenia są filtrowane, aby jego przestrzeń nazw. Tytuł okienko zawiera przestrzeń nazw zasobnik. |
| Kontrolery | Kontroler | Wydarzenie | Po wybraniu kontrolera zdarzenia są filtrowane, aby jego przestrzeń nazw. W tytule okienka wyświetlany obszaru nazw kontrolera. |
| Węzły/kontrolerów/kontenerów | Kontener | Dzienniki | Tytuł okienko pokazuje, że nazwa zasobnika kontenera jest zgrupowany z. |

Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi AAD, wyświetlany jest monit o uwierzytelnianie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i ukończenia uwierzytelniania za pomocą platformy Azure.  

Po pomyślnym uwierzytelnieniu w dolnej części środkowym okienku pojawi się okienko dziennika na żywo. Jeśli wskaźnik stanu pobierania wyświetlany zielony znacznik wyboru, który znajduje się w prawym okienku, oznacza to, że dane mogą być przywracane.
    
  ![Data pobrania okienka dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na pasku wyszukiwania można filtrować według słowa kluczowego, aby zaznaczyć ten tekst w dzienniku lub zdarzenia, a następnie na pasku wyszukiwania, na końcu z prawej strony, pokazuje, ile wyników dopasowania się filtru.

  ![Przykład filtr w okienku dzienniki na żywo](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Podczas wyświetlania zdarzeń, mogą dodatkowo ograniczyć wyniki za pomocą **filtru** skażone znaleźć po prawej stronie na pasku wyszukiwania. W zależności od zasobów, jakich wybrano skażone wymieniono zasobników, przestrzeń nazw lub klastra, aby wybrać z.  

Aby wstrzymać automatyczne przewijanie i kontrolować zachowanie okienka umożliwiają ręczne przewijanie przez nowe dane odczytu, kliknij **przewijania** opcji. Aby ponownie włączyć automatyczne przewijanie, wystarczy kliknąć łącze **przewijania** opcji ponownie. Pobieranie danych dziennika lub zdarzenia można również wstrzymać, klikając **wstrzymać** opcji, a gdy wszystko jest gotowe do wznowienia, wystarczy kliknąć **Odtwórz**.  

![Dzienniki na żywo wstrzymania na żywo widoku](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Możesz przejść do dzienników monitora platformy Azure, aby wyświetlić dzienniki kontenerów historycznych, wybierając **wyświetlanie dzienników kontenera** z listy rozwijanej **Wyświetl w obszarze analiza**.

## <a name="next-steps"></a>Kolejne kroki
- Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
- Widok [dziennika przykłady zapytań](container-insights-log-search.md#search-logs-to-analyze-data) wstępnie zdefiniowane zapytania i przykłady do oceny lub Dostosuj alerty, wizualizacji i analizowanie klastry usługi.
