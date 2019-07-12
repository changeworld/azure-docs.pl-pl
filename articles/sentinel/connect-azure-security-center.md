---
title: Łączenie danych w usłudze Azure Security Center przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych usługi Azure Security Center przez wartownika platformy Azure.
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 697983000f84f9e4efe7e2c8ef9dbb2f636e0735
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620573"
---
# <a name="connect-data-from-azure-security-center"></a>Połącz dane z usługi Azure Security Center

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Wartownik platformy Azure umożliwiają łączenie alertów z [usługi Azure Security Center](../security-center/security-center-intro.md) i przekazywać je strumieniowo do usługi Azure przez wartownika. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować alertów z usługi Azure Security Center, musi być współautorem subskrypcji, dla których dzienniki przesyłania strumieniowego.

- Konieczne jest posiadanie [Azure Security Center w warstwie standardowa](../security-center/security-center-pricing.md) uruchomiony w ramach subskrypcji. W przeciwnym razie [Uaktualnij swoją subskrypcję na warstwę standardowa](https://azure.microsoft.com/pricing/details/security-center/).

- Należy zalogować się jako użytkownik, który ma administratora globalnego lub uprawnienia administratora zabezpieczeń w każdej subskrypcji, którą chcesz się połączyć.


## <a name="connect-to-azure-security-center"></a>Łączenie z usługą Azure Security Center

1. Na platformie Azure przez wartownika, wybierz **łączników danych** a następnie kliknij przycisk **usługi Azure Security Center** kafelka.
1. Po prawej stronie, kliknij **Connect** obok każdej subskrypcji alertów, którego chcesz strumieniowo przesyłać do platformy Azure przez wartownika. Upewnij się, że uaktualnienie każdej subskrypcji do warstwy standardowej usługi Azure Security Center do strumienia alertów do platformy Azure przez wartownika.

3. Kliknij przycisk **Połącz**.

4. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów w usłudze Azure Security Center, możesz wyszukać **SecurityEvent**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure Security Center na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
