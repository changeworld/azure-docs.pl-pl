---
title: Najważniejsze wskazówki dotyczące znaczników obrazów
description: Najważniejsze wskazówki dotyczące tagowania i przechowywania wersji obrazów kontenerów platformy Docker podczas wypychania obrazów do rejestru kontenerów platformy Azure i ściągania ich z rejestru kontenerów platformy Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445739"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Zalecenia dotyczące oznaczania i przechowywania obrazów kontenerów

Podczas wypychania obrazów kontenerów do rejestru kontenerów, a następnie wdrażania ich, należy strategii tagowania obrazów i przechowywania wersji. W tym artykule omówiono dwa podejścia i gdzie każdy pasuje w trakcie cyklu życia kontenera:

* **Znaczniki stabilne** — znaczniki, których używasz ponownie, na przykład w celu wskazania wersji głównej lub pomocniczej, takiej jak *mycontainerimage:1.0*.
* **Unikalne tagi** - Inny tag dla każdego obrazu, który wypchniesz do rejestru, na przykład *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabilne tagi

**Zalecenie:** Użyj stabilnych tagów, aby zachować **obrazy podstawowe** dla kompilacji kontenerów. Unikaj wdrożeń ze stabilnymi tagami, ponieważ tagi te nadal otrzymują aktualizacje i mogą wprowadzać niespójności w środowiskach produkcyjnych.

*Tagi stabilne* oznaczają, że deweloper lub system kompilacji może nadal pobierać określony tag, który nadal otrzymuje aktualizacje. Stabilny nie oznacza, że zawartość jest zamrożona. Raczej stabilny oznacza, że obraz powinien być stabilny dla intencji tej wersji. Aby zachować "stabilność", może być obsługiwany w celu zastosowania poprawek zabezpieczeń lub aktualizacji struktury.

### <a name="example"></a>Przykład

Zespół frameworka jest dostarczany w wersji 1.0. Wiedzą, że będą wysyłać aktualizacje, w tym drobne aktualizacje. Aby obsługiwać stabilne tagi dla danej wersji głównej i pomocniczej, mają dwa zestawy stabilnych tagów.

* `:1`– stabilny tag dla wersji głównej. `1`reprezentuje "najnowszą" lub "najnowszą" wersję 1.*.
* `:1.0`- stabilny tag dla wersji 1.0, umożliwiający deweloperowi powiązanie aktualizacji 1.0 i nie zostać przewinięty do przodu do wersji 1.1 po jego wydaniu.

Zespół używa również `:latest` tagu, który wskazuje na najnowszy stabilny tag, bez względu na obecną wersję główną.

Gdy dostępne są aktualizacje obrazu podstawowego lub dowolnego rodzaju obsługi wersji struktury, obrazy ze stabilnymi tagami są aktualizowane do najnowszego skrótu, który reprezentuje najbardziej aktualne stabilne wydanie tej wersji.

W takim przypadku zarówno główne, jak i pomocnicze tagi są stale obsługiwane. W scenariuszu obrazu podstawowego umożliwia to właścicielowi obrazu dostarczanie obsługiwanych obrazów.

### <a name="delete-untagged-manifests"></a>Usuwanie nieoznakowanych manifestów

Jeśli obraz ze stabilnym znacznikiem zostanie zaktualizowany, poprzednio oznakowany obraz nie zostanie oznaczony, co spowoduje, że obraz zostanie oddzielony. Manifest poprzedniego obrazu i unikatowe dane warstwy pozostają w rejestrze. Aby zachować rozmiar rejestru, można okresowo usuwać nieoznakowane manifesty wynikające ze stabilnych aktualizacji obrazu. Na przykład [automatyczne przeczyszczanie](container-registry-auto-purge.md) nieoznakowanych manifestów starszych niż określony czas trwania lub ustaw [zasady przechowywania](container-registry-retention-policy.md) dla manifestów nieoznakowanych.

## <a name="unique-tags"></a>Unikatowe tagi

