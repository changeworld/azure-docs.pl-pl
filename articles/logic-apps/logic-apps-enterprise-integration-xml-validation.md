---
title: Walidacja danych XML dla integracji dla przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Walidacja danych XML przy użyciu schematów dla rozwiązań B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996336"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Walidacja danych XML dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Często w scenariuszy B2B partnerów w umowie musi upewnij się, że wymieniają komunikaty są prawidłowe, przed rozpoczęciem przetwarzania danych. Aby walidować dokumenty względem wstępnie zdefiniowanego schematu, należy przy użyciu łącznika sprawdzanie poprawności kodu XML w pakiet integracyjny dla przedsiębiorstw.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Sprawdź poprawność dokumentu za pomocą łącznika sprawdzanie poprawności kodu XML

1. Tworzenie aplikacji logiki i [połączyć aplikację z konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji aplikacji logiki") zawierającego schemat, którego chcesz użyć do weryfikacji danych XML.

2. Dodaj **żądania — zostanie odebrane żądanie HTTP podczas** wyzwalacz aplikacji logiki.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Aby dodać **sprawdzanie poprawności kodu XML** akcji, wybierz **Dodaj akcję**.

4. Aby odfiltrować wszystkie akcje do tego, który ma, wprowadź *xml* w polu wyszukiwania. Wybierz **sprawdzanie poprawności kodu XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Aby określić zawartość XML, który chcesz zweryfikować, zaznacz **zawartości**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Wybierz tag treści jako zawartość, którą chcesz zweryfikować.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Określić schemat, którego chcesz użyć do zweryfikowania poprzedniego *zawartości* danych wejściowych, wybierz polecenie **nazwa SCHEMATU**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Zapisz swoją pracę  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Teraz są wykonywane tylko z konfiguracją łącznika sprawdzania poprawności. W przypadku aplikacji rzeczywistych można przechowywać zweryfikowanych danych w aplikacji (LOB) line-of-business, takich jak SalesForce. Aby wysłać zweryfikowanych danych wyjściowych do usługi Salesforce, Dodaj akcję.

Aby przetestować akcję sprawdzania poprawności, należy wysłać żądanie do punktu końcowego HTTP.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")   

