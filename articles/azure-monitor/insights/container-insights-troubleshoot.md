---
title: Jak rozwiązywać problemy z usługą Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać problemy z usługą Azure Monitor dla kontenerów i rozwiązywać je.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403381"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Rozwiązywanie problemów z usługą Azure Monitor dla kontenerów

Podczas konfigurowania monitorowania klastra usługi Azure Kubernetes (AKS) za pomocą usługi Azure Monitor dla kontenerów, może wystąpić problem uniemożliwiający zbieranie danych lub stan raportowania. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Błąd autoryzacji podczas dołączania lub aktualizacji
Podczas włączania usługi Azure Monitor dla kontenerów lub aktualizowania klastra w celu obsługi metryk zbierania, może pojawić się błąd przypominający następujące — *klient <> tożsamości użytkownika" z identyfikatorem obiektu "<identyfikator obiektu użytkownika>" nie ma autoryzacji do wykonywania akcji "Microsoft.Authorization/roleAssignments/write" over scope*

Podczas procesu dołączania lub aktualizacji, przyznawanie przypisania roli **programu Monitorowania Metryki wydawcy** jest podejmowana w zasobie klastra. Użytkownik inicjujący proces, aby włączyć usługę Azure Monitor dla kontenerów lub aktualizacji do obsługi kolekcji metryk musi mieć dostęp do **uprawnień Microsoft.Authorization/roleAssignments/write** w zakresie zasobów klastra AKS. Tylko członkowie wbudowanych ról **administratora dostępu właściciela** i **użytkownika** mają dostęp do tego uprawnienia. Jeśli zasady zabezpieczeń wymagają przypisywania uprawnień na poziomie szczegółowości, zalecamy wyświetlanie [ról niestandardowych](../../role-based-access-control/custom-roles.md) i przypisywanie ich użytkownikom, którzy tego wymagają. 

Możesz również ręcznie udzielić tej roli z witryny Azure portal, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Kubernetes**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **pozycję Kubernetes platformy Azure**.
3. Na liście klastrów kubernetes wybierz jeden z listy.
2. W menu po lewej stronie kliknij pozycję **Kontrola dostępu (IAM)**.
3. Wybierz **+ Dodaj,** aby dodać przypisanie roli i wybierz rolę **Monitorowanie metryk wydawcy** i w polu **Wybierz** typ **AKS,** aby filtrować wyniki tylko na jednostkach usługi klastrów zdefiniowanych w subskrypcji. Wybierz ten z listy, który jest specyficzny dla tego klastra.
4. Wybierz **pozycję Zapisz,** aby zakończyć przypisywanie roli. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Usługa Azure Monitor dla kontenerów jest włączona, ale nie zgłasza żadnych informacji
Jeśli usługa Azure Monitor dla kontenerów jest pomyślnie włączona i skonfigurowana, ale nie można wyświetlić informacji o stanie lub nie są zwracane żadne wyniki z kwerendy dziennika, diagnozuj problem, wykonując następujące kroki: 

1. Sprawdź stan agenta, uruchamiając polecenie: 

    `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Sprawdź stan wdrożenia w wersji agenta *06072018* lub nowszej za pomocą polecenia:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Dane wyjściowe powinny przypominać następujący przykład, który wskazuje, że został wdrożony poprawnie:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Sprawdź stan zasobnika, aby sprawdzić, czy jest uruchomiony za pomocą polecenia:`kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny przypominać następujący przykład ze stanem *Uruchamianie* dla omsagenta:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Sprawdź dzienniki agenta. Po wdrożeniu konteneryzowanego agenta uruchamia szybkie sprawdzanie, uruchamiając polecenia OMI i wyświetla wersję agenta i dostawcy. 

5. Aby sprawdzić, czy agent został pomyślnie wdrożony, uruchom polecenie:`kubectl logs omsagent-484hw --namespace=kube-system`

    Stan powinien przypominać następujący przykład:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Komunikaty o błędach

W poniższej tabeli podsumowano znane błędy, które mogą wystąpić podczas korzystania z usługi Azure Monitor dla kontenerów.

