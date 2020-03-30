---
title: Konfigurowanie interfejsu przekazywania wiadomości dla hpc — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować mpi dla HPC na platformie Azure.
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
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023994"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurowanie interfejsu przekazywania wiadomości dla HPC

Obciążenia interfejsu MPI (Message Passing Interface) stanowią znaczną część tradycyjnych obciążeń HPC. Rozmiary maszyn wirtualnych SR-IOV włączone na platformie Azure umożliwiają użycie niemal dowolnego smaku interfejsu MPI. 

Uruchamianie zadań MPI na maszynach wirtualnych wymaga skonfigurowania kluczy partycji (kluczy p) w dzierżawie. Wykonaj kroki opisane w sekcji [Odkryj klucze partycji, aby](#discover-partition-keys) uzyskać szczegółowe informacje na temat określania wartości klucza p.

## <a name="ucx"></a>Ucx ( UCX )

[UCX](https://github.com/openucx/ucx) oferuje najlepszą wydajność na IB i współpracuje z MPICH i OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI ( OpenMPI )

Zainstaluj UCX zgodnie z wcześniejszym opisem.

```bash
sudo yum install –y openmpi
```

Zbuduj OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Uruchom OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="mpich"></a>MPICH (mpich)

Zainstaluj UCX zgodnie z wcześniejszym opisem.

Tworzenie MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Uruchamianie MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="mvapich2"></a>MVAPICH2

Zbuduj MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Uruchamianie MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platforma MPI Community Edition

Zainstaluj wymagane pakiety dla platformy MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postępuj zgodnie z procesem instalacji.

## <a name="intel-mpi"></a>Intel MPI

[Pobierz Intel MPI](https://software.intel.com/mpi-library/choose-download).

Zmień I_MPI_FABRICS zmienną środowiskową w zależności od wersji. W przypadku intel mpi 2018, użyj `I_MPI_FABRICS=shm:ofa` i `I_MPI_FABRICS=shm:ofi`na rok 2019, użyj .

Przypinanie procesu działa poprawnie dla 15, 30 i 60 PPN domyślnie.

## <a name="osu-mpi-benchmarks"></a>Testy porównawcze MPI OSU

[Pobierz OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) i untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Tworzenie testów porównawczych przy użyciu określonej biblioteki MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Testy porównawcze MPI znajdują się w folderze. `mpi/`


## <a name="discover-partition-keys"></a>Odnajdowanie kluczy partycji

Odnajdowanie kluczy partycji (kluczy p) do komunikowania się z innymi maszynami wirtualnymi w ramach tej samej dzierżawy (zestaw dostępności lub zestaw skalowania maszyny Wirtualnej).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Większy z dwóch jest klucz dzierżawy, który powinien być używany z MPI. Przykład: Jeśli są następujące klawisze p, 0x800b powinny być używane z MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Użyj partycji innej niż domyślny (0x7fff) klucz partycji. UCX wymaga, aby msb klucza p być wyczyszczone. Na przykład ustaw UCX_IB_PKEY jako 0x000b dla 0x800b.

Należy również zauważyć, że tak długo, jak dzierżawy (AVSet lub VMSS) istnieje, PKEYs pozostają takie same. Jest to prawdą nawet wtedy, gdy węzły są dodawane/usuwane. Nowi najemcy otrzymują różne PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurowanie limitów dla użytkowników mpi

Konfigurowanie limitów dla użytkowników mpi.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurowanie klawiszy SSH dla mpi

Skonfiguruj klucze SSH dla typów MPI, które tego wymagają.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Powyższa składnia zakłada, że udostępniony katalog macierzysty, w przeciwnym razie katalog .ssh musi zostać skopiowany do każdego węzła.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
