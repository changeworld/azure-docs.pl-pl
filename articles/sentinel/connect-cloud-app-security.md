---
title: Łączenie danych w usłudze Cloud App Security przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć dane w usłudze Cloud App Security z platformy Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5a7dfeed7b52453b38720c21c7d213679b8d2854
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789453"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Połącz dane z Microsoft Cloud App Security 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do platformy Azure przez wartownika za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alerty z usługi Cloud App Security do platformy Azure przez wartownika. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z administratorem globalnym lub uprawnienia administratora zabezpieczeń

## <a name="connect-to-cloud-app-security"></a>Łączenie z usługą Cloud App Security

Jeśli masz już Cloud App Security, upewnij się, jest [włączone w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli usługa Cloud App Security jest wdrażana i zbierać dane, dane alertu można łatwo przesyłane strumieniowo do usługi Azure przez wartownika.


1. Na platformie Azure przez wartownika, wybierz **łączników danych** a następnie kliknij przycisk **Cloud App Security** kafelka.

2. Kliknij przycisk **Połącz**.

3. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów w usłudze Cloud App Security, możesz wyszukać **SecurityAlert**.


## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia Microsoft Cloud App Security na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
