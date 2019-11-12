---
title: Obrazy tagów i wersji w Azure Container Registry
description: Najlepsze rozwiązania dotyczące tagowania i przechowywania wersji obrazów kontenerów platformy Docker podczas wypychania obrazów do i ściągania obrazów z usługi Azure Container Registry
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: 41013fb5831d09d7a4334e94d2b8b39e0cafe4d2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931560"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów

Podczas wypychania wdrażania obrazów kontenera do rejestru kontenerów, a następnie wdrażania ich, potrzebna jest strategia dla tagowania obrazów i przechowywania wersji. W tym artykule omówiono dwa podejścia i miejsce, w którym każdy z nich mieści się w cyklu życia kontenera:

* **Stałe znaczniki** — Tagi, których można użyć, na przykład, aby wskazać wersję główną lub pomocniczą, taką jak *mycontainerimage: 1.0*.
* **Unikatowe Tagi** — inny tag dla każdego obrazu, który można wypchnąć do rejestru, taki jak *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Stałe Tagi

**Zalecenie**: Użyj stabilnych tagów do obsługi **obrazów podstawowych** dla kompilacji kontenerów. Unikaj wdrożeń z stabilnymi tagami, ponieważ te Tagi nadal odbierają aktualizacje i mogą wprowadzać niespójności w środowiskach produkcyjnych.

*Stałe znaczniki* oznaczają dewelopera lub system kompilacji, można kontynuować ściąganie określonego tagu, który będzie nadal otrzymywać aktualizacje. Stabilne nie oznacza, że zawartość jest zamrożona. Zamiast tego, stabilne oznacza, że obraz powinien być stabilny dla zamiaru tej wersji. Aby zachować stabilność, może być możliwe zastosowanie poprawek zabezpieczeń lub aktualizacji struktury.

### <a name="example"></a>Przykład

Zespół struktury dostarcza wersję 1,0. Wiedzą oni, że będą dostarczali aktualizacje, w tym drobne aktualizacje. Aby zapewnić obsługę stabilnych tagów dla danej wersji głównej i pomocniczej, mają dwa zestawy stabilnych tagów.

* `:1` — stabilny tag wersji głównej. `1` reprezentuje "najnowszą" lub "najnowszą" wersję "1. *".
* `:1.0`— stabilny tag dla wersji 1,0, dzięki czemu deweloper może powiązać z aktualizacją 1,0 i nie zostać przesunięty do przodu do 1,1 po jego udostępnieniu.

Zespół używa również znacznika `:latest`, który wskazuje na najnowszy stabilny tag, niezależnie od tego, jaka jest bieżąca wersja główna.

Gdy dostępne są aktualizacje obrazu podstawowego lub dowolnego typu wersji obsługującej platformę, obrazy z stabilnymi tagami są aktualizowane do najnowszego podsumowania, które reprezentuje najbardziej aktualną, stabilną wersję tej wersji.

W takim przypadku Tagi główne i pomocnicze są stale poddane obsłużeniu. W scenariuszu obrazu podstawowego umożliwia właścicielowi obrazu udostępnianie obrazów z obsługą.

## <a name="unique-tags"></a>Unikatowe Tagi

**Zalecenie**: Użyj unikatowych znaczników dla **wdrożeń**, szczególnie w środowisku, które może być skalowane na wielu węzłach. Możesz chcieć zamierzone wdrożenia spójnej wersji składników. Jeśli kontener zostanie ponownie uruchomiony lub program Orchestrator skaluje więcej wystąpień, hosty nie będą przypadkowo ściągać nowszej wersji, niespójn z innymi węzłami.

Unikatowe znakowanie oznacza po prostu, że każdy obraz wypychany do rejestru ma unikatowy tag. Tagi nie są ponownie używane. Istnieje kilka wzorców, które można wykonać, aby generować unikatowe Tagi, w tym:

* **Sygnatura daty i godziny** — ta metoda jest dość powszechna, ponieważ można jasno określić, kiedy obraz został skompilowany. Ale jak skorelować ją z powrotem do systemu kompilacji? Czy musisz znaleźć kompilację, która została ukończona w tym samym czasie? W jakiej strefie czasowej jesteś? Czy wszystkie systemy kompilacji zostały skalibrowane do czasu UTC?
* **Zatwierdzenie git** — to podejście działa do momentu rozpoczęcia obsługi aktualizacji obrazu podstawowego. W przypadku aktualizacji obrazu podstawowego system kompilacji rozpoczyna pracę z tym samym zatwierdzeniem git co poprzednią kompilację. Obraz podstawowy ma jednak nową zawartość. Ogólnie rzecz biorąc, zatwierdzenie git zawiera tag *częściowo*stabilny.
* **Szyfrowanie manifestu** — każdy obraz kontenera wypychany do rejestru kontenerów jest skojarzony z manifestem, identyfikowanym przez unikatowy skrót SHA-256 lub skrótem. Chociaż unikatowy, skrót jest długi, trudny do odczytania i nieskorelowany ze środowiskiem kompilacji.
* **Identyfikator kompilacji** — ta opcja może być Najlepsza, ponieważ jest najprawdopodobniej przyrostowa i umożliwia korelację z powrotem z konkretną kompilacją w celu znalezienia wszystkich artefaktów i dzienników. Jednak takie jak szyfrowanie manifestu może być trudne do odczytania przez człowieka.

  Jeśli organizacja ma kilka systemów kompilacji, poprzedź tag nazwą systemu kompilacji jest odmianą tej opcji: `<build-system>-<build-id>`. Na przykład można odróżnić kompilacje od systemu kompilacji Jenkins zespołu interfejsu API i systemu kompilacji Azure Pipelines zespołu internetowego.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z bardziej szczegółowym omówieniem pojęć opisanych w tym artykule, zobacz wpis w blogu — [znaczniki Docker: najlepsze rozwiązania dotyczące tagowania i przechowywania wersji obrazów platformy Docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Aby zwiększyć wydajność i ekonomiczne użycie usługi Azure Container Registry, zobacz [najlepsze rozwiązania dotyczące Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

