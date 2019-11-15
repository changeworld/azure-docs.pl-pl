---
title: Importowanie interfejsu API protokołu SOAP przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak zaimportować interfejs API protokołu SOAP za pomocą usługi API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 359b90cc434dad04fc0296c54fcc762f3a75062d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107662"
---
# <a name="import-soap-api"></a>Importowanie interfejsu API protokołu SOAP

W tym artykule przedstawiono sposób importowania standardowej reprezentacji kodu XML dla interfejsu API protokołu SOAP. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie interfejsu API protokołu SOAP
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz **Interfejsy API** w obszarze **ZARZĄDZANIE INTERFEJSAMI API**.
2. Wybierz pozycję **WSDL** z listy **Dodaj nowy interfejs API**.

    ![interfejs api protokołu soap](./media/import-soap-api/wsdl-api.png)
3. W obszarze **Specyfikacja WSDL** wprowadź adres URL, pod którym znajduje się Twój interfejs API protokołu SOAP.
4. Przycisk radiowy **Przekazywanie protokołu SOAP** jest wybierany domyślnie. Ten wybór spowoduje, że interfejs API będzie uwidaczniany jako protokół SOAP. Użytkownik musi stosować reguły protokołu SOAP. Jeśli chcesz przekształcić interfejs API przy użyciu stylu REST, postępuj zgodnie z instrukcjami podanymi w temacie [Import a SOAP API and convert it to REST](restify-soap-api.md) (Importowanie interfejsu API protokołu SOAP i konwertowanie go do stylu REST).

    ![Przekazywanie](./media/import-soap-api/pass-through.png)
5. Naciśnij klawisz Tab.

    Następujące pola są wypełniane przy użyciu informacji z interfejsu API protokołu SOAP: Nazwa wyświetlana, Nazwa, Opis.
6. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi APIM. Musi ona być unikatowa w tym wystąpieniu usługi APIM.
9. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „*Nieograniczony*”.  Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Unlimited (nieograniczony)**   
10. Wybierz pozycję **Utwórz**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testowanie nowego interfejsu API w portalu administracyjnym

Operacje mogą być wywoływane bezpośrednio z portalu administracyjnego, który zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc klucz zostanie uzupełniony automatycznie. 
1. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)