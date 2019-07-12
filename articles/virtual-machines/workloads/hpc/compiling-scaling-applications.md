---
title: Skalowanie aplikacji HPC — w usłudze Azure Virtual Machines | Dokumentacja firmy Microsoft
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707835"
---
# <a name="scaling-hpc-applications"></a>Skalowanie aplikacji HPC

Optymalne skalowanie w pionie i poziomie wydajności aplikacji HPC na platformie Azure wymaga dostrajania wydajności i optymalizacji experiments dla określonego obciążenia. W tej sekcji i stron specyficzne dla serii maszyny Wirtualnej oferują ogólne wskazówki dotyczące skalowania swoich aplikacji.

## <a name="compiling-applications"></a>Kompilowanie aplikacji

Chociaż nie jest to konieczne, Kompilowanie aplikacji za pomocą flagi optymalizacji odpowiednie zapewnia najlepszą wydajność skalowania w górę na HB i maszyny wirtualne z serii połączenia Hybrydowego.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optymalizacja C /C++ kompilatora

AMD Optymalizacja C /C++ systemu kompilatora kompilatora (AOCC) zapewnia wysoki poziom zaawansowanej optymalizacji, wielowątkowości, i obsługę procesora, która zawiera optymalizacje globalne, wektoryzacji, analiz między procedurami, przekształcenia pętli i Generowanie kodu. Pliki binarne kompilatora AOCC nadają się dla systemów Linux, posiadające GNU biblioteki języka C (glibc) w wersji 2.17 lub nowszej. Pakiet kompilatora składa się z C /C++ kompilatora (clang), kompilator Fortran (FLANG) i Fortran frontonu do Clang (sceny Dragon Egg).

### <a name="clang"></a>Clang

Clang jest C C++i kompilator języka Objective-C, obsługa wstępne przetwarzanie, analizowanie, optymalizacji, generowania kodu, zestaw i łączenie. Clang obsługuje `-march=znver1` flagami, aby umożliwić najlepiej generowanie kodu i dostrajanie Zen firmy AMD podstawie x86 architektury.

### <a name="flang"></a>FLANG

Kompilator FLANG została ostatnio dodana do zestawu AOCC (dodany kwietnia 2018 r.) i jest obecnie dostępna w wersji wstępnej dla deweloperów pobrać i testowania. Oparte na Fortran 2008, AMD rozszerza wersji GitHub FLANG (https://github.com/flangcompiler/flang). Kompilator FLANG obsługuje wszystkie opcje kompilatora Clang i dodatkowe liczba FLANG opcje kompilatora.

### <a name="dragonegg"></a>DragonEgg

DragonEgg jest dodatek gcc, który zastępuje optymalizatory GCC firmy i generatorów kodu z tymi z projektu maszyny wirtualnej niskiego poziomu. DragonEgg dostarczanego z AOCC współpracuje z gcc 4.8.x, został przetestowany dla celów x86-32/x86-64 i pomyślnie zastosowano na różnych platformach systemu Linux.

GFortran jest rzeczywista frontonu dla programów Fortran odpowiedzialny za wstępnego przetwarzania, analizy i analizy semantycznej, wygenerowanie reprezentacji pośredniej GCC GIMPLE (IR). DragonEgg jest wtyczki GNU, podłączyć do przepływu kompilacji GFortran. Implementuje wtyczki GNU interfejsu API. Dzięki architekturze wtyczki DragonEgg staje się sterownik kompilatora zachęcanie poszczególne etapy kompilacji.  Po wykonaniu instrukcji pobierania i instalacji, Egg sceny Dragon może być wywoływany przy użyciu: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI Compiler
PGI Community Edition ver. Aby pracować z AMD EPYC potwierdza 17. Wersja kompilacji PGI strumienia dostarczanie przepustowości całej pamięci platformy. 18.10 nowszej wersji Community (listopada 2018 r.) również powinny działać prawidłowo. Poniżej przedstawiono przykład interfejsu wiersza polecenia kompilatora optymalnie za pomocą kompilatora Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilator firmy Intel
Wersja kompilatora firmy Intel Aby pracować z AMD EPYC potwierdza 18. Poniżej przedstawiono przykład interfejsu wiersza polecenia kompilatora optymalnie za pomocą kompilatora Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC Compiler 
W przypadku HPC AMD zaleca kompilatora GCC 7.3 lub nowszą. Nie zaleca się starszej wersji, takich jak 4.8.5 dołączone do systemu RHEL/CentOS 7.4. GCC 7.3 i nowszych, będzie dostarczać znacznie wyższej wydajności HPL HPCG i DGEMM testów.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skalowanie aplikacji 

Poniższe sugestie dotyczą aplikacji optymalne skalowanie efektywności, wydajności i spójności:

* Numer PIN przetwarza rdzeni 0-59 za pomocą sekwencyjne przypinanie podejście (w przeciwieństwie do podejścia saldo automatycznie). 
* Powiązanie przez Numa/Core/HwThread jest lepsza niż domyślne powiązania.
* Hybrydowe aplikacje równoległe (OpenMP + MPI) można użyć w wątki 4 i 1 ranga MPI na CCX.
* W przypadku aplikacji MPI czystego eksperymentować z 1 – 4, który MPI szereguje na CCX w celu uzyskania optymalnej wydajności.
* Niektóre aplikacje dzięki wyjątkowej czułości przepustowość pamięci może korzystać z ograniczoną liczbę rdzeni na CCX. W przypadku tych aplikacji za pomocą 3 lub 2 rdzeni CCX może zmniejszyć rywalizację przepustowość pamięci i uzyskanie większej wydajności rzeczywistych i bardziej spójną skalowalności. W szczególności MPI Allreduce mogą korzystać z tego.
* Znacznie większe skalowanie uruchomień zalecane jest użyj transportu RC + UD UD lub hybrydowe. Wiele bibliotek bibliotek/środowiska uruchomieniowego MPI w tym wewnętrznie (na przykład UCX lub MVAPICH2). Sprawdź konfiguracje transportu do uruchamiania na dużą skalę.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
