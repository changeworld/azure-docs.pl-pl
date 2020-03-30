---
title: Przepisywanie nagłówków żądań i odpowiedzi HTTP w portalu — brama aplikacji platformy Azure
description: Dowiedz się, jak skonfigurować bramę aplikacji platformy Azure do ponownego zapisu nagłówków HTTP w żądaniach i odpowiedziach przekazywanych przez bramę
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012856"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Przepisywanie nagłówków żądań i odpowiedzi HTTP za pomocą bramy aplikacji platformy Azure — witryna Azure portal

W tym artykule opisano sposób używania witryny Azure Portal do konfigurowania wystąpienia [jednostki SKU bramy aplikacji](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) w wersji 2 w celu ponownego ustawienia nagłówków HTTP w żądaniach i odpowiedziach.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, musisz mieć wystąpienie jednostki SKU bramy aplikacji w wersji 2. Przepisywanie nagłówków nie jest obsługiwane w jednostce SKU w wersji 1. Jeśli nie masz jednostki SKU w wersji 2, przed rozpoczęciem należy utworzyć wystąpienie [jednostki SKU bramy aplikacji w wersji 2.](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)

## <a name="create-required-objects"></a>Tworzenie wymaganych obiektów

Aby skonfigurować przepisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty, które są wymagane do ponownego zapisu nagłówka HTTP:

   - **Ponownie przepisz akcję:** Służy do określania pól nagłówka żądania i żądania, które mają zostać przepisane, oraz nowej wartości nagłówków. Można skojarzyć jeden lub więcej warunków przepisywania z akcją ponownego zapisu.

   - **Warunek przepisania:** Konfiguracja opcjonalna. Przepisz warunki oceny zawartości żądań i odpowiedzi HTTP(S). Akcja ponownego zapisu nastąpi, jeśli żądanie HTTP(S) lub odpowiedź jest zgodna z warunkiem ponownego zapisu.

     Jeśli skojarzysz więcej niż jeden warunek z akcją, akcja występuje tylko wtedy, gdy spełnione są wszystkie warunki. Innymi słowy operacja jest logiczną operacją AND.

   - **Przepisz regułę**: Zawiera wiele kombinacji warunków przepisywania akcji/ przepisywania.

   - **Sekwencja reguł:** Pomaga określić kolejność wykonywania reguł ponownego zapisu. Ta konfiguracja jest przydatna, gdy masz wiele reguł przepisywania w zestawie przepisywania. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli przypisać tę samą wartość sekwencji reguły do dwóch reguł ponownego zapisu, kolejność wykonywania jest niedeterministyczne.

   - **Przepisz zestaw:** Zawiera wiele reguł przepisywania, które będą skojarzone z regułą routingu żądań.

2. Dołącz zestaw ponownego zapisu do reguły routingu. Konfiguracja ponownego zapisu jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu konfiguracja ponownego zapisu nagłówka jest skojarzona z odbiornikiem źródłowym i jest regułą na nowo mówiącą o nagłówku globalnym. W przypadku korzystania z reguły routingu opartej na ścieżce konfiguracja przepisywania nagłówka jest definiowana na mapie ścieżki adresu URL. W takim przypadku ma ona zastosowanie tylko do określonego obszaru ścieżki witryny.

Można utworzyć wiele zestawów ponownego zapisu nagłówka HTTP i zastosować każdy zestaw ponownego zapisu do wielu odbiorników. Ale można zastosować tylko jeden zestaw przepisać do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą konta platformy Azure.

## <a name="configure-header-rewrite"></a>Konfigurowanie przepisywania nagłówka

W tym przykładzie zmodyfikujemy adres URL przekierowania, przepisując nagłówek lokalizacji w odpowiedzi HTTP wysyłanej przez aplikację zaplecza.

1. Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz bramę aplikacji.

2. Wybierz **pozycję Przepisuje** w lewym okienku.

