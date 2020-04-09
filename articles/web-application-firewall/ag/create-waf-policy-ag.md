---
title: Tworzenie zasad zapory aplikacji sieci Web (WAF) dla bramy aplikacji
description: Dowiedz się, jak utworzyć zasady zapory aplikacji sieci Web dla bramy aplikacji.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886929"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Tworzenie zasad zapory aplikacji sieci Web dla bramy aplikacji

Kojarzenie zasad WAF z detektorami umożliwia wiele witryn za jednym WAF być chronione przez różne zasady. Na przykład jeśli za waf w sieci WAF znajduje się pięć oddzielnych zasad WAF (po jednej dla każdego odbiornika), aby dostosować wykluczenia, reguły niestandardowe i zarządzane zestawy reguł dla jednej witryny bez konieczności wprowadzania innych czterech. Jeśli chcesz, aby jedna zasada miała zastosowanie do wszystkich witryn, możesz po prostu skojarzyć zasady z bramą aplikacji, a nie z poszczególnymi odbiornikami, aby była stosowana globalnie. Zasady można również zastosować do reguły routingu opartej na ścieżce. 

Możesz wprowadzić dowolną liczbę zasad. Po utworzeniu zasad musi być skojarzony z bramą aplikacji, aby wejść w życie, ale może być skojarzony z dowolną kombinacją bram aplikacji i odbiorników. 

Jeśli brama aplikacji ma zastosowaną zasadę, a następnie zastosuj inną zasadę do odbiornika w tej bramie aplikacji, zasady odbiornika zostaną zastosowane, ale tylko dla odbiorników, do których są przypisane. Zasady bramy aplikacji nadal ma zastosowanie do wszystkich innych odbiorników, które nie mają określonej zasady przypisane do nich. 

   > [!NOTE]
   > Zasady WAF dla witryn i identyfikatorów URI są w publicznej wersji zapoznawczej. Oznacza to, że ta funkcja podlega dodatkowym Warunkom użytkowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
   > [!NOTE]
   > Gdy zasady zapory jest skojarzony z WAF, zawsze musi być zasady skojarzone z tym WAF. Możesz zastąpić tę zasadę, ale całkowite odłączenie zasad od WAF nie jest obsługiwane. 

