---
title: Instalowanie pamięci podręcznej platformy Azure HPC
description: Jak połączyć klientów z usługą Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: v-erkell
ms.openlocfilehash: 46f221fd7c340b7f321d317f0e7493448d83177c
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036957"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej platformy Azure HPC

Po utworzeniu pamięci podręcznej klienci systemu plików NFS mogą uzyskać do niej dostęp za pomocą prostego polecenia instalacji.

Użyj adresów instalacji wymienionych na stronie Przegląd pamięci podręcznej i ścieżki wirtualnego obszaru nazw ustawionych podczas tworzenia miejsca docelowego magazynu. 

![zrzut ekranu strony Przegląd wystąpienia pamięci podręcznej platformy Azure HPC z wyróżnionym polem wyboru na liście adresy instalacji w prawym dolnym rogu](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Adresy instalacji pamięci podręcznej są zgodne z interfejsami sieciowymi w podsieci pamięci podręcznej. Te karty sieciowe są wyświetlane w grupie zasobów z nazwami `-cluster-nic-` kończącymi się na i numerami. Nie zmieniaj ani nie usuwaj tych interfejsów lub pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnych przestrzeni nazw są wyświetlane na stronie **miejsce docelowe magazynu** . Kliknij nazwę docelową, aby wyświetlić szczegóły, w tym zagregowaną ścieżkę przestrzeni nazw (lub ścieżki), z którą jest skojarzona. 
![zrzut ekranu przedstawiający panel docelowy magazynu pamięci podręcznej z ramką wyróżnienia wokół wpisu w kolumnie ścieżka tabeli.](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Składnia polecenia instalacji

Użyj polecenia instalacji w następujący sposób:

> sudo zainstaluj *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Przykład: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

Po pomyślnym wykonaniu tego polecenia zawartość eksportu magazynu powinna być widoczna w ``hpccache`` katalogu na kliencie. 

> [!NOTE] 
> Klienci muszą mieć dostęp do sieci wirtualnej i podsieci, która znajduje się w pamięci podręcznej. Można na przykład utworzyć maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyć punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej w celu uzyskania dostępu spoza zewnątrz. Należy pamiętać, że żadne inne elementy nie mogą być hostowane w podsieci pamięci podręcznej.

### <a name="mount-command-options"></a>Opcje polecenia instalacji

W przypadku niezawodnego instalowania klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Zalecane ustawienia poleceń instalacji | |
--- | --- 
``hard`` | Instalacje miękkie do klastra vFXT są skojarzone z awariami aplikacji i możliwymi utratą danych. 
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30`` , aby uniknąć błędów instalacji przejściowej. (W instalacjach na pierwszym planie zalecana jest inna wartość).

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do miejsc docelowych magazynu pamięci podręcznej, przeczytaj temat [Wypełnij nową usługę Azure Blob Storage](hpc-cache-ingest.md).
