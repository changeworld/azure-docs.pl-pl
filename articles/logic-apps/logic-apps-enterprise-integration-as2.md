---
title: Wysyłanie i odbieranie komunikatów AS2 dla B2B Azure Logic Apps
description: Komunikaty programu Exchange AS2 dotyczące scenariuszy integracji B2B przedsiębiorstwa przy użyciu Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 08/22/2019
ms.openlocfilehash: 1f063c0e8dada8eb6c4eee031764f6ca7dd3a91d
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680384"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Komunikaty programu Exchange AS2 dla integracji z usługą B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Aby móc korzystać z komunikatów AS2 w Azure Logic Apps, można użyć łącznika AS2, który udostępnia wyzwalacze i akcje zarządzania komunikacją AS2. Aby na przykład nawiązać bezpieczeństwo i niezawodność podczas przesyłania komunikatów, możesz użyć następujących akcji:

* [ **AS2 zakodować** akcję](#encode) w celu zapewnienia szyfrowania, podpisywania cyfrowego i potwierdzeń poprzez powiadomienia o dyspozycjach komunikatów (powiadomienia MDN), które ułatwiają odrzucanie. Na przykład ta akcja stosuje nagłówki AS2/HTTP i wykonuje te zadania po skonfigurowaniu:

  * Podpisuje komunikaty wychodzące.
  * Szyfruje wiadomości wychodzące.
  * Kompresuje komunikat.
  * Przesyła nazwę pliku w nagłówku MIME.

* [ **AS2 akcja dekodowania** ](#decode) do zapewniania odszyfrowywania, podpisywania cyfrowego i potwierdzeń przy użyciu powiadomień o dyspozycjach komunikatów (powiadomienia MDN). Na przykład ta akcja wykonuje następujące zadania:

  * Przetwarza nagłówki AS2/HTTP.
  * Uzgadnia odebrane MDNs z oryginalnymi komunikatami wychodzącymi.
  * Aktualizuje i skorelowanie rekordów w bazie danych bez wylogowania.
  * Zapisuje rekordy dla raportowania stanu AS2.
  * Wyprowadza zawartość ładunku jako zakodowaną algorytmem Base64.
  * Określa, czy MDNs jest wymagane. W oparciu o umowę AS2 określa, czy MDNs powinna być synchroniczna czy asynchroniczna.
  * Generuje synchroniczną lub asynchroniczną MDNs w oparciu o umowę AS2.
  * Ustawia tokeny korelacji i właściwości na serwerze MDNs.

  Ta akcja wykonuje także następujące zadania po skonfigurowaniu:

  * Weryfikuje podpis.
  * Odszyfrowuje komunikaty.
  * Dekompresuje komunikat.
  * Sprawdź i nie Zezwalaj na duplikaty identyfikatora wiadomości.

W tym artykule pokazano, jak dodać akcje kodowania i dekodowania AS2 do istniejącej aplikacji logiki.

> [!IMPORTANT]
> Oryginalny łącznik AS2 będzie przestarzały, więc upewnij się, że zamiast niego używasz łącznika **AS2 (v2)** . Ta wersja zapewnia te same możliwości co wersja oryginalna, jest natywna dla środowiska uruchomieniowego Logic Apps i zapewnia znaczną poprawę wydajności w zakresie przepływności i rozmiaru wiadomości. Dodatkowo łącznik macierzysty v2 nie wymaga utworzenia połączenia z kontem integracji. Zamiast tego, zgodnie z opisem w sekcji wymagania wstępne, należy się upewnić, że konto integracji zostało połączone z aplikacją logiki, w której zamierzasz korzystać z łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, z której ma być używany łącznik AS2 i wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Łącznik AS2 zawiera tylko akcje, a nie wyzwalacze. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacją logiki, w której zamierzasz używać łącznika AS2. Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tej samej lokalizacji lub regionie platformy Azure.

* Co najmniej dwóch [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) , które zostały już zdefiniowane na koncie integracji, przy użyciu kwalifikatora tożsamości AS2.

* Aby można było korzystać z łącznika AS2, należy utworzyć [umowę](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 między partnerami handlowymi i przechowywać tę umowę na koncie integracji.

* Jeśli używasz [Azure Key Vault](../key-vault/key-vault-overview.md) do zarządzania certyfikatami, sprawdź, czy klucze magazynu umożliwiają wykonywanie operacji **szyfrowania** i **odszyfrowywania** . W przeciwnym razie akcje kodowania i dekodowania kończą się niepowodzeniem.

  W Azure Portal przejdź do magazynu kluczy, Wyświetl **dozwolone operacje**klucza magazynu i upewnij się, że są wybrane operacje **szyfrowania** i **odszyfrowywania** .

  ![Sprawdzanie operacji klucza magazynu](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kodowanie komunikatów AS2

1. Jeśli jeszcze tego nie zrobiono, w [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W projektancie Dodaj nową akcję do aplikacji logiki.

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**. W polu wyszukiwania wprowadź ciąg "AS2 Encode" i upewnij się, że wybrano akcję AS2 (v2): **As2 Encode**

   ![Wybierz pozycję "AS2 Encode"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Podaj teraz informacje o tych właściwościach:

   | Właściwość | Opis |
   |----------|-------------|
   | **Komunikat do zakodowania** | Ładunek wiadomości |
   | **AS2 z** | Identyfikator nadawcy wiadomości określony przez umowę AS2 |
   | **AS2 do** | Identyfikator odbiorcy wiadomości określony przez umowę AS2 |
   |||

   Na przykład:

   ![Właściwości kodowania komunikatów](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekodowanie komunikatów AS2

1. Jeśli jeszcze tego nie zrobiono, w [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W projektancie Dodaj nową akcję do aplikacji logiki.

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**. W polu wyszukiwania wprowadź ciąg "dekodowanie AS2" i upewnij się, że wybrano akcję AS2 (v2): **dekodowanie AS2**

   ![Wybierz pozycję "dekodowanie AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Aby **komunikat był kodowany** i właściwości **nagłówków wiadomości** , wybierz te wartości z poprzedniego wyzwalacza lub wyjść akcji.

   Załóżmy na przykład, że aplikacja logiki otrzymuje komunikaty przez wyzwalacz żądania. Można wybrać dane wyjściowe z tego wyzwalacza.

   ![Wybieranie treści i nagłówków z danych wyjściowych żądania](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Przykład

Aby spróbować wdrożyć w pełni działającą aplikację logiki i przykład scenariusza AS2, zobacz [szablon i scenariusz aplikacji logiki AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/as2/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
