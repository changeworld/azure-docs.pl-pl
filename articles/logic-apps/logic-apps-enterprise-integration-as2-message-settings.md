---
title: Ustawienia komunikatów AS2
description: Przewodnik po ustawieniach wysyłania i odbierania as2 w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793025"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Odwołanie do ustawień komunikatu AS2 w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa

To odwołanie opisuje właściwości, które można ustawić dla określenia, jak umowa AS2 obsługuje wiadomości wysyłane i odbierane między partnerami handlowymi. Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia wiadomości z Tobą.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>As2 Odbieranie ustawień

![Wybierz "Ustawienia odbioru"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Właściwość | Wymagany | Opis |
|----------|----------|-------------|
| **Zastępowanie właściwości wiadomości** | Nie | Zastępuje właściwości wiadomości przychodzących z ustawieniami właściwości. |
| **Wiadomość powinna być podpisana** | Nie | Określa, czy wszystkie przychodzące wiadomości muszą być podpisane cyfrowo. Jeśli wymagane jest podpisywanie, z listy **Certyfikat** wybierz istniejący certyfikat publiczny partnera gościa do sprawdzania poprawności podpisu w wiadomościach. Jeśli nie masz certyfikatu, dowiedz się więcej o [dodawaniu certyfikatów.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Wiadomość powinna być zaszyfrowana** | Nie | Określa, czy wszystkie przychodzące wiadomości muszą być szyfrowane. Niezaszyfrowane wiadomości są odrzucane. Jeśli potrzebujesz szyfrowania, z listy **Certyfikat** wybierz istniejący prywatny certyfikat partnera hosta do odszyfrowywania wiadomości przychodzących. Jeśli nie masz certyfikatu, dowiedz się więcej o [dodawaniu certyfikatów.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Wiadomość powinna być skompresowana** | Nie | Określa, czy wszystkie przychodzące wiadomości muszą być skompresowane. Nieskompresowane wiadomości są odrzucane. |
| **Nie zezwalaj na duplikaty identyfikatorów wiadomości** | Nie | Określa, czy zezwolić na wiadomości o zduplikowanych identyfikatorach. Jeśli nie zezwalasz na zduplikowane identyfikatory, wybierz liczbę dni między czekami. Można również zdecydować, czy mają być zawieszane duplikaty. |
| **Tekst MDN** | Nie | Określa domyślne powiadomienie o dyspozycji wiadomości (MDN), które ma zostać wysłane do nadawcy wiadomości. |
| **Wyślij mdn** | Nie | Określa, czy dla odebranych komunikatów mają być wysyłane synchroniczne numery MDN.  |
| **Wyślij podpisaną nazwę MDN** | Nie | Określa, czy dla odebranych wiadomości mają być wysyłane podpisane numery MDN. Jeśli wymagane jest podpisywanie, z listy **Algorytm MIC** wybierz algorytm, który ma być używany do podpisywania wiadomości. |
| **Wyślij asynchroniczne MDN** | Nie | Określa, czy kody MDN mają być wysyłane asynchronicznie. Jeśli wybierzesz asynchroniczne mdny, w polu **adresu URL** określ adres URL, gdzie wysłać mdn. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Ustawienia as2 wysyłania

![Wybierz "Ustawienia wysyłania"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Właściwość | Wymagany | Opis |
|----------|----------|-------------|
| **Włączanie podpisywania wiadomości** | Nie | Określa, czy wszystkie wiadomości wychodzące muszą być podpisane cyfrowo. Jeśli wymagane jest podpisywanie, wybierz następujące wartości: <p>- Z listy **Algorytm podpisywania** wybierz algorytm, który ma być używany do podpisywania wiadomości. <br>- Z listy **Certyfikat wybierz** istniejący prywatny certyfikat partnera hosta do podpisywania wiadomości. Jeśli nie masz certyfikatu, dowiedz się więcej o [dodawaniu certyfikatów.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Włączanie szyfrowania wiadomości** | Nie | Określa, czy wszystkie wiadomości wychodzące muszą być szyfrowane. Jeśli potrzebujesz szyfrowania, wybierz następujące wartości: <p>- Z listy **Algorytm szyfrowania** wybierz algorytm certyfikatu publicznego partnera gościa, który będzie używany do szyfrowania wiadomości. <br>- Z listy **Certyfikat wybierz** istniejący prywatny certyfikat partnera gościa do szyfrowania wiadomości wychodzących. Jeśli nie masz certyfikatu, dowiedz się więcej o [dodawaniu certyfikatów.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Włączanie kompresji wiadomości** | Nie | Określa, czy wszystkie wiadomości wychodzące muszą być skompresowane. |
| **Rozwijanie nagłówków HTTP** | Nie | Umieszcza nagłówek `content-type` HTTP w jednym wierszu. |
| **Nazwa pliku przesyłania w nagłówku MIME** | Nie | Określa, czy nazwa pliku ma być dołączana do nagłówka MIME. |
| **Wniosek MDN** | Nie | Określa, czy powiadomienia o dyspozycji wiadomości (MDN) mają być odbierane dla wszystkich wiadomości wychodzących. |
| **Wniosek podpisany MDN** | Nie | Określa, czy dla wszystkich wiadomości wychodzących mają być odbierane podpisane mdny. Jeśli wymagane jest podpisywanie, z listy **Algorytm MIC** wybierz algorytm, który ma być używany do podpisywania wiadomości. |
| **Żądanie asynchroniczne MDN** | Nie | Określa, czy nazwy MDN mają być odbierane asynchronicznie. Jeśli wybierzesz asynchroniczne mdny, w polu **adresu URL** określ adres URL, gdzie wysłać mdn. |
| **Włączanie nrr** | Nie | Określa, czy wymagać potwierdzenia braku odrzucenia (NRR). Ten atrybut komunikacji dostarcza dowodów, że dane zostały odebrane zgodnie z omówieniem. |
| **Format algorytmu SHA2** | Nie | Określa format algorytmu MIC używany do podpisywania nagłówków wychodzących komunikatów AS2 lub mdn |
||||

## <a name="next-steps"></a>Następne kroki

[Wymienianie komunikatów AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
