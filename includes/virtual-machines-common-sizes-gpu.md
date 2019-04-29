---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c2908ef5c67665b5ba48879626370f977634dc83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776337"
---
Maszyny Wirtualnej zoptymalizowane pod kątem procesora GPU z jednym lub wieloma procesorami GPU firmy NVIDIA wyspecjalizowane maszyny wirtualne o rozmiarach. Te rozmiary są przeznaczone dla obciążeń intensywnie korzystających z obliczeń, bogatych w elementy graficzne i wizualizacji. Ten artykuł zawiera informacje o liczbie i rodzaju procesorów GPU, procesorów wirtualnych, dysków z danymi i kart sieciowych. Przepustowość przepływności i sieć magazynu dostępne są również dla każdego rozmiaru w tej metodzie grupowania. 

* **NC, NCv2, NCv3, ND i NDv2** rozmiarów są zoptymalizowane dla aplikacji z mocy obliczeniowej i intensywnie korzystających z sieci i algorytmów. Niektóre przykłady są CUDA i OpenCL oparte na aplikacje i symulacje, sztucznej Inteligencji i uczenia głębokiego. Seria NCv3 koncentruje się na obciążeń obliczeniowych o wysokiej wydajności, wyposażone w procesor GPU firmy NVIDIA Tesla V100.  Seria ND koncentruje na scenariuszach dotyczących szkolenia i wnioskowania na potrzeby uczenia szczegółowego. W tym przypadku jest używany procesor GPU NVIDIA Tesla P40.

* **Seria NC** funkcji Procesor 2,60 GHz Intel Xeon® E5-2690 v3.

* **NCSv3**, **NCSv2**, i **ND** rozmiarów są wyposażone w procesor Intel Xeon® E5-2690 v4 2,60 GHz.
                      
* **NV i NVv2** rozmiarów są zoptymalizowane pod kątem i przeznaczone dla zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania i scenariuszy VDI przy użyciu platform, takich jak OpenGL i DirectX.  Te maszyny wirtualne są wspierane przez procesorów GPU Tesla M60 firmy NVIDIA.


