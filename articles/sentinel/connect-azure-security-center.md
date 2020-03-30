---
title: Łączenie danych usługi Azure Security Center z usługą Azure Sentinel
description: Dowiedz się, jak połączyć dane usługi Azure Security Center z usługą Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588539"
---
# <a name="connect-data-from-azure-security-center"></a>Łączenie danych z usługi Azure Security Center





Usługa Azure Sentinel umożliwia łączenie alertów z [usługi Azure Security Center](../security-center/security-center-intro.md) i przesyłanie ich strumieniowo do usługi Azure Sentinel. 

## <a name="prerequisites"></a>Wymagania wstępne

- Aby wyeksportować alerty z usługi Azure Security Center, musisz mieć rolę czytnika zabezpieczeń w subskrypcji dzienników, które przesyłasz strumieniowo.

- Musisz mieć [warstwę Standard usługi Azure Security Center](../security-center/security-center-pricing.md) działającą w ramach subskrypcji. Jeśli nie, [uaktualnij subskrypcję do standardu](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Łączenie się z Centrum zabezpieczeń platformy Azure

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **Usługi Azure Security Center.**

1. Po prawej stronie kliknij pozycję **Połącz** obok każdej subskrypcji, której alerty chcesz przesyłać strumieniowo do usługi Azure Sentinel. Upewnij się, że każda subskrypcja jest uaktualniona do warstwy Standard usługi Azure Security Center, aby przesyłać strumieniowo alerty do usługi Azure Sentinel.

1. Można wybrać, czy alerty z usługi Azure Security Center mają automatycznie generować zdarzenia w usłudze Azure Sentinel. W obszarze **Tworzenie zdarzeń** wybierz **pozycję Włącz,** aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analytics,** a następnie **Aktywne reguły**.

3. Kliknij pozycję **Połącz**.

4. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Azure Security Center, wyszukaj **securityalert**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Azure Security Center z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
