---
title: Połącz dane ATP platformy Azure z podglądem platformy Azure — Wersja zapoznawcza | Microsoft Docs
description: Dowiedz się, jak połączyć dane ATP platformy Azure z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599160"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Łączenie danych z usługi Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Dzienniki usługi [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem wersji zapoznawczej usługi Azure ATP

## <a name="connect-to-azure-atp"></a>Nawiązywanie połączenia z usługą Azure ATP

Upewnij się, że [w sieci jest włączona](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)wersja zapoznawcza usługi Azure ATP.
W przypadku wdrożenia usługi Azure ATP i pozyskiwania danych podejrzane alerty mogą być łatwo przesyłane strumieniowo do wskaźnikowego platformy Azure. Rozpoczęcie przesyłania strumieniowego na platformę Azure jest możliwe dopiero po 24 godzinach.


1. Aby połączyć usługę Azure ATP z platformą Azure, musisz najpierw włączyć integrację z usługą Azure ATP i Microsoft Cloud App Security. Aby uzyskać informacje o tym, jak to zrobić, zobacz [integracja z usługą Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij KAFELEK **Azure ATP** .

2. Kliknij przycisk **Połącz**.

6. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Azure ATP, wyszukaj ciąg " **SecurityAlert**".

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zaawansowanej ochrony przed zagrożeniami na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

