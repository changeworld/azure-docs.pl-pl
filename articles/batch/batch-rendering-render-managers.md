---
title: Obsługa Menedżera renderowania usługi Azure Batch
description: Korzystanie z platformy Azure do renderowania, korzystając z integracji Menedżera renderowania usługi Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 066aab598628701bf7a60b0f4f20d996348fa5ce
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406725"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Za pomocą usługi Azure Batch przy użyciu menedżerów farmy renderowania

Jeśli używasz istniejącej lokalnej renderowania farmy, istnieje duże prawdopodobieństwo, że Menedżer renderowania kontroluje pojemności farmy renderowania i Renderuj zadania, a następnie.

System Azure oferuje wbudowaną obsługę lub dodatki menedżerów popularnych renderowania. Można następnie dodawanie i usuwanie maszyn wirtualnych platformy Azure, łącznie z maszynami wirtualnymi za pomocą licencjonowania płatności na użycie aplikacji i maszyn wirtualnych o niskim priorytecie.

Obsługiwane są następujące menedżerów renderowania:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Królewskie renderowania](http://www.royalrender.de/)
* [Ostateczny termin Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Korzystać z platformy Azure za pomocą PipelineFX Qube

Skrypty i instrukcje, aby umożliwić usłudze Azure Batch puli maszyn wirtualnych, która będzie używana jako pracowników Qube znajdują się w [repozytorium GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Korzystać z platformy Azure przy użyciu Royal renderowania

Królewskie renderowania ma integracji platformy Azure i usługi Azure Batch wbudowanych, dzięki czemu można rozszerzyć farmy renderowania z maszyn wirtualnych opartych na platformie Azure. Aby uzyskać podsumowanie, zobacz [pliki Pomocy](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Na przykład Royal renderowania klient korzystający z funkcji Integracja z platformą Azure, zobacz [historię klienta firmie Jellyfish](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Korzystać z platformy Azure z terminem wypadającym Thinkbox

Skrypty i instrukcje, aby umożliwić usłudze Azure Batch puli maszyn wirtualnych, która będzie używana jako elementy podrzędne termin znajdują się w [repozytorium GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj integracji usługi Azure Batch dla Menedżera renderowania przy użyciu odpowiedniej wtyczki i instrukcje w serwisie GitHub, jeśli ma to zastosowanie.