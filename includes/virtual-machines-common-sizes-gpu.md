---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: b7bc040ae799aec98454fb227bbeeb6027f9615a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673448"
---
Maszyny Wirtualnej zoptymalizowane pod kątem procesora GPU z jednym lub wieloma procesorami GPU firmy NVIDIA wyspecjalizowane maszyny wirtualne o rozmiarach. Te rozmiary są przeznaczone dla obciążeń intensywnie korzystających z obliczeń, bogatych w elementy graficzne i wizualizacji. Ten artykuł zawiera informacje o liczbie i rodzaju procesorów GPU, procesorów wirtualnych, dysków z danymi i kart sieciowych. Przepustowość przepływności i sieć magazynu dostępne są również dla każdego rozmiaru w tej metodzie grupowania.

* **NC, NCv2, NCv3** rozmiarów są zoptymalizowane dla aplikacji z mocy obliczeniowej i intensywnie korzystających z sieci i algorytmów. Niektóre przykłady są CUDA i OpenCL oparte na aplikacje i symulacje, sztucznej Inteligencji i uczenia głębokiego. Seria NCv3 koncentruje się na obciążeń obliczeniowych o wysokiej wydajności, wyposażone w procesor GPU firmy NVIDIA Tesla V100. Seria NC używa wersji 3 z 2,60 GHz Intel Xeon E5-2690 v3 (Haswell) z zegarem, a seria NCv2 i maszyny wirtualne z serii NCv3 Użyj v4 Intel Xeon E5-2690 procesora (Broadwell).

* **ND i NDv2** seria ND koncentruje się na scenariuszach szkoleń i wnioskowania na potrzeby uczenia głębokiego. Korzysta ona z procesorów GPU NVIDIA Tesla P40 i v4 Intel Xeon E5-2690 procesora (Broadwell). Seria NDv2 korzysta z procesorów Intel Xeon Platinum 8168 (Skylake).

* **NV i NVv3** rozmiarów są zoptymalizowane pod kątem i przeznaczone dla zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania i scenariuszy VDI przy użyciu platform, takich jak OpenGL i DirectX.  Te maszyny wirtualne są wspierane przez procesorów GPU Tesla M60 firmy NVIDIA.

