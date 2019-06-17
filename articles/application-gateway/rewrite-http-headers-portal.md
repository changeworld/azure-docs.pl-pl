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
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947163"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Ponownie zapisuje nagłówki żądania i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — witryna Azure portal

W tym artykule opisano sposób konfigurowania przy użyciu witryny Azure portal [jednostek SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) wystąpienia do przepisania nagłówków HTTP żądania i odpowiedzi.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musisz mieć wystąpienia bramy Application Gateway jednostkę SKU v2 wykonanie czynności opisanych w tym artykule. Ponowne napisanie nagłówków nie jest obsługiwana w ramach jednostki SKU v1. Jeśli nie masz jednostki SKU w wersji 2, Utwórz [jednostek SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) wystąpienia przed przystąpieniem do wykonywania.

## <a name="create-required-objects"></a>Utwórz wymagany obiekt

Aby skonfigurować ponownego napisania nagłówka HTTP, należy wykonać następujące czynności.

1. Tworzenie obiektów, które są wymagane do ponownego napisania nagłówka HTTP:

   - **Ponowne zapisywanie adresów akcji**: Służy do określania żądania i pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość dla nagłówków. Można skojarzyć jedną lub więcej warunków za pomocą akcji ponownego napisania ponowne zapisywanie adresów.

   - **Ponowne zapisywanie adresów warunek**: Konfiguracja opcjonalna. Ponowne zapisywanie adresów warunki zostaną obliczone treści żądania HTTP (S) i odpowiedzi. Akcja ponownego napisania wystąpi, jeśli żądania HTTP (S) lub odpowiedzi dopasowuje warunek ponownego zapisywania.

     Jeśli więcej niż jeden warunek jest skojarzona z akcją, akcja jest wykonywana tylko wtedy, gdy są spełnione wszystkie warunki. Innymi słowy operacja jest operacją i logicznych.

   - **Napisz ponownie reguły**: Zawiera wiele akcji ponownego napisania / ponownego zapisywania kombinacje warunku.

   - **Reguła sekwencji**: Ułatwia określenie kolejności, w którym wykonywanie reguły ponownego zapisywania. Ta konfiguracja jest przydatne, jeśli masz wiele reguł ponownego zapisywania w zestawie ponownego zapisywania. Reguły ponownego pisania, który ma mniejszą wartość kolejności reguły jest uruchamiany w pierwszy. Jeśli przypiszesz taką samą wartość w sekwencji regułę na dwie reguły ponownego zapisywania kolejność wykonywania jest niedeterministyczny.

   - **Napisz ponownie zestaw**: Zawiera wiele reguł ponownego zapisywania, które mają zostać skojarzone z regułą routingu żądania.

2. Dołącz Napisz ponownie ustawić reguły routingu. Konfiguracja ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. Gdy używasz regułę routingu opartego na ścieżkach, konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. W takim przypadku zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka HTTP i stosować każdego Napisz ponownie ustawić do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

## <a name="configure-header-rewrite"></a>Konfigurowanie nagłówka ponownego napisania

W tym przykładzie zmodyfikujemy adresem URL przekierowania, zapisując je ponownie nagłówek lokalizacji, w odpowiedzi HTTP wysłanej przez aplikację do zaplecza.

1. Wybierz **wszystkie zasoby**, a następnie wybierz pozycję Twojej bramy application gateway.

2. Wybierz **ponownie zapisuje** w okienku po lewej stronie.

3. Wybierz **Nadpisz zestaw**:

   ![Dodaj zestaw ponownego napisania](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Podaj nazwę dla zestawu ponownego zapisywania i skojarzyć ją z reguły routingu:

   - Wprowadź nazwę dla poprawione w **nazwa** pole.
   - Zaznacz jedną lub więcej reguł na liście **skojarzone reguły routingu** listy. Można wybrać tylko te zasady, które nie zostały skojarzone z innymi zestawami ponownego zapisywania. Reguły, które zostały już skojarzone z innymi zestawami ponownego zapisywania są niedostępne.
   - Wybierz opcję **Dalej**.
   
     ![Dodaj nazwę i skojarzenia](media/rewrite-http-headers-portal/name-and-association.png)

5. Tworzenie reguły ponownego zapisywania:

   - Wybierz **Dodawanie reguły ponownego zapisywania**.

     ![Dodawanie reguły ponownego zapisywania](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Wprowadź nazwę reguły ponownego pisania w **Nazwa reguły ponownego zapisywania** pole. Wprowadź numer w **reguły sekwencji** pole.

     ![Dodaj nazwę reguły ponownego zapisywania](media/rewrite-http-headers-portal/rule-name.png)

6. W tym przykładzie firma Microsoft będzie ponownie zapisać nagłówek location tylko wtedy, gdy zawiera on odwołanie do azurewebsites.net. Aby to zrobić, Dodaj warunek do oceny, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net:

   - Wybierz **Dodaj warunek** , a następnie wybierz pole, zawierający **Jeśli** instrukcjami, aby ją rozwinąć.

     ![Dodaj warunek](media/rewrite-http-headers-portal/add-condition.png)

   - W **typ zmiennej, aby sprawdzić** listy wybierz **nagłówka HTTP**.

   - W **typu nagłówka** listy wybierz **odpowiedzi**.

   - Ponieważ w tym przykładzie firma Microsoft oceniania nagłówek lokalizacji, która jest typowego nagłówka, wybierz **typowego nagłówka** w obszarze **nazwę nagłówka**.

   - W **typowego nagłówka** listy wybierz **lokalizacji**.

   - W obszarze **liter**, wybierz opcję **nie**.

   - W **Operator** listy wybierz **równości (=)** .

   - Wprowadź wzorzec wyrażenia regularnego. W tym przykładzie użyjemy wzorca `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Kliknij przycisk **OK**.

     ![Konfigurowanie If warunku](media/rewrite-http-headers-portal/condition.png)

7. Dodawanie akcji do przepisania nagłówek lokalizacji:

   - W **typ akcji** listy wybierz **ustaw**.

   - W **typu nagłówka** listy wybierz **odpowiedzi**.

   - W obszarze **nazwę nagłówka**, wybierz opcję **typowego nagłówka**.

   - W **typowego nagłówka** listy wybierz **lokalizacji**.

   - Wprowadź wartość nagłówka. W tym przykładzie użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka. Ta wartość spowoduje zastąpienie *azurewebsites.net* z *contoso.com* w nagłówku location.

   - Kliknij przycisk **OK**.

     ![Dodawanie akcji](media/rewrite-http-headers-portal/action.png)

8. Wybierz **Utwórz** utworzyć poprawione zestaw:

   ![Wybierz pozycję Utwórz](media/rewrite-http-headers-portal/create.png)

9. Spowoduje to otwarcie widoku zestawu ponownego zapisywania. Sprawdź, czy zestaw ponowne zapisywanie adresów, który został utworzony na liście zestawów ponownego zapisywania:

   ![Widok zestawu ponownego napisania](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sposobu konfigurowania niektórych typowych przypadków użycia, zobacz [typowego nagłówka Nadpisz scenariuszy](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).