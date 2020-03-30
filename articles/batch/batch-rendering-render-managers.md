---
title: Obsługa menedżera renderowania — usługa Azure Batch
description: Korzystanie z integracji menedżera renderowania usługi Azure Batch. Dowiedz się więcej o wbudowanej pomocy technicznej lub dodatkach dla popularnych menedżerów renderowania.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449682"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Korzystanie z usługi Azure Batch z menedżerami farm renderowania

Jeśli używasz istniejącej lokalnej farmy renderowania, jest wysoce prawdopodobne, że menedżer renderowania kontroluje pojemność farmy renderowania i zadania renderowania.

Platforma Azure zapewnia wbudowaną pomoc techniczną lub dodatki dla popularnych menedżerów renderowania. Następnie można dodawać i usuwać maszyny wirtualne platformy Azure, w tym maszyny wirtualne z płatnym licencjonowaniem aplikacji i maszynami wirtualnymi o niskim priorytecie.

Obsługiwane są następujące menedżery renderowania:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Królewski render](https://www.royalrender.de/)
* [Termin Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Centrum renderowania platformy Azure

Usługa Azure Render Hub upraszcza tworzenie farm renderowania platformy Azure i zarządzanie nimi.  Render Hub ma natywną obsługę PipelineFx Qube i Deadline 10.  Aby uzyskać więcej informacji i szczegółowe instrukcje, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Korzystanie z platformy Azure z PipelineFX Qube

Usługa Azure Render Hub obsługuje popularnych menedżerów renderowania, w tym deadline.  Aby uzyskać instrukcje dotyczące wdrażania i używania Centrum renderowania, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

Skrypty i instrukcje umożliwiające korzystanie z maszyn wirtualnych puli usługi Azure Batch jako pracowników Qube są również dostępne w [repozytorium GitHub.](https://github.com/Azure/azure-qube)

## <a name="using-azure-with-royal-render"></a>Korzystanie z platformy Azure z renderowaniem królewskim

Royal Render ma wbudowaną integrację platformy Azure i usługi Azure Batch, która umożliwia rozszerzenie farmy renderowania za pomocą maszyn wirtualnych opartych na platformie Azure. Aby uzyskać podsumowanie, zobacz [pliki pomocy](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Na przykład klienta Royal Render przy użyciu integracji platformy Azure, zobacz [historię klienta Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Korzystanie z platformy Azure z terminem thinkbox

Usługa Azure Render Hub obsługuje popularnych menedżerów renderowania, w tym deadline.  Aby uzyskać instrukcje dotyczące wdrażania i używania Centrum renderowania, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Następne kroki

Wypróbuj integrację usługi Azure Batch dla menedżera renderowania, korzystając z odpowiedniej wtyczki i instrukcji w usłudze GitHub, w stosownych przypadkach.
