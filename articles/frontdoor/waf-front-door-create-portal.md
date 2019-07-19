---
title: Utwórz zasady zapory aplikacji sieci Web dla drzwi platformy Azure przy użyciu Azure Portal
titlesuffix: Azure web application firewall
description: Dowiedz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) przy użyciu Azure Portal.
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
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846388"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Utwórz zasady WAF dla drzwi platformy Azure przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia podstawowych zasad zapory aplikacji sieci Web (WAF) platformy Azure i stosowania ich do hosta frontonu na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil z drzwiami przednimi, wykonując instrukcje opisane [w przewodniku szybki start: Utwórz profil](quickstart-create-front-door.md)z drzwiem przednim. 

## <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

Najpierw Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu portalu. 

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**> wyszukaj **WAF**> wybierz opcję **Zapora aplikacji sieci Web (wersja zapoznawcza)** > wybierz pozycję **Utwórz**.
2. Na karcie **podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

    | Ustawienie                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription            |Wybierz nazwę subskrypcji z drzwiami przednimi.|
    | Resource group          |Wybierz nazwę grupy zasobów drzwi.|
    | Nazwa zasad             |Wprowadź unikatową nazwę zasad WAF.|

   ![Tworzenie zasad WAF](./media/waf-front-door-create-portal/basic.png)

3. Na karcie **skojarzenie** na stronie **Tworzenie zasad WAF** wybierz pozycję **Dodaj hosta frontonu**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj**:

    | Ustawienie                 | Value                                              |
    | ---                     | ---                                                |
    | Drzwi z przodu              | Wybierz nazwę profilu frontonu.|
    | Host frontonu           | Wybierz nazwę hosta z drzwiami przednimi, a następnie wybierz pozycję **Dodaj**.|
    
    > [!NOTE]
    > Jeśli host frontonu jest skojarzony z zasadami WAF, jest pokazywany jako wyszarzony. Najpierw należy usunąć hosta frontonu ze skojarzonych zasad, a następnie ponownie skojarzyć hosta frontonu z nowymi zasadami WAF.
1. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-waf-rules-optional"></a>Konfigurowanie reguł WAF (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Podczas tworzenia zasad WAFymi domyślne zasady WAF są w trybie **wykrywania** . W trybie **wykrywania** WAF nie blokuje żadnych żądań, a zamiast tego żądania zgodne z regułami WAF są rejestrowane w dziennikach WAF.
Aby wyświetlić WAF w działaniu, można zmienić ustawienia trybu z **wykrywania** na **zapobieganie**. W trybie **zapobiegania** żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 ![Zmień tryb zasad WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Reguły niestandardowe

Regułę niestandardową można utworzyć, wybierając pozycję **Dodaj regułę** niestandardową w sekcji **reguły niestandardowe** . Spowoduje to uruchomienie strony Konfiguracja reguły niestandardowej. Poniżej znajduje się przykład konfigurowania reguły niestandardowej do blokowania żądania, jeśli ciąg zapytania zawiera **blockme**.

![Zmień tryb zasad WAF](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Domyślny zestaw reguł zarządzany przez platformę Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz **pole wyboru** przed numerem reguły, a następnie na karcie powyżej wybierz pozycję **Wyłącz** . Aby zmienić typy akcji dla poszczególnych reguł w zestawie reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz powyższą kartę **Zmień akcję** .

 ![Zmień zestaw reguł WAF](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](waf-overview.md).
- Dowiedz się więcej na temat [zewnętrznych drzwi platformy Azure](front-door-overview.md).
