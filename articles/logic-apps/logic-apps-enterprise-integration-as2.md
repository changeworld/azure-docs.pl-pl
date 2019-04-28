---
title: Komunikaty AS2 integracji przedsiębiorstw B2B — Azure Logic Apps
description: Wymienianie komunikatów AS2 w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760169"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymienianie komunikatów AS2 integracji przedsiębiorstw B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Aby pracować z komunikatów AS2 w usłudze Azure Logic Apps, można użyć łącznika AS2, który zawiera wyzwalacze i akcje dla zarządzania komunikacją AS2. Na przykład aby ustanowić bezpieczeństwa i niezawodności, podczas przesyłania wiadomości, służy te akcje:

* [**Koduj jako komunikat AS2** akcji](#encode) zapewniające szyfrowania, cyfrowe podpisywanie i Potwierdzanie za pośrednictwem komunikatu powiadomienia dyspozycji (MDN) pomocy bez odrzucania obsługi. Na przykład ta akcja ma zastosowanie AS2/HTTP, nagłówków i wykonuje te zadania, w przypadku skonfigurowania:

  * Objawy komunikatów wychodzących.
  * Szyfruje komunikaty wychodzące.
  * Kompresuje wiadomości.
  * Przesyła nazwę pliku w nagłówku MIME.

* [**Dekoduj komunikat AS2** akcji](#decode) zapewniające odszyfrowywania cyfrowe podpisywanie i Potwierdzanie za pośrednictwem komunikatu powiadomienia dyspozycji (MDN). Na przykład ta akcja powoduje wykonanie następujących zadań: 

  * Przetwarza nagłówków AS2/HTTP.
  * Uzgadnia odebranych komunikatów Mdn z oryginalnej wiadomości wychodzących.
  * Aktualizacje i koreluje uznawania bazy danych.
  * Zapisuje rekordy dla raportowania stanu AS2.
  * Treści ładunek danych wyjściowych algorytmem Base64.
  * Określa, czy wymagane są komunikatów Mdn. AS2 — na podstawie umowy, określa, czy komunikatów Mdn powinny być synchroniczna lub asynchroniczna.
  * Generuje synchronicznych lub asynchronicznych komunikatów Mdn, w oparciu o umowy AS2.
  * Ustawia właściwości i tokenów korelacji komunikatów Mdn.

  Ta akcja powoduje również wykonanie tych zadań w przypadku skonfigurowania:

  * Weryfikuje podpis.
  * Odszyfrowuje komunikaty.
  * Dekompresuje wiadomości. 
  * Sprawdź, a nie zezwalaj na duplikaty identyfikatorów komunikatów.

W tym artykule przedstawiono sposób dodawania kodowanie AS2 i dekodowania akcji do istniejącej aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, z której chcesz użyć łącznika AS2 i wyzwalacza uruchamiającego przepływ pracy aplikacji logiki. Łącznik AS2 udostępnia tylko akcje, wyzwalacze nie. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) który ma skojarzony z subskrypcją platformy Azure i połączone z aplikacją logiki, w którym planujesz używać łącznika AS2. Zarówno swojej logiki aplikacji i konto integracji muszą istnieć w tej samej lokalizacji lub regionu platformy Azure.

* Co najmniej dwóch [partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md) czy zdefiniowanych już w ramach konta integracji przy użyciu kwalifikator tożsamości AS2.

* Przed użyciem łącznika AS2, należy utworzyć AS2 [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md) między partnerami handlowymi i magazynu tej umowy na koncie integracji.

* Jeśli używasz [usługi Azure Key Vault](../key-vault/key-vault-overview.md) certyfikatu zarządzania, sprawdź, że klucze magazynu na **Szyfruj** i **odszyfrować** operacji. W przeciwnym razie kodowania i dekodowania akcje działać.

  W witrynie Azure portal przejdź do magazynu kluczy, wyświetlanie klucz magazynu **dozwolone operacje**i upewnij się, że **Szyfruj** i **odszyfrować** operacje są zaznaczone.

  ![Sprawdź kluczowe operacje magazynu](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kodowanie komunikatów AS2

1. Jeśli nie jest już w [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W Projektancie Dodaj nową akcję do aplikacji logiki. 

1. W obszarze **wybierz akcję** i wyszukiwania, wybierz **wszystkich**. W polu wyszukiwania wprowadź "kodowanie as2" i wybierz następującą akcję: **Koduj jako komunikat AS2**.

   ![Wybierz pozycję "Koduj komunikat AS2."](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Jeśli masz istniejące połączenie z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, wybierz konto integracji, który chcesz połączyć, a następnie wybierz **Utwórz**.

   ![Utwórz połączenie z kontem integracji](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Teraz podaj informacje dotyczące tych właściwości:

   | Właściwość | Opis |
   |----------|-------------|
   | **AS2-z** | Identyfikator nadawcy wiadomości określony przez umowy AS2 |
   | **AS2 — do** | Identyfikator odbiorcy komunikat określony przez umowy AS2 |
   | **body** | Ładunek komunikatu |
   |||

   Na przykład:

   ![Właściwości kodowania komunikatu](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekodowanie komunikatów AS2

1. Jeśli nie jest już w [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W Projektancie Dodaj nową akcję do aplikacji logiki. 

1. W obszarze **wybierz akcję** i wyszukiwania, wybierz **wszystkich**. W polu wyszukiwania wprowadź "dekodowania as2" i wybierz następującą akcję: **Dekoduj komunikat AS2**

   ![Wybierz opcję "dekodowania AS2 komunikat"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Jeśli masz istniejące połączenie z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, wybierz konto integracji, który chcesz połączyć, a następnie wybierz **Utwórz**.

   ![Utwórz połączenie z kontem integracji](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Aby uzyskać **treści** i **nagłówki**, wybierz te wartości z danych wyjściowych poprzedniej, wyzwalacza lub akcji.

   Na przykład załóżmy, że Twoja aplikacja logiki odbiera komunikaty za pośrednictwem wyzwalacza żądania. Możesz wybrać dane wyjściowe z tego wyzwalacza.

   ![Wybierz nagłówki i treść z danych wyjściowych żądań](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Sample

Aby wypróbować wdrażanie scenariusza AS2 przykład i aplikacji logiki w pełni funkcjonalne, zobacz [AS2 szablon aplikacji logiki i scenariusz](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w łącznika interfejsu OpenAPI (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/as2/).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