Wszystkie nowe ustawienia WAF zapory aplikacji sieci Web (reguły niestandardowe, zarządzane konfiguracje rulset, wykluczenia itp.) znajdują się wewnątrz zasad WAF. Jeśli masz istniejący WAF, te ustawienia mogą nadal istnieć w konfiguracji WAF. Aby uzyskać instrukcje dotyczące przechodzenia do nowych zasad WAF, zobacz [Migrowanie configa WAF do zasad WAF](#migrate) w dalszej części tego artykułu. 

## <a name="create-a-policy"></a>Tworzenie zasad

Najpierw utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu witryny Azure portal.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**. Wyszukaj **pozycję WAF**, wybierz **pozycję Zapora aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.
2. Na stronie **Tworzenie zasad WAF,** karta **Podstawy,** wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **pozycję Przejrzyj + utwórz:**

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Polityka dotycząca     |Regionalny WAF (brama aplikacji)|
   |Subskrypcja     |Wybierz nazwę subskrypcji|
   |Grupa zasobów     |Wybieranie grupy zasobów|
   |Nazwa zasady     |Wpisz unikatową nazwę zasad WAF.|
3. Na karcie **Skojarzenie** wprowadź jedno z następujących ustawień, a następnie wybierz pozycję **Dodaj:**

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Skojarz bramę aplikacji     |Wybierz nazwę profilu bramy aplikacji.|
   |Skojarzyć słuchaczy     |Wybierz nazwę odbiornika bramy aplikacji, a następnie wybierz pozycję **Dodaj**.|

   > [!NOTE]
   > Jeśli zostanie przypisana zasada do bramy aplikacji (lub odbiornika), która ma już zasadę, oryginalna zasada zostanie zastąpiona i zastąpiona nowymi zasadami.
4. Wybierz **pozycję Recenzja + utwórz**, a następnie wybierz pozycję **Utwórz**.

   ![Podstawy polityki WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurowanie reguł WAF (opcjonalnie)

Podczas tworzenia zasad WAF, domyślnie jest w *trybie wykrywania.* W trybie wykrywania WAF nie blokuje żadnych żądań. Zamiast tego pasujące reguły WAF są rejestrowane w dziennikach WAF. Aby wyświetlić w akcję w trybie WAF, można zmienić ustawienia trybu na *Zapobieganie*. W trybie zapobiegania reguły dopasowywania zdefiniowane w wybranym forsie CRS są blokowane i/lub rejestrowane w dziennikach WAF.

## <a name="managed-rules"></a>Reguły zarządzane

Reguły owasp zarządzane przez platformę Azure są domyślnie włączone. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz pole wyboru przed numerem reguły i wybierz **pozycję Wyłącz** na powyższej karcie.

[![Reguły](../media/create-waf-policy-ag/managed-rules.png) zarządzane](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Reguły niestandardowe

Aby utworzyć regułę niestandardową, wybierz pozycję **Dodaj regułę niestandardową** na karcie **Reguły niestandardowe.** Spowoduje to otwarcie strony konfiguracji reguły niestandardowej. Na poniższym zrzucie ekranu przedstawiono przykładową regułę niestandardową skonfigurowaną do blokowania żądania, jeśli ciąg zapytania zawiera plik *blockme*tekstu .

[![Edytowanie](../media/create-waf-policy-ag/edit-custom-rule.png) reguły niestandardowej](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrowanie konfiguracji WAF do zasad WAF

Jeśli masz istniejący WAF, być może zauważyłeś pewne zmiany w portalu. Najpierw musisz określić, jaki rodzaj zasad został włączony w WAF. Istnieją trzy potencjalne stany:

- Brak zasad WAF
- Zasady tylko reguły niestandardowe
- Polityka WAF

Możesz sprawdzić, w jakim stanie znajduje się WAF, patrząc na to w portalu. Jeśli ustawienia WAF są widoczne i można je zmienić z poziomu widoku bramy aplikacji, waf jest w stanie 1.

[![Konfiguracja](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Jeśli wybierzesz **Zaporę aplikacji sieci Web** i pojawi się skojarzona zasada, WAF jest w stanie 2 lub stanie 3. Po przejściu do zasad, jeśli pokazuje **tylko** reguły niestandardowe i skojarzone bramy aplikacji, to jest to reguły niestandardowe tylko zasady.

![Reguły niestandardowe zapory aplikacji internetowej](../media/create-waf-policy-ag/waf-custom-rules.png)

Jeśli jest również wyświetlany ustawienia zasad i reguły zarządzane, jest to pełna zasada zapory aplikacji sieci Web. 

![Ustawienia zasad WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migracja do zasad WAF

Jeśli masz reguły niestandardowe tylko zasady WAF, możesz chcieć przejść do nowych zasad WAF. W przyszłości zasady zapory będą obsługiwać ustawienia zasad WAF, zarządzane zestawy reguł, wykluczenia i wyłączone grupy reguł. Zasadniczo wszystkie konfiguracje WAF, które zostały wcześniej wykonane wewnątrz bramy aplikacji są teraz wykonywane za pośrednictwem zasad WAF. 

Zmiany reguły niestandardowej tylko zasady WAF są wyłączone. Aby edytować wszystkie ustawienia WAF, takie jak wyłączanie reguł, dodawanie wykluczeń itp., należy przeprowadzić migrację do nowego zasobu zasad zapory najwyższego poziomu.

W tym celu należy utworzyć *zasady zapory aplikacji sieci Web* i skojarzyć ją z bramami aplikacji i wybranymi odbiornikami. Ta nowa Polityka **musi** być dokładnie taka sama jak bieżąca reguła WAF, co oznacza, że każda reguła niestandardowa, wykluczenie, wyłączona reguła itp. Po dokonaniu zasad skojarzonych z bramą aplikacji można nadal wprowadzać zmiany w regułach i ustawieniach usługi WAF. Można to również zrobić za pomocą programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Kojarzenie zasad WAF z istniejącą bramą aplikacji](associate-waf-policy-existing-gateway.md).

Opcjonalnie można użyć skryptu migracji do migracji do zasad WAF. Aby uzyskać więcej informacji, zobacz [Migrowanie zasad Zapory aplikacji sieci Web przy użyciu programu Azure PowerShell](migrate-policy.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [grupach i regułach crs zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).
