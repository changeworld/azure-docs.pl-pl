---
title: Zbieranie danych usługi Azure ATP w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane z usługi Azure ATP w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 154af3988084792331db082b99cae0ae06126f1b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242177"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Zbieranie danych z usługi Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Można przesyłać strumieniowo dzienniki z [usługi Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) do platformy Azure przez wartownika za pomocą jednego kliknięcia.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z administratorem globalnym lub uprawnienia administratora zabezpieczeń
- Musi być klientem prywatnej wersji zapoznawczej narzędzia Azure ATP

## <a name="connect-to-azure-atp"></a>Łączenie do usługi Azure ATP

Upewnij się, wersja prywatnej wersji zapoznawczej usługi Azure ATP jest [włączone w sieci](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Jeśli wdrożono narzędzia Azure ATP i zbierać dane, podejrzanych alertów można łatwo przesyłane strumieniowo do usługi Azure przez wartownika. Może upłynąć do 24 godzin dla alertów rozpocząć przesyłanie strumieniowe do usługi Azure przez wartownika.



1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **narzędzia Azure ATP** kafelka.

2. Kliknij przycisk **Połącz**.


## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure Advanced Threat Protection na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

