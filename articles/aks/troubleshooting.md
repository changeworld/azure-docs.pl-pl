---
title: Rozwiązywanie typowych problemów z usługą Azure Kubernetes
description: Dowiedz się, jak rozwiązywać typowe problemy i rozwiązywać je podczas korzystania z usługi Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368336"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Kubernetes Service

Podczas tworzenia klastrów usługi Azure Kubernetes (AKS) lub zarządzania nimi, czasami mogą wystąpić problemy. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje na temat problemów z debugowaniem kubernetes?

Wypróbuj [oficjalny przewodnik po rozwiązywaniu problemów z klastrami kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Istnieje również [przewodnik rozwiązywania problemów](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), opublikowany przez inżyniera firmy Microsoft do rozwiązywania problemów zasobników, węzłów, klastrów i innych funkcji.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Podczas tworzenia lub uaktualniania pojawia się błąd "przekroczono przydział". Co mam zrobić? 

Musisz [poprosić o rdzenie](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Jakie jest maksymalne ustawienie zasobników na węzeł dla aks?

Maksymalne ustawienie zasobników na węzeł jest domyślnie 30, jeśli wdrożysz klaster AKS w witrynie Azure portal.
Maksymalne ustawienie zasobników na węzeł jest domyślnie 110, jeśli wdrożysz klaster AKS w wierszu polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie domyślne można `–-max-pods` zmienić `az aks create` za pomocą flagi w poleceniu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Podczas wdrażania klastra AKS z zaawansowaną siecią pojawia się błąd niewystarczającej wielkości sieci. Co mam zrobić?

Jeśli używana jest usługa Azure CNI (sieć zaawansowana), usługa AKS przydziela adresy IP na podstawie skonfigurowanych "maksymalnych zasobników". Na podstawie skonfigurowanych zasobników maksymalnej na węzeł rozmiar podsieci musi być większy niż iloczyn liczby węzłów i ustawienia maksymalnej liczby zasobników na węzeł. Poniższe równanie przedstawia tę grę:

Rozmiar podsieci > liczbę węzłów w klastrze (biorąc pod uwagę przyszłe wymagania dotyczące skalowania) * maksymalna liczba zasobników na zestaw węzłów.

Aby uzyskać więcej informacji, zobacz [Planowanie adresowania ADRESÓW IP dla klastra](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mój pod utknął w trybie CrashLoopBackOff. Co mam zrobić?

Mogą istnieć różne powody, dla zasobnika tkwi w tym trybie. Możesz przyjrzeć się:

* Sam strąk, `kubectl describe pod <pod-name>`za pomocą .
* Dzienniki, za `kubectl logs <pod-name>`pomocą .

Aby uzyskać więcej informacji na temat rozwiązywania problemów z zasobami zasobnymi, zobacz [Debugowanie aplikacji](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Próbuję włączyć RBAC w istniejącym klastrze. Jak to zrobić?

Niestety włączenie kontroli dostępu opartej na rolach (RBAC) w istniejących klastrach nie jest obecnie obsługiwane. Należy jawnie utworzyć nowe klastry. Jeśli używasz interfejsu wiersza polecenia, RBAC jest domyślnie włączona. Jeśli używasz portalu AKS, przycisk przełączania, aby włączyć RBAC jest dostępny w przepływie pracy tworzenia.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Utworzyłem klaster z włączoną funkcją RBAC przy użyciu interfejsu wiersza polecenia platformy Azure z wartościami domyślnymi lub witryny Azure portal, a teraz widzę wiele ostrzeżeń na pulpicie nawigacyjnym aplikacji Kubernetes. Pulpit nawigacyjny używany do pracy bez żadnych ostrzeżeń. Co mam zrobić?

Powodem ostrzeżeń na pulpicie nawigacyjnym jest to, że klaster jest teraz włączony z funkcją RBAC i dostęp do niego został domyślnie wyłączony. Ogólnie rzecz biorąc takie podejście jest dobrą praktyką, ponieważ domyślna ekspozycja pulpitu nawigacyjnego dla wszystkich użytkowników klastra może prowadzić do zagrożeń bezpieczeństwa. Jeśli nadal chcesz włączyć pulpit nawigacyjny, wykonaj czynności opisane w [tym wpisie](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)w blogu .

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nie mogę nawiązać połączenia z pulpitem nawigacyjnym. Co mam zrobić?

Najprostszym sposobem uzyskania dostępu do usługi poza `kubectl proxy`klastrem jest uruchomienie żądania proxy wysyłane do portu localhost 8001 do serwera interfejsu API kubernetes. Stamtąd serwer API może proxy do `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`usługi: .

Jeśli nie widzisz pulpitu nawigacyjnego kubernetes, sprawdź, `kube-proxy` czy zasobnik jest uruchomiony w obszarze `kube-system` nazw. Jeśli nie jest w stanie uruchomionym, usuń zasobnik i uruchomi się ponownie.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nie mogę uzyskać dzienników przy użyciu dzienników kubectl lub nie mogę połączyć się z serwerem interfejsu API. Pojawia się komunikat "Błąd z serwera: błąd wybierania backendu: wybieranie numeru tcp...". Co mam zrobić?

Upewnij się, że domyślna siećowa grupa zabezpieczeń nie jest modyfikowana i że oba porty 22 i 9000 są otwarte dla połączenia z serwerem interfejsu API. Sprawdź, `tunnelfront` czy zasobnik jest uruchomiony w obszarze `kubectl get pods --namespace kube-system` nazw systemu *kube* za pomocą polecenia. Jeśli tak nie jest, wymusić usunięcie zasobnika i zostanie ponownie uruchomiony.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Próbuję uaktualnić lub skalować i pojawia się komunikat: Zmiana właściwości 'imageReference' nie jest dozwolona" błąd. Jak rozwiązać ten problem?

Może być otrzymasz ten błąd, ponieważ zostały zmodyfikowane tagi w węzłach agenta wewnątrz klastra AKS. Modyfikowanie i usuwanie tagów i innych właściwości zasobów w grupie zasobów MC_* może prowadzić do nieoczekiwanych wyników. Modyfikowanie zasobów w grupie MC_* w klastrze AKS przerywa cel poziomu usług (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Otrzymuję błędy, że mój klaster jest w stanie awarii i uaktualnianie lub skalowanie nie będzie działać, dopóki nie zostanie naprawiony

*Ta pomoc w rozwiązywaniu problemów jest kierowanahttps://aka.ms/aks-cluster-failed*

Ten błąd występuje, gdy klastry wprowadzić stan awarii z wielu powodów. Wykonaj poniższe czynności, aby rozwiązać stan awarii klastra przed ponowieniem próby wykonania wcześniej nieudanej operacji:

1. Dopóki klaster nie `failed` jest `upgrade` `scale` stan, a operacje nie powiedzie się. Typowe główne problemy i rozwiązania obejmują:
    * Skalowanie przy **niewystarczającej mocy obliczeniowej (CRP).** Aby rozwiązać problem, najpierw przeskaluj klaster z powrotem do stabilnego stanu celu w ramach przydziału. Następnie wykonaj następujące [kroki, aby zażądać zwiększenia przydziału obliczeń](../azure-portal/supportability/resource-manager-core-quotas-request.md) przed próbą ponownego skalowania w górę poza początkowe limity przydziału.
    * Skalowanie klastra z zaawansowanymi zasobami sieciowymi i **niewystarczającymi zasobami podsieci .** Aby rozwiązać problem, najpierw przeskaluj klaster z powrotem do stabilnego stanu celu w ramach przydziału. Następnie wykonaj [następujące kroki, aby zażądać zwiększenia przydziału zasobów](../azure-resource-manager/templates/error-resource-quota.md#solution) przed próbą ponownego skalowania w górę poza początkowe limity przydziału.
2. Po rozwiązaniu podstawowej przyczyny niepowodzenia uaktualnienia klaster powinien być w stanie pomyślnym. Po zweryfikowaniu stanu, który się powiodło, ponów próbę wykonania oryginalnej operacji.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Otrzymuję błędy podczas próby uaktualnienia lub skalowania tego stanu, w którym mój klaster jest obecnie uaktualniany lub nie powiodło się uaktualnienie

*Ta pomoc w rozwiązywaniu problemów jest kierowanahttps://aka.ms/aks-pending-upgrade*

Operacje uaktualniania i skalowania w klastrze z pulą pojedynczego węzła lub klastra z [wieloma pulami węzłów](use-multiple-node-pools.md) wzajemnie się wykluczają. Nie można jednocześnie uaktualnić i skalować puli klastra lub węzła. Zamiast tego każdy typ operacji musi zakończyć się na zasób docelowy przed następnym żądaniem w tym samym zasobie. W rezultacie operacje są ograniczone, gdy aktywne operacje uaktualniania lub skalowania występują lub próbują, a następnie nie powiodły się. 

Aby ułatwić diagnozowanie `az aks show -g myResourceGroup -n myAKSCluster -o table` uruchomienia problemu w celu pobrania szczegółowego stanu w klastrze. Na podstawie wyniku:

* Jeśli klaster jest aktywnie uaktualniania, poczekaj, aż operacja zostanie zakończona. Jeśli to się udało, ponów próbę wykonania wcześniej nieudanej operacji ponownie.
* Jeśli uaktualnienie klastra nie powiodło się, wykonaj kroki opisane w poprzedniej sekcji.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Czy mogę przenieść klaster do innej subskrypcji lub subskrypcji z klastrem do nowej dzierżawy?

Jeśli klaster usługi AKS został przeniesiony do innej subskrypcji lub klastra posiadającego subskrypcję nowej dzierżawy, klaster utraci funkcjonalność z powodu utraty przypisań ról i praw podmiotów usługi. **Usługa AKS nie obsługuje przenoszenia klastrów w ramach subskrypcji lub dzierżaw** z powodu tego ograniczenia.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Otrzymuję błędy podczas próby użycia funkcji wymagających zestawów skalowania maszyny wirtualnej

*Ta pomoc w rozwiązywaniu problemów jest kierowana z aka.ms/aks-vmss-enablement*

Mogą pojawić się błędy wskazujące, że klaster AKS nie znajduje się w zestawie skalowania maszyny wirtualnej, na przykład w następującym przykładzie:

**"Agentpool" agentpool ustawił automatyczne skalowanie jako włączone, ale nie znajduje się w zestawach skalowania maszyny wirtualnej**

Aby używać funkcji, takich jak skalowanie automatyczne klastra lub wiele pul węzłów, należy utworzyć klastry AKS, które używają zestawów skalowania maszyny wirtualnej. Błędy są zwracane, jeśli spróbujesz użyć funkcji, które zależą od zestawów skalowania maszyny wirtualnej i docelowej regularne, nie-wirtualnej skali skalowania ustawić klaster AKS.

Postępuj zgodnie *z kroki przed rozpoczęciem* w odpowiednim doc poprawnie utworzyć klaster AKS:

* [Korzystanie z skalowania automatycznego klastra](cluster-autoscaler.md)
* [Tworzenie i używanie wielu pul węzłów](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jakie ograniczenia nazewnictwa są wymuszane dla zasobów i parametrów AKS?

*Ta pomoc w rozwiązywaniu problemów jest kierowana z aka.ms/aks-naming-rules*

Ograniczenia nazewnictwa są implementowane zarówno przez platformę Azure, jak i usługę AKS. Jeśli nazwa zasobu lub parametr łamie jedno z tych ograniczeń, zwracany jest błąd z prośbą o podanie innego danych wejściowych. Obowiązują następujące wspólne wskazówki dotyczące nazewnictwa:

* Nazwy klastra muszą zawierać od 1 do 63 znaków. Jedynymi dozwolonymi znakami są litery, cyfry, kreski i podkreślenia. Pierwszy i ostatni znak musi być literą lub cyfrą.
* Nazwa grupy zasobów *AKS MC_* łączy nazwę grupy zasobów i nazwę zasobu. Automatycznie generowana składnia `MC_resourceGroupName_resourceName_AzureRegion` nie może być większa niż 80 znaków. W razie potrzeby zmniejsz długość nazwy grupy zasobów lub nazwy klastra AKS.
* *DnsPrefix* musi zaczynać się i kończyć wartościami alfanumerycznymi i zawierać od 1 do 54 znaków. Prawidłowe znaki obejmują wartości alfanumeryczne i łączniki (-). *DnsPrefix* nie może zawierać znaków specjalnych, takich jak kropka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Otrzymuję błędy podczas próby utworzenia, aktualizacji, skalowania, usuwania lub uaktualniania klastra, ta operacja nie jest dozwolona, ponieważ trwa inna operacja.

*Ta pomoc w rozwiązywaniu problemów jest kierowana z aka.ms/aks-pending-operation*

Operacje klastra są ograniczone, gdy poprzednia operacja jest nadal w toku. Aby pobrać szczegółowy stan klastra, `az aks show -g myResourceGroup -n myAKSCluster -o table` użyj polecenia. W razie potrzeby użyj własnej grupy zasobów i nazwy klastra AKS.

Na podstawie danych wyjściowych stanu klastra:

* Jeśli klaster jest w dowolnym stanie inicjowania obsługi administracyjnej innym niż *Powodzenie* lub *Niepowodzenie*, poczekaj, aż operacja *(Uaktualnianie / Aktualizowanie / Tworzenie / Skalowanie / Usuwanie / Migrowanie)* kończy. Po zakończeniu poprzedniej operacji spróbuj ponownie wykonać najnowszą operację klastra.

* Jeśli klaster ma nieudane uaktualnienie, wykonaj kroki [opisane, że otrzymuję błędy, że mój klaster jest w stanie awarii, a uaktualnianie lub skalowanie nie będzie działać, dopóki nie zostanie naprawione.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Otrzymuję błędy, których nie znaleziono podczas próby utworzenia nowego klastra bez przekazywania w istniejącym.

Podczas tworzenia klastra usługi AKS wymaga jednostki usługi do tworzenia zasobów w Twoim imieniu. Usługa AKS oferuje możliwość utworzenia nowego w czasie tworzenia klastra, ale wymaga to, aby usługa Azure Active Directory w pełni propagować nowego podmiotu usługi w rozsądnym czasie, aby klaster odniósł sukces w tworzeniu. Gdy ta propagacja trwa zbyt długo, klaster zakończy sprawdzanie poprawności zakończy się niepowodzeniem, aby utworzyć, ponieważ nie można znaleźć dostępnego podmiotu usługi, aby to zrobić. 

W tym celu należy użyć następujących rozwiązań:
1. Użyj istniejącego podmiotu zabezpieczeń usługi, który został już propagowany między regionami i istnieje do przekazywania do usługi AKS w czasie tworzenia klastra.
2. Jeśli używasz skryptów automatyzacji, dodaj opóźnienia czasowe między tworzeniem jednostki usługi a tworzeniem klastra AKS.
3. Jeśli używasz witryny Azure portal, wróć do ustawień klastra podczas tworzenia i ponów próbę strony sprawdzania poprawności po kilku minutach.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Otrzymuję błędy po ograniczeniu ruchu wychodzącego

Podczas ograniczania ruchu wychodzącego z klastra AKS, istnieją [wymagane i opcjonalne zalecane](limit-egress-traffic.md) porty wychodzące / reguły sieciowe i FQDN / reguły aplikacji dla usługi AKS. Jeśli ustawienia są w konflikcie z dowolną z tych reguł, może nie być w stanie uruchomić niektórych `kubectl` poleceń. Podczas tworzenia klastra AKS mogą być również widoczne błędy.

Sprawdź, czy ustawienia nie są w konflikcie z żadnym z wymaganych lub opcjonalnych zalecanych portów wychodzących / reguł sieciowych i reguł FQDN / aplikacji.

## <a name="azure-storage-and-aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Storage i AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jakie są zalecane stabilne wersje kubernetes dla dysku platformy Azure? 

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.9 lub nowsze |
| 1.13 | 1.13.6 lub nowsze |
| 1.14 | 1.14.2 lub nowsze |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Jakie wersje usługi Kubernetes mają obsługę usługi Azure Disk w chmurze suwerennej?

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.0 lub nowsze |
| 1.13 | 1.13.0 lub nowsze |
| 1.14 | 1.14.0 lub nowsze |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach nie powiodło się dla usługi Azure Disk: analizowanie "/dev/disk/azure/scsi1/lun1": nieprawidłowa składnia

W wersji 1.10 usługi Kubernetes mountvolume.WaitForAttach może zakończyć się niepowodzeniem przy ponownym zainstalowaniu dysku Azure.

W systemie Linux może pojawić się niepoprawny błąd formatu DevicePath. Przykład:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

W systemie Windows może zostać wyświetlony nieprawidłowy błąd numeru DevicePath(LUN). Przykład:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.10 | 1.10.2 lub nowsze |
| 1.11 | 1.11.0 lub nowsze |
| 1.12 i nowsze | Nie dotyczy |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Błąd podczas ustawiania uid i gid w mountOptions for Azure Disk

Usługa Azure Disk domyślnie używa systemu plików ext4,xfs i nie można ustawić w czasie instalacji opcje mountOptions, takie jak uid=x,gid=x. Na przykład, jeśli próbowano ustawić mountOptions uid= 999, gid = 999, zobaczy błąd, taki jak:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Problem można rozwiązać, wykonując następujące czynności:

* [Skonfiguruj kontekst zabezpieczeń zasobnika,](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) ustawiając uid w runAsUser i gid w fsGroup. Na przykład następujące ustawienie ustawi zasobnik uruchomiony jako katalog główny, aby był dostępny dla dowolnego pliku:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Ponieważ gid i uid są domyślnie montowane jako root lub 0. Jeśli gid lub uid są ustawione jako nie-root, na przykład 1000, Kubernetes użyje `chown` do zmiany wszystkich katalogów i plików na tym dysku. Ta operacja może być czasochłonna i może sprawić, że montaż dysku będzie bardzo powolny.

* Użyj `chown` initContainers, aby ustawić gid i uid. Przykład:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Błąd podczas usuwania funkcji Azure Disk PersistentVolumeClaim używany przez zasobnik

Jeśli spróbujesz usunąć Azure Disk PersistentVolumeClaim, który jest używany przez zasobnika, może pojawić się błąd. Przykład:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

W wersji 1.10 firmy Kubernetes lub nowszej jest domyślnie włączona funkcja ochrony PersistentVolumeClaim, aby zapobiec temu błędowi. Jeśli używasz wersji kubernetes, która nie ma rozwiązania tego problemu, można złagodzić ten problem, usuwając zasobnika przy użyciu PersistentVolumeClaim przed usunięciem PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Błąd "Nie można odnaleźć lun dla dysku" podczas podłączania dysku do węzła

Podczas dołączania dysku do węzła może wystąpić następujący błąd:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.10 | 1.10.10 lub nowsze |
| 1.11 | 1.11.5 lub nowsze |
| 1.12 | 1.12.3 lub nowsze |
| 1.13 | 1.13.0 lub nowsze |
| 1.14 i nowsze | Nie dotyczy |

Jeśli używasz wersji kubernetes, która nie ma poprawki dla tego problemu, można złagodzić problem, czekając kilka minut i ponów próbę.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Błąd dołączania/odłączania dysku Azure, problemy z instalacją lub błędy we/wy podczas wielu operacji dołączania/odłączania

Począwszy od programu Kubernetes w wersji 1.9.2, podczas uruchamiania wielu operacji dołączania/odłączania równolegle, mogą wystąpić następujące problemy z dyskiem z powodu brudnej pamięci podręcznej maszyny Wirtualnej:

* Awarie dołączania/odłączania dysku
* Błędy we/wy dysku
* Nieoczekiwane odłączenie dysku od maszyny Wirtualnej
* Maszyna wirtualna działa w stanie awarii z powodu podłączania nieistniejącego dysku

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.10 | 1.10.12 lub nowsze |
| 1.11 | 1.11.6 lub nowsze |
| 1.12 | 1.12.4 lub nowsze |
| 1.13 | 1.13.0 lub nowsze |
| 1.14 i nowsze | Nie dotyczy |

Jeśli używasz wersji kubernetes, która nie ma rozwiązania tego problemu, można złagodzić problem, próbując poniżej:

* Jeśli dysk czeka na odłączenie przez dłuższy czas, spróbuj ręcznie odłączyć dysk

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Dysk Azure oczekujący na odłączenie na czas nieokreślony

W niektórych przypadkach jeśli operacja odłączania dysku Azure nie powiedzie się przy pierwszej próbie, nie ponowi próby operacji odłączania i pozostanie dołączony do oryginalnego węzła maszyny Wirtualnej. Ten błąd może wystąpić podczas przenoszenia dysku z jednego węzła do drugiego. Przykład:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.11 | 1.11.9 lub nowsze |
| 1.12 | 1.12.7 lub nowsze |
| 1.13 | 1.13.4 lub nowsze |
| 1.14 i nowsze | Nie dotyczy |

Jeśli używasz wersji kubernetes, która nie ma rozwiązania tego problemu, można złagodzić problem, ręcznie odłączając dysk.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Awaria odłączania dysku Azure, co prowadzi do potencjalnego problemu z chorobą wyścigu i nieprawidłowej listy dysków danych

Gdy dysk Azure nie może odłączyć, ponowi próbę do sześciu razy, aby odłączyć dysk przy użyciu wykładniczego wycofywania. Będzie również zawierać blokadę na poziomie węzła na liście dysków danych przez około 3 minuty. Jeśli lista dysków jest aktualizowana ręcznie w tym okresie czasu, takich jak ręczne dołączanie lub odłączać operacji, spowoduje to, że lista dysków przechowywanych przez blokadę na poziomie węzła jest przestarzała i spowoduje niestabilność maszyny Wirtualnej węzła.

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.12 | 1.12.9 lub nowsze |
| 1.13 | 1.13.6 lub nowsze |
| 1.14 | 1.14.2 lub nowsze |
| 1.15 i nowsze | Nie dotyczy |

Jeśli używasz wersji kubernetes, która nie ma rozwiązania tego problemu, a maszyna wirtualna węzła ma przestarzałą listę dysków, można rozwiązać ten problem, odłączając wszystkie nieistniejące dyski od maszyny Wirtualnej jako pojedynczą, zbiorczą operację. **Pojedyncze odłączanie nieistniejących dysków może zakończyć się niepowodzeniem.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Duża liczba dysków platformy Azure powoduje powolne dołączanie/odłączanie

Gdy liczba dysków platformy Azure dołączonych do węzła maszyny Wirtualnej jest większa niż 10, dołączanie i odłączać operacje mogą być powolne. Ten problem jest znany problem i nie ma żadnych obejść w tej chwili.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Błąd odłączania dysku Azure prowadzący do potencjalnej maszyny Wirtualnej węzła w stanie awarii

W niektórych przypadkach krawędzi odłączyć dysku Azure może częściowo zakończyć się niepowodzeniem i pozostawić węzeł maszyny Wirtualnej w stanie awarii.

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.12 | 1.12.10 lub nowsze |
| 1.13 | 1.13.8 lub nowsze |
| 1.14 | 1.14.4 lub nowsze |
| 1.15 i nowsze | Nie dotyczy |

Jeśli używasz wersji kubernetes, która nie ma rozwiązania tego problemu, a maszyna wirtualna węzła jest w stanie failed, można rozwiązać problem ręcznie aktualizując stan maszyny Wirtualnej przy użyciu jednego z poniższych:

* W przypadku klastra opartego na zestawie dostępności:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* W przypadku klastra opartego na vmss:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Rozwiązywanie problemów z plikami platformy Azure i usługami AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jakie są zalecane stabilne wersje kubernetes dla plików platformy Azure?
 
| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.6 lub nowsze |
| 1.13 | 1.13.4 lub nowsze |
| 1.14 | 1.14.0 lub nowsze |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Jakie wersje usługi Kubernetes mają obsługę plików Azure w chmurze suwerennej?

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.0 lub nowsze |
| 1.13 | 1.13.0 lub nowsze |
| 1.14 | 1.14.0 lub nowsze |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jakie są domyślne opcje mountOptions podczas korzystania z usługi Azure Files?

Zalecane ustawienia:

| Wersja Kubernetes | wartość datamodu i dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 i nowsze | 0777 |

Jeśli używasz klastra z programem Kubernetes w wersji 1.8.5 lub nowszej i dynamicznie tworzy wolumin trwały z klasą magazynu, można określić opcje instalacji w obiekcie klasy magazynu. Poniższy przykład ustawia *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Kilka dodatkowych przydatnych ustawień *mountOptions:*

* *mfsymlinks* sprawi, że usługa Azure Files mount (cifs) będzie obsługiwać łącza symboliczne
* *nobrl* uniemożliwi wysyłanie żądań blokady zakresu bajtów do serwera. To ustawienie jest konieczne dla niektórych aplikacji, które łamią się z cifs style obowiązkowe blokady zakresu bajtów. Większość serwerów cifs nie obsługuje jeszcze żądających blokad zakresu bajtów doradczych. Jeśli nie *używasz nobrl*, aplikacje, które łamią się z cifs style obowiązkowe blokady zakresu bajtów może powodować komunikaty o błędach podobne do:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Błąd "nie można zmienić uprawnień" podczas korzystania z usługi Azure Files

Podczas uruchamiania PostgreSQL na platformie Azure Files wtyczki, może pojawić się błąd podobny do:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Ten błąd jest spowodowany przez wtyczkę Usługi Azure Files przy użyciu protokołu cifs/SMB. Podczas korzystania z protokołu cifs/SMB nie można zmienić uprawnień do plików i katalogów po zamontowaniu.

Aby rozwiązać ten problem, należy użyć *podpath* wraz z wtyczki usługi Azure Disk. 

> [!NOTE] 
> W przypadku typu dysku ext3/4 po sformatowanym dysku znajduje się katalog utracony+znaleziony.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Usługa Azure Files ma duże opóźnienia w porównaniu z usługą Azure Disk podczas obsługi wielu małych plików

W niektórych przypadkach, takich jak obsługa wielu małych plików, może wystąpić duże opóźnienia podczas korzystania z usługi Azure Files w porównaniu do usługi Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Błąd podczas włączania ustawienia "Zezwalaj na dostęp z wybranej sieci" na koncie magazynu

Jeśli *włączysz zezwalanie na dostęp z wybranej sieci* na koncie magazynu, który jest używany do dynamicznego inicjowania obsługi administracyjnej w usłudze AKS, zostanie wyświetlony błąd, gdy usługa AKS utworzy udział plików:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ten błąd jest spowodowane błędem *kontrolera errorume firmy* Kubernetes, który nie znajduje się w sieci wybranej podczas ustawiania zezwalania na *dostęp z wybranej sieci*.

Problem można rozwiązać, korzystając [ze statycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Files.](azure-files-volume.md)

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Ponowne ponowne zamontowanie usługi Azure Files w zasobniku systemu Windows

Jeśli zasobnik systemu Windows z instalacją plików platformy Azure zostanie usunięty, a następnie zaplanowane do odtworzenia w tym samym węźle, instalacja zakończy się niepowodzeniem. Ten błąd jest `New-SmbGlobalMapping` z powodu niepowodzenia polecenia, ponieważ instalacja usługi Azure Files jest już zainstalowana w węźle.

Na przykład może pojawić się błąd podobny do:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ten problem został rozwiązany w następujących wersjach kubernetes:

| Wersja Kubernetes | Wersja stała |
| -- | :--: |
| 1.12 | 1.12.6 lub nowsze |
| 1.13 | 1.13.4 lub nowsze |
| 1.14 i nowsze | Nie dotyczy |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Instalacja usługi Azure Files kończy się niepowodzeniem z powodu zmiany klucza konta magazynu

Jeśli klucz konta magazynu uległ zmianie, mogą zostać wyświetlone błędy instalacji usługi Azure Files.

Można rozwiązać ten problem, wykonując ręcznie aktualizowanie *pola azurestorageaccountkey* ręcznie w kluczu tajnym pliku platformy Azure za pomocą klucza konta magazynu zakodowanego base64.

Aby zakodować klucz konta magazynu w base64, można użyć `base64`programu . Przykład:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Aby zaktualizować tajny plik `kubectl edit secret`platformy Azure, użyj programu . Przykład:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po kilku minutach węzeł agenta ponowi próbę instalacji pliku platformy Azure ze zaktualizowanym kluczem magazynu.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Skalowanie automatyczne klastra nie może być skalowane z błędem nie można naprawić rozmiarów grup węzłów

Jeśli skalowanie automatyczne klastra nie jest skalowanie w górę / w dół i widzisz błąd, jak poniżej w [dziennikach skalowania automatycznego klastra][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Ten błąd jest spowodowany upstream autoscaler wyścigu, gdzie automatycznego skalowania klastra kończy się inną wartość niż ten, który jest faktycznie w klastrze. Aby wydostać się z tego stanu, wystarczy wyłączyć i ponownie włączyć [skalowanie automatyczne klastra][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Powolny załącznik dysku, GetAzureDiskLun trwa od 10 do 15 minut i pojawia się błąd

W wersjach Kubernetes **starszych niż 1.15.0** może pojawić się błąd, taki jak **Błąd WaitForAttach Nie można znaleźć Lun dla dysku**.  Obejście tego problemu polega na odczekanie około 15 minut i ponowienie próby.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
