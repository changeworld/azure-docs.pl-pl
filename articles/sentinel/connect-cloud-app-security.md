---
title: Łączenie danych Cloud App Security z wersją zapoznawczą platformy Azure Microsoft Docs
description: Dowiedz się, jak połączyć Cloud App Security dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881087"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z Microsoft Cloud App Security 

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do usługi Azure wskaźnikowego za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z Cloud App Security do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń

## <a name="connect-to-cloud-app-security"></a>Połącz z Cloud App Security

Jeśli masz już Cloud App Security, upewnij się, że jest [włączona w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli Cloud App Security zostanie wdrożona i pozyskuje dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Cloud App Security** .

2. Kliknij przycisk **Połącz**.

3. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Cloud App Security, Wyszukaj pozycję **SecurityAlert**.


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Microsoft Cloud App Security z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
