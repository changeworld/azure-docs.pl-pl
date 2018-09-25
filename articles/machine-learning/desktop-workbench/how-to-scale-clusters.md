---
title: Jak skalować klaster usługi kontenera platformy Azure dla usługi Machine Learning | Dokumentacja firmy Microsoft
description: Skalowanie klastra usługi ACS - automatycznego skalowania i skalowanie statycznej; Skalowanie liczby węzłów w klastrze
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 718f351ad4ce325f3585ebcbe7adfc4b5905e734
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998614"
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Skalowanie klastra zarządzania przepływnością usługi sieci web

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


## <a name="why-scale-the-cluster"></a>Dlaczego skalowanie klastra?

Skalowanie klastra usługi Azure Container Service (ACS) jest efektywne w celu zoptymalizowania przepływności usług przy jednoczesnym zachowaniu rozmiar klastra do minimum, aby zmniejszyć koszt. 

Aby lepiej zrozumieć skalowania automatycznego, rozważmy następujący przykład klastra uruchomione trzy usługi sieci web:

![Przykład: Trzy usługi w klastrze](media/how-to-scale-clusters/three-services.png)

Usługi mają różne szczytowego zapotrzebowania: Z 1 (niebieska linia) wymaga 40 zasobników przy szczytowym zapotrzebowaniu, z 2 (pomarańczowa linia) wymaga 38 akurat w okresie szczytowym, a usługa 3 (szara linia) wymaga 50 akurat w okresie szczytowym. Jeśli zarezerwujesz indywidualnie pojemności szczytu wymaganych dla każdej usługi, ten klaster potrzebuje co najmniej 40 + 38 + 50 = 128 łączna liczba zasobników.

Jednak należy wziąć pod uwagę użycie rzeczywistych zasobnik w dowolnym momencie w czasie, reprezentowane przez czarna linia przerywana na wykresie. W tym przypadku *największa liczba zasobników używane w dowolnym momencie* jest 64, która pojawia się o 20:00, gdy usługa 3 jest akurat w okresie szczytowym. W tej chwili z 3 używa 50 zasobników, ale Service 2 używa tylko 9 zasobników, a Service 1 używa tylko 5. Należy pamiętać, że jest to *szczytowe użycie* dla tego klastra. Oznacza to, że w żadnym momencie klaster używa więcej niż 64 zasobników — połowa obliczeniowe wymaganie zasobników 128 dla trzech usług niezależne skalowanie użycia szczytowego.

Przypisując zasobników w klastrze — czyli przez ponowne skalowanie — do zaspokojenia bieżącego zapotrzebowania poszczególnych usług, a nie po prostu wymagają wystarczające zasoby do szczytowego zapotrzebowania wszystkich usług, można zmniejszyć rozmiaru klastra. W tym prostym przykładzie automatyczne skalowanie zmniejsza wymagana liczba zasobników od 128-64, wycinanie rozmiar klastra wymagany połowę.

Skalowanie liczby zasobników jest operacją stosunkowo krótkie, wymaga mniej niż minutę, więc czas odpowiedzi usługi nie ma wpływu na naszych użytkowników bardzo poważnie.

> [!NOTE]
> Skalowanie klastra nie pomoże żądania opóźnień. Na potrzeby operacjonalizacji skalowanie w górę należy zwiększyć liczbę sukcesów i zmniejszyć powodować występowanie błędów niedostępności usługi. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Jak skalować usługi sieci web w klastrze usługi ACS

Opcja instalacji klastra zarządzania modelami interfejsu wiersza polecenia domyślnie konfiguruje dwóch agentów i pod jedną w danym środowisku. Ponadto moduł zbierający instaluje interfejs wiersza polecenia usługi Kubernetes.

Możesz skalować usługi sieci web, wdrożone do usługi ACS, dostosowując:

* Liczbę węzłów agenta w klastrze
* Liczba replik zasobników Kubernetes, uruchomione w węzłach agenta

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Skalowanie liczby węzłów w klastrze

