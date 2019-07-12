---
title: Wersja i tag obrazy w usłudze Azure Container Registry
description: Najlepsze praktyki dla wersji platformy Docker i tagowania obrazów kontenerów
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800395"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Zalecenia dotyczące obrazów kontenerów znakowanie i przechowywania wersji

Podczas wypychania wdrażanie obrazów kontenera do rejestru kontenerów, a następnie wdrożenie ich, potrzebujesz strategii tagowanie obrazu oraz zarządzanie ich wersjami. W tym artykule omówiono dwa podejścia i gdzie każdy znajdzie się podczas cyklu życia kontenera:

* **Czasu trwania stanu stabilnego tagi** — tagi, które możesz ponownie użyć, na przykład, aby wskazywać główny lub podrzędny numer wersji, takich jak *mycontainerimage:1.0*.
* **Tagi unikatowy** — inny znacznik dla każdego obrazu wypchnąć do rejestru, takie jak *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabilna tagów

**Zalecenie**: Obsługa za pomocą tagów stabilne **podstawowa obrazów** kompilacji kontenera. Unikaj wdrożeń przy użyciu tagów stabilny, ponieważ tych znaczników nadal otrzymywać aktualizacje i może powodować niespójności w środowiskach produkcyjnych.

*Czasu trwania stanu stabilnego tagi* oznacza deweloperem aplikacji lub systemu kompilacji, mogą w dalszym ściągnięcia konkretny tag, który będzie można pobrać aktualizacji. Wersja stabilna nie oznacza, że zawartość są zablokowane. Zamiast stabilny sugeruje, że obraz powinien być stabilna dla celem tej wersji. Na bieżąco "stałe", może być obsługiwane do zastosowania poprawek zabezpieczeń, lub aktualizacje framework.

### <a name="example"></a>Przykład

Zespół framework jest dostarczany w wersji 1.0. Ich o tym, że będą one dostarczanie aktualizacji, w tym aktualizacje pomocnicze. Aby obsługiwać stabilne znaczników dla danej wersji głównych i pomocniczych, mają dwa rodzaje stabilne tagów.

* `:1` — stabilne tagu dla wersji głównej. `1` reprezentuje wersji 1.* "najnowsza" lub "najnowsza".
* `:1.0`-stabilne tagu dla wersji 1.0, dzięki czemu Deweloper powiązać aktualizacji 1.0, a nie zostać przeniesiona do przodu 1.1 po jego udostępnieniu.

Zespół używa również `:latest` znacznik, który wskazuje na najnowszy stabilny tagu, niezależnie od tego, jakie obecnie wersja główna jest.

Jeśli są dostępne aktualizacje obrazu podstawowego lub dowolnego typu obsługi wersji Framework obrazów za pomocą tagów stabilne zostały zaktualizowane pod kątem najnowszych digest, reprezentujący stabilne najbardziej aktualną wersję tej wersji.

W tym przypadku znaczniki główne i pomocnicze są stale serwisowana. W przypadku obrazu podstawowego dzięki temu właściciela obrazu, aby zapewnić obsługiwanych obrazów.

## <a name="unique-tags"></a>Unikatowe tagów

**Zalecenie**: Za pomocą unikatowych tagów dla **wdrożeń**, szczególnie w środowisku, które można skalować w wielu węzłach. Prawdopodobnie zamierzonego wdrożeń spójne wersja składników. Jeśli ponownym uruchomieniu kontenera lub koordynatora skaluje większej liczby wystąpień hostów przypadkowo nie będzie ściągać nowszej wersji, niezgodne z innych węzłów.

Po prostu unikatowy znakowanie oznacza, że każdy obraz wypchnięty do rejestru ma unikatowych tagów. Tagi nie są używane ponownie. Istnieje kilka wzorców, które można wykonać w celu generowania unikatowych tagów, w tym:

* **Sygnatura daty i godziny** — ta metoda jest dość typowy, ponieważ wyraźnie poinformuj, kiedy obraz, który został skompilowany. Jednak jak korelować je z powrotem do systemu kompilacji? Czy masz znajdowanie kompilacji, które zostało zakończone, w tym samym czasie? Strefy czasowej bierzesz udział? Wszystkie systemy kompilacji są kalibrowane na czas UTC?
* **Zatwierdzenie Git** — ta metoda działa, dopóki nie zostanie uruchomiony, obsługa aktualizacji obrazu podstawowego. W przypadku aktualizacji obrazów podstawowych systemu kompilacji dotyczącego z tym samym zatwierdzeniu Git jako poprzednia kompilacja. Jednak obraz podstawowy ma nową zawartość. Ogólnie rzecz biorąc, zapewnia się zatwierdzenia Git *o częściowej lub*-czasu trwania stanu stabilnego tagu.
* **Podsumowanie manifestu** — każdy obraz kontenera, wypychany do rejestru kontenera jest skojarzony z manifestem określona przez unikatowy skrót SHA-256, lub szyfrowane. Unikatowa, skrótu jest długa, trudne do odczytania i nieskorelowane za pomocą środowiska kompilacji.
* **Identyfikator kompilacji** — ta opcja może być najlepszym, ponieważ prawdopodobnie przyrostowe, i umożliwia skorelowanych specyficznej kompilacji, aby znaleźć wszelkie artefakty i dzienniki. Jednak takie jak manifestu digest, może być trudne dla ludzi do odczytu.

  Jeśli Twoja organizacja ma kilka systemów kompilacji, tworzenie prefiksu tagu o nazwie system kompilacji jest odmianą na temat tej opcji: `<build-system>-<build-id>`. Na przykład kompilacji można odróżnić od systemu kompilacji usługi Jenkins zespołu interfejsu API i systemu kompilacji zespół sieci web Azure potoków.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowe omówienie koncepcji, w tym artykule, zobacz wpis w blogu [znakowanie platformy Docker: Najlepsze rozwiązania dotyczące obrazów platformy docker przechowywanie wersji i tagowania](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Aby pomóc zmaksymalizować wydajność i oszczędność kosztów podczas korzystania z usługi Azure container registry, zobacz [najlepsze rozwiązania dotyczące usługi Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

