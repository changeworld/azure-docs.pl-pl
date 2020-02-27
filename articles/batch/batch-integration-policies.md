---
title: Integracja z zasadami platformy Azure — Azure Batch | Microsoft Docs
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618375"
---
# <a name="integration-with-azure-policy"></a>Integracja z usługą Azure Policy

Azure Policy to usługa platformy Azure służąca do tworzenia, przypisywania i zarządzania zasadami, które wymuszają reguły dotyczące zasobów, aby zapewnić, że te zasoby pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Azure Policy szacuje zasoby pod kątem braku zgodności z przypisaną zasadami. 

Azure Batch ma dwa wbudowane rozszerzenia ułatwiające zarządzanie zgodnością zasad. 

|**Nazwa**...|   **Opis**|**Efekt (s)**|  **Wersja**|    **Element źródłowy**
|----------------|----------|----------|----------------|---------------|
|Należy włączyć dzienniki diagnostyczne na kontach wsadowych|   Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci|AuditIfNotExists, wyłączone|  2.0.0|  GitHub|
|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych| Inspekcja konfiguracji reguł alertów metryk na koncie wsadowym, aby włączyć wymaganą metrykę|   AuditIfNotExists, wyłączone| 1.0.0|  GitHub|

Definicje zasad opisują warunki, które muszą zostać spełnione. Warunek porównuje właściwość zasobu z wymaganą wartością. Pola właściwości zasobu są dostępne przy użyciu wstępnie zdefiniowanych aliasów. Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości dla typu zasobu. Aliasy pozwalają ograniczyć, jakie wartości lub warunków, które są dozwolone dla właściwości zasobu. Każdy alias mapuje do ścieżki w różnych wersjach interfejsu API dla danego typu zasobu. Podczas oceny zasad aparatu zasad pobiera ścieżkę właściwości dla danej wersji interfejsu API.

Zasoby wymagane przez zadanie wsadowe obejmują: konto, węzeł obliczeniowy, pulę, zadanie i zadanie. Aby uzyskać dostęp do określonych właściwości dla tych zasobów, należy użyć aliasów właściwości. Dowiedz się więcej o [aliasach](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Aby upewnić się, że znasz bieżące aliasy i zapoznaj się z zasobami i zasadami, użyj rozszerzenia zasad platformy Azure dla Visual Studio Code. Można ją zainstalować na wszystkich platformach obsługiwanych przez Visual Studio Code. Ta obsługa obejmuje systemy Windows, Linux i macOS. Zobacz [wskazówki dotyczące instalacji](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



