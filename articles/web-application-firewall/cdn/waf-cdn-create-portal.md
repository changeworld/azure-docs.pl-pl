---
title: 'Samouczek: Tworzenie zasad WAF dla usługi Azure CDN — witryna Azure portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) w usłudze Azure CDN przy użyciu portalu Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485594"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Samouczek: Tworzenie zasad WAF w usłudze Azure CDN przy użyciu portalu Azure

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web platformy Azure (WAF) i zastosować ją do punktu końcowego w usłudze Azure Content Delivery Network (CDN).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad WAF
> * Skojarzenie go z punktem końcowym sieci CDN
> * Konfigurowanie reguł WAF

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil i punkt końcowy usługi Azure CDN, postępując zgodnie z instrukcjami w [przewodniku Szybki start: Tworzenie profilu i punktu końcowego usługi Azure CDN.](../../cdn/cdn-create-new-endpoint.md) 

## <a name="create-a-web-application-firewall-policy"></a>Tworzenie zasad Zapory aplikacji sieci Web

Najpierw utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) za pomocą portalu.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**>wyszukaj **pozycję WAF**>wybierz pozycję **Zapora aplikacji sieci Web** > wybierz pozycję **Utwórz**.
2. Na karcie **Podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **pozycję Przejrzyj + utwórz:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Polityka dotycząca            |Wybierz usługę Azure CDN (wersja zapoznawcza).|
    | Subskrypcja            |Wybierz nazwę subskrypcji drzwiami frontowymi.|
    | Grupa zasobów          |Wybierz nazwę grupy zasobów drzwiami frontowymi.|
    | Nazwa zasady             |Wprowadź unikatową nazwę zasad WAF.|

   ![Tworzenie zasad WAF](../media/waf-cdn-create-portal/basic.png)

3. Na karcie **Skojarzenia** na stronie **Tworzenie zasad WAF** wybierz pozycję **Dodaj punkt końcowy sieci CDN**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Profil CDN              | Wybierz nazwę profilu sieci CDN.|
    | Endpoint           | Wybierz nazwę punktu końcowego, a następnie wybierz pozycję **Dodaj**.|
    
    > [!NOTE]
    > Jeśli punkt końcowy jest skojarzony z zasadami WAF, jest wyświetlany wyszarzony. Najpierw należy usunąć punkt końcowy z skojarzonych zasad, a następnie ponownie skojarzyć punkt końcowy z nową zasadą WAF.
1. Wybierz **pozycję Recenzja + utwórz**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurowanie zasad Zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Domyślnie zasady WAF jest w *trybie wykrywania* podczas tworzenia zasad WAF. W trybie *wykrywania* WAF nie blokuje żadnych żądań. Zamiast tego żądania pasujące do reguł WAF są rejestrowane w dziennikach WAF.

Aby wyświetlić w trybie akcji w akcję WAF, można zmienić ustawienia trybu z *Wykrywanie* na *Zapobieganie*. W trybie *zapobiegania* żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 ![Zmienianie trybu zasad WAF](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Reguły niestandardowe

Aby utworzyć regułę niestandardową, wybierz pozycję **Dodaj regułę niestandardową** w sekcji **Reguły niestandardowe.** Spowoduje to otwarcie strony konfiguracji reguły niestandardowej. Istnieją dwa typy reguł niestandardowych: **reguła dopasowania** i **reguła limitu szybkości.**

Poniższy zrzut ekranu przedstawia niestandardową regułę dopasowania, aby zablokować żądanie, jeśli ciąg zapytania zawiera **blok**wartości .

![Zmienianie trybu zasad WAF](../media/waf-cdn-create-portal/custommatch.png)

Reguły limitu stawek wymagają dwóch dodatkowych pól: **Czas trwania limitu szybkości** i próg limitu szybkości **(żądania),** jak pokazano w poniższym przykładzie:

![Zmienianie trybu zasad WAF](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Zarządzany domyślny zestaw reguł platformy Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz pole wyboru przed numerem reguły i wybierz **pozycję Wyłącz** na powyższej karcie. Aby zmienić typy akcji dla poszczególnych reguł w ramach zestawu reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz kartę **Zmień akcję** powyżej.

 ![Zmienianie zestawu reguł WAF](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Zaporze aplikacji sieci Web platformy Azure](../overview.md)
