---
title: Kodowanie komunikatów AS2 — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Koduj jako komunikaty z usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 455056feaa1b13022be3ab3c15880a87c50dedd9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003551"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Kodowanie komunikatów AS2 za pomocą usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw

Ustalenie, bezpieczeństwo i niezawodność podczas przesyłania wiadomości, korzystania z łącznika komunikatu kodowanie AS2. Ten łącznik umożliwia cyfrowe podpisywanie, szyfrowanie i Potwierdzanie za pośrednictwem wiadomości dyspozycji powiadomienia (powiadomienia MDN), który prowadzi do Obsługa Niemożność wyparcia się.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikatu kodowanie AS2.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [Umowy AS2](logic-apps-enterprise-integration-as2.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-as2-messages"></a>Kodowanie komunikatów AS2

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikatu kodowanie AS2 nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź "AS2" jako filtr. Wybierz **AS2 - komunikat AS2 kodowanie**.
   
    ![Wyszukaj "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć. 
   
    ![Utwórz połączenie z kontem integracji](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**.
   
    ![Szczegóły połączenia integracji](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, należy podać szczegóły dotyczące **AS2 — od**, **AS2 — do identyfikatorów** zgodnie z konfiguracją w umowie, i **treści**, czyli ładunek komunikatu.
   
    ![Podaj wymagane pola](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Szczegóły kodera AS2

Łącznik AS2 kodowanie wykonuje następujące zadania: 

* Stosuje nagłówków AS2/HTTP
* Podpisuje komunikaty wychodzące (jeśli jest skonfigurowane)
* Szyfruje komunikaty wychodzące (jeśli jest skonfigurowane)
* Kompresuje komunikatu (jeśli jest skonfigurowane)
* Przekazuje nazwę pliku w nagłówku MIME (jeśli jest skonfigurowane)


  > [!NOTE]
  > Jeśli używasz usługi Azure Key Vault dla certyfikatu zarządzania, upewnij się, należy skonfigurować klucze, aby zezwolić na **Szyfruj** operacji.
  > W przeciwnym razie — kodowanie komunikatów AS2 zakończy się niepowodzeniem.
  >
  > ![Odszyfrowuje magazyn kluczy](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Spróbuj tego przykładu

Aby wypróbować wdrażanie scenariusza AS2 przykład i aplikacji logiki w pełni funkcjonalne, zobacz [AS2 szablon aplikacji logiki i scenariusz](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/as2/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") 

