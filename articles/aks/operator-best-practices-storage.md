---
title: Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych
titleSuffix: Azure Kubernetes Service
description: Poznaj najważniejsze wskazówki dotyczące obsługi operatora klastra dotyczące magazynu, szyfrowania danych i tworzenia kopii zapasowych w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 843b775f7761af7cd40140c9bf34768d63eb5a50
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877902"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące magazynu i tworzenia kopii zapasowych w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) aplikacje często wymagają magazynu. Ważne jest, aby zrozumieć potrzeby wydajności i metody dostępu dla zasobników, dzięki czemu można zapewnić odpowiednią pamięć masową dla aplikacji. Rozmiar węzła usługi AKS może mieć wpływ na te opcje magazynu. Należy również zaplanować sposoby utworzenia kopii zapasowej i przetestowania procesu przywracania dołączonego magazynu.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na zagadnieniach dotyczących magazynu dla operatorów klastrów. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Jakie rodzaje pamięci masowej są dostępne
> * Jak poprawnie rozmiar węzłów AKS dla wydajności magazynu
> * Różnice między dynamicznym i statycznym inicjowania obsługi administracyjnej woluminów
> * Sposoby zabezpieczania i zabezpieczania woluminów danych

## <a name="choose-the-appropriate-storage-type"></a>Wybierz odpowiedni typ pamięci masowej

**Wskazówki dotyczące najlepszych praktyk** — zrozumienie potrzeb aplikacji, aby wybrać odpowiedni magazyn. Użyj wysokiej wydajności magazynu z kopii SSD dla obciążeń produkcyjnych. Planowanie magazynu sieciowego, gdy istnieje potrzeba wielu równoczesnych połączeń.

Aplikacje często wymagają różnych typów i szybkości przechowywania. Czy aplikacje potrzebują miejsca do magazynowania, który łączy się z poszczególnymi zasobnikami lub jest współużytkowane przez wiele zasobników? Czy magazyn dla dostępu tylko do odczytu do danych lub do zapisu dużych ilości danych strukturalnych? Te potrzeby magazynowania określają najbardziej odpowiedni typ magazynu do użycia.

W poniższej tabeli przedstawiono dostępne typy magazynu i ich możliwości:

| Przypadek użycia | Wtyczka głośności | Odczyt/zapis raz | Wiele tylko do odczytu | Odczyt/zapis wielu | Obsługa kontenerów systemu Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Konfiguracja współdzielona       | Azure Files   | Tak | Tak | Tak | Tak |
| Dane aplikacji strukturalnej        | Azure Disks   | Tak | Nie  | Nie  | Tak |
| Dane nieustrukturyzowane, operacje systemu plików | [BlobFuse (Wrzsie na kłębie)][blobfuse] | Tak | Tak | Tak | Nie |

Dwa podstawowe typy magazynu dostępne dla woluminów w usłudze AKS są wspierane przez dyski azure lub usługi Azure Files. Aby zwiększyć bezpieczeństwo, oba typy magazynu domyślnie używają szyfrowania usługi Azure Storage Service Encryption (SSE), które szyfruje dane w spoczynku. Dysków nie można obecnie szyfrować przy użyciu szyfrowania dysków platformy Azure na poziomie węzła AKS.

Pliki azure i dyski platformy Azure są dostępne w warstwach wydajności standard i Premium:

- Dyski *premium* są wspierane przez wysokowydajne dyski PÓŁPRZEWODNIKOWE (SSD). Dyski premium są zalecane dla wszystkich obciążeń produkcyjnych.
- *Dyski standardowe* są wspierane przez zwykłe dyski wirujące (HDD) i są dobre dla archiwalnych lub rzadko dostępnych danych.

