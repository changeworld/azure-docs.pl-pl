---
title: Obsługiwane wersje Kubernetes w usłudze Azure Kubernetes Service
description: Informacje na temat zasad obsługi wersji Kubernetes i cyklu życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 27b180d8d95d7dad967b8ac2495a795ed70836b9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147224"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Obsługiwane wersje Kubernetes w usłudze Azure Kubernetes Service (AKS)

Społeczność platformy Kubernetes wydaje wersje pomocnicze średnio co trzy miesiące. Te wydania zawierają nowe funkcje i ulepszenia. Wydania poprawek odbywają się częściej (czasami co tydzień) i mają na celu naprawienie tylko krytycznych usterek w wersji pomocniczej. Te wersje poprawek obejmują poprawki dotyczące luk w zabezpieczeniach lub poważnych usterek, które mają wpływ na dużą liczbę klientów i produktów działających w środowisku produkcyjnym w oparciu o Kubernetes.

AKS mają na celu certyfikowanie i wydanie nowych wersji Kubernetes w ciągu 30 dni od wydania nadrzędnego, z zastrzeżeniem stabilności wersji.

## <a name="kubernetes-versions"></a>Wersje Kubernetes

Kubernetes używa standardowego schematu [](https://semver.org/) obsługi wersji semantycznej. Oznacza to, że każda wersja programu Kubernetes jest zgodna z tym schematem numeracji:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* Wersje główne zmieniają się, gdy niezgodne zmiany interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* Wersje pomocnicze zmieniają się, gdy wprowadzane są zmiany funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* Wersje poprawek zmieniają się w przypadku wprowadzenia poprawek błędów zgodnych z poprzednimi wersjami.

Ogólnie rzecz biorąc, użytkownicy powinni Endeavor, aby uruchomić najnowszą wersję poprawki wersji pomocniczej, na przykład jeśli klaster produkcyjny znajduje się na *1.12.14* i *1.12.15* to najnowsza dostępna wersja poprawki dostępna dla serii *1,12* należy przeprowadzić uaktualnienie do *1.12.15* zaraz po upewnieniu się, że klaster jest w pełni objęty poprawką i jest obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji platformy Kubernetes

Usługa AKS obsługuje cztery wersje pomocnicze platformy Kubernetes:

* Bieżąca wersja pomocnicza wydana w AKS (N)
* Trzy poprzednie wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje także dwie stabilne poprawki.

Jest to tzw. "N-3" — (N (Najnowsza wersja) — 3 (wersje pomocnicze)).

Na przykład jeśli AKS wprowadza *1.13. a* dzisiaj, pomoc techniczna jest świadczona dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.13. a               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Gdzie ". a" i ". b" są reprezentatywnymi wersjami poprawek ". element "from 1.13. a może się różnić od elementu 1.12. a. Na przykład 1.13.9 i 1.12.8.

Aby uzyskać szczegółowe informacje na temat komunikacji dotyczącej zmian wersji i oczekiwań, zobacz "komunikacja" poniżej.

Po wprowadzeniu nowej wersji pomocniczej, najstarsza wersja pomocnicza i wersje poprawek są przestarzałe i usunięte. Na przykład jeśli bieżącą obsługiwaną listą wersji jest:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

I AKS wersje 1,13. *oznacza to, że 1,9.* wersje (wszystkie wersje 1,9) zostaną usunięte i nie będą obsługiwane.

> [!NOTE]
> Należy pamiętać, że jeśli klienci korzystają z nieobsługiwanej wersji programu Kubernetes, zostanie poproszony o uaktualnienie w przypadku żądania pomocy technicznej dotyczącej klastra. Klastry z nieobsługiwanymi wersjami Kubernetes nie są objęte [zasadami obsługi AKS](https://docs.microsoft.com/azure/aks/support-policies).


Oprócz powyższych w wersjach pomocniczych program AKS obsługuje dwie najnowsze wersje *poprawki** danej wersji pomocniczej. Na przykład, mając następujące obsługiwane wersje:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Jeśli Kubernetes 1.12.3 i 1.11.6 i AKS wystawia te wersje, najstarsze wersje poprawek są przestarzałe i usunięte, a lista obsługiwanych wersji staje się:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Klienci nie powinni przypinać tworzenia klastra, CI ani innych zautomatyzowanych zadań do określonych wersji poprawek. 

### <a name="communications"></a>Komunikacja

* Nowe wersje **pomocnicze** programu Kubernetes
  * Wszyscy użytkownicy są powiadamiani publicznie o nowej wersji i o wersji, która zostanie usunięta.
  * Po wydaniu nowej wersji poprawki najstarsza wersja poprawki jest usuwana w tym samym czasie.
  * Klienci mają **60 dni** od publicznej daty powiadomienia, aby przeprowadzić uaktualnienie do obsługiwanej wersji pomocniczej.
* Nowe wersje **poprawek** Kubernetes
  * Wszyscy użytkownicy otrzymają powiadomienie o wydaniu nowej wersji poprawki i przeprowadź uaktualnienie do najnowszej wersji poprawki.
  * Użytkownicy mający **30 dni** na uaktualnienie do nowszej obsługiwanej wersji poprawki. Użytkownicy mają **30 dni** , aby przeprowadzić uaktualnienie do obsługiwanej wersji poprawki przed usunięciem najstarszego elementu.

AKS definiuje "wydane" jako ogólną dostępność, włączone we wszystkich pomiarach SLO/Quality of Service i dostępne we wszystkich regionach.

> [!NOTE]
> Klienci są powiadamiani o wydaniach wersji Kubernetes i ich zaniechaniu, gdy wersja pomocnicza jest przestarzała/usuniętych użytkowników podano 60 dni, aby przeprowadzić uaktualnienie do obsługiwanej wersji. W przypadku wersji poprawek klienci otrzymują 30 dni na uaktualnienie do obsługiwanej wersji.

Powiadomienia są wysyłane za pośrednictwem:

* [Informacje o wersji AKS](https://aka.ms/aks/releasenotes)
* Powiadomienia Azure Portal
* [Kanał aktualizacji platformy Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Wyjątki zasad

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji, które zostały zidentyfikowane, aby mieć co najmniej jeden krytyczny wpływ na błędy lub problemy z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą zostać pominięte lub przyspieszone wdrożenia w zależności od ważności usterki lub problemu z zabezpieczeniami.

### <a name="azure-portal-and-cli-default-versions"></a>Azure Portal i domyślne wersje interfejsu wiersza polecenia

W przypadku wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest zawsze ustawiany jako wersja pomocnicza N-1 i Najnowsza poprawka. Na przykład, jeśli AKS obsługuje *1.13. a*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11. b*, *1.10. a* + , wersja domyślna dla nowych klastrów to *1.12. b* .

AKS domyślnie N-1 (pomocnicze. latestPatch, EG 1.12. b), aby zapewnić klientom domyślnie znaną, stabilną i zaznaczoną dla wersji wersję.

## <a name="list-currently-supported-versions"></a>Wyświetl listę obecnie obsługiwanych wersji

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji i regionu, użyj polecenia [AZ AKS Get-Versions][az-aks-get-versions] . Poniższy przykład zawiera listę dostępnych wersji Kubernetes dla regionu *wschodniego* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Dane wyjściowe są podobne do poniższego przykładu, który pokazuje, że Kubernetes wersja *1.14.6* to najnowsza dostępna wersja:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy klient uaktualnia klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

Jeśli korzystasz z wersji *n-4* , jesteś poza pomocą techniczną i zostanie wyświetlony monit o uaktualnienie. Jeśli uaktualnienie z wersji n-4 do n-3 powiedzie się, jesteś teraz w naszych zasadach pomocy technicznej. Przykład:

- Jeśli obsługiwane wersje programu AKS to *1.13. a*, *1.12. b* + *1.12. c*, *1.11. d* + *1.11. e*, i *1.10. f* + *1.10. g* i są one w *1.9. h* lub *1.9. i* , jesteś poza pomocą techniczną.
- Jeśli uaktualnienie z programu *1.9. h* lub *1.9. i* do *1.10. f* lub *1.10. g* powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej.

Uaktualnienia do wersji starszych niż *n-4* nie są obsługiwane. W takich przypadkach zalecamy utworzenie nowych klastrów AKS i ponowne wdrożenie ich obciążeń.

**Co oznacza "poza pomocą techniczną"**

"Poza obsługą" oznacza, że uruchomiona wersja znajduje się poza listą obsługiwanych wersji, a podczas żądania pomocy technicznej zostanie wyświetlony monit o uaktualnienie klastra do obsługiwanej wersji. Ponadto AKS nie wykonuje żadnego środowiska uruchomieniowego ani innych gwarancji dla klastrów spoza listy obsługiwanych wersji.

**Co się stanie, gdy klient skaluje klaster Kubernetes z wersją pomocniczą, która nie jest obsługiwana?**

W przypadku wersji pomocniczych nieobsługiwanych przez AKS skalowanie w górę lub w dół w dalszym ciągu działa bez żadnych problemów.

**Czy klient może nadal korzystać z wersji Kubernetes?**

Tak. Jeśli jednak klaster nie znajduje się w jednej z wersji obsługiwanej przez AKS, klaster jest spoza zasad obsługi AKS. Platforma Azure nie uaktualnia automatycznie klastra ani nie usuwa go.

**Jaka wersja jest obsługiwana przez główną, Jeśli klaster agentów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Wzorzec zostanie automatycznie zaktualizowany do najnowszej obsługiwanej wersji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
