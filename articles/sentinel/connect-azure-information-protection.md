---
title: Łączenie usługi Azure Information Protection z usługą Azure Sentinel
description: Dowiedz się, jak połączyć dane usługi Azure Information Protection w usłudze Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588556"
---
# <a name="connect-data-from-azure-information-protection"></a>Łączenie danych z usługi Azure Information Protection

> [!IMPORTANT]
> Łącznik danych usługi Azure Information Protection w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo informacje rejestrowania z [usługi Azure Information Protection](https://azure.microsoft.com/services/information-protection/) do usługi Azure Sentinel, konfigurując łącznik danych usługi Azure Information Protection. Usługa Azure Information Protection ułatwia kontrolowanie i zabezpieczanie poufnych danych, niezależnie od tego, czy są przechowywane w chmurze, czy lokalnie.

Jeśli [raportowanie centralne dla usługi Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) jest już skonfigurowane tak, aby informacje rejestrowania z tej usługi były przechowywane w tym samym obszarze roboczym usługi Log Analytics, który został wybrany obecnie dla usługi Azure Sentinel, można pominąć konfigurację tego łącznika danych. Informacje o rejestrowaniu z usługi Azure Information Protection są już dostępne dla usługi Azure Sentinel.

Jeśli jednak rejestrowanie informacji z usługi Azure Information Protection przechodzi do innego obszaru roboczego usługi Log Analytics niż ten, który został wybrany obecnie dla usługi Azure Sentinel, wykonaj jedną z następujących czynności:

- Zmień obszar roboczy wybrany w usłudze Azure Sentinel.

- Zmień obszar roboczy usługi Azure Information Protection, co można zrobić, konfigurując ten łącznik danych.
    
    Jeśli zmienisz obszar roboczy, nowe dane raportowania dla usługi Azure Information Protection będą teraz przechowywane w obszarze roboczym używanym dla usługi Azure Sentinel, a dane historyczne nie będą dostępne dla usługi Azure Sentinel. Ponadto jeśli poprzedni obszar roboczy jest skonfigurowany dla kwerend niestandardowych, alertów lub interfejsów API REST, muszą one zostać ponownie skonfigurowane dla obszaru roboczego usługi Azure Sentinel, jeśli chcesz kontynuować korzystanie z nich dla usługi Azure Information Protection. Nie jest wymagana ponowna konfiguracja dla klientów i usług korzystających z usługi Azure Information Protection.

## <a name="prerequisites"></a>Wymagania wstępne

- Jedna z następujących ról administratora usługi Azure AD dla dzierżawy: 
    - Administrator usługi Azure Information Protection
    - Administrator zabezpieczeń
    - Administrator zgodności
    - Administrator danych zgodności
    - Administrator globalny
    
    > [!NOTE]
    > Nie można użyć roli administratora usługi Azure Information Protection, jeśli dzierżawa znajduje się na [ujednoliconej platformie etykietowania.](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
    
    Te role administratora są wymagane tylko do konfigurowania łącznika usługi Azure Information Protection i nie są wymagane, gdy usługa Azure Sentinel jest połączona z usługą Azure Information Protection.

- Uprawnienia do odczytu i zapisu w obszarze roboczym usługi Log Analytics, którego używasz dla usługi Azure Sentinel i usługi Azure Information Protection.

- Usługa Azure Information Protection została dodana do witryny Azure portal. Jeśli potrzebujesz pomocy w tym kroku, zobacz [Dodawanie usługi Azure Information Protection do witryny Azure portal.](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)

## <a name="connect-to-azure-information-protection"></a>Łączenie się z usługą Azure Information Protection

Skorzystaj z poniższych instrukcji, jeśli nie skonfigurowano obszaru roboczego usługi Log Analytics dla usługi Azure Information Protection lub musisz zmienić obszar roboczy, w który przechowuje informacje rejestrowania usługi Azure Information Protection.

1. W usłudze Azure Sentinel wybierz pozycję **Łączniki danych**, a następnie **usługę Azure Information Protection (Wersja zapoznawcza)**.

2. Wybierz **pozycję Otwórz stronę łącznika**.

3. W bloku **Konfiguruj analizy (wersja zapoznawcza)** wybierz obszar roboczy, którego aktualnie używasz dla usługi Azure Sentinel. Jeśli wybierzesz inny obszar roboczy, dane raportowania z usługi Azure Information Protection nie będą dostępne dla usługi Azure Sentinel.

4. Po wybraniu obszaru roboczego wybierz przycisk **OK,** a **stan** łącznika powinien teraz zmienić się na **Połączony**.

5. Dane raportowania z usługi Azure Information Protection są przechowywane w tabeli **InformationProtectionLogs_CL** w wybranym obszarze roboczym. 
    
    Aby użyć odpowiedniego schematu w usłudze Azure Monitor dla tych danych raportowania, wyszukaj **informacjeProtectionEvents**. Aby uzyskać informacje na temat tych funkcji zdarzeń, zobacz [Przyjazne odwołanie do schematu dla funkcji zdarzeń](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) sekcji z dokumentacji usługi Azure Information Protection.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć usługę Azure Information Protection z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