Poznaj potrzeby dotyczące wydajności aplikacji i wzorce dostępu, aby wybrać odpowiednią warstwę magazynu. Aby uzyskać więcej informacji na temat rozmiarów dysków zarządzanych i warstw wydajności, zobacz [Omówienie dysków zarządzanych platformy Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tworzenie i używanie klas magazynu do definiowania potrzeb aplikacji

Typ używanego magazynu jest definiowany przy użyciu *klas magazynu*Kubernetes . Do klasy magazynu odwołuje się następnie w specyfikacji zasobnika lub wdrożenia. Definicje te współpracują ze sobą, aby utworzyć odpowiedni magazyn i podłączyć go do zasobników. Aby uzyskać więcej informacji, zobacz [Klasy magazynu w aks][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Rozmiar węzłów do potrzeb magazynu

**Wskazówki dotyczące najlepszych praktyk** — każdy rozmiar węzła obsługuje maksymalną liczbę dysków. Różne rozmiary węzłów zapewniają również różne ilości lokalnej pamięci masowej i przepustowości sieci. Zaplanuj wymagania aplikacji, aby wdrożyć odpowiedni rozmiar węzłów.

Węzły usługi AKS działają jako maszyny wirtualne platformy Azure. Dostępne są różne typy i rozmiary maszyny Wirtualnej. Każdy rozmiar maszyny Wirtualnej zapewnia inną ilość podstawowych zasobów, takich jak procesor i pamięć. Te rozmiary maszyn wirtualnych mają maksymalną liczbę dysków, które mogą być dołączone. Wydajność magazynu różni się również między rozmiarami maszyn wirtualnych dla maksymalnej liczby operacji We/Wy na dysku lokalnym i podłączonych (operacje wejścia/wyjścia na sekundę).

Jeśli aplikacje wymagają usługi Azure Disks jako rozwiązania magazynu, zaplanuj i wybierz odpowiedni rozmiar maszyny Wirtualnej węzła. Ilość procesora CPU i pamięci nie jest jedynym czynnikiem przy wyborze rozmiaru maszyny Wirtualnej. Funkcje pamięci masowej są również ważne. Na przykład rozmiary maszyn wirtualnych *Standard_B2ms* i *Standard_DS2_v2* zawierają podobną ilość zasobów procesora CPU i pamięci. Ich potencjalna wydajność pamięci masowej jest inna, jak pokazano w poniższej tabeli:

| Typ i rozmiar węzła | Procesor wirtualny | Pamięć (GiB) | Maks. liczba dysków danych | Maksymalna liczba we/wy na dysku niezakazyowanym | Maksymalna przepływność bez buforów (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standardowa_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

W tym miejscu *Standard_DS2_v2* umożliwia podwojenie liczby dołączonych dysków i zapewnia trzy do czterech razy więcej czasu i przepustowości dysku. Jeśli przyjrzano się tylko podstawowym zasobom obliczeniowym i porównanym kosztom, możesz wybrać *Standard_B2ms* rozmiar maszyny Wirtualnej i mieć niską wydajność i ograniczenia magazynu. Współpracuj z zespołem deweloperów aplikacji, aby zrozumieć ich pojemność i wydajność potrzeb. Wybierz odpowiedni rozmiar maszyny Wirtualnej dla węzłów AKS, aby spełnić lub przekroczyć ich potrzeby w zakresie wydajności. Regularnie linii bazowej aplikacji, aby dostosować rozmiar maszyny Wirtualnej w razie potrzeby.

Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamiczne inicjowania obsługi administracyjnej woluminów

**Wskazówki dotyczące najlepszych rozwiązań** — aby zmniejszyć obciążenie związane z zarządzaniem i umożliwić skalowanie, nie statycznie tworzyć i przypisywać woluminów trwałych. Użyj dynamicznego inicjowania obsługi administracyjnej. W klasach magazynu zdefiniuj odpowiednie zasady odzyskiwania, aby zminimalizować niepotrzebne koszty magazynowania po usunięciu zasobników.

Gdy trzeba dołączyć magazyn do zasobników, należy użyć woluminów trwałych. Te woluminy trwałe można tworzyć ręcznie lub dynamicznie. Ręczne tworzenie woluminów trwałych zwiększa obciążenie związane z zarządzaniem i ogranicza możliwość skalowania. Użyj dynamicznej obsługi administracyjnej woluminów trwałych, aby uprościć zarządzanie pamięcią masową i umożliwić aplikacjom powiększanie i skalowanie w razie potrzeby.

![Oświadczenia woluminów trwałych w klastrze usług Kubernetes platformy Azure](media/concepts-storage/persistent-volume-claims.png)

Trwałe oświadczenie woluminu (PVC) umożliwia dynamiczne tworzenie magazynu w razie potrzeby. Podstawowe dyski platformy Azure są tworzone jako zasobników żądania ich. W definicji zasobnika żądasz utworzenia woluminu i dołączenia go do wyznaczonej ścieżki instalacji.

Aby zapoznać się z pojęciami dotyczącymi dynamicznego tworzenia i używania woluminów, zobacz [Oświadczenia woluminów trwałych][aks-concepts-storage-pvcs].

Aby wyświetlić te woluminy w akcji, zobacz, jak dynamicznie tworzyć i używać woluminu trwałego za pomocą [dysków platformy Azure][dynamic-disks] lub [plików Azure .][dynamic-files]

W ramach definicji klas magazynu ustaw odpowiednie *reclaimPolicy*. To reclaimPolicy kontroluje zachowanie źródłowego zasobu magazynu platformy Azure, gdy zasobnik jest usuwany i wolumin trwały może nie być już wymagane. Podstawowy zasób magazynu można usunąć lub zachować do użycia z przyszłą zasobem zasobu. ReclaimPolicy można ustawić, aby *zachować* lub *usunąć*. Zrozumienie potrzeb aplikacji i zaimplementowanie regularnych kontroli magazynu, który jest zachowywany, aby zminimalizować ilość nieużytego magazynu, który jest używany i rozliczane.

Aby uzyskać więcej informacji na temat opcji klasy magazynu, zobacz [zasady odzyskiwania magazynu][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpieczanie i zabezpieczanie danych

**Wskazówki dotyczące najlepszych rozwiązań** — tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla danego typu magazynu, takiego jak Velero lub Azure Site Recovery. Sprawdź integralność i bezpieczeństwo tych kopii zapasowych.

Gdy aplikacje przechowują i zużywają dane utrwalone na dyskach lub w plikach, należy wykonać regularne kopie zapasowe lub migawki tych danych. Usługi Azure Disks można użyć wbudowanych technologii migawek. Przed wykonaniem operacji migawki może być konieczne wyszukanie aplikacji do opróżnienia zapisów na dysku. [Velero][velero] może odtwarzać zapasy trwałych woluminów wraz z dodatkowymi zasobami i konfiguracjami klastra. Jeśli nie można [usunąć stanu z aplikacji,][remove-state]kopię zapasową danych z woluminów trwałych i regularnie testuj operacje przywracania, aby zweryfikować integralność danych i wymagane procesy.

Zrozumienie ograniczeń różnych podejść do tworzenia kopii zapasowych danych i jeśli musisz quiesce danych przed migawką. Kopie zapasowe danych nie muszą umożliwiać przywracania środowiska aplikacji wdrażania klastra. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [Najważniejsze wskazówki dotyczące ciągłości działania i odzyskiwania po awarii w udręki AKS][best-practices-multi-region].

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na najlepszych rozwiązaniach dotyczących magazynu w układzie AKS. Aby uzyskać więcej informacji na temat podstaw magazynu w umiań, zobacz [Pojęcia dotyczące magazynu dla aplikacji w uzywniu usługi AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
