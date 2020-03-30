---
title: Integracja z zasadami platformy Azure — usługa Azure Batch | Dokumenty firmy Microsoft
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618375"
---
# <a name="integration-with-azure-policy"></a>Integracja z zasadami platformy Azure

Zasady platformy Azure to usługa na platformie Azure używana do tworzenia, przypisywania i zarządzania zasadami wymuszania reguł dotyczących zasobów, aby zapewnić zgodność tych zasobów ze standardami firmowymi i umowami dotyczącymi poziomu usług. Usługa Azure Policy ocenia zasoby pod kątem niezgodności z przypisanymi zasadami. 

Usługa Azure Batch ma dwa wbudowane rozszerzenia ułatwiające zarządzanie zgodnością zasad. 

|**Nazwa**...|   **Opis**|**Efekt(-y)**|  **Wersja**|    **Źródła**
|----------------|----------|----------|----------------|---------------|
|Dzienniki diagnostyczne na kontach usługi Batch powinny być włączone|   Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci|AuditIfNotExists, Wyłączone|  2.0.0|  GitHub|
|Reguły alertów metryk powinny być skonfigurowane na kontach usługi Batch| Konfiguracja inspekcji reguł alertów metryk na koncie usługi Batch w celu włączenia wymaganej metryki|   AuditIfNotExists, Wyłączone| 1.0.0|  GitHub|

Definicje zasad opisują warunki, które muszą być spełnione. Warunek porównuje właściwość zasobu z wymaganą wartością. Dostęp do pól właściwości zasobu są dostępne przy użyciu wstępnie zdefiniowanych aliasów. Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości typu zasobu. Aliasy umożliwiają ograniczenie, jakie wartości lub warunki są dozwolone dla właściwości w zasobie. Każdy alias jest mapowyny do ścieżek w różnych wersjach interfejsu API dla danego typu zasobu. Podczas oceny zasad aparat zasad pobiera ścieżkę właściwości dla tej wersji interfejsu API.

Zasoby wymagane przez partię obejmują: konto, węzeł obliczeniowy, pulę, zadanie i zadanie. Dlatego można użyć aliasów właściwości, aby uzyskać dostęp do określonych właściwości dla tych zasobów. Dowiedz się więcej o [aliasach](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Aby upewnić się, że znasz bieżące aliasy i przejrzyj zasoby i zasady, użyj rozszerzenia zasad platformy Azure dla programu Visual Studio Code. Można go zainstalować na wszystkich platformach, które są obsługiwane przez visual studio code. Ta obsługa obejmuje systemy Windows, Linux i macOS. Zapoznaj się [z wytycznymi dotyczącymi instalacji](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



