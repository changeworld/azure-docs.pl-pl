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
ms.openlocfilehash: b9637265d263a75949d5a70c3e4f0ce06044d93c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901624"
---
Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU są wyspecjalizowanymi maszynami wirtualnymi z jednym lub wieloma procesorami GPU firmy NVIDIA. Te rozmiary są przeznaczone do obciążeń intensywnie korzystających z operacji obliczeniowych, intensywnie korzystających z grafiki i wizualizacji. Ten artykuł zawiera informacje o liczbie i typie procesorów GPU, procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Przepustowość magazynu i przepustowość sieci są również uwzględniane dla każdego rozmiaru w ramach grupowania.

* **NC, NCv2, rozmiary seria NCV3** są zoptymalizowane pod kątem intensywnie korzystających z obliczeń aplikacji i algorytmów intensywnie korzystających z sieci. Niektóre przykłady to aplikacje i symulacje oparte na CUDAach i OpenCLach, a następnie bogate w szczegółowe uczenie. Seria Seria NCV3 koncentruje się na obciążeniach obliczeniowych o wysokiej wydajności, które obejmują procesor GPU Tesla V100 firmy NVIDIA. W serii NC jest używany procesor Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell), a maszyny wirtualne serii NCv2 i seria NCV3 używają procesora Intel Xeon E5-2690 v4 (Broadwell).

* **ND i NDv2** Seria ND koncentruje się na scenariuszach szkolenia i wnioskowania o uczenie głębokie. Używa procesora GPU NVIDIA Tesla P40 i procesorów Intel Xeon E5-2690 v4 (Broadwell). Seria NDv2 używa procesora Intel Xeon Platinum 8168 (Skylake).

* Rozmiary **NV i NVv3** są zoptymalizowane i zaprojektowane pod kątem zdalnego wizualizacji, przesyłania strumieniowego, gier, kodowania i infrastruktury VDI przy użyciu struktur, takich jak OpenGL i DirectX.  Te maszyny wirtualne są obsługiwane przez procesor GPU NVIDIA Tesla M60.

* Rozmiary **NVv4** są zoptymalizowane i zaprojektowane pod kątem infrastruktury VDI i zdalnej wizualizacji. W przypadku procesorów GPU partioned NVv4 oferuje odpowiedni rozmiar dla obciążeń wymagających mniejszych zasobów procesora GPU.  Te maszyny wirtualne są obsługiwane przez procesor GPU z procesorem AMD Radeon Instinct MI25.


## <a name="nc-series"></a>Seria NC

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Maszyny wirtualne z serii NC są obsługiwane przez kartę [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) i procesor Intel Xeon E5-2690 v3 (Haswell). Użytkownicy mogą szybciej przetwarzanie dane, wykorzystując CUDA do obsługi aplikacji, symulacji awarii, renderowanie śledzenia, śledzenie, uczenie głębokie i wiele innych. Konfiguracja NC24r zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna liczba kart interfejsów sieciowych |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standardowa_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standardowa_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standardowa_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standardowa_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA

