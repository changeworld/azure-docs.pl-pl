---
title: Utwórz zasady zapory aplikacji sieci web Azure drzwiami frontowymi przy użyciu witryny Azure portal
titlesuffix: Azure web application firewall
description: Dowiedz się, jak utworzyć zasady zapory aplikacji sieci web przy użyciu witryny Azure portal.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 15a80dac0e0601480e22ad960f2827f3d8f290c0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479069"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Utwórz zasadę zapory aplikacji sieci Web Azure drzwiami frontowymi przy użyciu witryny Azure portal

W tym artykule opisano jak utworzyć zasady zapory aplikacji podstawowych sieci web platformy Azure i zastosować je do hosta frontonu na platformie Azure drzwi wejściowe.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil drzwiami frontowymi, wykonując instrukcje opisane w [Szybki Start: Utwórz profil drzwiami frontowymi](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Utwórz zasady zapory aplikacji sieci Web

Najpierw utwórz podstawowe zasady zapory aplikacji sieci Web przy użyciu zarządzanych domyślne reguły zestawu (DRS) przy użyciu portalu. 

1. W lewym górnym rogu ekranu wybierz **Utwórz zasób**> Wyszukaj **zapory aplikacji sieci Web**> Wybierz **zapory aplikacji sieci Web (wersja zapoznawcza)** > Wybierz  **Utwórz**.
2. W **podstawy** karcie **Utwórz zasady zapory aplikacji sieci Web** strony, wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz **przeglądu + Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja            |Wybierz nazwę subskrypcji wejściu.|
    | Grupa zasobów          |Wybierz nazwę grupy zasobów wejściu.|
    | Nazwa zasad             |Wprowadź unikatową nazwę dla zasad zapory aplikacji sieci Web.|

   ![Utwórz zasady zapory aplikacji sieci Web](./media/waf-front-door-create-portal/basic.png)

3. W **skojarzenia** karcie **Utwórz zasady zapory aplikacji sieci Web** wybierz opcję **hosta serwera sieci Web Dodaj**, wprowadź następujące ustawienia, a następnie wybierz **Dodaj**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Drzwi              | Wybierz nazwę profilu wejściu.|
    | Hosta serwera sieci Web           | Wybierz nazwę hosta drzwi wejściowe, a następnie wybierz **Dodaj**.|
    
    > [!NOTE]
    > Jeśli host frontonu jest skojarzony z zasadami zapory aplikacji sieci Web, wyświetlany jest jako wyszarzone. Najpierw usuń hosta serwera sieci Web ze skojarzonych zasad, a następnie ponowne kojarzenie hosta serwera sieci Web do nowych zasad zapory aplikacji sieci Web.
1. Wybierz **przeglądu + Utwórz**, a następnie wybierz **Utwórz**.

## <a name="configure-waf-rules-optional"></a>Konfigurowanie reguł zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Podczas tworzenia zasad zapory aplikacji sieci Web przez zaporę aplikacji sieci Web w domyślnej zasady są w **wykrywania** trybu. W **wykrywania** trybie zapory aplikacji sieci Web nie blokuje wszystkie żądania, zamiast tego żądania pasujące reguły zapory aplikacji sieci Web są rejestrowane w dziennikach zapory aplikacji sieci Web.
Aby zobaczyć zapory aplikacji sieci Web w działaniu, można zmienić ustawienia trybu z **wykrywania** do **zapobiegania**. W **zapobiegania** trybie żądań, że reguły dopasowania, które są zdefiniowane w domyślnych reguł zestawu (DRS) są zablokowane i rejestrowane w dziennikach zapory aplikacji sieci Web.

 ![Tryb zasad zmiany zapory aplikacji sieci Web](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Reguły niestandardowe

Można utworzyć regułę niestandardową, wybierając **dodaj niestandardową regułę** w obszarze **reguły niestandardowe** sekcji. Spowoduje to uruchomienie na stronie konfiguracji reguły niestandardowej. Poniżej przedstawiono przykład konfigurowania niestandardowych regułę blokującą żądania, jeśli ciąg zapytania zawiera **blockme**.

![Tryb zasad zmiany zapory aplikacji sieci Web](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Usługi Azure managed ustawić reguły domyślne jest domyślnie włączona. Aby wyłączyć poszczególnych reguł w ramach grupy reguł, rozwiń węzeł reguł w tej grupie regułę, wybierz opcję **pole wyboru** przed numer reguły, a następnie wybierz pozycję **wyłączyć** na karcie powyżej. Aby zmienić typy akcji dla poszczególnych reguł w ramach reguły jest ustawiona, zaznacz pole wyboru przed numer reguły, a następnie wybierz **zmienić akcję** powyżej kartę.

 ![Zmień zestaw reguł zapory aplikacji sieci Web](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zapory aplikacji sieci web platformy Azure](waf-overview.md).
- Dowiedz się więcej o [Azure drzwiami frontowymi](front-door-overview.md).
