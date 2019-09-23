---
title: Omówienie wersji zapoznawczej pamięci podręcznej Azure HPC
description: Opisuje pamięć podręczną platformy Azure HPC, rozwiązanie dostępu do pliku do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/19/2019
ms.author: v-erkell
ms.openlocfilehash: f4c858d7a71cf02b4a8fe181deecbf3a1b652885
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180685"
---
# <a name="what-is-azure-hpc-cache-preview"></a>Co to jest usługa Azure HPC Cache? (Wersja zapoznawcza)

Pamięć podręczna Azure HPC umożliwia przyspieszenie dostępu do danych na potrzeby zadań obliczeniowych o wysokiej wydajności (HPC). Buforowanie plików na platformie Azure sprawia, że skalowalność w chmurze jest dostępna nawet w przypadku przepływów pracy, w których dane są przechowywane przez linki sieci WAN, na przykład w lokalnym środowisku magazynu (NAS) podłączonym do sieci centrum danych.

Pamięć podręczna Azure HPC jest łatwa do uruchomienia i monitorowania z Azure Portal. Istniejący magazyn NFS lub nowe kontenery obiektów BLOB mogą stać się częścią zagregowanej przestrzeni nazw, co sprawia, że dostęp klienta jest prosty nawet w przypadku zmiany miejsca docelowego magazynu zaplecza.

## <a name="use-cases"></a>Przypadki zastosowań

Pamięć podręczna Azure HPC zwiększa produktywność w przypadku przepływów pracy takich jak:

* Przepływ pracy dostępu do pliku z dużym dostępem do odczytu
* Dane przechowywane w magazynie dostępnym NFS, obiekt blob platformy Azure lub oba
* Farmy obliczeniowe do 75 000 rdzeni procesora CPU

Pamięć podręczna Azure HPC można dodać do wielu różnych przepływów pracy w wielu branżach. Wszystkie systemy, w których duża liczba maszyn musi uzyskać dostęp do zestawu plików na dużą skalę i z małym opóźnieniem, będzie korzystne dla tej usługi. Poniższe sekcje zawierają konkretne przykłady.

### <a name="visual-effects-vfx-rendering"></a>Renderowanie efektów wizualnych (VFX)

W przypadku multimediów i rozrywki pamięć podręczna Azure HPC może przyspieszyć dostęp do danych dla projektów renderowania o kluczowym znaczeniu. Przepływy pracy renderowania VFX często wymagają przetwarzania ostatnich minut przez dużą liczbę węzłów obliczeniowych. Dane dla tych przepływów pracy zwykle znajdują się w lokalnym środowisku usługi NAS. Pamięć podręczna Azure HPC może buforować te dane plików w chmurze w celu zmniejszenia opóźnień i zwiększenia elastyczności renderowania na żądanie.

### <a name="life-sciences"></a>Nauki przyrodnicze

Wiele przepływów pracy nauki przyrodniczej może korzystać z buforowania plików skalowalnych w poziomie.

Instytut badawczy, który chce przenieść swoje przepływy pracy analizy genomiki do platformy Azure, może je łatwo przenieść przy użyciu pamięci podręcznej platformy Azure HPC. Ponieważ pamięć podręczna zapewnia dostęp do plików POSIX, może uruchamiać swój istniejący przepływ pracy po stronie klienta w chmurze bez wprowadzania żadnych zmian.

Pamięć podręczna Azure HPC może być również wykorzystywana w celu zwiększenia wydajności zadań, takich jak analiza pomocnicza, Symulacja farmakologiczna lub Analiza obrazu oparta na AI.

### <a name="financial-services-analytics"></a>Analiza usług finansowych

Pamięć podręczna Azure HPC może ułatwić przyspieszenie obliczeń ilościowych, obciążeń związanych z analizą ryzyka i symulacje Monte Carlo w celu zapewnienia firmowych usług finansowych lepszy wgląd w podejmowanie decyzji strategicznych.

## <a name="region-availability"></a>Dostępność w danym regionie

Pamięć podręczna Azure HPC jest dostępna w następujących regionach platformy Azure:

* East US
* Wschodnie stany USA 2
* Europa Północna
* Europa Zachodnia
* Azja Południowo-Wschodnia
* Zachodnie stany USA 2

Aby uzyskać najnowsze informacje o dostępności, należy zapoznać się ze [stroną produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .

## <a name="preview-availability"></a>Podgląd dostępności

Publiczna wersja zapoznawcza pamięci podręcznej platformy Azure HPC jest ograniczona do zapewnienia jakości usług. Zażądaj dostępu, wypełniając [ten formularz](https://aka.ms/onboard-hpc-cache). Po dodaniu subskrypcji do listy dostępu można utworzyć pamięci podręczne testów.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [stronę produktu usługi Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) , aby dowiedzieć się więcej o jej możliwościach
* Informacje o [wymaganiach wstępnych](hpc-cache-prereqs.md) dotyczących produktu
* [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) z poziomu Azure Portal
