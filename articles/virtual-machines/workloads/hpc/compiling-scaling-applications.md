---
title: Skalowanie aplikacji HPC — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skalować aplikacje HPC na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707835"
---
# <a name="scaling-hpc-applications"></a>Skalowanie aplikacji HPC

Optymalna wydajność skalowania w górę i w poziomie aplikacji HPC na platformie Azure wymaga dostrajania wydajności i optymalizacji eksperymentów dla określonego obciążenia. W tej sekcji i strony specyficzne dla serii maszyn wirtualnych oferują ogólne wskazówki dotyczące skalowania aplikacji.

## <a name="compiling-applications"></a>Kompilowanie aplikacji

Chociaż nie jest to konieczne, kompilowanie aplikacji z odpowiednimi flagami optymalizacji zapewnia najlepszą wydajność skalowania w górę na maszynach wirtualnych z serii HB i HC.

### <a name="amd-optimizing-cc-compiler"></a>Kompilator AMD optymalizujący C/C++

System kompilatora kompilatora AOCC (AOCC) optymalizacji AMD optymalizujący system AOCC oferuje wysoki poziom zaawansowanych optymalizacji, obsługi wielu wątków i procesorów, która obejmuje globalną optymalizację, wektoryzację, analizy między proceduralne, przekształcenia pętli i generowanie kodu. Pliki binarne kompilatora AOCC są odpowiednie dla systemów Linux o bibliotece GNU C (glibc) w wersji 2.17 lub nowszej. Zestaw kompilatora składa się z kompilatora C/C++(clang), kompilatora Fortran (FLANG) i frontonu Fortran do Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang to kompilator C, C++ i Objective-C obsługujący przetwarzanie wstępne, analizowanie, optymalizację, generowanie kodu, zestaw i łączenie. Clang obsługuje `-march=znver1` flagę, aby umożliwić najlepszą generację kodu i strojenie dla architektury x86 opartej na Zen firmy AMD.

### <a name="flang"></a>KOŁNIERZ

Kompilator FLANG jest najnowszym dodatkiem do pakietu AOCC (dodany kwiecień 2018) i jest obecnie w wersji wstępnej dla programistów do pobrania i przetestowania. W oparciu o Fortran 2008, AMD rozszerza wersjęhttps://github.com/flangcompiler/flang)GitHub FLANG ( . Kompilator FLANG obsługuje wszystkie opcje kompilatora Clang i dodatkową liczbę opcji kompilatora specyficzne dla FLANG.

### <a name="dragonegg"></a>Dragonegg ( Dragonegg )

DragonEgg to wtyczka gcc, która zastępuje optymalizatory GCC i generatory kodu tymi z projektu LLVM. DragonEgg, który jest wyposażony w AOCC współpracuje z gcc-4.8.x, został przetestowany dla celów x86-32/x86-64 i został z powodzeniem stosowany na różnych platformach Linux.

GFortran jest rzeczywistą frontend dla programów Fortran odpowiedzialnych za wstępne przetwarzanie, analizowanie i analizę semantyczną generującą reprezentację pośrednią GCC GIMPLE (IR). DragonEgg to wtyczka GNU, wchodząca w przepływ kompilacji GFortran. Implementuje api wtyczki GNU. Dzięki architekturze wtyczki DragonEgg staje się sterownikiem kompilatora, napędzając różne fazy kompilacji.  Po zastosowaniu instrukcji pobierania i instalacji, Dragon Egg można wywołać za pomocą: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Kompilator CHOG
WYDANIE WSPÓLNOTY CHOG ver. 17 jest potwierdzona do współpracy z AMD EPYC. Skompilowana przez PGI wersja STREAM zapewnia pełną przepustowość pamięci platformy. Nowsza edycja społecznościowa 18.10 (listopad 2018) również powinna działać dobrze. Poniżej znajduje się przykładowy wiersz polecenia do kompilatora optymalnie z Intel Compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilator Intela
Intel Compiler ver. 18 jest potwierdzona do współpracy z AMD EPYC. Poniżej znajduje się przykładowy wiersz polecenia do kompilatora optymalnie z Kompilatorem Intela.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilator GCC 
W przypadku HPC firma AMD zaleca kompilator GCC 7.3 lub nowszy. Starsze wersje, takie jak 4.8.5 dołączone do RHEL/CentOS 7.4, nie są zalecane. GCC 7.3 i nowsze zapewni znacznie wyższą wydajność w testach HPL, HPCG i DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skalowanie aplikacji 

Następujące sugestie dotyczą optymalnej wydajności skalowania aplikacji, wydajności i spójności:

* Przypnij procesy do rdzeni 0-59 przy użyciu sekwencyjnego podejścia przypinającego (w przeciwieństwie do podejścia automatycznego równoważenia). 
* Powiązanie przez Numa/Core/HwThread jest lepsze niż domyślne powiązanie.
* W przypadku hybrydowych aplikacji równoległych (OpenMP+MPI) użyj 4 wątków i 1 mpi na CCX.
* W przypadku czystych aplikacji MPI, eksperymentuj z 1-4 mpi szeregi na CCX dla optymalnej wydajności.
* Niektóre aplikacje o ekstremalnej wrażliwości na przepustowość pamięci mogą korzystać z mniejszej liczby rdzeni na CCX. W przypadku tych aplikacji użycie 3 lub 2 rdzeni na ccx może zmniejszyć rywalizację o przepustowość pamięci i uzyskać wyższą wydajność w świecie rzeczywistym lub bardziej spójną skalowalność. W szczególności MPI Allreduce może z tego skorzystać.
* W przypadku znacznie większych przebiegów zaleca się stosowanie ud lub hybrydowych transportów RC + UD. Wiele bibliotek MPI/bibliotek runtime to zrobić wewnętrznie (na przykład UCX lub MVAPICH2). Sprawdź konfiguracje transportu dla dużych przebiegów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
