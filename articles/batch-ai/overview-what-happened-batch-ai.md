---
title: Co się dzieje w usłudze Apache Batch AI? | Microsoft Docs
description: Dowiedz się więcej o tym, co się dzieje z usługą Azure Batch AI i opcją obliczeniową usługi Azure Machine Learning.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436879"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co się dzieje z usługą Apache Batch AI?

**Usługa Azure Batch AI zostanie wycofana w marcu.** Możliwości usługi Batch AI związane z trenowaniem i ocenianiem na dużą skalę są teraz dostępne w [usłudze Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), która stanie się ogólnodostępna 4 grudnia 2018 r.

Wśród wielu możliwości dotyczących uczenia maszynowego usługa Azure Machine Learning obejmuje chmurowy, zarządzany docelowy obiekt obliczeniowy do trenowania, wdrażania i oceniania modeli uczenia maszynowego. Ten docelowy obiekt obliczeniowy jest nazywany [środowiskiem obliczeniowym usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Migrację i korzystanie z tego obiektu warto zacząć już dziś](#migrate). Interakcja z usługą Azure Machine Learning jest możliwa za pomocą wbudowanych zestawów [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), interfejsu wiersza polecenia i [witryny Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

| Date | Szczegółowe informacje o obsłudze usługi Batch AI |
| ---- |-----------------|
| &nbsp;14&nbsp; grudnia 2018| Z istniejących subskrypcji usługi Azure Batch AI można korzystać jak dotychczas. Jednak rejestrowanie **nowych subskrypcji** nie będzie już możliwe a usługa nie będzie rozwijana.|
| &nbsp;31&nbsp; marca 2019 | Po tej dacie istniejące subskrypcje usługi Batch AI przestaną działać. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Jak przeprowadzić migrację?

Aby uniknąć przerw w działaniu aplikacji i móc korzystać z najnowszych funkcji, przed 31 marca 2019 r. wykonaj następujące kroki:

1. Utwórz obszar roboczy usługi Azure Machine Learning i rozpocznij pracę:
    + [Szybki start oparty na języku Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Szybki start oparty na platformie Azure](../machine-learning/service/quickstart-get-started.md)

1. Skonfiguruj [środowisko obliczeniowe usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) do trenowania modelu.

1. Zaktualizuj swoje skrypty pod kątem korzystania ze środowiska obliczeniowego usługi Azure Machine Learning.

## <a name="support"></a>Pomoc techniczna

Pomoc techniczna jest dostępna dla dotychczasowych klientów, którzy chcą migrować do bardziej rozbudowanej [usługi Azure Machine Learning](https://aka.ms/aml-docs).

W przypadku gdy usługa Azure Machine Learning nie spełnia wymagań, a obsługiwana funkcja istnieje w usłudze Batch AI, można wysłać do zespołu pomocy technicznej usługi Batch AI żądanie dodania subskrypcji do białej listy, co umożliwi korzystanie z usługi Batch AI do momentu jej wycofania.

## <a name="next-steps"></a>Następne kroki

+ Przeczytaj artykuł [Przegląd usługi Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Skonfiguruj docelowy obiekt obliczeniowy do trenowania modelu](../machine-learning/service/how-to-set-up-training-targets.md) za pomocą usługi Azure Machine Learning.

+ Zapoznaj się z [harmonogramem działania dla platformy Azure](https://azure.microsoft.com/updates/), aby dowiedzieć się o innych aktualizacjach usługi Azure.
