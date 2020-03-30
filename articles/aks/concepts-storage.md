---
title: Pojęcia — magazyn w usługach Azure Kubernetes (AKS)
description: Dowiedz się więcej o magazynie w usłudze Azure Kubernetes Service (AKS), w tym woluminach, woluminach trwałych, klasach magazynu i oświadczeniach
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595998"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje magazynu dla aplikacji w usłudze Azure Kubernetes Service (AKS)

Aplikacje uruchamiane w usłudze Azure Kubernetes Service (AKS) mogą wymagać przechowywania i pobierania danych. W przypadku niektórych obciążeń aplikacji ten magazyn danych może używać lokalnego, szybkiego magazynu w węźle, który nie jest już potrzebny po usunięciu zasobników. Inne obciążenia aplikacji może wymagać magazynu, który utrzymuje się na więcej regularnych woluminów danych w ramach platformy Azure platformy. Wiele zasobników może być konieczne współużytkowanie tych samych woluminów danych lub ponowne dołączanie woluminów danych, jeśli zasobnik jest przełożony w innym węźle. Na koniec może być konieczne wstrzyknięcie poufnych danych lub informacji o konfiguracji aplikacji do zasobników.

![Opcje magazynu dla aplikacji w klastrze usług Kubernetes platformy Azure (AKS)](media/concepts-storage/aks-storage-options.png)

W tym artykule przedstawiono podstawowe pojęcia, które zapewniają magazyn do aplikacji w uzywki AKS:

