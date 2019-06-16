---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Zasady pomocy technicznej wersji platformy Kubernetes i cyklem życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069676"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Społeczność platformy Kubernetes wydaje wersje pomocnicze średnio co trzy miesiące. Te wydania zawierają nowe funkcje i ulepszenia. Wydania poprawek odbywają się częściej (czasami co tydzień) i mają na celu naprawienie tylko krytycznych usterek w wersji pomocniczej. Wersje te poprawki zawierają poprawki luk w zabezpieczeniach lub główne usterki wpływające na ochronę dużej liczby klientów i produktów, działających w oparciu o rozwiązania Kubernetes w środowisku produkcyjnym.

AKS ma na celu certyfikowania i wydawania nowych wersji rozwiązania Kubernetes w ciągu 30 dni wersji nadrzędnego, z zastrzeżeniem stabilności wersji.

## <a name="kubernetes-versions"></a>Wersje rozwiązania Kubernetes

Kubernetes używa standardu [Semantic Versioning](https://semver.org/) schematu przechowywania wersji. Oznacza to, że każda wersja programu Kubernetes następuje tego systemu numeracji:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Każdą liczbę w wersji wskazuje ogólnej zgodności z poprzednią wersją:

* Zmiany interfejsu API zmiany wersji głównych, gdy jest to niezgodne, lub wstecznej zgodności może być uszkodzony.
* Wersje pomocnicze zmianie, gdy funkcje zmian, które są wstecznie zgodne z innych wersji pomocniczej.
* Zastosować poprawki zostały wprowadzone zmiany, gdy poprawki błędów wstecznie zgodne wersje.

Ogólnie rzecz biorąc, użytkownicy powinna wszelkich starania, aby uruchomić najnowszą wersję poprawki wersji pomocniczej, są uruchomione, na przykład jeśli klastrem produkcyjnym znajduje się na *1.13.6* i *1.13.7* jest najnowsza wersja poprawki wersja jest dostępna dla *1.13* serii, należy uaktualnić system do *1.13.7* jak tylko można zapewnić klastra jest w pełni poprawkami i obsługiwane.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji platformy Kubernetes

Usługa AKS obsługuje cztery wersje pomocnicze platformy Kubernetes:

* Bieżąca wersja pomocnicza publikowany w usłudze AKS (N)
* Trzy poprzednie wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje także dwie stabilne poprawki.

Jest to nazywane "N-3"-(N (Najnowsza wersja) - 3 (wersje pomocnicze)).

Na przykład, jeśli wprowadzono AKS *1.13.x* obecnie Pomoc techniczna jest dostępna dla następujących wersji:

Nowa wersja pomocnicza listę wersji obsługiwane
-----------------        ----------------------
1.13.x                   1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Gdzie "x" i ".a" i "b" to wersje reprezentatywny poprawki.

Szczegółowe informacje dotyczące komunikacji w sprawie zmiany wersji i oczekiwania zobacz "Komunikacja" poniżej.

Jeśli wprowadzono nową wersję pomocniczą najstarsze pomocniczej wersji i poprawki wersji obsługiwane są przestarzałe i usuwać. Na przykład, jeśli bieżący obsługiwany listę wersji jest:

<a name="supported-version-list"></a>Lista obsługiwanych wersji
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

I AKS zwalnia 1.13.x, oznacza to, zostaną usunięte wersje 1.9.x (wszystkich 1,9 wersje) oraz okres wsparcia technicznego upływa.

> [!NOTE]
> Należy pamiętać, że klienci korzystający z nieobsługiwanej wersji platformy Kubernetes, ich poprosimy Cię o uaktualnienie podczas przesyłania żądania pomocy technicznej dla klastra. Klastry z systemem nieobsługiwane wersje rozwiązania Kubernetes nie są objęte [AKS obsługuje zasady](https://docs.microsoft.com/azure/aks/support-policies).


Oprócz powyższych na wersje pomocnicze AKS obsługuje dwa najnowsze *poprawki** wersji danej wersji pomocniczej. Na przykład biorąc pod uwagę następujące obsługiwane wersje:

<a name="supported-version-list"></a>Lista obsługiwanych wersji
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Jeśli oryginalne rozwiązanie Kubernetes wydane 1.12.3 i 1.11.6 i zwalnia AKS patch te wersje, najstarsze wersje poprawki są przestarzałe i usunięte i staje się lista obsługiwanych wersji:

<a name="supported-version-list"></a>Lista obsługiwanych wersji
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>Komunikacja

* Dla nowych **pomocnicza** wersji rozwiązania Kubernetes
  * Wszyscy użytkownicy są powiadamiane o nowej wersji i jakie wersja zostanie usunięta.
  * Klienci korzystający z wersji **do usunięcia** Powiadomimy Cię, że **60 dni** uaktualnić do obsługiwanej wersji (np. wersja pomocnicza).
* Dla nowych **poprawki** wersji rozwiązania Kubernetes
  * Wszyscy użytkownicy są powiadamiani, zostały udostępnione nowej wersji poprawki i uaktualnienia do najnowszej wersji poprawki.
  * Użytkownicy mają **30 dni** uaktualnić do wersji nowsze, obsługiwanych poprawki. Użytkownicy mają **30 dni** uaktualnienia do wersji obsługiwana poprawka, zanim najstarsze zostanie usunięte.

AKS definiuje "wydane" jako ogólnej dostępności w celu SLO wszystkie włączone / jakości pomiarów usługi i jest dostępna we wszystkich regionach.

> [!NOTE]
> Klienci są powiadamiani kubernetes wersjach i deprecations, gdy wersja pomocnicza jest przestarzała lub usunięci użytkownicy otrzymują 60 dni uaktualnić do obsługiwanej wersji. W przypadku wersji poprawki klienci otrzymują 30 dni uaktualnić do obsługiwanej wersji.

Powiadomienia są wysyłane za pośrednictwem:

* [Informacje o wersji usługi AKS](https://aka.ms/aks/releasenotes)
* Powiadomienia o witrynie Azure portal
* [Kanał aktualizacji platformy Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Wyjątki od zasad

AKS zastrzega sobie prawo do dodawania lub usuwania nowe i istniejące wersje, zidentyfikowanych mieć co najmniej jeden produkcyjne krytyczne, wpływ na błędy lub problemy z zabezpieczeniami bez wcześniejszego powiadomienia.

Wersje określona poprawka może zostać pominięta lub accelerated wdrożenia, w zależności od ważności problemu usterki lub zabezpieczeń.

### <a name="azure-portal-and-cli-default-versions"></a>Witryna Azure portal i interfejs wiersza polecenia domyślne wersje

Podczas wdrażania klastra usługi AKS w portalu lub przy użyciu wiersza polecenia platformy Azure, klaster ma zawsze wartość wersji pomocniczej n-1 i najnowszych poprawek. Na przykład, jeśli obsługuje AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, jest wersja domyślna dla nowych klastrów *1.12.b*.

AKS wartość domyślna to n-1 (na przykład 1.12.b minor.latestPatch) aby zapewnić klientom znanych, stabilnego i poprawiono wersji domyślnie.

## <a name="list-currently-supported-versions"></a>Lista aktualnie obsługiwane wersje

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji oraz regionu, należy użyć [az aks get wersje] [ az-aks-get-versions] polecenia. Poniższy przykład wyświetla listę dostępnych wersji rozwiązania Kubernetes dla *EastUS* regionu:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Dane wyjściowe są podobne do poniższego przykładu, który pokazuje tę wersję rozwiązania Kubernetes *pytanie 1.13.5* jest dostępny do najnowszej wersji:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy klient uaktualni klaster usługi Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Jeśli użytkownik pracuje na *n-4* wersji, możesz znajdują się poza pomocy technicznej i zostanie poproszony o uaktualnienie. W przypadku uaktualnienia z wersji n-4 do n-3 zakończy się powodzeniem, jest teraz w ramach naszych zasad pomocy technicznej. Na przykład:

- W przypadku obsługiwanych wersji usługi AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, i *1.10.e* + *1.10F* i znajdują się na *1.9.g* lub *1.9.h*, znajdują się poza pomocy technicznej.
- Jeśli uaktualnienie z *1.9.g* lub *1.9.h* do *1.10.e* lub *1.10.f* zakończy się powodzeniem, są ponownie w ramach naszych zasad pomocy technicznej.

Uaktualnienia do wersji starszej niż *n-4* nie są obsługiwane. W takich przypadkach firma Microsoft zaleca klientom tworzenie nowych klastrów usługi AKS i ponownie wdrożyć swoje obciążenia.

**Co oznacza "poza Support"**

"Poza pomocy technicznej" oznacza, że wersję, którą pracujesz, jest poza listą obsługiwanych wersji i użytkownik będzie musiał uaktualnić klaster do obsługiwanej wersji, przesyłając żądanie pomocy technicznej. Ponadto AKS nie powoduje żadnych wykonawcza lub innych gwarancji, w przypadku klastrów poza listą obsługiwanych wersji.

**Co się stanie, gdy klient skaluje klaster usługi Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Dla wersji pomocniczej, nie są obsługiwane przez usługę AKS skalowanie do wewnątrz lub na zewnątrz w dalszym ciągu działać bez problemów.

**Można klient Pozostań na wersję rozwiązania Kubernetes nieskończona?**

Tak. Jednakże jeśli klaster nie znajduje się w jednej z wersji obsługiwane przez usługę AKS, klastra jest poza zasady pomocy technicznej usługi AKS. Azure automatycznie uaktualnić klaster lub usunąć łącznik.

**Jaka wersja jest główny Pomocy technicznej, jeśli klaster agenta nie znajduje się w jednym z obsługiwanych wersji usługi AKS?**

Wzorzec zostanie automatycznie zaktualizowany do najnowszej obsługiwanej wersji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
