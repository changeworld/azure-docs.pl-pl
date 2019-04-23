---
title: Dekodowanie komunikatów AS2 — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dekodowanie jako komunikaty z usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: ca297e1b4a007db3020b4369132b190608484738
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001630"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Dekodowanie komunikatów AS2 za pomocą usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw 

Ustalenie, bezpieczeństwo i niezawodność podczas przesyłania wiadomości, korzystania z łącznika komunikat dekodowania AS2. Ten łącznik udostępnia cyfrowego podpisywania, odszyfrowywania i Potwierdzanie za pośrednictwem komunikatu powiadomienia dyspozycji (MDN).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikat dekodowania AS2.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [Umowy AS2](logic-apps-enterprise-integration-as2.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-as2-messages"></a>Dekodowanie komunikatów AS2

1. [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikat dekodowanie AS2 nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź "AS2" jako filtr. Wybierz **AS2 - komunikat dekodowania AS2**.
   
    ![Wyszukaj "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć.
   
    ![Utwórz połączenie integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**.

    ![Szczegóły połączenia integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, wybierz **treści** i **nagłówki** z danych wyjściowych żądania.
   
    ![utworzone połączenie integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Na przykład:

    ![Wybierz nagłówki i treść z danych wyjściowych żądań](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>Szczegóły dekoder AS2

Łącznik dekodowania AS2 wykonuje następujące zadania: 

* Przetwarza nagłówków AS2/HTTP
* Weryfikuje podpis (jeśli jest skonfigurowane)
* Odszyfrowuje wiadomości (jeśli jest skonfigurowane)
* Dekompresuje komunikatu (jeśli jest skonfigurowane)
* Sprawdź i nie zezwalaj na duplikaty identyfikatorów komunikatów (jeśli jest skonfigurowane)
* Uzgadnia odebranego komunikatu MDN z oryginalnym komunikatem ruchu wychodzącego
* Aktualizacje i koreluje uznawania bazy danych
* Zapisuje rekordy dla raportowania stanu AS2
* Zawartość ładunek danych wyjściowych jest zakodowany w formacie base64
* Określa, czy powiadomienia MDN jest wymagana i tego, czy powiadomienia MDN powinny być synchroniczne czy asynchroniczne na podstawie konfiguracji umowy AS2
* Generuje synchroniczna lub asynchroniczna MDN, (na podstawie umowy konfiguracji)
* Ustawia właściwości i tokenów korelacji powiadomienia MDN


  > [!NOTE]
  > Jeśli używasz usługi Azure Key Vault dla certyfikatu zarządzania, upewnij się, należy skonfigurować klucze, aby zezwolić na **odszyfrować** operacji.
  > W przeciwnym razie dekodowania AS2 zakończy się niepowodzeniem.
  >
  > ![Odszyfrowuje magazyn kluczy](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Spróbuj tego przykładu

Aby wypróbować wdrażanie scenariusza AS2 przykład i aplikacji logiki w pełni funkcjonalne, zobacz [AS2 szablon aplikacji logiki i scenariusz](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/as2/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md) 