| Komunikaty o błędach  | Akcja |  
| ---- | --- |  
| Komunikat o błędzie`No data for selected filters`  | Uruchomienie przepływu danych monitorowania z nowo utworzonych klastrów może zająć trochę czasu. Odczekaj co najmniej 10 do 15 minut, aby dane były wyświetlane dla klastra. |   
| Komunikat o błędzie`Error retrieving data` | Podczas gdy klaster usługi Azure Kubernetes konfiguruje monitorowanie kondycji i wydajności, nawiązywano połączenie między klastrem a obszarem roboczym usługi Azure Log Analytics. Obszar roboczy usługi Log Analytics służy do przechowywania wszystkich danych monitorowania dla klastra. Ten błąd może wystąpić, gdy obszar roboczy usługi Log Analytics został usunięty. Sprawdź, czy obszar roboczy został usunięty, a jeśli tak było, należy ponownie włączyć monitorowanie klastra za pomocą usługi Azure Monitor dla kontenerów i określić istniejący lub utworzyć nowy obszar roboczy. Aby ponownie włączyć, należy [wyłączyć](container-insights-optout.md) monitorowanie klastra i [ponownie włączyć](container-insights-enable-new-cluster.md) usługę Azure Monitor dla kontenerów. |  
| `Error retrieving data`po dodaniu usługi Azure Monitor dla kontenerów przez az aks cli | Po włączeniu `az aks cli`monitorowania przy użyciu usługi Azure Monitor dla kontenerów może nie być poprawnie wdrożony. Sprawdź, czy rozwiązanie jest wdrożone. Aby to zrobić, przejdź do obszaru roboczego usługi Log Analytics i sprawdź, czy rozwiązanie jest dostępne, wybierając **rozwiązania** z okienka po lewej stronie. Aby rozwiązać ten problem, należy ponownie wdrożyć rozwiązanie, postępując zgodnie z instrukcjami [dotyczącymi wdrażania usługi Azure Monitor dla kontenerów](container-insights-onboard.md) |  

Aby pomóc w zdiagnozowaniu problemu, udostępniliśmy skrypt rozwiązywania problemów dostępny [tutaj.](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Usługa Azure Monitor dla kontenerów agenta ReplicaSet Pods nie są zaplanowane w klastrze kubernetes innych niż Azure

Usługa Azure Monitor dla kontenerów agenta ReplicaSet Pods ma zależność od następujących selektorów węzłów w węzłach procesu roboczego (lub agenta) dla planowania:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Jeśli węzły procesu roboczego nie mają dołączonych etykiet węzłów, agent ReplicaSet Pods nie zostanie zaplanowany. Zapoznaj się z [programem Kubernetes przypisuj selektory etykiet,](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) aby uzyskać instrukcje dotyczące dołączania etykiety.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Wykresy wydajności nie pokazują procesora CPU ani pamięci węzłów i kontenerów w klastrze nienawiązanym z platformą Azure

Usługa Azure Monitor dla kontenerów agent zasobników używa punktu końcowego cAdvisor w agencie węzła do zbierania metryk wydajności. Sprawdź, czy konteneryzowany agent w `cAdvisor port: 10255` węźle jest skonfigurowany tak, aby umożliwić otwarcie na wszystkich węzłach w klastrze w celu zbierania metryk wydajności.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Klaster kubernetes innych niż azure nie jest wyświetlany w usłudze Azure Monitor dla kontenerów

Aby wyświetlić klaster kubernetes nieobjętej usługą Azure w usłudze Azure Monitor dla kontenerów, dostęp do odczytu jest wymagany w obszarze roboczym usługi Log Analytics obsługującym tę usługę Insight oraz w zasobie container insights **ContainerInsights *(obszar roboczy).***

## <a name="next-steps"></a>Następne kroki

Dzięki włączonej kontroli monitorowania do przechwytywania metryk kondycji zarówno dla węzłów klastra AKS, jak i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [Wyświetlanie kondycji usługi Azure Kubernetes.](container-insights-analyze.md)