**Zalecenie:** Użyj unikatowych tagów dla **wdrożeń,** szczególnie w środowisku, które może być skalowane w wielu węzłach. Prawdopodobnie chcesz zamierzone wdrożenia spójnej wersji składników. Jeśli kontener zostanie uruchomiony ponownie lub koordynator skaluje więcej wystąpień, hosty nie przypadkowo wyciągną nowszą wersję, niezgodną z innymi węzłami.

Unikalne tagowanie oznacza po prostu, że każdy obraz wypchnięty do rejestru ma unikalny tag. Tagi nie są ponownie używane. Istnieje kilka wzorców, które można wykonać, aby wygenerować unikatowe tagi, w tym:

* **Sygnatura data-godzina** — to podejście jest dość powszechne, ponieważ można wyraźnie stwierdzić, kiedy obraz został zbudowany. Ale, jak skorelować go z powrotem do systemu kompilacji? Czy musisz znaleźć kompilację, która została ukończona w tym samym czasie? W jakiej strefie czasowej się znajdujesz? Czy wszystkie twoje systemy kompilacji są skalibrowane do czasu UTC?
* **Git commit** — to podejście działa, dopóki nie zaczniesz obsługiwać aktualizacji obrazu podstawowego. Jeśli nastąpi aktualizacja obrazu podstawowego, system kompilacji rozpoczyna się z tym samym zatwierdzeniem Git, co poprzednia kompilacja. Jednak obraz podstawowy ma nową zawartość. Ogólnie rzecz biorąc, zatwierdzenie Git zapewnia *tag pół-stabilny.*
* **Podsumowanie manifestu** — każdy obraz kontenera wypchnięty do rejestru kontenerów jest skojarzony z manifestem, identyfikowanym przez unikatowy skrót SHA-256 lub skrót. Chociaż unikatowe, skrót jest długa, trudne do odczytania i nieskorelowane ze środowiskiem kompilacji.
* **Identyfikator kompilacji** — ta opcja może być najlepsza, ponieważ jest prawdopodobnie przyrostowa i umożliwia skorelowanie z powrotem do określonej kompilacji, aby znaleźć wszystkie artefakty i dzienniki. Jednak, jak oczywiste podsumowanie, może to być trudne dla człowieka do odczytania.

  Jeśli organizacja ma kilka systemów kompilacji, prefiksowanie tagu o nazwie `<build-system>-<build-id>`systemu kompilacji jest odmianą tej opcji: . Na przykład można odróżnić kompilacje od systemu kompilacji jenkins zespołu interfejsu API i systemu kompilacji azure potoków usługi Web zespołu sieci web.

### <a name="lock-deployed-image-tags"></a>Blokowanie rozmieszczonych znaczników obrazów

Najlepszym rozwiązaniem jest [zablokowanie](container-registry-image-lock.md) dowolnego wdrożonego znacznika obrazu, `write-enabled` ustawiając `false`jego atrybut na . Ta praktyka uniemożliwia nieumyślne usunięcie obrazu z rejestru i ewentualnie zakłócenie wdrożeń. Krok blokowania można uwzględnić w potoku wydania.

Blokowanie wdrożonego obrazu nadal umożliwia usunięcie innych, nierozmieszczonych obrazów z rejestru przy użyciu funkcji rejestru kontenerów platformy Azure w celu obsługi rejestru. Na przykład [automatyczne przeczyszczanie](container-registry-auto-purge.md) nieoznakowanych manifestów lub odblokowanych obrazów starszych niż określony czas trwania lub ustaw [zasady przechowywania](container-registry-retention-policy.md) dla manifestów nieoznakowanych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej szczegółowe omówienie pojęć w tym artykule, zobacz blogu [Docker Tagging: Najlepsze wskazówki dotyczące tagowania i przechowywania obrazów docker.](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)

Aby zmaksymalizować wydajność i ekonomiczne korzystanie z rejestru kontenerów platformy Azure, zobacz [Najważniejsze wskazówki dotyczące rejestru kontenerów platformy Azure.](container-registry-best-practices.md)

<!-- IMAGES -->


<!-- LINKS - Internal -->