## <a name="ncv2-series"></a>Seria NCv2

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Maszyny wirtualne z serii NCv2 są obsługiwane przez procesory [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU. Te procesory GPU mogą zapewnić ponad 2. wydajność obliczeniową serii NC. Klienci mogą korzystać z tych zaktualizowanych procesorów GPU dla tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białka, symulacje Monte Carlo i inne. Poza procesorami GPU maszyny wirtualne serii NCv2 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfiguracja NC24rs v2 zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

> [!IMPORTANT]
> W przypadku tej rodziny rozmiarów limit przydziału vCPU (rdzeń) w subskrypcji jest początkowo ustawiany na 0 w każdym regionie. [Zażądaj zwiększenia limitu przydziału vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart interfejsów sieciowych |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P100.

*Obsługa technologii RDMA

## <a name="ncv3-series"></a>Seria NCv3

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Maszyny wirtualne z serii seria NCV3 są obsługiwane przez procesory [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU. Te procesory GPU mogą zapewnić 1,5 x wydajności obliczeniowej serii NCv2. Klienci mogą korzystać z tych zaktualizowanych procesorów GPU dla tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białka, symulacje Monte Carlo i inne. Konfiguracja NC24rs v3 zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych. Poza procesorami GPU maszyny wirtualne serii seria NCV3 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

> [!IMPORTANT]
> W przypadku tej rodziny rozmiarów limit przydziału vCPU (rdzeń) w subskrypcji jest początkowo ustawiany na 0 w każdym regionie. [Zażądaj zwiększenia limitu przydziału vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart interfejsów sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta V100.

*Obsługa technologii RDMA

## <a name="updated-ndv2-series-preview"></a>Zaktualizowana seria NDv2 (wersja zapoznawcza)

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

InfiniBand: obsługiwane

Maszyna wirtualna z serii NDv2 to nowa dołączenie do rodziny procesorów GPU przeznaczonej do potrzeb najbardziej wymagających obciążeń procesora GPU, uczenia maszynowego, symulacji i wdrożenia HPC. 

NDv2 jest obsługiwany przez 8 NVIDIA Tesla V100 NVLINK-podłączonych procesorów GPU, z których każdy ma 32 GB pamięci GPU. Każda maszyna wirtualna NDv2 ma także 40 rdzenie procesora Intel Xeon Platinum 8168 (Skylake) i 672 GiB pamięci systemowej. 

Wystąpienia NDv2 zapewniają doskonałą wydajność obciążeń HPC i AI wykorzystujących jądra CUDA z zoptymalizowanymi procesorami GPU, a także wiele narzędzi typu AI, ML i analitycznego, które obsługują Przyspieszenie GPU ", takie jak TensorFlow, Pytorch, Caffe, RAPIDS i inne platform. 

W sposób krytyczny NDv2 jest zbudowany na potrzeby skalowania w górę i skalowalnego w górę (wykorzystującego 8 procesorów GPU na maszynę wirtualną) i skalowania w poziomie (łączenie wielu maszyn wirtualnych współpracujących) z obciążeniami. Seria NDv2 obsługuje teraz sieci 100 Gigabit InfiniBand EDR zaplecza, podobnie jak te dostępne w serii HB maszyny wirtualnej HPC, aby umożliwić klastrowanie o wysokiej wydajności dla scenariuszy równoległych, w tym rozmieszczonych szkoleń dla AI i ML. Ta sieć zaplecza obsługuje wszystkie główne protokoły InfiniBand, włącznie z tymi, które są używane przez biblioteki NCCL2 firmy NVIDIA, umożliwiając bezproblemowe klastrowanie procesorów GPU.

> Podczas [włączania funkcji InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na maszynie wirtualnej ND40rs_v2 należy użyć sterownika 1.0.0.1 Mellanox OFED.

> Ze względu na zwiększoną ilość pamięci GPU Nowa maszyna wirtualna ND40rs_v2 wymaga użycia [maszyn wirtualnych 2. generacji](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) i obrazów w portalu Marketplace. 

> [Utwórz konto, aby zażądać wczesnego dostępu do wersji zapoznawczej maszyny wirtualnej NDv2.](https://aka.ms/AzureNDrv2Preview)

> Uwaga: ND40s_v2 z 16 GB pamięci na procesor GPU nie jest już dostępny do wersji zapoznawczej i został zastąpiony przez zaktualizowane ND40rs_v2.
<br>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna przepustowość sieci | Maksymalna liczba kart interfejsów sieciowych |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 MB/s | 8 |

## <a name="nd-series"></a>Seria ND

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Maszyny wirtualne z serii ND to nowe dodanie do rodziny procesorów GPU zaprojektowanej dla systemu AI i obciążeń głębokiego uczenia. Oferują one doskonałą wydajność na potrzeby szkoleń i wnioskowania. Wystąpienia ND są obsługiwane przez procesory [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) i procesory Intel Xeon E5-2690 v4 (Broadwell). Te wystąpienia zapewniają doskonałą wydajność operacji zmiennoprzecinkowych o pojedynczej precyzji dla obciążeń AI wykorzystujących Microsoft Cognitive Toolkit, TensorFlow, Caffe i inne platformy. Seria ND oferuje także dużo pojemniejszą pamięć procesora GPU (24 GB) mieszczącą znacznie większe modele sieci neuronowych. Podobnie jak w przypadku serii NC, Seria ND oferuje konfigurację z dodatkową siecią o małym opóźnieniu i wysokiej przepływności za pośrednictwem funkcji RDMA oraz łączności InfiniBand, dzięki czemu można uruchamiać zadania szkoleniowe o dużej skali obejmujące wiele procesorów GPU.

> [!IMPORTANT]
> W przypadku tej rodziny rozmiarów limit przydziału vCPU (rdzeń) na region w ramach subskrypcji jest początkowo ustawiony na 0. [Zażądaj zwiększenia limitu przydziału vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej rodziny w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart interfejsów sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P40.

*Obsługa technologii RDMA

## <a name="nv-series"></a>Seria NV

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Maszyny wirtualne z serii NV są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID dla aplikacji klasycznych i pulpitów wirtualnych, na których klienci mogą wizualizować swoje dane lub symulacje. Użytkownicy mogą wizualizować przepływy pracy intensywnie korzystających z grafiki w wystąpieniach TRWAŁych w celu uzyskania najwyższej jakości grafiki i dodatkowo uruchamiania obciążeń o pojedynczej precyzji, takich jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez procesory Intel Xeon E5-2690 v3 (Haswell).

Każdy procesor GPU w wystąpieniach TRWAŁych zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna liczba kart interfejsów sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standardowa_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standardowa_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

## <a name="nvv3-series--sup1sup"></a>NVv3 — seria <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Maszyny wirtualne z serii NVv3 są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i technologię NVIDIA GRID z procesorem Intel E5-2690 v4 (Broadwell). Te maszyny wirtualne są przeznaczone dla procesorów GPU i pulpitów wirtualnych, na których klienci chcą wizualizować swoje dane, symulują wyniki do wyświetlania, pracy w programie CAD lub renderowania i przesyłania strumieniowego zawartości. Dodatkowo te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. NVv3 maszyny wirtualne obsługują Premium Storage i mają dwa razy większą ilość pamięci systemowej (RAM) w porównaniu z poprzednią serią NV.  

Każdy procesor GPU w wystąpieniach NVv3 zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart interfejsów sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 procesor GPU = połowa karty M60.

<sup>1</sup> maszyny wirtualne serii NVv3 z technologią Intel Hyper-Threading

## <a name="nvv4-series-preview--sup1sup"></a>NVv4 — seria (wersja zapoznawcza) <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Maszyny wirtualne z serii NVv4 są obsługiwane przez procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU i AMD EPYC 7V12 (Rzym). Dzięki serii NVv4 platforma Azure wprowadza maszyny wirtualne ze częściowym procesorami GPU. Wybierz maszynę wirtualną o odpowiednim rozmiarze dla przyspieszanych przez procesor GPU aplikacji graficznych i pulpitów wirtualnych, rozpoczynając od 1/8. procesora GPU z 2 GiB bufora klatek do pełnego procesora GPU z 16 GiB buforem ramek. NVv4 maszyny wirtualne obecnie obsługują tylko system operacyjny gościa systemu Windows.

[Utwórz konto i uzyskaj dostęp do tych maszyn w trakcie okresu zapoznawczego](https://aka.ms/nvv4signup).
<br>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maksymalna liczba dysków danych | Maksymalna liczba kart interfejsów sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> maszyny wirtualne z serii NVv4 — technologia wielowątkowego rozwiązania AMD

