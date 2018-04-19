---
title: "Jak skalować klastra usługi kontenera platformy Azure na potrzeby usługi Machine Learning | Dokumentacja firmy Microsoft"
description: "Skalowanie klastra usługi ACS — skalowania automatycznego i skalowanie statycznych; skalowania liczby węzłów w klastrze"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 58709b5a5b060552e1bc349a6f720d3f45348ba0
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Skalowanie klastra zarządzania przepływności usługi sieci web

## <a name="why-scale-the-cluster"></a>Dlaczego skalowanie klastra?

Skalowanie klastra usługi kontenera platformy Azure (ACS) jest efektywny sposób zoptymalizowania przepływności usługi przy zachowaniu rozmiar klastra do minimum będzie zmniejszenie kosztów. 

Aby lepiej zrozumieć Skalowanie automatyczne, należy wziąć pod uwagę poniższy przykład klastra z systemem trzy usługi sieci web:

![Przykład: Trzy usługi w klastrze](media/how-to-scale-clusters/three-services.png)

Usługi mają różne wymagania szczytu: Service 1 (linię) wymaga 40 stanowiskami na szczytowego zapotrzebowania, 2 usługi (wiersz pomarańczowy) wymaga 38 w piku, a Service 3 (szara linia) wymaga 50 w piku. Jeśli pojemność szczytu wymagane dla każdej usługi rezerwowania pojedynczo, tego klastra potrzebny co najmniej 40 + 38 + 50 = 128 stanowiskami razem.

Jednak rozważyć użycie rzeczywiste pod w dowolnym momencie w czasie, reprezentowany przez czarnej linii kreskowanej na wykresie. W takim przypadku *największa liczba stanowiskami używane w dowolnym momencie* jest 64, w którym występuje, gdy 3 usługi jest w piku 20:00. W tej chwili z 3 wykorzystuje stanowiskami 50, ale 2 usługi używa stanowiskami tylko 9, a Service 1 używa tylko 5. Należy pamiętać, że jest to *szczytowe użycie* dla tego klastra. Oznacza to, że w żadnym momencie klaster używa więcej niż 64 stanowiskami — połowa obliczeniowej wymóg stanowiskami 128 dla trzy niezależne skalowanie szczytowego użycia usługi.

Przez ponowne przypisywanie stanowiskami klastra — czyli przez ponowne skalowanie — bieżącego zapotrzebowania każdej usługi, a nie po prostu wymagają wystarczające zasoby do szczytowego zapotrzebowania wszystkich usług, można zmniejszyć rozmiar klastra. W tym prostym przykładzie Skalowanie automatyczne zmniejsza wymaganej liczby stanowiskami od 128-64, wycinanie rozmiar klastra wymagane w połowie.

Skalowania liczby stanowiskami jest stosunkowo szybkie operacji wymagających mniej niż minutę, więc czas odpowiedzi usługi nie ma wpływu na poważnie.

> [!NOTE]
> Skalowanie klastra nie pomoże opóźnień żądania. Do celów operationalization skalowaniu należy zwiększyć liczbę sukcesów i zmniejszyć błędów niedostępności usługi. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Jak skalować usług sieci web w klastrze usługi ACS

Opcja instalacji klastra zarządzania modelu interfejsu wiersza polecenia domyślnie konfiguruje ci dwaj agenci i pod jedną w danym środowisku. Instaluje Kubernetes interfejsu wiersza polecenia.

Można skalować usługi sieci web, wdrożonej w ACS przez dostosowanie wartości właściwości:

* Liczba agentów węzłów w klastrze
* Liczba replik pod Kubernetes uruchomione w węzłach agenta

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Skalowania liczby węzłów w klastrze