Następujące polecenie ustawia liczbę węzłów agenta w klastrze:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Ukończenie tej operacji potrwa kilka minut. Aby uzyskać więcej informacji na temat skalowania liczby węzłów w klastrze, zobacz [skalowanie węzłów agenta w klastrze usługi Container Service](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Skalowanie liczby replik zasobników Kubernetes w klastrze
 
Możesz skalować liczbę replik zasobników przypisane do klastra przy użyciu interfejsu wiersza polecenia usługi Azure Machine Learning lub [pulpit nawigacyjny platformy Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Aby uzyskać więcej informacji dotyczących replik zasobników, zobacz [zasobników](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentacji.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Skalowanie klastra przy użyciu interfejsu wiersza polecenia usługi Azure Machine Learning

Istnieją dwa sposoby skalowania klastra przy użyciu interfejsu wiersza polecenia:

- Automatyczne skalowanie
- Skala statyczne

Automatyczne skalowanie jest aktywne, domyślnie, gdy usługa zostanie utworzona i w większości przypadków jest to preferowana metoda skalowania.

##### <a name="autoscale"></a>Automatyczne skalowanie

Następujące polecenie umożliwia automatyczne skalowanie i Ustawia minimalną i maksymalną liczbę replik dla usługi.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Na przykład ustawienie `autoscale-min-replicas` 5 utworzy pięciu replik. W celu osiągnięcia optymalnej liczby usługi sieci web, ustaw liczbę wartości, np. 10 i monitorować liczbę 503 komunikaty o błędach. Następnie odpowiednio numer.


| Nazwa parametru | Typ | Opis |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | wartość logiczna | Określa, czy jest włączona funkcja automatycznego skalowania. Domyślnie: true |
| `autoscale-min-replicas` | liczba całkowita | Określa minimalną liczbę zasobników. Musi być mniejsza od 0. Domyślne: 1 |
| `autoscale-max-replicas` | liczba całkowita | Określa maksymalną liczbę zasobników. Musi być mniejsze niż 1. Jeśli automatyczne skalowanie max repliki jest mniejszy niż automatyczne skalowanie — minimalna liczba replik, automatycznego skalowania max repliki zostaną zignorowane. Domyślnie: 10 |
| `autoscale-refresh-period-seconds` | liczba całkowita | Określa czas w sekundach między operacjami odświeżania automatycznego skalowania. Domyślne: 1 |
| `autoscale-target-utilization` | liczba całkowita | Określa procent wykorzystania przeznaczonego automatyczne skalowanie od 1 do 100. Domyślne: 70 |

Automatyczne skalowanie działa zapewnienie następujących warunków:

1. Wykorzystanie docelowe jest spełniony.
2. Skalowanie nigdy nie przekracza minimalnych i maksymalnych ustawień

Usługi w klastrze konkurują o zasoby klastra. Usługi przez funkcję spowoduje zwiększenie jego użycia zasobów klastra jako jego żądań na sekundę rośnie (jednostek Uzależnionych), wraz ze i powoli zwolni zasoby, co zmniejsza RPS. Zasoby klastra będzie można uzyskać na żądanie tak długo, jak istnieją takie zasoby dla usługi w celu uzyskania.

Aby uzyskać więcej informacji na temat korzystania z parametrów skalowania automatycznego, zobacz [dokumentacja interfejsu wiersza polecenia zarządzania modelu](model-management-cli-reference.md) dokumentacji.

##### <a name="static-scale"></a>Skala statyczne

Ogólnie rzecz biorąc statycznej skalowania należy unikać, ponieważ nie zezwala na zmniejszenie rozmiaru klastra skalowania automatycznego. Mimo tego w niektórych sytuacjach statyczne Skalowanie może być poinformowany. Na przykład gdy klaster jest dedykowany do jednej usługi, skalowaniem automatycznym zapewnia żadnych korzyści, wszystkie zasoby klastra należy przypisać do tej usługi.

Statycznie Skaluj klaster, należy wyłączyć automatyczne skalowanie. Aby wyłączyć automatyczne skalowanie za pomocą następującego polecenia:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Po wyłączeniu automatycznego skalowania, polecenie bezpośrednio skaluje liczbę replik dla usługi.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Aby uzyskać więcej informacji na temat skalowania liczby węzłów w klastrze zobacz Skalowanie węzłów agenta w klastrze usługi Container Service.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Skalowanie liczby replik, za pomocą pulpitu nawigacyjnego rozwiązania Kubernetes

W wierszu polecenia wpisz polecenie:

```
kubectl proxy
```

W Windows lokalizacji instalacji rozwiązania Kubernetes nie jest automatycznie dodawane do ścieżki. Najpierw należy przejść do folderu instalacji:

```
c:\users\<user name>\bin
```

Po uruchomieniu polecenia, powinien zostać wyświetlony następujący komunikat informacyjny:

```
Starting to serve on 127.0.0.1:8001
```

Jeśli port jest już w użyciu, zostanie wyświetlony komunikat podobny do poniższego przykładu:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Możesz określić alternatywny port numer przy użyciu *— port* parametru.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Po rozpoczęciu serwer pulpitu nawigacyjnego, otwórz przeglądarkę i wprowadź następujący adres URL:

```
127.0.0.1:<port number>/ui
```

Na ekranie głównym pulpitu nawigacyjnego, kliknij przycisk **wdrożeń** na pasku nawigacyjnym po lewej stronie. Jeśli nie są wyświetlane w okienku nawigacji, wybierz tę ikonę, ![Menu składający się z krótkim trzy poziome linie](media/how-to-scale-clusters/icon-hamburger.png) w lewym górnym rogu.

Znajdź wdrożenie do zmodyfikowania, a następnie kliknij tę ikonę, ![ikonę Menu składający się z trzech pionowych punktów](media/how-to-scale-clusters/icon-kebab.png) po prawej stronie, a następnie kliknij przycisk **YAML Wyświetl/Edytuj**.

Na ekranie edytowania wdrożenia, zlokalizuj *specyfikacja* węzła, zmodyfikuj *replik* wartości, a następnie kliknij przycisk **aktualizacji**.
