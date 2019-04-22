---
title: Ponownie zapisuje nagłówki żądania i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure Application Gateway do przepisania nagłówków HTTP żądania i odpowiedzi na przekazywanie za pośrednictwem bramy za pomocą witryny Azure portal
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 9d52114f5d01beca53ed48ee2114dc98cc20f3a2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682428"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Ponownie zapisuje nagłówki żądania i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — witryna Azure portal

W tym artykule dowiesz się, jak skonfigurować za pomocą witryny Azure portal [jednostek SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) do przepisania nagłówków HTTP żądania i odpowiedzi.

> [!IMPORTANT]
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musisz mieć usługi Application Gateway w wersji 2 jednostki SKU, ponieważ możliwość ponownego zapisywania nagłówka nie jest obsługiwana dla jednostek SKU v1. Jeśli nie masz jednostki SKU w wersji 2, Utwórz [jednostek SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) przed przystąpieniem do wykonywania.

## <a name="what-is-required-to-rewrite-a-header"></a>Co jest wymagane do przepisania nagłówka

Aby skonfigurować ponownego napisania nagłówka HTTP, konieczne będzie:

1. Utwórz nowe obiekty, które są wymagane do przepisania nagłówków http:

   - **Ponowne zapisywanie adresów akcji**: używany do określenia żądania i pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby. Można skojarzyć więcej warunków ponownego napisania co najmniej jeden element za pomocą akcji ponownego zapisywania.

   - **Ponowne zapisywanie adresów warunek**: Jest opcjonalna konfiguracja. warunek ponownego zapisywania jest dodawany, oceni treści żądania HTTP (S) i odpowiedzi. Decyzja do wykonania akcji ponownego napisania skojarzone z warunkiem ponownego napisania będzie zależeć czy dopasowywane żądania HTTP (S) lub odpowiedzi z warunkiem ponownego zapisywania. 

     Jeśli więcej niż jeden warunki są skojarzone z akcji, a następnie akcję będą wykonywane tylko wtedy, gdy wszystkie warunki są spełnione, czyli, logiczne i będzie można wykonać operacji.

   - **Napisz ponownie reguły**: reguły ponownego pisania zawiera wiele akcji ponownego napisania — Nadpisz kombinacje warunku.

   - **Reguła sekwencji**: ułatwia określenie kolejności, w której poszczególne reguły ponownego zapisywania są wykonywane. Jest to przydatne, gdy wiele reguł ponownego zapisywania znajduje się w zestawie ponownego zapisywania. Reguły ponownego pisania o niższej wartości kolejności reguł pobiera wykonywany jako pierwszy. Możesz podać tę samą sekwencję regułę na dwie reguły ponownego zapisywania kolejność wykonywania zostaną deterministyczna.

   - **Napisz ponownie zestaw**: zawiera wiele reguł ponownego zapisywania, które będą skojarzone reguły routingu żądania.

2. Trzeba będzie można dołączyć Napisz ponownie ustawić za pomocą reguły routingu. Jest to spowodowane konfiguracji ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. W przypadku regułę routingu opartego na ścieżkach Konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. Tak mają zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka http, a każdy zestaw ponownego napisania można zastosować do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

## <a name="configure-header-rewrite"></a>Konfigurowanie nagłówka ponownego napisania

W tym przykładzie zostanie zmodyfikujemy adresu URL przekierowania, zapisując je ponownie nagłówek lokalizacji, w odpowiedzi http wysłanej przez aplikację w wewnętrznej bazie danych. 

1. Wybierz **wszystkie zasoby**, a następnie wybierz pozycję Twojej bramy application gateway.

2. Wybierz **ponownie zapisuje** menu po lewej stronie.

3. Kliknij pozycję **+ Nadpisz zestaw**. 

   ![Dodaj zestaw ponownego napisania](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Podaj nazwę zestawu ponownego zapisywania i skojarzyć ją z reguły routingu:

   - Wprowadź nazwę poprawione w **nazwa** pola tekstowego.
   - Wybierz co najmniej jedną regułę na liście **skojarzone reguły routingu** listy. Można wybrać tylko te reguły, które nie zostały skojarzone z innymi zestawami ponownego zapisywania. Reguły, które zostały już skojarzone z innymi zestawami ponowne zapisywanie adresów będą wyszarzone.
   - Kliknij przycisk Dalej.
   
     ![Dodaj nazwę i skojarzenia](media/rewrite-http-headers-portal/name-and-association.png)

5. Tworzenie reguły ponownego zapisywania:

   - Kliknij pozycję **+ Dodaj regułę ponownego zapisywania**.![ Dodawanie reguły ponownego zapisywania](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Podaj nazwę reguły ponownego pisania w polu tekstowym Nazwa reguły ponownego zapisywania i zapewniają sekwencji reguły.![Dodaj nazwę reguły](media/rewrite-http-headers-portal/rule-name.png)

6. W tym przykładzie firma Microsoft przepisze nagłówek location tylko wtedy, gdy zawiera on odwołanie do "azurewebsites.net". Aby to zrobić, Dodaj warunek do oceny, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net:

   - Kliknij pozycję **+ Dodaj warunek** a następnie kliknij sekcję z **Jeśli** instrukcjami, aby rozwinąć jego![ Dodaj nazwę reguły](media/rewrite-http-headers-portal/add-condition.png)

   - Wybierz **nagłówka HTTP** z **typ zmiennej, aby sprawdzić** listy rozwijanej. 

   - Wybierz **typu nagłówka** jako **odpowiedzi**.

   - Ponieważ w tym przykładzie firma Microsoft ocenia nagłówek lokalizacji, które akurat jest wspólne nagłówka, wybierz opcję **typowego nagłówka** przycisk radiowy jako **nazwę nagłówka**.

   - Wybierz **lokalizacji** z **typowego nagłówka** listy rozwijanej.

   - Wybierz **nie** jako **liter** ustawienie.

   - Wybierz **równości (=)** z **Operator** listy rozwijanej.

   - Wprowadź wzorzec wyrażenia regularnego. W tym przykładzie użyjemy wzorca `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Kliknij przycisk **OK**.

     ![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers-portal/condition.png)

7. Dodawanie akcji do przepisania nagłówek lokalizacji:

   - Wybierz **ustaw** jako **typ akcji**.

   - Wybierz **odpowiedzi** jako **typu nagłówka**.

   - Wybierz **typowego nagłówka** jako **nazwę nagłówka**.

   - Wybierz **lokalizacji** z **typowego nagłówka** listy rozwijanej.

   - Wprowadź wartość nagłówka. W tym przykładzie użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka. Spowoduje to zamianę *azurewebsites.net* z *contoso.com* w nagłówku location.

   - Kliknij przycisk **OK**.

     ![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers-portal/action.png)

8. Kliknij pozycję **Utwórz** utworzyć poprawione zestaw.

   ![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers-portal/create.png)

9. Nastąpi przejście do widoku zestawu ponownego zapisywania. Sprawdź, czy występuje na liście zestawów ponownego napisania zestaw ponownego zapisywania, utworzoną wcześniej.

   ![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat konfiguracji wymagane do wykonania, niektóre typowe przypadki użycia, zobacz [typowego nagłówka Nadpisz scenariuszy](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
