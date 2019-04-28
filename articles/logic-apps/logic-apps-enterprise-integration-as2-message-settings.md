---
title: Ustawienia komunikatu AS2 — Azure Logic Apps
description: Podręcznik informacyjny dla pola AS2 wysyłania i odbierania ustawień w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769454"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dokumentacja dotycząca ustawień komunikatów AS2 w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Ta dokumentacja opisuje właściwości, które można ustawić do określania, jak umowy AS2 obsługuje wiadomości wysłanych i odebranych między partnerami handlowymi. Należy skonfigurować te właściwości, oparte na umowie z partnerem, który wymienia wiadomości z Tobą.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Ustawienia odbierania AS2

![Wybierz pozycję "Otrzymywać ustawienia"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Przesłoń właściwości komunikatu** | Nie | Zastąpienia właściwości na komunikaty przychodzące z ustawieniami właściwości. |
| **Komunikat powinien być podpisany** | Nie | Określa, czy komunikaty przychodzące musi być podpisany cyfrowo. W razie potrzeby podpisywania, z **certyfikatu** listy, wybierz istniejący gościa partnera publiczny certyfikat do zweryfikowania podpisu dla wiadomości. Jeśli nie ma certyfikatu, Dowiedz się więcej o [Dodanie certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Komunikat powinien być zaszyfrowany** | Nie | Określa, czy komunikaty przychodzące musi być szyfrowana. Szyfrowane inne niż wiadomości są odrzucane. Jeśli jest wymagane szyfrowanie, z **certyfikatu** listy, wybierz istniejący host partnera prywatny certyfikat do odszyfrowywania wiadomości przychodzących. Jeśli nie ma certyfikatu, Dowiedz się więcej o [Dodanie certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Komunikat powinien być skompresowany** | Nie | Określa, czy komunikaty przychodzące musi być kompresowane. Skompresowane inne niż wiadomości są odrzucane. |
| **Nie zezwalaj na duplikaty identyfikatorów komunikatów** | Nie | Określa, czy zezwolić na komunikaty z zduplikowane identyfikatory. Jeśli nie zezwalaj na zduplikowane identyfikatory, wybierz liczbę dni między każdym sprawdzeniem. Możesz również wybrać, czy wstrzymać duplikaty. |
| **Tekst powiadomienia MDN** | Nie | Określa domyślny dyspozycji powiadomienia (powiadomienia MDN) przewidzianą wysyłane do nadawcy wiadomości. |
| **Wyślij powiadomienie MDN** | Nie | Określa, czy do wysyłania komunikatów synchroniczne Mdn po odebraniu wiadomości.  |
| **Wyślij podpisane powiadomienie MDN** | Nie | Określa, czy do wysyłania komunikatów podpisem Mdn po odebraniu wiadomości. W razie potrzeby podpisywania, z **algorytm MIC** Wybierz algorytm podpisywania wiadomości. |
| **Wyślij asynchroniczne powiadomienie MDN** | Nie | Określa, czy do asynchronicznego wysyłania komunikatów Mdn. Jeśli wybierzesz asynchronicznych komunikatów Mdn, w **adresu URL** polu, podaj adres URL, gdzie wysyłać komunikatów Mdn. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Ustawienia wysyłania AS2

![Wybierz pozycję "Wyślij ustawienia"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Włącz podpisywanie komunikatów** | Nie | Określa, czy wszystkie komunikaty wychodzące muszą być podpisane cyfrowo. Jeśli wymagane jest podpisywanie, wybierz następujące wartości: <p>-From **algorytmu podpisywania** Wybierz algorytm podpisywania wiadomości. <br>-From **certyfikatu** listy, wybierz istniejący host partnera prywatny certyfikat do podpisywania wiadomości. Jeśli nie ma certyfikatu, Dowiedz się więcej o [Dodanie certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Włącz szyfrowanie komunikatów** | Nie | Określa, czy wszystkie komunikaty wychodzące muszą być szyfrowane. Jeśli wymagane jest szyfrowanie, wybierz następujące wartości: <p>-From **algorytm szyfrowania** Wybierz algorytm certyfikatu publicznego partnera gościa, który ma być używany do szyfrowania wiadomości. <br>-From **certyfikatu** listy, wybierz istniejący gościa partnera prywatny certyfikat służący do szyfrowania wiadomości wychodzących. Jeśli nie ma certyfikatu, Dowiedz się więcej o [Dodanie certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Włącz kompresję komunikatów** | Nie | Określa, czy wszystkie komunikaty wychodzące muszą być skompresowany. |
| **Rozwiń nagłówki HTTP** | Nie | Umieszcza HTTP `content-type` nagłówek na jeden wiersz. |
| **Przekazuje nazwę pliku w nagłówku MIME** | Nie | Określa, czy należy umieszczać nazwy pliku w nagłówku MIME. |
| **Żądaj powiadomienia MDN** | Nie | Określa, czy chcesz otrzymywać powiadomienia dyspozycji (komunikatów Mdn) dla wszystkich wiadomości wychodzących. |
| **Żądaj podpisanego powiadomienia MDN** | Nie | Określa, czy chcesz otrzymywać podpisane komunikatów Mdn dla wszystkich wiadomości wychodzących. W razie potrzeby podpisywania, z **algorytm MIC** Wybierz algorytm podpisywania wiadomości. |
| **Żądaj asynchronicznego powiadomienia MDN** | Nie | Określa, czy do odbierania komunikatów Mdn asynchronicznie. Jeśli wybierzesz asynchronicznych komunikatów Mdn, w **adresu URL** polu, podaj adres URL, gdzie wysyłać komunikatów Mdn. |
| **Włącz opcję NRR** | Nie | Określa, czy będą musieli odrzucenie bez otrzymania (NRR). Ten atrybut komunikacji dostarcza dowodów odebrania danych jako rozwiązane. |
| **Format algorytmu SHA2** | Nie | Określa format algorytm MIC do użycia podczas podpisywania w nagłówkach wiadomości wychodzących AS2 lub powiadomienia MDN |
||||

## <a name="next-steps"></a>Kolejne kroki

[Wymienianie komunikatów AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
