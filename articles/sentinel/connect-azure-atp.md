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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240186"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>Łączenie danych z usługi Azure Advanced Threat Protection (ATP) — wersja zapoznawcza




Dzienniki usługi [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem wersji zapoznawczej usługi Azure ATP

## <a name="connect-to-azure-atp"></a>Nawiązywanie połączenia z usługą Azure ATP

Upewnij się, że [w sieci jest włączona](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)wersja zapoznawcza usługi Azure ATP.
W przypadku wdrożenia usługi Azure ATP i pozyskiwania danych podejrzane alerty mogą być łatwo przesyłane strumieniowo do wskaźnikowego platformy Azure. Rozpoczęcie przesyłania strumieniowego na platformę Azure jest możliwe dopiero po 24 godzinach.


1. Aby połączyć usługę Azure ATP z platformą Azure, musisz najpierw włączyć integrację z usługą Azure ATP i Microsoft Cloud App Security. Aby uzyskać informacje o tym, jak to zrobić, zobacz [integracja z usługą Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij KAFELEK **Azure ATP** .

1. Możesz wybrać, czy alerty z usługi Azure ATP mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Kliknij przycisk **Połącz**.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Azure ATP, wyszukaj ciąg " **SecurityAlert**".

> [!NOTE]
> Jeśli alerty są większe niż 30 KB, wskaźnik na platformie Azure zatrzyma wyświetlanie pola jednostki w alertach.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zaawansowanej ochrony przed zagrożeniami na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

