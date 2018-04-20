---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/30/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 5457ac8bd229889ed2b96354c44066959c00c64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
Procesor GPU zoptymalizowanych pod kątem maszyny Wirtualnej są dostępne z jednego lub wielu procesorów graficznych NVIDIA specjalne maszyn wirtualnych. Rozmiary są przeznaczone dla obciążeń obliczeniowych, dużą ilością grafiki i wizualizacji. Ten artykuł zawiera informacje o liczbę i typ procesorów graficznych, Vcpu, dyski danych i karty sieciowe, jak również przepustowości przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania. 

* **NC, NCv2 NCv3 i ND** rozmiary są optymalizowane dla aplikacji obliczeniowych i obciążenie sieci i algorytmy, w tym aplikacji opartych na CUDA i OpenCL oraz symulacje, AI i uczenie się bezpośrednich. 
* **Trwałym** rozmiary są zoptymalizowane i przeznaczony dla zdalnego wizualizacji, przesyłania strumieniowego, gier, kodowanie i scenariuszy VDI przy użyciu platform, takich jak OpenGL i DirectX.  


## <a name="nc-series"></a>Seria NC

Maszyny wirtualne z serii NC są obsługiwane przez [K80 tesla — NVIDIA](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty. Użytkownicy mogą wykonywać za pomocą danych szybsze dzięki wykorzystaniu CUDA dla aplikacji eksploracji energii, symulacji awarii, ray śledzonych renderowania, głębokie uczenie i inne. Konfiguracja NC24r zapewnia małe opóźnienia wysokiej przepustowości sieciowej zoptymalizowane pod kątem silnie sprzężonego równoległego przetwarzania obciążeń.


| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standardowa_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standardowa_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standardowa_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA

## <a name="ncv2-series"></a>Seria NCv2

Maszyny wirtualne z serii NCv2 są obsługiwane przez [P100 tesla — NVIDIA](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU. Te jednostki GPU zapewniają więcej niż 2 x obliczeniowej NC serii. Klienci mogą wykorzystać te GPU zaktualizowane dla tradycyjnych obciążeń HPC, takich jak modelowania zbiornika, DNS sekwencjonowania białka analizy, Symulacje Monte Carlo i inne. Konfiguracja v2 NC24rs zapewnia małe opóźnienia wysokiej przepustowości sieciowej zoptymalizowane pod kątem silnie sprzężonego równoległego przetwarzania obciążeń.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału vCPU (podstawowe) w subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2 * |24 |448 | 2948 | 4 | 32 | 8 |

Procesor GPU 1 = co karta P100.

*Obsługa technologii RDMA

## <a name="ncv3-series"></a>Seria NCv3

Maszyny wirtualne z serii NCv3 są obsługiwane przez [V100 tesla — NVIDIA](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU. Te jednostki GPU zapewniają 1,5 x obliczeniowej NCv2 serii. Klienci mogą wykorzystać te GPU zaktualizowane dla tradycyjnych obciążeń HPC, takich jak modelowania zbiornika, DNS sekwencjonowania białka analizy, Symulacje Monte Carlo i inne. Konfiguracja v3 NC24rs zapewnia małe opóźnienia wysokiej przepustowości sieciowej zoptymalizowane pod kątem silnie sprzężonego równoległego przetwarzania obciążeń.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału vCPU (podstawowe) w subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 32 | 8 |

Procesor GPU 1 = co karta V100.

*Obsługa technologii RDMA

## <a name="nd-series"></a>Seria ND

Maszyny wirtualne ND serii są nowe uzupełnienie rodziny procesora GPU, przeznaczone dla obciążeń AI i uczenie się bezpośrednich. Oferują one doskonałą wydajność dla szkolenia i wnioskowania. ND wystąpienia są obsługiwane przez [P40 tesla — NVIDIA](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU. Te wystąpienia zapewnić doskonałą wydajność dla pojedynczej precyzji operacje zmiennoprzecinkowe, AI obciążeń przy użyciu kognitywnych zestaw narzędzi firmy Microsoft, TensorFlow Caffe i innych platform. Seria ND oferuje także dużo pojemniejszą pamięć procesora GPU (24 GB) mieszczącą znacznie większe modele sieci neuronowych. Jak NC serii serii ND oferuje konfiguracji dodatkowej małym opóźnieniu i dużej przepustowości sieci przez funkcję RDMA i łączność InfiniBand, więc można uruchamiać zadania na dużą skalę szkolenia obejmujących wiele procesorów graficznych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału vCPU (podstawowe) na region w ramach subskrypcji początkowo jest równa 0. [Zażądać zwiększenia limitu przydziału vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 32 | 8 |

Procesor GPU 1 = co karta P40.

*Obsługa technologii RDMA

## <a name="nv-series"></a>Seria NV

Serii wirtualizacją sieci maszyn wirtualnych są obsługiwane przez [M60 tesla — NVIDIA](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i siatki NVIDIA technologii pulpitu przyspieszony aplikacji i pulpitów wirtualnych gdzie klienci mogą się wizualizacji ich danych lub symulacji. Użytkownicy będą mogli wizualizacji w wystąpieniach wirtualizacją sieci, aby uzyskać możliwość wyższego poziomu grafiki i dodatkowo uruchamiania obciążeń pojedynczej precyzji, takie jak kodowanie i renderowania znacznym przepływy pracy ich grafiki. 

Każdy procesor GPU w Trwałym wystąpień pochodzi z licencją siatki. Ta licencja zapewnia elastyczność być wystąpienia wirtualizacją sieci wirtualnych stacji roboczej dla jednego użytkownika lub 25 równoczesnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standardowa_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standardowa_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

 
