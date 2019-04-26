---
title: Dodatek routingu HTTP aplikacji w usłudze Azure Kubernetes Service (AKS)
description: Użyj dodatku routing aplikacji protokołu HTTP w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: d6e1cc033416c90e27b5caf4bba310400e55b3a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466310"
---
# <a name="http-application-routing"></a>Routing aplikacji protokołu HTTP

Rozwiązanie routing aplikacji protokołu HTTP można łatwo uzyskiwać dostęp do aplikacji, które są wdrażane w klastrze usługi Azure Kubernetes Service (AKS). Po włączeniu rozwiązania konfiguruje kontrolera danych przychodzących w klastrze AKS. Podczas wdrażania aplikacji, rozwiązania wzrasta, powstaje publicznie dostępne nazwy DNS dla punktów końcowych aplikacji.

Gdy dodatek jest włączona, tworzy strefę DNS w ramach subskrypcji. Aby uzyskać więcej informacji na temat kosztu DNS Zobacz [ceny DNS][dns-pricing].

> [!CAUTION]
> Dodatek routing aplikacji protokołu HTTP jest przeznaczona do umożliwiają szybkie tworzenie kontrolera danych przychodzących i dostęp do aplikacji. Ten dodatek nie jest zalecane do użytku produkcyjnego. Dla obsługi wdrożenia gotowe do produkcji transferu danych przychodzących, które obejmują wiele replik i TLS, zobacz [utworzyć kontroler danych przychodzących HTTPS](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>Omówienie rozwiązania routingu protokołu HTTP

Dodatek wdraża dwa składniki: [kontrolera danych przychodzących rozwiązania Kubernetes] [ ingress] i [DNS zewnętrznego] [ external-dns] kontrolera.

- **Kontroler danych przychodzących**: Kontroler danych przychodzących jest uwidaczniany w Internecie przy użyciu usługi Kubernetes typu usługi równoważenia obciążenia. Kontroler danych przychodzących oczekuje i implementuje [zasobów transferu danych przychodzących rozwiązania Kubernetes][ingress-resource], co powoduje utworzenie tras do punktów końcowych aplikacji.
- **Zewnętrzne DNS kontrolera**: Oczekuje na zasoby platformy Kubernetes transferu danych przychodzących i tworzy rekordy A systemu DNS w strefie DNS specyficzne dla klastra.

## <a name="deploy-http-routing-cli"></a>Wdrażanie, routing protokołu HTTP: Interfejs wiersza polecenia

W przypadku wdrażania klastra usługi AKS, można włączyć dodatek routing aplikacji protokołu HTTP przy użyciu wiersza polecenia platformy Azure. Aby to zrobić, należy użyć [tworzenie az aks] [ az-aks-create] polecenia `--enable-addons` argumentu.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Jeśli chcesz włączyć wielu dodatków, należy podać je jako listę rozdzielaną przecinkami. Na przykład, aby włączyć routing aplikacji protokołu HTTP i monitorowania, użyj formatu `--enable-addons http_application_routing,monitoring`.

Można również włączyć routing protokołu HTTP na istniejące usługi AKS klastra przy użyciu [az aks enable dodatków] [ az-aks-enable-addons] polecenia. Aby włączyć routing protokołu HTTP w istniejącym klastrze, należy dodać `--addons` parametru i określ *http_application_routing* jak pokazano w poniższym przykładzie:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Po wdrożeniu klastra lub zaktualizowaniu [az aks show] [ az-aks-show] polecenie, aby pobrać nazwę strefy DNS. Ta nazwa jest wymagane do wdrażania aplikacji w klastrze AKS.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Wdrażanie, routing protokołu HTTP: Portal

Dodatek routing aplikacji protokołu HTTP można włączyć w witrynie Azure portal w przypadku wdrażania klastra usługi AKS.

![Włączanie funkcji routingu protokołu HTTP](media/http-routing/create.png)

Po wdrożeniu klastra, przejdź do grupy zasobów usługi AKS utworzony automatycznie, a następnie wybierz strefę DNS. Zanotuj nazwę strefy DNS. Ta nazwa jest wymagane do wdrażania aplikacji w klastrze AKS.

![Pobierz nazwę strefy DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Użyć routing protokołu HTTP

Rozwiązanie routing aplikacji protokołu HTTP mogą być wyzwalane tylko na ruch przychodzący zasoby, które są przypisane w następujący sposób:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Utwórz plik o nazwie **przykłady http aplikacji routing.yaml** i skopiuj do poniższego kodu YAML. W wierszu 43 aktualizacji `<CLUSTER_SPECIFIC_DNS_ZONE>` z nazwą strefy DNS zebrane w poprzednim kroku w tym artykule.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby utworzyć zasoby.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Za pomocą programu cURL lub w przeglądarce przejdź do nazwy hosta określonej w sekcji hosta przykłady http aplikacji routing.yaml pliku. Aplikacja może zająć jedną minutę, zanim będzie dostępna za pośrednictwem Internetu.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Usuń routing protokołu HTTP

Rozwiązanie routing protokołu HTTP można je usunąć za pomocą wiersza polecenia platformy Azure. W tym celu uruchom następujące polecenie, zastępując usługi AKS klastra i zasobu Nazwa grupy.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Po wyłączeniu dodatku routing aplikacji protokołu HTTP w klastrze mogą pozostać niektóre zasoby platformy Kubernetes. Te zasoby obejmują *configMaps* i *wpisów tajnych*i są tworzone w *systemu kubernetes* przestrzeni nazw. Aby zachować czyste klastra, możesz usunąć te zasoby.

Wyszukaj *dodatek —-routing aplikacji protokołu http* zasobów za pomocą następujących [kubectl get-] [ kubectl-get] poleceń:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Następujące przykładowe dane wyjściowe pokazuje configMaps, które powinny zostać usunięte:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Aby usunąć zasoby, należy użyć [Usuń kubectl] [ kubectl-delete] polecenia. Określ typ zasobu, nazwa zasobu i przestrzeni nazw. Poniższy przykład usuwa jeden z poprzednim configmaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Powtórz poprzedni `kubectl delete` krok dla wszystkich *dodatek —-routing aplikacji protokołu http* zasoby, które pozostały w klastrze.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Użyj [Dzienniki narzędzia kubectl] [ kubectl-logs] polecenie, aby wyświetlić dzienniki aplikacji dla aplikacji zewnętrznych DNS. Dzienniki należy potwierdzić, czy rekordu TXT DNS i A zostały pomyślnie utworzone.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Te rekordy można także znaleźć w zasobie strefy DNS w witrynie Azure portal.

![Pobierz rekordy DNS](media/http-routing/clippy.png)

Użyj [Dzienniki narzędzia kubectl] [ kubectl-logs] polecenie, aby wyświetlić dzienniki aplikacji dla kontrolera danych przychodzących Nginx. Należy się upewnić, dzienniki `CREATE` zasobu usługi transferu danych przychodzących i załaduj ponownie kontrolera. Wszystkie działania protokołu HTTP jest rejestrowane.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Czyszczenie

Usuń skojarzone obiekty usługi Kubernetes, utworzone w tym artykule.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat sposobu instalowania kontrolera transferu danych przychodzących z zabezpieczonej przez protokół HTTPS w usłudze AKS, zobacz [ruch przychodzący protokołu HTTPS w usłudze Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
