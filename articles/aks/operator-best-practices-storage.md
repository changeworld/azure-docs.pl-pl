---
title: Operator najlepsze rozwiązania — magazynu w usłudze Azure Kubernetes usługi (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące magazynowania, szyfrowanie danych i kopie zapasowe w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: a58a42f65472a9c4b495e0cb964eefa40bf82041
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649623"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i zarządzania klastrami w usłudze Azure Kubernetes Service (AKS), aplikacje często muszą magazynu. Należy do zrozumienia potrzeb związanych z wydajnością i dostęp do metody do zasobników, tak, aby można było zapewnić odpowiedni magazyn dla aplikacji. Rozmiar węzłów AKS może mieć wpływ na te opcje magazynu. Należy również zaplanować, aby poznać sposoby wykonywania kopii zapasowych i przetestuj proces przywracania do dołączonego magazynu.

Najlepsze rozwiązania dotyczące tej koncentruje się na zagadnienia dotyczące magazynu dla operatorów klastra. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Jakie typy magazynu są dostępne
> * Jak poprawnie rozmiar węzłów AKS pod kątem wydajności magazynu
> * Różnice między statycznych i dynamicznych Inicjowanie obsługi woluminów
> * Sposoby tworzenia kopii zapasowej i zabezpieczyć swoje woluminy danych

## <a name="choose-the-appropriate-storage-type"></a>Wybierz typ odpowiedniego magazynu

**Wskazówki dotyczące najlepszych** rozwiązań — zapoznaj się z potrzebami aplikacji, aby wybrać odpowiedni magazyn. Użyj magazyn o wysokiej wydajności, opartych na dyskach SSD dla obciążeń produkcyjnych. Planowanie magazynu opartego na sieci, gdy istnieje potrzeba wiele równoczesnych połączeń.

Aplikacje często wymagają różnych typów i szybkość magazynu. Czy aplikacje mogą mieć magazynu, który łączy się indywidualne zasobników lub współużytkowane przez wielu zasobnikach? Jest magazynu, aby uzyskać dostęp tylko do odczytu do danych lub do zapisania dużych ilości danych strukturalnych? Te magazynu należy określić najbardziej odpowiedni typ magazynu do użycia.

W poniższej tabeli przedstawiono typy dostępnego magazynu i ich funkcji:

| Przypadek użycia | Wtyczka woluminu | Odczyt/zapis raz | Wiele tylko do odczytu | Odczyt/zapis wielu | Obsługa kontenerów systemu Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Konfiguracja udostępniona       | Azure Files   | Yes | Yes | Yes | Yes |
| Dane ze strukturą aplikacji        | Azure Disks   | Yes | Nie  | Nie  | Yes |
| Dane bez określonej struktury, operacje systemu plików | [BlobFuse][blobfuse] | Yes | Yes | Yes | Nie |

Dwa podstawowe typy magazynu określone woluminy w usłudze AKS są wspierane przez dyski platformy Azure lub usługi Azure Files. Aby zwiększyć bezpieczeństwo, oba typy magazynu, należy użyć szyfrowanie usługi Storage (SSE) Azure domyślnie, który szyfruje dane magazynowane. Obecnie nie można zaszyfrować dyski za pomocą usługi Azure Disk Encryption na poziomie węzłów AKS.

Usługa pliki systemu Azure są obecnie dostępne w warstwie standardowa wydajności. Dyski platformy Azure są dostępne w warstwach wydajności Standard i Premium:

- Dyski w *warstwie Premium* są obsługiwane przez dyski półprzewodnikowe o wysokiej wydajności (dysków SSD). Dyski w warstwie Premium są zalecane w przypadku wszystkich obciążeń produkcyjnych.
- Dyski *standardowe* są obsługiwane przez regularne wirowanie dysków (HDD) i są dobre dla archiwizowania lub rzadko używanych danych.

Zrozumienie potrzeb dotyczących wydajności aplikacji i wzorców wyboru warstwy magazynowania odpowiedniego dostępu. Aby uzyskać więcej informacji na temat rozmiarów Managed Disks i warstw wydajności, zobacz [Omówienie usługi Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tworzenie i używanie klas magazynu do definiowania aplikacji potrzebom

Używany typ magazynu jest definiowany przy użyciu *klas magazynu*Kubernetes. Klasa magazynu następnie jest przywoływany w specyfikacji zasobnika lub wdrożenia. Te definicje współpracują w celu tworzenia odpowiedniego magazynu i podłącz go do zasobników. Aby uzyskać więcej informacji, zobacz [klasy magazynu w AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Rozmiar węzłów potrzeb dotyczących magazynowania

**Wskazówki dotyczące najlepszych** rozwiązań — każdy rozmiar węzła obsługuje maksymalną liczbę dysków. Rozmiary innego węzła udostępniają różne ilości lokalnego przepustowości sieci i magazynu. Planowanie wymagań aplikacji wdrożyć odpowiedni rozmiar węzłów.

Węzłów AKS są uruchamiane jako maszyny wirtualne platformy Azure. Dostępne są różne typy i rozmiary maszyn wirtualnych. Rozmiar każdej maszyny Wirtualnej zapewnia różne ilości podstawowych zasobów, takich jak procesor CPU i pamięci. Te rozmiary maszyn wirtualnych mają maksymalna liczba dysków, które mogą być dołączone. Wydajność magazynu również są różne rozmiary maszyn wirtualnych dla maksymalnej lokalne i dołączone dysku, operacje We/Wy (operacje wejścia/wyjścia na sekundę).

Jeśli aplikacje wymagają usługi Azure Disks jako rozwiązanie do magazynowania, planowanie i wybierz węzeł odpowiedniego rozmiaru maszyny Wirtualnej. Ilość czasu Procesora i pamięci nie jest jedynym czynnikiem w przypadku wybrania rozmiaru maszyny Wirtualnej. Możliwości magazynu również są ważne. Na przykład rozmiary maszyn wirtualnych *Standard_B2ms* i *Standard_DS2_v2* obejmują podobną ilość zasobów procesora CPU i pamięci. Wydajność magazynu, ich potencjalne różni się, jak pokazano w poniższej tabeli:

| Typ węzła i rozmiar | Procesor wirtualny | Pamięć (GiB) | Maks. liczba dysków danych | Maksymalna liczba bez buforowania dysku operacje We/Wy | Maksymalna przepływność bez buforowania (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standardowa_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

W tym miejscu *Standard_DS2_v2* umożliwia podwójnej liczby dołączonych dysków i oferuje trzy do cztery razy większą liczbę operacji we/wy na sekundę. Jeśli oglądasz tylko podstawowe zasoby obliczeniowe i porównane koszty, możesz wybrać *Standard_B2ms* rozmiar maszyny wirtualnej i mieć niską wydajność i ograniczenia magazynu. Praca z zespołem deweloperów aplikacji, aby zrozumieć ich potrzeb pojemność i wydajność magazynu. Wybierz odpowiedni rozmiar maszyny Wirtualnej dla węzłów AKS spełniają lub przekraczają najbardziej ich potrzeb dotyczących wydajności. Regularnie linii bazowej aplikacje, aby dostosować rozmiar maszyny Wirtualnej, zgodnie z potrzebami.

Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamicznie zainicjowania woluminów

**Wskazówki dotyczące najlepszych** rozwiązań — aby zmniejszyć koszty związane z zarządzaniem i umożliwić skalowanie, nie można statycznie tworzyć i przypisywać woluminów trwałych. Za pomocą dynamicznego inicjowania obsługi administracyjnej. W Twoich zajęciach magazynu należy zdefiniować odzyskać odpowiednie zasady, aby zminimalizować niepotrzebne koszty usuniętego zasobników.

Gdy zachodzi potrzeba Dołącz magazyn do zasobników, należy użyć woluminy trwałe. Te woluminy trwałe mogą być tworzone ręcznie lub dynamicznie. Ręczne tworzenie trwałych woluminów dodaje narzutu związanego z zarządzaniem i ogranicza możliwość skalowania. Użyj dynamicznych trwały wolumin inicjowania obsługi administracyjnej, aby uprościć zarządzanie magazynem i pozwala aplikacjom na rozwój i skalowanie zgodnie z potrzebami.

![Trwały wolumin oświadczeń w klastrze usługi Kubernetes usługi Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Oświadczenie trwały wolumin (PVC) umożliwia dynamiczne tworzenie magazynu zgodnie z potrzebami. Odpowiednie dyski platformy Azure są tworzone zgodnie z ich zażądać zasobników. W definicji zasobnika żądania woluminu na utworzone i dołączone do ścieżki instalacji zaprojektowane

Aby uzyskać informacje na temat sposobu dynamicznego tworzenia i używania woluminów, zobacz [oświadczenia dotyczące woluminów trwałych][aks-concepts-storage-pvcs].

Aby wyświetlić te woluminy, zobacz sposób dynamicznego tworzenia i używania woluminu trwałego z dyskami lub [Azure Files][dynamic-files] [platformy Azure][dynamic-disks] .

W ramach definicji klasy magazynu Ustaw odpowiednie *reclaimPolicy*. Ten reclaimPolicy steruje zachowaniem bazowego zasobu usługi Azure storage, gdy Zasobnik zostanie usunięty i trwały wolumin przestaną być potrzebne. Bazowego zasobu magazynu można usunąć lub przechowywane do użytku z programem przyszłych zasobników. ReclaimPolicy można ustawić, aby *zachować* lub *usunąć*. Zrozumienie wymagań aplikacji i zaimplementować regularnych kontroli magazynu, które są przechowywane, aby zminimalizować ilość miejsca w magazynie nie jest używany, która jest używana i rozliczane.

Aby uzyskać więcej informacji o opcjach klasy magazynu, zobacz [zasady odzyskiwania magazynu][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpieczanie i kopii zapasowej danych

**Wskazówki dotyczące najlepszych** rozwiązań — tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero lub Azure Site Recovery. Sprawdź integralność i bezpieczeństwo tych kopii zapasowych.

Gdy Twoje aplikacje przechowują i zużywać dane utrwalone na dyskach lub w plikach, należy wykonać, regularnie Twórz kopie zapasowe lub migawki danych. Dyski platformy Azure można użyć technologii wbudowanych migawek. Przed wykonaniem operacji migawki może być konieczne wyszukanie aplikacji w celu opróżnienia zapisu na dysku. [Velero][velero] może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. Jeśli nie możesz [usunąć stanu z aplikacji][remove-state], Utwórz kopię zapasową danych z woluminów trwałych i regularnie Przetestuj operacje przywracania, aby zweryfikować integralność danych i wymagane procesy.

Omówienie ograniczeń różne podejścia do tworzenia kopii zapasowych danych i jeśli musisz przełączyć w stan spoczynku dane przed migawki. Kopie zapasowe danych nie zawsze pozwalają na przywrócenie środowiska aplikacji, wdrażania klastra. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w programie AKS][best-practices-multi-region].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na magazyn najlepsze rozwiązania w usłudze AKS. Aby uzyskać więcej informacji na temat podstawy magazynu w programie Kubernetes, zobacz [pojęcia dotyczące magazynu dla aplikacji w AKS][aks-concepts-storage].

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
