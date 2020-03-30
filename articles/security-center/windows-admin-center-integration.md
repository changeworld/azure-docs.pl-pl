---
title: Jak zintegrować Centrum administracyjne systemu Windows z Centrum zabezpieczeń Platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zintegrować Usługę Azure Security Center z Centrum administracyjnym systemu Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139627"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integracja Centrum zabezpieczeń platformy Azure z Centrum administracyjnym systemu Windows

Centrum administracyjne systemu Windows jest narzędziem do zarządzania serwerami systemu Windows. Jest to pojedyncza lokalizacja dla administratorów systemu, aby uzyskać dostęp do większości najczęściej używanych narzędzi administracyjnych. Z Poziomu Centrum administracyjnego systemu Windows można bezpośrednio dołączać serwery wstępne do usługi Azure Security Center. Następnie można wyświetlić podsumowanie zaleceń dotyczących zabezpieczeń i alertów bezpośrednio w centrum administracyjnym systemu Windows.

> [!NOTE]
> Subskrypcja platformy Azure i skojarzony obszar roboczy usługi Log Analytics muszą mieć włączoną warstwę standardową usługi Security Center, aby umożliwić integrację z Centrum administracyjnym systemu Windows.
> Warstwa standardowa jest bezpłatna przez pierwsze 30 dni, jeśli wcześniej nie była używana w subskrypcji i obszarze roboczym. Aby uzyskać więcej informacji, zobacz [stronę z informacjami o cenach](security-center-pricing.md).
>

Po pomyślnym zainstalowaniu serwera z Centrum administracyjnego systemu Windows do Usługi Azure Security Center można:

* Wyświetlanie alertów i zaleceń dotyczących zabezpieczeń w rozszerzeniu Centrum zabezpieczeń w Centrum administracyjnym systemu Windows
* Wyświetlanie postawy zabezpieczeń i pobieranie dodatkowych szczegółowych informacji o zarządzanych serwerach Centrum administracyjnego systemu Windows w Centrum zabezpieczeń w portalu Azure (lub za pośrednictwem interfejsu API)

Łącząc te dwa narzędzia, usługa Security Center staje się pojedynczym okienkiem szkła w celu wyświetlenia wszystkich informacji zabezpieczających, niezależnie od tego, jaki jest zasób: ochrona zarządzanych serwerów w trybie prem centrum windows, maszyn wirtualnych i dodatkowych obciążeń PaaS.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Dołączanie serwerów zarządzanych Centrum administracyjnego systemu Windows w Centrum zabezpieczeń

1. W Centrum administracyjnym systemu Windows wybierz jeden z serwerów, a następnie w okienku **Narzędzia** wybierz rozszerzenie Usługi Azure Security Center:

    ![Rozszerzenie Centrum zabezpieczeń platformy Azure w Centrum administracyjnym systemu Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Jeśli serwer jest już włączony do usługi Security Center, okno konfiguracji nie zostanie wyświetlone.

1. Kliknij **pozycję Zaloguj się na platformie Azure i skonfiguruj**plik .
    ![Dołączanie rozszerzenia Centrum administracyjnego systemu Windows do Usługi Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Postępuj zgodnie z instrukcjami, aby połączyć serwer z usługą Security Center. Po wprowadzeniu niezbędnych szczegółów i potwierdzeniu usługa Security Center wprowadza niezbędne zmiany konfiguracji, aby upewnić się, że wszystkie poniższe informacje są spełnione:
    * Usługa Azure Gateway jest zarejestrowana.
    * Serwer ma obszar roboczy do raportowania i skojarzoną subskrypcję.
    * Rozwiązanie usługi Security Center do analizy dzienników warstwy standardowej jest włączone w obszarze roboczym. To rozwiązanie zapewnia funkcje warstwy Standardowa usługi Security Center dla *wszystkich* serwerów i maszyn wirtualnych raportowania do tego obszaru roboczego.
    * Usługa Security Center's standard tier pricing for Virtual Machine jest włączona w ramach subskrypcji.
    * Program Microsoft Monitoring Agent (MMA) jest zainstalowany na serwerze i skonfigurowany do raportowania do wybranego obszaru roboczego. Jeśli serwer zgłasza się już do innego obszaru roboczego, jest skonfigurowany do raportowania do nowo wybranego obszaru roboczego.

    > [!NOTE]
    > Może upłynąć trochę czasu po dołączaniu do zaleceń, aby pojawić się. W rzeczywistości, w zależności od aktywności serwera, możesz nie otrzymywać *żadnych* alertów. Aby wygenerować alerty testowe w celu przetestowania alertów działają poprawnie, postępuj zgodnie z instrukcjami zawartymi w [procedurze sprawdzania poprawności alertów.](security-center-alert-validation.md)


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Wyświetlanie zaleceń dotyczących zabezpieczeń i alertów w Centrum administracyjnym systemu Windows

Po wprowadzeniu na pokład możesz wyświetlać alerty i zalecenia bezpośrednio w obszarze Centrum zabezpieczeń platformy Azure w Centrum administracyjnym systemu Windows. Kliknij zalecenie lub alert, aby wyświetlić je w witrynie Azure portal. Tam otrzymasz dodatkowe informacje i dowiesz się, jak rozwiązywać problemy.

[![Zalecenia i alerty usługi Security Center widoczne w Centrum administracyjnym systemu Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Wyświetlanie zaleceń dotyczących zabezpieczeń i alertów dotyczących serwerów zarządzanych Centrum administracyjnego systemu Windows w Centrum zabezpieczeń
Z Usługi Azure Security Center:

* Aby wyświetlić zalecenia dotyczące zabezpieczeń dla wszystkich serwerów Centrum administracyjnego systemu Windows, otwórz **pozycję Aplikacje & obliczeniowe** i kliknij kartę **Maszyny wirtualne i komputery.**

    [![Wyświetlanie zaleceń dotyczących zabezpieczeń serwerów zarządzanych Centrum administracyjnego systemu Windows](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Aby wyświetlić alerty zabezpieczeń dla wszystkich serwerów Centrum administracyjnego systemu Windows, otwórz **okno Alerty zabezpieczeń**. Kliknij **przycisk Filtr** i upewnij **się,** że wybrano tylko opcję "Non-Azure":

    ![Filtrowanie alertów zabezpieczeń dla serwerów zarządzanych Centrum administracyjnego systemu Windows](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Wyświetlanie alertów zabezpieczeń dla serwerów zarządzanych Centrum administracyjnego systemu Windows](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)