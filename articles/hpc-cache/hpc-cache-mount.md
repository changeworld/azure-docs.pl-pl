---
title: Instalowanie pamięci podręcznej platformy Azure HPC
description: Jak połączyć klientów z usługą Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582209"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej platformy Azure HPC

Po utworzeniu pamięci podręcznej klienci systemu plików NFS mogą uzyskać do niej dostęp za pomocą prostego polecenia instalacji.

Polecenie instalacji składa się z dwóch elementów:

* Jeden z adresów pamięci podręcznej (na liście na stronie Przegląd pamięci podręcznej)
* Ścieżka wirtualnej przestrzeni nazw ustawiana podczas tworzenia miejsca docelowego magazynu

![zrzut ekranu strony Przegląd wystąpienia pamięci podręcznej platformy Azure HPC z wyróżnionym polem wyboru na liście adresy instalacji w prawym dolnym rogu](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Adresy instalacji pamięci podręcznej są zgodne z interfejsami sieciowymi w podsieci pamięci podręcznej. W grupie zasobów te karty sieciowe są wyświetlane z nazwami kończącymi się na `-cluster-nic-` i liczbą. Nie zmieniaj ani nie usuwaj tych interfejsów lub pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnych przestrzeni nazw są wyświetlane na stronie **miejsce docelowe magazynu** . Kliknij nazwę obiektu docelowego magazynu, aby wyświetlić jego szczegóły, w tym zagregowane ścieżki przestrzeni nazw, z którymi są skojarzone.

![zrzut ekranu przedstawiający panel docelowy magazynu pamięci podręcznej z ramką wyróżnienia wokół wpisu w kolumnie ścieżka tabeli.](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Składnia polecenia instalacji

Użyj polecenia instalacji w następujący sposób:

> sudo zainstaluj *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Przykład:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Po pomyślnym wykonaniu tego polecenia zawartość eksportu magazynu powinna być widoczna w katalogu ``hpccache`` na kliencie.

> [!NOTE] 
> Klienci muszą mieć dostęp do sieci wirtualnej i podsieci, która znajduje się w pamięci podręcznej. Można na przykład utworzyć maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyć punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej w celu uzyskania dostępu spoza zewnątrz. Należy pamiętać, że żadne inne elementy nie mogą być hostowane w podsieci pamięci podręcznej.

### <a name="mount-command-options"></a>Opcje polecenia instalacji

W przypadku niezawodnego instalowania klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Zalecane ustawienia poleceń instalacji | |
--- | --- 
``hard`` | Instalacje miękkie do pamięci podręcznej platformy Azure HPC są skojarzone z niepowodzeńmi aplikacji i możliwymi utratą danych. 
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30``, aby uniknąć błędów instalacji przejściowej. (W instalacjach na pierwszym planie zalecana jest inna wartość).

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do miejsc docelowych magazynu pamięci podręcznej, przeczytaj temat [Wypełnij nową usługę Azure Blob Storage](hpc-cache-ingest.md).
