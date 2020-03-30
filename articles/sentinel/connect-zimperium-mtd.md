---
title: Połącz Zimperium Mobile Threat Defense z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć usługę Zimperium Mobile Threat Defense z usługą Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587944"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Połącz swoją mobilną obronę przed zagrożeniami Zimperium z usługą Azure Sentinel


> [!IMPORTANT]
> Zimperium Mobile Threat Defense łącznika danych w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Zimperium Mobile Threat Defense connector daje możliwość połączenia dziennika zagrożeń Zimperium z usługi Azure Sentinel, aby wyświetlić pulpity nawigacyjne, tworzyć niestandardowe alerty i usprawnić badania. Zapewnia to lepszy wgląd w krajobraz zagrożeń mobilnych w organizacji i zwiększa możliwości operacji zabezpieczeń.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurowanie i podłączanie Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense może integrować i eksportować dzienniki bezpośrednio do **usługi Azure Sentinel.**

1. W portalu Azure Sentinel kliknij pozycję Łączniki danych i wybierz pozycję **Zimperium Mobile Threat Defense**.
2. Wybierz **pozycję Otwórz stronę łącznika**.
3. Postępuj zgodnie z instrukcjami na stronie **złącza Zimperium Mobile Threat Defense,** podsumowane w następujący sposób.
 1. W aplikacji zConsole kliknij pozycję **Zarządzaj** na pasku nawigacyjnym.
 2. Kliknij kartę **Integrations** (Integracje).
 3. Kliknij przycisk **Raportowanie zagrożeń,** a następnie przycisk **Dodaj integracje.**
 4. Utwórz integrację, wybierając **usługę Microsoft Azure Sentinel** z dostępnych integracji i wprowadź identyfikator obszaru roboczego i klucz podstawowy, aby połączyć się z usługą Azure Sentinel.
 5. Dostępna jest również opcja wyboru poziomu filtru dla danych o zagrożeniach do wypychania do usługi Azure Sentinel. 

4. Dodatkowe informacje można znaleźć w [portalu obsługi klienta Zimperium.](https://support.zimperium.com)


## <a name="find-your-data"></a>Znajdowanie danych

Po nawiązaniu pomyślnego połączenia dane są wyświetlane w usłudze Log Analytics w obszarze Dzienniki niestandardowe ZimperiumThreatLog_CL i ZimperiumMitigationLog_CL.

Aby użyć odpowiedniego schematu w usłudze Log Analytics dla Zimperium Mobile Threat Defense, wyszukaj ZimperiumThreatLog_CL i ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć Zimperium Mobile Threat Defense z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:

- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)

- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

Aby dowiedzieć się więcej o Zimperium, zobacz następujące elementy:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Bezpieczeństwa Blog](https://blog.zimperium.com)

- [Portal obsługi klienta Zimperium](https://support.zimperium.com)