Polecenie ustawia liczbę agenta węzłów w klastrze:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Potrwa to kilka minut. Aby uzyskać więcej informacji na temat skalowania liczby węzłów w klastrze, zobacz [skalować agenta węzłów w klastrze usługi kontenera](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Skalowania liczby replikami pod Kubernetes w klastrze
 
Możesz skalować liczbę replik pod przypisany do klastra przy użyciu interfejsu wiersza polecenia Azure Machine Learning lub [nawigacyjnym Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Aby uzyskać więcej informacji na Kubernetes stanowiskami repliki, zobacz [stanowiskami Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentacji.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Skalowanie klastra z interfejsu wiersza polecenia Azure Machine Learning

Istnieją dwa sposoby skalowanie klastra przy użyciu interfejsu wiersza polecenia:

- Automatyczne skalowanie
- Statyczne skali

Skalowania automatycznego jest domyślnie aktywny, gdy usługa jest tworzony i w większości przypadków jest to preferowana metoda skalowania.

##### <a name="autoscale"></a>Automatyczne skalowanie

Poniższe polecenie umożliwia automatyczne skalowanie i Ustawia minimalną i maksymalną liczbę replik dla usługi.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Na przykład ustawienie `autoscale-min-replicas` do 5 spowoduje utworzenie pięciu replik. Odebranie w optymalnych numer dla usługi sieci web, ustaw liczbę do wartości, takie jak 10 i monitorować liczbę 503 komunikaty o błędach. Następnie Dostosuj liczbę odpowiednio.


| Nazwa parametru | Typ | Opis |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | wartość logiczna | Określa, czy jest włączona funkcja automatycznego skalowania. Domyślnie: true |
| `autoscale-min-replicas` | integer | Określa minimalną liczbę stanowiskami. Musi być mniejsza od 0. Domyślne: 1 |
| `autoscale-max-replicas` | integer | Określa maksymalną liczbę stanowiskami. Musi być mniejsza od 1. Jeśli funkcja automatycznego skalowania max repliki jest mniejsza niż skalowania automatycznego min replik, skalowania automatycznego max repliki zostaną zignorowane. Domyślny: 10 |
| `autoscale-refresh-period-seconds` | integer | Określa czas w sekundach między operacjami odświeżania automatycznego skalowania. Domyślne: 1 |
| `autoscale-target-utilization` | integer | Określa procent wykorzystania, którego element docelowy skalowania automatycznego, od 1 do 100. Domyślne: 70 |

Funkcja automatycznego skalowania działa zapewnienie następujących warunków:

1. Użycie docelowy jest spełniony.
2. Skalowanie nigdy nie przekracza minimalne i maksymalne ustawienia

Usługi w klastrze konkurują o zasoby klastra. Usługa funkcję spowoduje zwiększenie jego użycia zasobów klastra jako jego żądania na drugi zwiększa (RPS) i będzie powoli zwolnić zasoby jako spadku RPS. Zasoby klastra będzie można uzyskać na żądanie tak długo, jak istnieją takie zasoby usługi do uzyskania dostępu.

Aby uzyskać więcej informacji na temat używania parametrów skalowania automatycznego, zobacz [odwołanie interfejsu wiersza polecenia do zarządzania modelu](model-management-cli-reference.md) dokumentacji.

##### <a name="static-scale"></a>Statyczne skali

Ogólnie rzecz biorąc skalowanie statyczne należy unikać, ponieważ nie zezwala na zmniejszenie rozmiaru klastra Skalowanie automatyczne. Mimo tego w niektórych sytuacjach skalowanie statyczne mogą poinformowany. Na przykład w przypadku klastra jest przeznaczone wyłącznie dla jednej usługi, skalowanie automatyczne zapewnia żadnych korzyści; wszystkie zasoby klastra należy przypisać do tej usługi.

Aby skalować statycznie klastra, skalowanie automatyczne musi być wyłączona. Wyłączanie automatycznego skalowania za pomocą następującego polecenia:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Po wyłączeniu automatycznego skalowania, polecenie bezpośrednio skalowanie liczby replik usługi.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Aby uzyskać więcej informacji na temat skalowania liczby węzłów w klastrze Zobacz skali agenta węzłów w klastrze usługi kontenera.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Skalowania liczby replik przy użyciu pulpitu nawigacyjnego Kubernetes

W wierszu polecenia wpisz:

```
kubectl proxy
```

W systemie Windows Lokalizacja instalacji Kubernetes nie została automatycznie dodana do ścieżki. Najpierw przejdź do folderu instalacji:

```
c:\users\<user name>\bin
```

Po uruchomieniu polecenia powinien zostać wyświetlony następujący komunikat informacyjny:

```
Starting to serve on 127.0.0.1:8001
```

Jeśli port jest już w użyciu, zobaczysz komunikat podobny do poniższego przykładu:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Można określić alternatywnego portu numer przy użyciu *--port* parametru.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Po uruchomieniu serwera pulpitu nawigacyjnego, otwórz przeglądarkę i wprowadź następujący adres URL:

```
127.0.0.1:<port number>/ui
```

Na ekranie głównym pulpitu nawigacyjnego, kliknij przycisk **wdrożeń** na pasku nawigacyjnym po lewej stronie. Jeśli nie zostanie wyświetlone w okienku nawigacji, wybierz tę ikonę ![Menu składające się z trzech krótkich poziomych linii](media/how-to-scale-clusters/icon-hamburger.png) w lewym górnym rogu.

Znajdź wdrożenie do zmodyfikowania, a następnie kliknij tę ikonę ![ikonę Menu składające się z trzech punktów w pionie](media/how-to-scale-clusters/icon-kebab.png) po prawej stronie, a następnie kliknij przycisk **yaml programu przeglądanie i edytowanie**.

Na ekranie wdrożenia edycji Znajdź *spec* węzła, zmodyfikuj *replik* wartość, a następnie kliknij przycisk **aktualizacji**.
