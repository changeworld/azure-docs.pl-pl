---
title: Walidacja danych XML — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Sprawdzanie poprawności XML o schematach w scenariuszach Azure Logic Apps i B2B za pomocą pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 901f0e576d28ab163fe2c46dff0594a338fbaf73
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299603"
---
# <a name="validate-xml-for-enterprise-integration"></a>Walidacja danych XML dla integracji przedsiębiorstwa

Często w scenariusze B2B partnerzy umowy należy się upewnić, które wymieniają komunikaty są prawidłowe, przed rozpoczęciem przetwarzania danych. Dokumenty schematem wstępnie zdefiniowanych można sprawdzić za pomocą Użyj łącznika sprawdzanie poprawności kodu XML w pakiet integracyjny dla przedsiębiorstw.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Sprawdź poprawność dokumentu z łącznikiem sprawdzanie poprawności kodu XML

1. Tworzenie aplikacji logiki, i [połączyć aplikację z konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md "Dowiedz się połączyć konto integracji aplikacji logiki") mający schematu, którego chcesz użyć dla sprawdzanie poprawności danych XML.

2. Dodaj **żądania - zostanie odebrane żądanie HTTP podczas** do aplikacji logiki wyzwalacza.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Aby dodać **sprawdzanie poprawności kodu XML** akcji, wybierz **Dodaj akcję**.

4. Aby filtrować wszystkie akcje do tego, który ma, wprowadź *xml* w polu wyszukiwania. Wybierz **sprawdzanie poprawności kodu XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Aby określić, który chcesz zweryfikować zawartość XML, wybierz **zawartości**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Zaznacz tag treści jako zawartości, który chcesz zweryfikować.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Aby określić schematu, którego chcesz użyć do zweryfikowania poprzedniej *zawartości* danych wejściowych, wybierz **nazwy SCHEMATU**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Zapisz swoją pracę  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Wykonuje się teraz w skonfigurowaniu łącznika z weryfikacji. W przypadku aplikacji rzeczywistych można przechowywać dane zweryfikowanych w aplikacji — biznesowych (LOB) takie jak SalesForce. Aby wysłać zweryfikowanych dane wyjściowe do usług Salesforce, Dodaj akcję.

Aby przetestować operację sprawdzania poprawności, należy wysłać żądanie do punktu końcowego HTTP.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")   

