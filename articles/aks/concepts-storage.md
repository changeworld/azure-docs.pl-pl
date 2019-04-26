---
title: Pojęcia — usługach magazynu w usłudze Azure Kubernetes (AKS)
description: Informacje na temat magazynowania w usłudze Azure Kubernetes Service (AKS), w tym woluminy, trwały, klasy magazynów i oświadczenia
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 03/01/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: cce38eb12d803c0640d9ee774dbc6c98ab5db219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466821"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje magazynu dla aplikacji w usłudze Azure Kubernetes Service (AKS)

Aplikacje, które są uruchamiane w usłudze Azure Kubernetes Service (AKS) może być konieczne przechowywanie i pobieranie danych. Dla niektórych obciążeń aplikacji ten magazyn danych można użyć magazynu lokalnego, szybkie na węzeł, który nie jest już potrzebny, gdy zasobników zostaną usunięte. Inne obciążenia aplikacji może wymagać magazynu, który będzie nadal występował w bardziej regularnych danych woluminów na platformie Azure. Wielu zasobnikach może być konieczne udostępnianie tej samej ilości danych lub ponownie dołączyć ilości danych, jeśli zmiany harmonogramu zasobnik w innym węźle. Ponadto może być konieczne wstrzyknąć dane poufne lub informacje o konfiguracji aplikacji do zasobników.

![Opcje magazynu dla aplikacji w klastrze usługi Kubernetes usługi Azure (AKS)](media/concepts-storage/aks-storage-options.png)

W tym artykule przedstawiono podstawowe pojęcia, które zapewniają magazynu do aplikacji w usłudze AKS:

