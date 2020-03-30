---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Poznaj zasady obsługi technicznej wersji usługi Kubernetes i cykl życia klastrów w usłudze Azure Kubernetes (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593448"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service)

Społeczność Kubernetes wydaje wersje pomocnicze mniej więcej co trzy miesiące. Te wersje zawierają nowe funkcje i ulepszenia. Wersje poprawek są częstsze (czasami co tydzień) i są przeznaczone tylko do krytycznych poprawek błędów w wersji pomocniczej. Te wersje poprawek zawierają poprawki dotyczące luk w zabezpieczeniach lub głównych błędów wpływających na dużą liczbę klientów i produktów działających w produkcji na podstawie aplikacji Kubernetes.

AKS ma na celu certyfikację i wydanie nowych wersji Kubernetes w ciągu 30 dni od wydania wyższego szczebla, z zastrzeżeniem stabilności wydania.

## <a name="kubernetes-versions"></a>Wersje Kubernetes

Kubernetes używa standardowego schematu przechowywania [wersji semantycznych.](https://semver.org/) Oznacza to, że każda wersja kubernetes jest zgodna z tym schematem numeracji:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Każdy numer w wersji wskazuje ogólną zgodność z poprzednią wersją:

* Główne wersje zmieniają się, gdy niezgodne zmiany interfejsu API lub zgodność z powrotem mogą zostać przerwane.
* Wersje pomocnicze zmieniają się po wprowadzeniu zmian funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* Wersje poprawek zmieniają się po wprowadzeniu poprawek błędów zgodnych z powrotem.

Użytkownicy powinni dążyć do uruchomienia najnowszej wersji poprawki wersji pomocniczej, którą są uruchomione, na przykład jeśli klaster produkcyjny jest na *1.12.14* i *1.12.15* jest najnowszą dostępną wersją poprawki dostępną dla serii *1.12,* należy uaktualnić do *wersji 1.12.15,* gdy tylko będziesz w stanie upewnić się, że klaster jest w pełni poprawiony i obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji usługi Kubernetes

Usługa AKS obsługuje trzy pomocnicze wersje usługi Kubernetes:

* Bieżąca wersja pomocnicza wydana w AKS (N)
* Dwie poprzednie wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje również dwie stabilne poprawki.

Jest to znane jako "N-2": (N (Najnowsza wersja) - 2 (wersje pomocnicze)).

Na przykład jeśli program AKS wprowadzi *obecnie standard 1.15.a,* pomoc techniczna jest dostępna dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Gdzie ".letter" jest reprezentatywna dla wersji poprawek.

Szczegółowe informacje na temat komunikatów dotyczących zmian wersji i oczekiwań można znaleźć w "Komunikatach" poniżej.

Po wprowadzeniu nowej wersji pomocniczej najstarsza wersja pomocnicza i obsługiwane wersje poprawek są przestarzałe i usuwane. Na przykład, jeśli bieżąca obsługiwana lista wersji jest:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

AKS wydaje 1.16. *, oznacza to, że 1.13.* (wszystkie wersje 1.13) zostaną usunięte i nie są obsługiwane.

> [!NOTE]
> Należy pamiętać, że jeśli klienci są uruchomione nieobsługiwany wersji Kubernetes, zostaną poproszeni o uaktualnienie podczas żądania pomocy technicznej dla klastra. Klastry z nieobsługiwały wersji kubernetes nie są objęte [zasadami obsługi usługi AKS](https://docs.microsoft.com/azure/aks/support-policies).

Oprócz powyższych wersji pomocniczych, AKS obsługuje dwie najnowsze wersje **poprawek** danej wersji pomocniczej. Na przykład, biorąc pod uwagę następujące obsługiwane wersje:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Jeśli upstream Kubernetes wydany 1.15.3 i 1.14.6 i AKS zwalnia te wersje poprawek, najstarsze wersje poprawki są przestarzałe i usuwane, a lista obsługiwanych wersji staje się:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Komunikacja

* Dla nowych **wersji pomocniczych** Kubernetes
  * Wszyscy użytkownicy są publicznie powiadamiani o nowej wersji i jakiej wersji zostaną usunięte.
  * Po wydaniu nowej wersji poprawki najstarsza wersja poprawki jest usuwana w tym samym czasie.
  * Klienci mają **30 dni** od daty powiadomienia publicznego, aby uaktualnić do obsługiwanej wersji pomocniczej.
* Dla nowych **wersji poprawek** Kubernetes
  * Wszyscy użytkownicy są powiadamiani o nowej wersji poprawki jest zwolniony i uaktualnić do najnowszej wersji poprawki.
  * Użytkownicy mają **30 dni** na uaktualnienie do nowszej, obsługiwanej wersji poprawki przed usunięciem najstarszej.

AKS definiuje "wydaną wersję" jako ogólnie dostępne wersje, włączoną we wszystkich pomiarach SLO / Jakość usługi i dostępną we wszystkich regionach. AKS może również obsługiwać wersje w wersji zapoznawczej, które są jawnie oznaczone i podlegają warunkom i postanowieniom w wersji zapoznawczej.

#### <a name="notification-channels-for-aks-changes"></a>Kanały powiadomień o zmianach AKS

Usługa AKS publikuje regularne aktualizacje usługi, które podsumowują nowe wersje kubernetes, zmiany usług i aktualizacje składników, które zostały wydane w usłudze w [usłudze GitHub](https://github.com/Azure/AKS/releases).

Te zmiany są walcowane do wszystkich klientów w ramach regularnej konserwacji, która jest oferowana jako część usługi zarządzanej, niektóre wymagają jawnych uaktualnień, podczas gdy inne nie wymagają żadnych działań.

Powiadomienia są również wysyłane za pośrednictwem:

* [Informacje o wersji AKS](https://aka.ms/aks/releasenotes)
* Powiadomienia w witrynie Azure Portal
* [Kanał aktualizacji platformy Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Wyjątki zasad obsługiwanych wersji

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji, które zostały zidentyfikowane jako posiadające jeden lub więcej krytycznych problemów produkcyjnych i problemów z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą zostać pominięte lub przyspieszone wdrażanie w zależności od ważności błędu lub problemu z zabezpieczeniami.

### <a name="azure-portal-and-cli-default-versions"></a>Domyślne wersje witryny Azure portal i interfejsu wiersza polecenia

Podczas wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure, klaster jest domyślnie do wersji pomocniczej N-1 i najnowszej poprawki. Na przykład, jeśli AKS obsługuje *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*i *1.13.f*, wybrana wersja domyślna to *1.14.c*.

AKS wybiera domyślną wartość N-1, aby domyślnie zapewnić klientom znaną, stabilną i poprawioną wersję.

## <a name="list-currently-supported-versions"></a>Lista aktualnie obsługiwanych wersji

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla twojej subskrypcji i regionu, użyj polecenia [az aks get-versions.][az-aks-get-versions] Poniższy przykład zawiera listę dostępnych wersji kubernetes dla regionu *EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Najczęściej zadawane pytania

**Co się stanie, gdy klient uaktualni klaster Kubernetes z wersją pomocniczą, która nie jest obsługiwana?**

Jeśli korzystasz z wersji *n-3,* jesteś poza pomocą techniczną i zostaniesz poproszony o uaktualnienie. Jeśli uaktualnienie z wersji n-3 do n-2 zakończy się pomyślnie, jesteś teraz w naszych zasadach pomocy technicznej. Przykład:

- Jeśli najstarsza obsługiwana wersja AKS to *1.13.a* i jesteś na *1.12.b* lub starszych, jesteś poza pomocą techniczną.
- Jeśli uaktualnienie z *1.12.b* do *1.13.a* lub wyższej zakończy się pomyślnie, powrócisz do naszych zasad pomocy technicznej.

Uaktualnienia do wersji starszych niż obsługiwane okno *N-2* nie są obsługiwane. W takich przypadkach zaleca się klientom tworzenie nowych klastrów AKS i ponowne wdrożenie ich obciążeń z wersjami w obsługiwanym oknie.

**Co oznacza "Poza wsparciem"**

"Poza pomocą techniczną" oznacza, że wersja, którą uruchamiasz, znajduje się poza listą obsługiwanych wersji i zostaniesz poproszony o uaktualnienie klastra do obsługiwanej wersji podczas żądania pomocy technicznej. Ponadto usługa AKS nie zapewnia żadnych gwarancji środowiska uruchomieniowego ani innych gwarancji dla klastrów poza listą obsługiwanych wersji.

**Co się stanie, gdy klient skaluje klaster Kubernetes z wersją pomocniczą, która nie jest obsługiwana?**

W przypadku wersji pomocniczych nie obsługiwanych przez usługi AKS skalowanie w lub obecnie powinno nadal działać, ale zdecydowanie zaleca się uaktualnienie, aby przywrócić klaster do obsługi.

**Czy klient może pozostać w wersji Kubernetes na zawsze?**

Tak. Jeśli jednak klaster nie znajduje się w jednej z wersji obsługiwanych przez usługi AKS, klaster jest poza zasadami obsługi usługi AKS. Platforma Azure nie uaktualnia automatycznie klastra ani nie usuwa go.

**Jaka wersja obsługuje płaszczyznę sterowania, jeśli pula węzłów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Płaszczyzna kontrolna musi znajdować się w oknie wersji ze wszystkich pul węzłów. Szczegółowe informacje na temat uaktualniania pul płaszczyzny sterowania lub węzłów można znaleźć w dokumentacji [dotyczącej uaktualniania pul węzłów](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania [klastra, zobacz Uaktualnianie klastra usługi Azure Kubernetes Service (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