- [Woluminów](#volumes)
- [Trwałe woluminy](#persistent-volumes)
- [Klasy magazynu](#storage-classes)
- [Trwałe woluminy — oświadczenia](#persistent-volume-claims)

## <a name="volumes"></a>Woluminy

Aplikacje często muszą mieć możliwość przechowywania i pobierania danych. Jak Kubernetes zazwyczaj traktuje poszczególnych zasobników jako efemeryczne, jednorazowe zasoby, różne podejścia są dostępne dla aplikacji do użycia i utrwalania danych w razie potrzeby. *Wolumin* reprezentuje sposób przechowywania, pobierania i utrwalania danych w zasobnikach i za pośrednictwem cyklu życia aplikacji.

Tradycyjne woluminy do przechowywania i pobierania danych są tworzone jako zasoby kubernetes wspierane przez usługę Azure Storage. Można ręcznie utworzyć te woluminy danych, które mają być przypisane bezpośrednio do zasobników lub kubernetes automatycznie je utworzyć. Te woluminy danych można użyć dysków platformy Azure lub usługi Azure Files:

- *Usługi Azure Disks* może służyć do tworzenia zasobu Kubernetes *DataDisk.* Dyski mogą korzystać z usługi Azure Premium Storage, wspieranej przez dyski SSD o wysokiej wydajności lub usługi Azure Standard storage, wspierane przez zwykłe dyski twarde. W przypadku większości obciążeń produkcyjnych i deweloperskich należy użyć magazynu w wersji Premium. Dyski platformy Azure są montowane jako *ReadWriteOnce*, więc są dostępne tylko dla pojedynczego zasobnika. W przypadku woluminów magazynu, do których można uzyskać dostęp przez wiele zasobników jednocześnie, należy użyć usługi Azure Files.
- *Usługa Azure Files* może służyć do instalowania udziału SMB 3.0 wspieranego przez konto usługi Azure Storage w zasobnikach. Pliki umożliwiają udostępnianie danych w wielu węzłach i zasobnikach. Pliki mogą korzystać z usługi Azure Standard storage wspierane przez zwykłe dyski twarde lub usługi Azure Premium storage, wspierane przez dyski SSD o wysokiej wydajności.
> [!NOTE] 
> Usługa Azure Files obsługuje magazyn w wersji premium w klastrach AKS, w których są uruchamiane sieci Kubernetes 1.13 lub nowsze.

W umięsień woluminy mogą reprezentować więcej niż tylko tradycyjny dysk, na którym można przechowywać i pobierać informacje. Woluminy Kubernetes może być również używany jako sposób wstrzyknąć dane do zasobnika do użycia przez kontenery. Typowe dodatkowe typy woluminów w uliczce Kubernetes obejmują:

- *emptyDir* — ten wolumin jest powszechnie używany jako tymczasowe miejsce dla zasobnika. Wszystkie kontenery w zasobniku mogą uzyskać dostęp do danych na woluminie. Dane zapisane w tym typie woluminu utrzymują się tylko przez cały okres ważności zasobnika — po usunięciu zasobnika wolumin jest usuwany. Ten wolumin zazwyczaj używa magazynu dysku węzła lokalnego, chociaż może również istnieć tylko w pamięci węzła.
- *tajny* — ten wolumin służy do wstrzykiwania poufnych danych do zasobników, takich jak hasła. Najpierw utwórz klucz tajny przy użyciu interfejsu API kubernetes. Podczas definiowania zasobnika lub wdrożenia można zażądać określonego klucza tajnego. Wpisy tajne są dostarczane tylko do węzłów, które mają zaplanowane zasobnik, który tego wymaga, a klucz tajny jest przechowywany w *tmpfs*, nie jest zapisywany na dysku. Po usunięciu ostatniego zasobnika w węźle, który wymaga klucza tajnego, klucz tajny jest usuwany z tmpfs węzła. Wpisy tajne są przechowywane w danym obszarze nazw i są dostępne tylko przez zasobników w tym samym obszarze nazw.
- *configMap* — ten typ woluminu służy do wstrzykiwania właściwości pary klucz-wartość do zasobników, takich jak informacje o konfiguracji aplikacji. Zamiast definiować informacje o konfiguracji aplikacji w obrazie kontenera, można zdefiniować go jako zasób Kubernetes, który można łatwo zaktualizować i zastosować do nowych wystąpień zasobników podczas ich wdrażania. Podobnie jak przy użyciu klucza tajnego, najpierw utworzyć ConfigMap przy użyciu interfejsu API Kubernetes. Ten ConfigMap można następnie zażądać podczas definiowania zasobnika lub wdrożenia. ConfigMaps są przechowywane w danym obszarze nazw i mogą być dostępne tylko przez zasobników w tym samym obszarze nazw.

## <a name="persistent-volumes"></a>Trwałe woluminy

Woluminy, które są zdefiniowane i utworzone jako część cyklu życia zasobnika istnieją tylko do momentu usunięcia zasobnika. Zasobników często oczekują, że ich magazyn pozostanie, jeśli zasobnik jest przełożony na innym hoście podczas zdarzenia konserwacji, zwłaszcza w StatefulSets. *Wolumin trwały* (PV) to zasób magazynu utworzony i zarządzany przez interfejs API usługi Kubernetes, który może istnieć poza okresem istnienia pojedynczej zasobu.

Dyski azure lub pliki są używane do zapewnienia PersistentVolume. Jak wspomniano w poprzedniej sekcji na woluminy, wybór dysków lub plików jest często określana przez potrzebę równoczesnego dostępu do danych lub warstwy wydajności.

![Woluminy trwałe w klastrze usług Kubernetes platformy Azure](media/concepts-storage/persistent-volumes.png)

PersistentVolume może być *statycznie* tworzone przez administratora klastra lub *dynamicznie* tworzone przez serwer interfejsu API Kubernetes. Jeśli zasobnik jest zaplanowane i żąda magazynu, który nie jest obecnie dostępny, Kubernetes można utworzyć podstawowej platformy Azure Disk lub files storage i dołączyć go do zasobnika. Dynamiczne inicjowanie obsługi administracyjnej używa *StorageClass* do określenia, jaki typ magazynu platformy Azure musi zostać utworzony.

## <a name="storage-classes"></a>Klasy magazynu

Aby zdefiniować różne warstwy magazynu, takie jak Premium i Standard, można utworzyć *pamięć StorageClass*. Klasa storageclass definiuje również *reclaimPolicy*. To reclaimPolicy kontroluje zachowanie źródłowego zasobu magazynu platformy Azure, gdy zasobnik jest usuwany i wolumin trwały może nie być już wymagane. Podstawowy zasób magazynu można usunąć lub zachować do użycia z przyszłą zasobem zasobu.

W Uzywu AKS tworzone są dwie początkowe klasy magazynu:

- *domyślnie* — używa magazynu Azure Standard do utworzenia dysku zarządzanego. Zasady odzyskiwania wskazują, że podstawowa usługa Azure Disk jest usuwana po usunięciu woluminu trwałego, który go użył.
- *managed-premium* — używa magazynu w usłudze Azure Premium do tworzenia dysku zarządzanego. Zasady odzyskiwania ponownie wskazuje, że podstawowa usługa Azure Disk jest usuwany, gdy wolumin trwały, który go użył, zostanie usunięty.

Jeśli dla woluminu trwałego nie określono klasy storageclass, używana jest domyślna klasa storageclass. Należy uważać podczas żądania woluminów trwałych, tak aby korzystały z odpowiedniego magazynu, którego potrzebujesz. Można utworzyć StorageClass dla dodatkowych `kubectl`potrzeb za pomocą programu . W poniższym przykładzie użyto dysków zarządzanych w wersji Premium i określono, że podstawowa platforma Azure Disk powinna zostać *zachowana* po usunięciu zasobnika:

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

## <a name="persistent-volume-claims"></a>Trwałe woluminy — oświadczenia

PersistentVolumeClaim żąda dysku lub magazynu plików określonej klasy pamięci magazynu, trybu dostępu i rozmiaru. Serwer interfejsu API usługi Kubernetes może dynamicznie aprowizować podstawowy zasób magazynu na platformie Azure, jeśli nie ma istniejącego zasobu do spełnienia oświadczenia opartego na zdefiniowanej klasie magazynu. Definicja zasobnika obejmuje mocowanie woluminu po podłączeniu woluminu do zasobnika.

![Oświadczenia woluminów trwałych w klastrze usług Kubernetes platformy Azure](media/concepts-storage/persistent-volume-claims.png)

PersistentVolume jest *powiązany z* PersistentVolumeClaim po zasób dostępnego magazynu został przypisany do zasobu żądającego go. Istnieje mapowanie 1:1 woluminów trwałych do oświadczeń.

Poniższy przykładowy manifest YAML pokazuje trwałe oświadczenie woluminu, które używa klasy storageclass *w klasie zarządzanej w wersji premium* i żąda rozmiaru dysku *5Gi:*

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

Podczas tworzenia definicji zasobnika, trwałe oświadczenie woluminu jest określony, aby zażądać żądanego magazynu. Następnie należy również określić *woluminMount* dla aplikacji do odczytu i zapisu danych. Poniższy przykładowy manifest YAML pokazuje, jak poprzednie oświadczenie woluminu trwałego może służyć do instalowania woluminu na */mnt/azure:*

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

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

Aby zobaczyć, jak utworzyć woluminy dynamiczne i statyczne korzystające z dysków platformy Azure lub plików platformy Azure, zobacz następujące artykuły inicjacyjne:

- [Tworzenie woluminu statycznego przy użyciu dysków platformy Azure][aks-static-disks]
- [Tworzenie woluminu statycznego przy użyciu usługi Azure Files][aks-static-files]
- [Tworzenie woluminu dynamicznego przy użyciu dysków platformy Azure][aks-dynamic-disks]
- [Tworzenie woluminu dynamicznego przy użyciu usługi Azure Files][aks-dynamic-files]

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes / AKS][aks-concepts-clusters-workloads]
- [Kubernetes / AKS tożsamości][aks-concepts-identity]
- [Zabezpieczenia Kubernetes / AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes / AKS][aks-concepts-network]
- [Skala Kubernetes / AKS][aks-concepts-scale]

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