3. Wybierz **opcję Przepisz zestaw:**

   ![Dodaj zestaw przepisać](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Podaj nazwę zestawu ponownego zapisu i skojarz go z regułą routingu:

   - Wprowadź nazwę zestawu ponownego zapisu w polu **Nazwa.**
   - Wybierz jedną lub więcej reguł wymienionych na liście **Skojarzone reguły routingu.** Można wybrać tylko reguły, które nie zostały skojarzone z innymi zestawami ponownego zapisu. Reguły, które zostały już skojarzone z innymi zestawami przepisywania są wyszarzone.
   - Wybierz **pozycję Dalej**.
   
     ![Dodawanie nazwy i skojarzenia](media/rewrite-http-headers-portal/name-and-association.png)

5. Utwórz regułę przepisywania:

   - Wybierz **pozycję Dodaj regułę przepisywania**.

     ![Dodaj regułę przepisywania](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Wprowadź nazwę reguły ponownego przepisywania w polu **Nazwa reguły Przepisywanie.** Wprowadź liczbę w polu **Sekwencja reguł.**

     ![Dodawanie nazwy reguły namysłaj](media/rewrite-http-headers-portal/rule-name.png)

6. W tym przykładzie będziemy przepisywać nagłówek lokalizacji tylko wtedy, gdy zawiera odwołanie do azurewebsites.net. Aby to zrobić, dodaj warunek, aby ocenić, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net:

   - Wybierz **pozycję Dodaj warunek,** a następnie zaznacz pole zawierające instrukcje **If,** aby go rozwinąć.

     ![Dodawanie warunku](media/rewrite-http-headers-portal/add-condition.png)

   - Na liście **Typ zmiennej do sprawdzenia** wybierz nagłówek **HTTP**.

   - Na liście **Typ nagłówka** wybierz pozycję **Odpowiedź**.

   - Ponieważ w tym przykładzie oceniamy nagłówek lokalizacji, który jest wspólnym nagłówkiem, wybierz **pozycję Wspólny nagłówek** w obszarze Nazwa **nagłówka**.

   - Na liście **Nagłówek Wspólny** wybierz pozycję **Lokalizacja**.

   - W obszarze **Rozróżniana wielkość liter**wybierz pozycję **Nie**.

   - Na liście **Operator** wybierz **equal (=)**.

   - Wprowadź wzorzec wyrażenia regularnego. W tym przykładzie użyjemy `(https?):\/\/.*azurewebsites\.net(.*)$`wzorca .

   - Kliknij przycisk **OK**.

     ![Konfigurowanie warunku If](media/rewrite-http-headers-portal/condition.png)

7. Dodaj akcję, aby przepisać nagłówek lokalizacji:

   - Na liście **Typ akcji** wybierz pozycję **Ustaw**.

   - Na liście **Typ nagłówka** wybierz pozycję **Odpowiedź**.

   - W **obszarze Nazwa nagłówka**wybierz pozycję **Nagłówek wspólny**.

   - Na liście **Nagłówek Wspólny** wybierz pozycję **Lokalizacja**.

   - Wprowadź wartość nagłówka. W tym przykładzie użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartości nagłówka. Ta wartość zastąpi *azurewebsites.net* *contoso.com* w nagłówku lokalizacji.

   - Kliknij przycisk **OK**.

     ![Dodawanie akcji](media/rewrite-http-headers-portal/action.png)

8. Wybierz **pozycję Utwórz,** aby utworzyć zestaw ponownego zapisu:

   ![Wybierz pozycję Utwórz](media/rewrite-http-headers-portal/create.png)

9. Zostanie otwarty widok zestawu Przepisanie. Sprawdź, czy utworzony zestaw ponownego zapisu znajduje się na liście zestawów przepisywania:

   ![Przepisanie widoku zestawu](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu niektórych typowych przypadków użycia, zobacz [typowe scenariusze przepisywania nagłówka](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).