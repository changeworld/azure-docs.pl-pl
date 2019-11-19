---
title: Łączenie danych ATP platformy Azure z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane ATP platformy Azure z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: fb8f4de3b3b24d1eba372600c817627771ef0ef6
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158872"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Łączenie danych z usługi Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Łącznik danych zaawansowanej ochrony przed zagrożeniami na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzienniki usługi [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem wersji zapoznawczej usługi Azure ATP i zapewnić integrację między usługą Azure ATP i Microsoft Cloud App Security. Aby uzyskać więcej informacji, zobacz [integracja z usługą Azure Advanced Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Nawiązywanie połączenia z usługą Azure ATP

Upewnij się, że [w sieci jest włączona](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)wersja zapoznawcza usługi Azure ATP.
W przypadku wdrożenia usługi Azure ATP i pozyskiwania danych podejrzane alerty mogą być łatwo przesyłane strumieniowo do wskaźnikowego platformy Azure. Rozpoczęcie przesyłania strumieniowego na platformę Azure jest możliwe dopiero po 24 godzinach.


1. Aby połączyć usługę Azure ATP z platformą Azure, musisz najpierw włączyć integrację z usługą Azure ATP i Microsoft Cloud App Security. Aby uzyskać informacje o tym, jak to zrobić, zobacz [integracja z usługą Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Advanced Threat Protection (wersja zapoznawcza)** .

1. Możesz wybrać, czy alerty z usługi Azure ATP mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Kliknij przycisk **Połącz**.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Azure ATP, wyszukaj ciąg " **SecurityAlert**".

> [!NOTE]
> Jeśli alerty są większe niż 30 KB, wskaźnik na platformie Azure zatrzyma wyświetlanie pola jednostki w alertach.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zaawansowanej ochrony przed zagrożeniami na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

