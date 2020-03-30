---
title: Instalowanie pamięci podręcznej HPC platformy Azure
description: Jak połączyć klientów z usługą Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582209"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej HPC platformy Azure

Po utworzeniu pamięci podręcznej klienci NFS mogą uzyskać do niej dostęp za pomocą prostego polecenia instalacji.

Polecenie mount składa się z dwóch elementów:

* Jeden z adresów instalacji pamięci podręcznej (wymieniony na stronie przeglądu pamięci podręcznej)
* Ścieżka wirtualnego obszaru nazw ustawiona podczas tworzenia obiektu docelowego magazynu

![zrzut ekranu przedstawiający stronę Przegląd wystąpienia usługi Azure HPC Cache z polem wyróżnienia wokół listy adresów instalacji w prawym dolnym przycisku](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Adresy instalacji pamięci podręcznej odpowiadają interfejsom sieciowym wewnątrz podsieci pamięci podręcznej. W grupie zasobów te karty sieciowe są `-cluster-nic-` wyświetlane z nazwami kończącymi się i liczbą. Nie należy zmieniać ani usuwać tych interfejsów, ponieważ pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnego obszaru nazw są wyświetlane na stronie **Obiekty docelowe magazynu.** Kliknij nazwę obiektu docelowego magazynu, aby wyświetlić jej szczegóły, w tym skojarzone z nią zagregowane ścieżki obszaru nazw.

![zrzut ekranu panelu docelowego Magazyn pamięci podręcznej z polem podświetlenia wokół wpisu w kolumnie Ścieżka tabeli](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Składnia polecenia Montowanie

Użyj polecenia mount, takiego jak następujące:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

Przykład:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Po pomyślnym powiódł się to polecenie, zawartość eksportu ``hpccache`` magazynu powinna być widoczna w katalogu na kliencie.

> [!NOTE] 
> Klienci muszą mieć dostęp do sieci wirtualnej i podsieci, w której znajduje się pamięć podręczna. Na przykład utwórz maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyj punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej, aby uzyskać dostęp z zewnątrz. Należy pamiętać, że nic innego nie może być hostowane wewnątrz podsieci pamięci podręcznej.

### <a name="mount-command-options"></a>Opcje polecenia Montuj

W przypadku niezawodnego montażu klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Zalecane ustawienia polecenia montowania | |
--- | --- 
``hard`` | Miękkie instalacje do pamięci podręcznej HPC usługi Azure są skojarzone z awariami aplikacji i możliwością utraty danych. 
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych dla operacji instalacji.
``retry=n`` | Ustaw, ``retry=30`` aby uniknąć przejściowych błędów instalacji. (W instalacjach pierwszego planu zalecana jest inna wartość).

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do obiektów docelowych magazynu pamięci podręcznej, przeczytaj Artykuł [Wypełnianie nowego magazynu obiektów Blob platformy Azure](hpc-cache-ingest.md).