- [Woluminy](#volumes)
- [Woluminy trwałe](#persistent-volumes)
- [Klasy magazynu](#storage-classes)
- [Oświadczenia trwały wolumin](#persistent-volume-claims)

## <a name="volumes"></a>Woluminy

Aplikacje często muszą mieć możliwość przechowywania i pobierania danych. Zgodnie z rozwiązania Kubernetes zwykle traktuje poszczególnych zasobników jako zasoby tymczasowych, możliwe do rozporządzania, różne podejścia są dostępne dla aplikacji do użycia i utrwalić dane zgodnie z potrzebami. A *woluminu* przedstawia sposób przechowywanie, pobieranie i utrwalanie danych zasobników i w całym cyklu życia aplikacji.

Tradycyjne woluminy do przechowywania i pobierania danych są tworzone jako zasoby platformy Kubernetes, wspierane przez usługi Azure Storage. Można ręcznie utworzyć te woluminy można przypisać bezpośrednio do zasobników lub mieć Kubernetes utworzyć je automatycznie. Te woluminy danych przy użyciu usługi Azure Disks lub usługi Azure Files:

- *Usługa Azure Disks* może służyć do tworzenia rozwiązania Kubernetes *DataDisk* zasobów. Dyski można użyć usługi Azure Premium storage obsługiwane przez dyski SSD o wysokiej wydajności, lub usługi Azure Standard storage, wspierane przez regularne dysków twardych. Dla większości celów produkcyjnych i obciążeń deweloperskich Użyj usługi Premium storage. Dyski platformy Azure są instalowane jako *ReadWriteOnce*, dlatego są dostępne tylko do jednego węzła. Woluminy magazynu, które mogą być udostępniane jednocześnie przez wiele węzłów można użyć w usłudze Azure Files.
- *Usługa Azure Files* może służyć do instalowania udziału SMB 3.0 wspierany przez konto usługi Azure Storage do zasobników. Pliki umożliwiają udostępnianie danych w wielu węzłach i zasobników. Obecnie plików można używać tylko magazyn Standard platformy Azure przez regularne dysków twardych.

W usłudze Kubernetes woluminów może reprezentować dysku tradycyjnych więcej niż tylko gdzie można przechowywane i pobierane informacje. Woluminy Kubernetes może także służyć jako sposób, aby wstawić dane do zasobników do użytku przez kontenery. Typowe typy dodatkowe woluminu w usłudze Kubernetes:

- *emptyDir* -tego woluminu jest najczęściej używana jako tymczasowego miejsca w zasobniku. Wszystkie kontenery w zasobniku można uzyskać dostęp do danych na woluminie. Dane zapisane na ten typ woluminu tylko będzie się powtarzać dla cyklem życia zasobnika — po usunięciu zasobnik wolumin zostanie usunięty. Ten wolumin zazwyczaj używa magazynu dysku lokalnego węzła usługi, jednak mogą istnieć tylko w pamięci węzła.
- *klucz tajny* -tego woluminu jest używana iniekcję poufne dane do zasobników, takie jak hasła. Należy najpierw utworzyć wpis tajny przy użyciu interfejsu API rozwiązania Kubernetes. Podczas definiowania wdrożenia lub zasobników, można żądać określonego klucza tajnego. Klucze tajne są udostępniane tylko węzły, które mają zaplanowane zasobnik, który go wymaga i klucz tajny jest przechowywany w *tmpfs*robaków napisanych nie na dysku. Po usunięciu ostatniego zasobnik w węźle, który wymaga klucza tajnego klucza tajnego został usunięty z tmpfs węzła. Klucze tajne są przechowywane w ramach danego obszaru nazw i może zostać oceniony jedynie przez zasobników w tej samej przestrzeni nazw.
- *configMap* — ten typ woluminu jest używany iniekcję właściwości pary klucz wartość do zasobników, takie jak informacje o konfiguracji aplikacji. Zamiast definiować informacje o konfiguracji aplikacji w postaci obrazu kontenera, można zdefiniować ją jako zasób usługi Kubernetes, który można łatwo zaktualizować i stosowane do nowych wystąpień zasobników, ponieważ są one wdrażane. Podobnie jak za pomocą klucza tajnego, należy najpierw utworzyć ConfigMap, przy użyciu interfejsu API rozwiązania Kubernetes. Następnie można żądać tej ConfigMap, podczas definiowania zasobników lub wdrożenia. ConfigMaps są przechowywane w ramach danego obszaru nazw i może zostać oceniony jedynie przez zasobników w tej samej przestrzeni nazw.

## <a name="persistent-volumes"></a>Woluminy trwałe

Woluminy, które są zdefiniowane i utworzonego w ramach cyklu życia pod istnieje tylko do momentu usunięcia zasobnik. Zasobników często oczekiwać, że pozostaje jeśli Zasobnik jest ponownie zaplanować na inny host, podczas zdarzenia konserwacji, szczególnie w przypadku StatefulSets ich przechowywania. A *trwały wolumin* (PV) jest tworzony i zarządzany przez interfejs API rozwiązania Kubernetes, który może znajdować się poza okres istnienia poszczególnych zasobnika zasobu magazynu.

Azure Disks lub pliki są używane do zapewnienia PersistentVolume. Jak wspomniano w poprzedniej sekcji na woluminach, wybór dysków lub pliki często jest określany przez potrzebę równoczesnego dostępu do danych lub warstwy wydajności.

![Woluminy trwałe w klastrze usługi Kubernetes usługi Azure (AKS)](media/concepts-storage/persistent-volumes.png)

Może być PersistentVolume *statycznie* utworzone przez administratora klastra lub *dynamicznie* utworzonych przez serwer interfejsu API rozwiązania Kubernetes. Jeśli zasobnik jest zaplanowane, żądań magazynu, który nie jest obecnie dostępna Kubernetes można Tworzenie magazynu usługi Azure Disk lub plików i dołączyć go do zasobnika ustawiany. Dynamiczne Inicjowanie obsługi administracyjnej używa *StorageClass* do identyfikowania, jakiego rodzaju magazynu platformy Azure, musi zostać utworzona.

## <a name="storage-classes"></a>Klasy magazynu

Aby zdefiniować różne warstwy magazynu, na przykład Premium i standardowa, można utworzyć *StorageClass*. Definiuje również StorageClass *reclaimPolicy*. Ten reclaimPolicy steruje zachowaniem bazowego zasobu usługi Azure storage, gdy Zasobnik zostanie usunięty i trwały wolumin przestaną być potrzebne. Bazowego zasobu magazynu można usunąć lub przechowywane do użytku z programem przyszłych zasobników.

W usłudze AKS tworzone są dwa początkowe StorageClasses:

- *domyślne* -korzysta z usługi Azure Standard storage do utworzenia dysku zarządzanego. Zasady odzyskać wskazuje, że podstawowy dysk platformy Azure zostanie usunięta po usunięciu zasobnik, który jej.
- *Managed premium* -korzysta z usługi Azure Premium storage do utworzenia dysku zarządzanego. Zasady odzyskać ponownie wskazuje, że podstawowy dysk platformy Azure zostanie usunięta po usunięciu zasobnik, który jej.

Jeśli nie StorageClass jest określona dla trwały wolumin, używana jest domyślna StorageClass. Zachowaj ostrożność przy żądanie woluminy trwałe, tak aby używały odpowiednie magazynu, których potrzebujesz. Możesz utworzyć StorageClass dla dodatkowych wymagań przy użyciu `kubectl`. Poniższy przykład używa dysków Premium Managed Disks i określa, czy podstawowy dysk Azure powinien być *przechowywane* gdy Zasobnik zostanie usunięty:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Oświadczenia trwały wolumin

PersistentVolumeClaim żądań magazynu dysku lub pliku określonego StorageClass, tryb dostępu i rozmiar. Jeśli nie istniejącego zasobu do spełnienia oświadczenie oparte na zdefiniowanych StorageClass serwera interfejsu API rozwiązania Kubernetes dynamicznie aprowizować bazowego zasobu magazynu na platformie Azure. Definicji pod obejmuje instalacji woluminu, gdy wolumin jest połączony zasobnik.

![Trwały wolumin oświadczeń w klastrze usługi Kubernetes usługi Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Jest PersistentVolume *powiązany* do PersistentVolumeClaim po przypisaniu zasobu dostępnego magazynu, do zasobnika żądanie. Istnieje mapowanie 1:1 trwałego woluminów do oświadczeń.

Następujące manifest YAML przykład przedstawia oświadczenia trwały wolumin, który używa *managed premium* StorageClass i żąda dysku *5Gi* rozmiar:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Podczas tworzenia definicji zasobnika oświadczenia trwały wolumin określono, aby zażądać potrzebnych magazynów. Można także następnie określić *volumeMount* dla aplikacji na odczytywanie i zapisywanie danych. Następujące manifest YAML przykład pokazuje, jak poprzednie oświadczenia trwały wolumin można zainstalować woluminu na */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze AKS][operator-best-practices-storage].

Aby zobaczyć, jak można utworzyć woluminów statycznych i dynamicznych, korzystające z usługi Azure Disks lub usługi Azure Files, zobacz następujące artykuły z poradami:

- [Tworzenie statycznej woluminu za pomocą usługi Azure Disks][aks-static-disks]
- [Tworzenie statycznej woluminu za pomocą usługi Azure Files][aks-static-files]
- [Tworzenie woluminu dynamicznego przy użyciu usługi Azure Disks][aks-dynamic-disks]
- [Tworzenie woluminu dynamicznego przy użyciu usługi Azure Files][aks-dynamic-files]

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes / tożsamości AKS][aks-concepts-identity]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md