## <a name="nc-series"></a>Seria NC

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Maszyny wirtualne z serii NC są obsługiwane przez [procesory GPU NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty i 2690 z technologią Intel Xeon E5 v3 (Haswell) z zegarem. Użytkownicy mogą przetwarzanie danych szybciej dzięki wykorzystaniu architektury CUDA dla aplikacji eksploracji energii, symulacji awarii, ray śledzenia renderowania, uczenie głębokie i inne. Konfigurację NC24r zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standardowa_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standardowa_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standardowa_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standardowa_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA

## <a name="ncv2-series"></a>Seria NCv2

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyny wirtualne z serii NCv2 są obsługiwane przez [procesory GPU NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) procesorów GPU. Te procesory GPU oferują ponad 2 razy większą wydajność w porównaniu z serii NC. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Oprócz procesorów GPU, maszyny wirtualne z serii NCv2 są również obsługiwane przez Intel Xeon E5-2690 v4 (broadwell z zegarem) procesorów.

Konfiguracja v2 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2 * | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 procesor GPU = jedną kartę P100.

*Obsługa technologii RDMA

## <a name="ncv3-series"></a>Seria NCv3

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyny wirtualne z serii NCv3 są obsługiwane przez [procesory GPU NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) procesorów GPU. Te procesory GPU oferują ponad 1,5 raza większą wydajność seria NCv2. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Konfiguracja v3 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych. Oprócz procesorów GPU, maszyny wirtualne z serii NCv3 są również obsługiwane przez Intel Xeon E5-2690 v4 (broadwell z zegarem) procesorów.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000 / 200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 procesor GPU = jedną kartę V100.

*Obsługa technologii RDMA

## <a name="ndv2-series-preview"></a>Seria NDv2 (wersja zapoznawcza)

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Infiniband: Nieobsługiwane

Maszyna wirtualna serii NDv2 jest nowy dodatek do rodziny procesorów GPU zaprojektowanej na potrzeby HPC, sztucznej Inteligencji i usługi machine learning obciążeń. Usługa jest obsługiwana przez 8 procesory GPU NVIDIA Tesla V100 NVLINK połączone ze sobą, procesorów GPU i 40 rdzeni Intel Xeon Platinum 8168 (Skylake) i 672 GiB pamięci systemowej. Wystąpienie NDv2 zapewnia świetną wydajność w przypadku precyzji FP32 i FP64 dla obciążeń obliczeń HPC i sztucznej inteligencji korzystających ze struktur Cuda, TensorFlow, Pytorch, Caffe i innych.

[Utwórz konto i Uzyskaj dostęp do tych maszyn w trakcie okresu zapoznawczego](https://aka.ms/ndv2signup).
<br>

| Size | Procesor wirtualny | Procesory GPU | Memory (Pamięć) | Karty sieciowe (maks.) | Temp Storage (SSD) GiB | Maksymalnie z Dyski z danymi | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna przepustowość sieci | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000 / 800 | 24000 MB/s |

## <a name="nd-series"></a>Seria ND

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyny wirtualne serii ND to nowy dodatek do rodziny procesorów GPU zaprojektowana pod kątem sztucznej Inteligencji i uczenia głębokiego obciążeń. Oferują doskonałą wydajność na potrzeby szkoleń i wnioskowania. ND wystąpienia są obsługiwane przez [procesory GPU NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) procesorów GPU i Intel Xeon E5-2690 v4 (broadwell z zegarem) procesorów. Te wystąpienia oferują doskonałą wydajność dla pojedynczej precyzji operacje zmiennoprzecinkowe, w przypadku obciążeń sztucznej Inteligencji przy użyciu zestawu narzędzi Microsoft Cognitive Toolkit, TensorFlow, Caffe i innych platform. Seria ND oferuje także dużo pojemniejszą pamięć procesora GPU (24 GB) mieszczącą znacznie większe modele sieci neuronowych. Podobnie jak seria NC, seria ND oferuje konfigurację z pomocniczych sieci o małych opóźnieniach i wysokiej przepływności przez funkcję RDMA oraz łączność InfiniBand, dzięki czemu można uruchamiać zadania szkoleń o dużej skali obejmujące wiele procesorów GPU.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych na region w ramach subskrypcji początkowo jest równa 0. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000 / 200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs * | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 procesor GPU = jedną kartę P40.

*Obsługa technologii RDMA

## <a name="nv-series"></a>Seria NV

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Maszyn wirtualnych z serii NV są obsługiwane przez [procesory GPU NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologii dla pulpitu przyspieszonych aplikacji komputerowych i pulpitów wirtualnych gdzie klienci będą mogli wizualizować swoje dane lub symulacje. Użytkownicy będą mogli wizualizować swoje grafiki przepływy pracy na wystąpieniach NV, aby uzyskać doskonałe możliwości grafiki oraz dodatkowo uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez 2690 z technologią Intel Xeon E5 v3 (Haswell) procesorów.

Każdy procesor GPU w wystąpieniach NV jest dostarczany z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standardowa_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standardowa_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

## <a name="nvv3-series-preview-sup1sup"></a>Seria NVv3 (wersja zapoznawcza) <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyn wirtualnych z serii NVv3 są obsługiwane przez [procesory GPU NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologię Intel E5-2690 v4 (broadwell z zegarem) procesorów. Te maszyny wirtualne są przeznaczone dla procesora GPU accelerated aplikacji graficznych i pulpitów wirtualnych, których klienci chcą wizualizować swoje dane, symulowanie wyników, aby wyświetlić, pracować nad CAD lub renderowania i dane ze strumienia zawartości. Dodatkowo te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne NVv3 obsługuje usługi Premium Storage i dołączone dwa razy pamięć systemową (RAM) w porównaniu z jego poprzednika, seria NV.  

Każdy procesor GPU w NVv3 wystąpienia jest powiązana z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000 / 200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 procesor GPU = połowa karty M60.

<sup>1</sup> maszyny wirtualne z serii NVv3 są wyposażone w technologię Intel Hyper-Threading
