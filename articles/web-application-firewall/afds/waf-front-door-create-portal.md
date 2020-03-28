---
title: 'Samouczek: Tworzenie zasad WAF dla drzwi y frontowej platformy Azure — witryna Azure portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) przy użyciu witryny Azure portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475845"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Samouczek: Tworzenie zasad zapory aplikacji sieci Web w drzwiach frontowych platformy Azure przy użyciu portalu Azure

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web platformy Azure (WAF) i zastosować ją do hosta front-end w usłudze Azure Front Door.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad WAF
> * Skojarz go z hostem frontendu
> * Konfigurowanie reguł WAF

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil drzwi przednich, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Utwórz profil drzwi przednich](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Tworzenie zasad Zapory aplikacji sieci Web

Najpierw utwórz podstawową zasadę WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu portalu. 

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**>wyszukaj **>** wybierz **pozycję Zapora aplikacji sieci Web (Podgląd)** > wybierz pozycję **Utwórz**.
2. Na karcie **Podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **pozycję Przejrzyj + utwórz:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja            |Wybierz nazwę subskrypcji drzwiami frontowymi.|
    | Grupa zasobów          |Wybierz nazwę grupy zasobów drzwiami frontowymi.|
    | Nazwa zasady             |Wprowadź unikatową nazwę zasad WAF.|

   ![Tworzenie zasad WAF](../media/waf-front-door-create-portal/basic.png)

3. Na karcie **Skojarzenia** na stronie **Tworzenie zasad WAF** wybierz pozycję **Dodaj hosta frontendu**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Drzwi              | Wybierz nazwę profilu drzwi eks-owych.|
    | Host frontendu           | Wybierz nazwę hosta drzwi wejściowych, a następnie wybierz pozycję **Dodaj**.|
    
    > [!NOTE]
    > Jeśli host frontendu jest skojarzony z zasadami WAF, jest wyświetlany jako wyszarzony. Najpierw należy usunąć hosta frontendu z skojarzonych zasad, a następnie ponownie skojarzyć hosta frontendu z nową zasadą WAF.
1. Wybierz **pozycję Recenzja + utwórz**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurowanie reguł Zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Podczas tworzenia zasad WAF, przez domyślne zasady WAF jest w **trybie wykrywania.** W trybie **wykrywania** WAF nie blokuje żadnych żądań, zamiast tego żądania pasujące do reguł WAF są rejestrowane w dziennikach WAF.
Aby wyświetlić w trybie akcji w akcję WAF, można zmienić ustawienia trybu z **Wykrywanie** na **Zapobieganie**. W trybie **zapobiegania** żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 ![Zmienianie trybu zasad WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Reguły niestandardowe

Regułę niestandardową można utworzyć, wybierając pozycję **Dodaj regułę niestandardową** w sekcji **Reguły niestandardowe.** Spowoduje to uruchomienie strony konfiguracji reguły niestandardowej. Poniżej znajduje się przykład konfigurowania reguły niestandardowej w celu blokowania żądania, jeśli ciąg zapytania zawiera **plik blockme**.

![Zmienianie trybu zasad WAF](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Domyślny zestaw reguł zarządzanych przez platformę Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz **pole wyboru** przed numerem reguły i wybierz **pozycję Wyłącz** na powyższej karcie. Aby zmienić typy akcji dla poszczególnych reguł w ramach zestawu reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz kartę **Zmień akcję** powyżej.

 ![Zmienianie zestawu reguł WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Zaporze](../overview.md)
> aplikacji sieci Web platformy[Azure Dowiedz się więcej o drzwiach frontowych platformy Azure](../../frontdoor/front-door-overview.md)