## <a name="nc-series"></a>Seria NC

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Maszyny wirtualne z serii NC są obsługiwane przez [procesory GPU NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty. Użytkownicy mogą przetwarzanie danych szybciej dzięki wykorzystaniu architektury CUDA dla aplikacji eksploracji energii, symulacji awarii, ray śledzenia renderowania, uczenie głębokie i inne. Konfigurację NC24r zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.


| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
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

Maszyny wirtualne z serii NCv2 są obsługiwane przez [procesory GPU NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) procesorów GPU. Te procesory GPU oferują ponad 2 razy większą wydajność w porównaniu z serii NC. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Konfiguracja v2 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2 * |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 procesor GPU = jedną kartę P100.

*Obsługa technologii RDMA

## <a name="ncv3-series"></a>Seria NCv3

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyny wirtualne z serii NCv3 są obsługiwane przez [procesory GPU NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) procesorów GPU. Te procesory GPU oferują ponad 1,5 raza większą wydajność seria NCv2. Klienci mogą korzystać z zalet tych zaktualizowanych procesorów GPU w przypadku tradycyjnych obciążeń obliczeń HPC, takie jak modelowanie dotyczące złóż, DNA sekwencjonowania, dna, analiza, symulacje metodą Monte Carlo i inne. Konfiguracja v3 NC24rs zapewnia małe opóźnienia interfejsu sieci o wysokiej przepływności zoptymalizowany pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych w ramach subskrypcji jest początkowo równa 0 w każdym regionie. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 procesor GPU = jedną kartę V100.

*Obsługa technologii RDMA

## <a name="ndv2-series-preview"></a>Seria NDv2 (wersja zapoznawcza)


Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Infiniband: Nieobsługiwane


Maszyna wirtualna serii NDv2 jest nowy dodatek do rodziny procesorów GPU zaprojektowanej na potrzeby HPC, sztucznej Inteligencji i usługi machine learning obciążeń. Dysponuje ona 8 wzajemnie połączonymi procesorami GPU NVIDIA Tesla V100 NVLINK, 40 rdzeniami procesorów Intel Skylake oraz 672 GiB pamięci systemowej. Wystąpienie NDv2 zapewnia świetną wydajność w przypadku precyzji FP32 i FP64 dla obciążeń obliczeń HPC i sztucznej inteligencji korzystających ze struktur Cuda, TensorFlow, Pytorch, Caffe i innych.

[Utwórz konto i Uzyskaj dostęp do tych maszyn w trakcie okresu zapoznawczego](https://aka.ms/ndv2signup).
<br>


| Rozmiar              | Procesor wirtualny | Procesory GPU              | Memory (Pamięć)  | Karty sieciowe (maks.) | Maksymalnie z Rozmiar dysku           | Maksymalnie z dyski z danymi (1023 GB każdy) | Maksymalna przepustowość sieci | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672 GiB | 8          | Temporary 1344 / 2948XIO | 32                             | 24 000 MB/s           |

## <a name="nd-series"></a>Seria ND

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyny wirtualne serii ND to nowy dodatek do rodziny procesorów GPU zaprojektowana pod kątem sztucznej Inteligencji i uczenia głębokiego obciążeń. Oferują doskonałą wydajność na potrzeby szkoleń i wnioskowania. ND wystąpienia są obsługiwane przez [procesory GPU NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) procesorów GPU. Te wystąpienia oferują doskonałą wydajność dla pojedynczej precyzji operacje zmiennoprzecinkowe, w przypadku obciążeń sztucznej Inteligencji przy użyciu zestawu narzędzi Microsoft Cognitive Toolkit, TensorFlow, Caffe i innych platform. Seria ND oferuje także dużo pojemniejszą pamięć procesora GPU (24 GB) mieszczącą znacznie większe modele sieci neuronowych. Podobnie jak seria NC, seria ND oferuje konfigurację z pomocniczych sieci o małych opóźnieniach i wysokiej przepływności przez funkcję RDMA oraz łączność InfiniBand, dzięki czemu można uruchamiać zadania szkoleń o dużej skali obejmujące wiele procesorów GPU.

> [!IMPORTANT]
> Dla tej rodziny rozmiar przydziału rdzeni procesorów wirtualnych na region w ramach subskrypcji początkowo jest równa 0. [Zażądać zwiększenia limitu przydziału procesorów wirtualnych](../articles/azure-supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępny region](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 procesor GPU = jedną kartę P40.

*Obsługa technologii RDMA

## <a name="nv-series"></a>Seria NV

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Maszyn wirtualnych z serii NV są obsługiwane przez [procesory GPU NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologii dla pulpitu przyspieszonych aplikacji komputerowych i pulpitów wirtualnych gdzie klienci będą mogli wizualizować swoje dane lub symulacje. Użytkownicy będą mogli wizualizować swoje grafiki przepływy pracy na wystąpieniach NV, aby uzyskać doskonałe możliwości grafiki oraz dodatkowo uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. 

Każdy procesor GPU w wystąpieniach NV jest dostarczany z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standardowa_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standardowa_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standardowa_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

## <a name="nvv2-series-preview"></a>Seria NVv2 (wersja zapoznawcza)

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Maszyn wirtualnych z serii NVv2 są obsługiwane przez [procesory GPU NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) NVIDIA oraz technologii NVIDIA GRID technologię Intel Broadwell procesorów. Te maszyny wirtualne są przeznaczone dla procesora GPU accelerated aplikacji graficznych i pulpitów wirtualnych, których klienci chcą wizualizować swoje dane, symulowanie wyników, aby wyświetlić, pracować nad CAD lub renderowania i dane ze strumienia zawartości. Dodatkowo te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne NVv2 obsługuje usługi Premium Storage i dołączone dwa razy pamięć systemową (RAM) w porównaniu z jego poprzednika, seria NV.  

Każdy procesor GPU w NVv2 wystąpienia jest powiązana z licencją siatki. Ta licencja zapewnia elastyczność do korzystania z wystąpienia NV jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 współbieżnych użytkownicy mogą łączyć się maszyna wirtualna dla scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 procesor GPU = połowa karty M60.

 
