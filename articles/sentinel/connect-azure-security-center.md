---
title: Łączenie Azure Security Center danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Azure Security Center dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240220"
---
# <a name="connect-data-from-azure-security-center"></a>Łączenie danych z Azure Security Center





System Azure — wskaźnik produkcji umożliwia łączenie alertów z [Azure Security Center](../security-center/security-center-intro.md) i przesyłanie ich strumieniowo do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować alerty z Azure Security Center, musisz być współautorem subskrypcji, której rejestrujesz dzienniki.

- W ramach subskrypcji musi być uruchomiona [Azure Security Center warstwa standardowa](../security-center/security-center-pricing.md) . W przeciwnym razie [Uaktualnij subskrypcję do wersji Standard](https://azure.microsoft.com/pricing/details/security-center/).

- Musisz zalogować się przy użyciu użytkownika, który ma uprawnienia administratora globalnego lub administratora zabezpieczeń dla każdej subskrypcji, którą chcesz połączyć.


## <a name="connect-to-azure-security-center"></a>Połącz z Azure Security Center

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Security Center** .

1. Po prawej stronie kliknij pozycję **Połącz** obok każdej subskrypcji, której alerty chcesz przesłać do usługi Azure wskaźnikowej. Pamiętaj, aby uaktualnić każdą subskrypcję do Azure Security Center warstwy Standardowa, aby przesyłać strumieniowo alerty do platformy Azure.

1. Możesz wybrać, czy alerty od Azure Security Center mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

3. Kliknij przycisk **Połącz**.

4. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure Security Center, Wyszukaj pozycję **SecurityAlert**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Security Center z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
