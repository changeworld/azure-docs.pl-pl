---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/10/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: e9f88a018ba0f40d41bf7ad17a74850a9edf8ae6
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043857"
---
Maszyny Wirtualnej zoptymalizowane pod kątem procesora GPU z jednym lub wieloma procesorami GPU firmy NVIDIA wyspecjalizowane maszyny wirtualne o rozmiarach. Te rozmiary są przeznaczone dla obciążeń intensywnie korzystających z obliczeń, bogatych w elementy graficzne i wizualizacji. Ten artykuł zawiera informacje o liczbie i rodzaju procesorów GPU, procesorów wirtualnych, dysków z danymi i kart sieciowych. Przepustowość przepływności i sieć magazynu dostępne są również dla każdego rozmiaru w tej metodzie grupowania. 

* **NC, NCv2, NCv3 i ND** rozmiarów są zoptymalizowane dla aplikacji z mocy obliczeniowej i intensywnie korzystających z sieci i algorytmów. Niektóre przykłady są CUDA i OpenCL oparte na aplikacje i symulacje, sztucznej Inteligencji i uczenia głębokiego. Seria NCv3 koncentruje się na obciążeń obliczeniowych o wysokiej wydajności, wyposażone w procesor GPU firmy NVIDIA Tesla V100.  Seria ND koncentruje na scenariuszach dotyczących szkolenia i wnioskowania na potrzeby uczenia szczegółowego. W tym przypadku jest używany procesor GPU NVIDIA Tesla P40.
* **NV i NVv2** rozmiarów są zoptymalizowane pod kątem i przeznaczone dla zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania i scenariuszy VDI przy użyciu platform, takich jak OpenGL i DirectX.  Te maszyny wirtualne są wspierane przez procesorów GPU Tesla M60 firmy NVIDIA.


## <a name="nc-series"></a>Seria NC

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

Maszyny wirtualne z serii NC są obsługiwane przez [procesory GPU NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty. Użytkownicy mogą przetwarzanie danych szybciej dzięki wykorzystaniu architektury CUDA dla aplikacji eksploracji energii, symulacji awarii, ray śledzenia renderowania, uczenie głębokie i inne. Konfigurację NC24r zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.


| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standardowa_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standardowa_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standardowa_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA

## <a name="ncv2-series"></a>Seria NCv2

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Maszyny wirtualne z serii NCv2 są obsługiwane przez [procesory GPU NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) procesorów GPU. Te procesory GPU oferują ponad 2 razy większą wydajność w porównaniu z serii NC. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Konfiguracja v2 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2 * |24 |448 | 2948 | 4 | 32 | 8 |

1 procesor GPU = jedną kartę P100.

*Obsługa technologii RDMA

## <a name="ncv3-series"></a>Seria NCv3

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Maszyny wirtualne z serii NCv3 są obsługiwane przez [procesory GPU NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) procesorów GPU. Te procesory GPU oferują ponad 1,5 raza większą wydajność seria NCv2. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Konfiguracja v3 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 32 | 8 |

1 procesor GPU = jedną kartę V100.

*Obsługa technologii RDMA

## <a name="nd-series"></a>Seria ND

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Maszyny wirtualne serii ND to nowy dodatek do rodziny procesorów GPU zaprojektowana dla obciążeń sztucznej Inteligencji i uczenia głębokiego. Oferują doskonałą wydajność na potrzeby szkoleń i wnioskowania. ND wystąpienia są obsługiwane przez [procesory GPU NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) procesorów GPU. Te wystąpienia oferują doskonałą wydajność dla pojedynczej precyzji operacje zmiennoprzecinkowe, w przypadku obciążeń sztucznej Inteligencji przy użyciu zestawu narzędzi Microsoft Cognitive Toolkit, TensorFlow, Caffe i innych platform. Seria ND oferuje także dużo pojemniejszą pamięć procesora GPU (24 GB) mieszczącą znacznie większe modele sieci neuronowych. Podobnie jak seria NC, seria ND oferuje konfigurację z pomocniczych sieci o małych opóźnieniach i wysokiej przepływności przez funkcję RDMA oraz łączność InfiniBand, dzięki czemu można uruchamiać zadania szkoleń o dużej skali obejmujące wiele procesorów GPU.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych na region w ramach subskrypcji początkowo jest równa 0. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 32 | 8 |

1 procesor GPU = jedną kartę P40.

*Obsługa technologii RDMA

## <a name="nv-series"></a>Seria NV

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

Maszyn wirtualnych z serii NV są obsługiwane przez [procesory GPU NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologii dla pulpitu przyspieszonych aplikacji komputerowych i pulpitów wirtualnych gdzie klienci będą mogli wizualizować swoje dane lub symulacje. Użytkownicy będą mogli wizualizować swoje grafiki przepływy pracy na wystąpieniach NV, aby uzyskać doskonałe możliwości grafiki oraz dodatkowo uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. 

Każdy procesor GPU w wystąpieniach NV jest dostarczany z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standardowa_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standardowa_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

## <a name="nvv2-series-preview"></a>Seria NVv2 (wersja zapoznawcza)

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Maszyn wirtualnych z serii NVv2 są obsługiwane przez [procesory GPU NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologię Intel Broadwell procesorów. Te maszyny wirtualne są przeznaczone dla procesora GPU accelerated aplikacji graficznych i pulpitów wirtualnych, których klienci chcą wizualizować swoje dane, symulowanie wyników, aby wyświetlić, pracować nad CAD lub renderowania i dane ze strumienia zawartości. Dodatkowo te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne NVv2 obsługuje usługi Premium Storage i dołączone dwa razy pamięć systemową (RAM) w porównaniu z jego poprzednika, seria NV.  

Każdy procesor GPU w NVv2 wystąpienia jest powiązana z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 8 | 4 | 100 |

1 procesor GPU = połowa karty M60.

 
