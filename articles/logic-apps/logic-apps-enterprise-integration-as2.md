---
title: Komunikaty AS2 integracji przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wymienianie komunikatów AS2 integracji przedsiębiorstw B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.date: 06/08/2017
ms.openlocfilehash: 3413b235d9202530eb1a3129637e3746bbe6585b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872579"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymienianie komunikatów AS2 integracji przedsiębiorstw B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Przed mogą wymieniać komunikaty AS2 dla usługi Azure Logic Apps, należy utworzyć umowy AS2 i przechowywać tej umowy na koncie integracji. Poniżej przedstawiono procedurę tworzenia umowy AS2.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , są już zdefiniowane na koncie integracji i skonfigurowany z kwalifikatorem AS2 w ramach **tożsamości biznesowych**

> [!NOTE]
> Podczas tworzenia umowy zawartość w pliku Umowa musi być zgodna z typem umowy.    

Po zakończeniu [utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md) i [partnerów dodane](logic-apps-enterprise-integration-partners.md), umowy AS2 można utworzyć, wykonując następujące kroki.

## <a name="create-an-as2-agreement"></a>Tworzenie umowy AS2

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal").  

2. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wprowadź "integration", a następnie wybierz pozycję **kont integracji**.

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Jeśli nie widzisz **wszystkich usług**, trzeba najpierw rozwiń menu. W górnej części menu zwinięty wybierz **Pokaż etykiety tekstowe**.

3. W obszarze **kont integracji**, wybierz konto integracji, w którym chcesz utworzyć umowy.

   ![Wybierz miejsce utworzenia umowę konta integracji](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Wybierz **umów** kafelka. Jeśli nie masz umowy kafelka, dodać Kafelek.

    ![Wybierz Kafelek "Umowy"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. W obszarze **umów**, wybierz **Dodaj**.

    ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. W obszarze **Dodaj**, wprowadź **nazwa** dla umowy. Aby uzyskać **typ umowy**, wybierz opcję **AS2**. Wybierz **Partner hosta**, **tożsamości hosta**, **Partner gościa**, i **tożsamość gościa** dla umowy.

    ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Właściwość | Opis |
    | --- | --- |
    | Name (Nazwa) |Nazwa umowy |
    | Typ umowy | Powinien być AS2 |
    | Partner hosta |Umowa musi mieć partnera hosta i gościa. Partner hosta reprezentuje organizację, która umożliwia skonfigurowanie umowy. |
    | Tożsamość hosta |Identyfikator partnera hosta |
    | Partner gościa |Umowa musi mieć partnera hosta i gościa. Partner gościa reprezentuje organizację, która jest prowadzących działalność we współpracy z partnerem hosta. |
    | Tożsamość gościa |Identyfikator partnera gościa |
    | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez umowę. |
    | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez umowę. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurowanie, jak dojścia Twojej umowy odebranych komunikatów

Teraz, gdy ustawiono właściwości umowy, można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty przychodzące otrzymany od partnera za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz opcję **ustawienia odbierania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości Zobacz tabelę w tej sekcji.

    ![Skonfiguruj "Otrzymywać ustawienia"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Opcjonalnie można zastąpić właściwości wiadomości przychodzących, wybierając **Przesłoń właściwości komunikatu**.

3. Aby wymagać komunikaty przychodzące były podpisane, wybierz **komunikat powinien być podpisany**. Z **certyfikatu** listy, wybierz istniejącą [certyfikatu publicznego partnera gościa](../logic-apps/logic-apps-enterprise-integration-certificates.md) weryfikowania podpisu dla wiadomości. Lub Utwórz certyfikat, jeśli nie masz.

4.  Aby wymagać komunikaty przychodzące były szyfrowane, wybierz **komunikat powinien być zaszyfrowany**. Z **certyfikatu** listy, wybierz istniejącą [certyfikatu prywatnego partnera hosta](../logic-apps/logic-apps-enterprise-integration-certificates.md) do odszyfrowywania wiadomości przychodzących. Lub Utwórz certyfikat, jeśli nie masz.

5. Wymaganie wiadomości, które ma być skompresowany, wybierz **komunikat powinien być skompresowany**.

6. Aby wysłać powiadomienie dyspozycji synchronicznego komunikatu (MDN) po odebraniu wiadomości, zaznacz **Wyślij powiadomienie MDN**.

7. Aby wysłać podpisem komunikatów Mdn po odebraniu wiadomości, zaznacz **Wyślij podpisane powiadomienie MDN**.

8. Aby wysłać asynchronicznych komunikatów Mdn po odebraniu wiadomości, zaznacz **wysyłanie asynchronicznych powiadomień MDN**.

9. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości przychodzących, które są zgodne z wybrane ustawienia.

| Właściwość | Opis |
| --- | --- |
| Przesłoń właściwości komunikatu |Wskazuje, można zastąpić właściwości w odebranej wiadomości. |
| Komunikat powinien być podpisany |Wymaga wiadomości, które ma zostać podpisany cyfrowo. Skonfigurować certyfikat publiczny partnera gościa do weryfikacji podpisu.  |
| Komunikat powinien być zaszyfrowany |Wymaga szyfrowanie wiadomości. Szyfrowane inne niż wiadomości są odrzucane. Konfigurowanie certyfikatu prywatnego partnera hosta do odszyfrowywania wiadomości.  |
| Komunikat powinien być skompresowany |Wymaga wiadomości do skompresowania. Skompresowane inne niż wiadomości są odrzucane. |
| Tekst powiadomienia MDN |Domyślne dyspozycji powiadomienia (powiadomienia MDN) do wysłania do nadawcy wiadomości. |
| Wyślij powiadomienie MDN |Wymaga komunikatów Mdn do wysłania. |
| Wyślij podpisane powiadomienie MDN |Wymaga komunikatów Mdn były podpisane. |
| Algorytm MIC |Wybierz algorytm podpisywania wiadomości. |
| Wyślij asynchroniczne powiadomienie MDN | Wymaga komunikaty wysyłane asynchronicznie. |
| Adres URL | Podaj adres URL, gdzie wysyłać komunikatów Mdn. |

## <a name="configure-how-your-agreement-sends-messages"></a>Skonfiguruj sposób umowie wysyłania wiadomości

Można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty wychodzące wysyłane do partnerów za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz opcję **ustawienia wysyłania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości Zobacz tabelę w tej sekcji.

    ![Ustaw właściwości "Wyślij ustawienia"](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Aby wysyłać komunikaty podpisem partnera, wybierz **Włącz podpisywanie komunikatów**. Do podpisywania komunikatów w **algorytm MIC** listy wybierz *certyfikatu prywatnego partnera hosta algorytm MIC*. A następnie w **certyfikatu** listy, wybierz istniejącą [certyfikatu prywatnego partnera hosta](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Do wysyłania zaszyfrowanych wiadomości do partnera, wybierz **Włącz szyfrowanie komunikatów**. Do szyfrowania wiadomości, w **algorytm szyfrowania** listy wybierz *algorytm certyfikatu publicznego partnera gościa*.
A następnie w **certyfikatu** listy, wybierz istniejącą [certyfikatu publicznego partnera gościa](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Aby skompresować komunikatu, wybierz **Włącz kompresję komunikatów**.

5. Do rozwijania nagłówka content-type protokołu HTTP w jednym wierszu, wybierz **nagłówków HTTP rozwijania**.

6. Aby otrzymać synchroniczne komunikatów Mdn dla wysyłanych wiadomości, zaznacz **żądania powiadomienia MDN**.

7. Aby otrzymywać podpisane komunikatów Mdn dla wysyłanych wiadomości, zaznacz **Żądaj podpisanego powiadomienia MDN**.

8. Aby otrzymać asynchronicznych komunikatów Mdn dla wysyłanych wiadomości, zaznacz **Żądaj asynchronicznego powiadomienia MDN**. Jeśli wybierzesz tę opcję, wprowadź adres URL, gdzie wysyłać komunikatów Mdn.

9. Wymaganie uznawania potwierdzenia, wybierz **włączenia NRR**.  

10. Aby określić format algorytmu do użycia w Mikrofon lub logowanie nagłówków wychodzących komunikatu AS2 lub powiadomienia MDN, zaznacz **format algorytmu SHA2**.  

11. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości wychodzących, które są zgodne z wybrane ustawienia.

| Właściwość | Opis |
| --- | --- |
| Włącz podpisywanie komunikatów |Wymaga wszystkie komunikaty, które są wysyłane z umowy do podpisania. |
| Algorytm MIC |Algorytm podpisywania wiadomości. Umożliwia skonfigurowanie certyfikatu prywatnego algorytm MIC partnera hosta do podpisywania wiadomości. |
| Certyfikat |Wybierz certyfikat do użycia podczas podpisywania wiadomości. Umożliwia skonfigurowanie certyfikatu prywatnego partnera hosta do podpisywania wiadomości. |
| Włącz szyfrowanie komunikatów |Wymaga szyfrowania wszystkich wiadomości, które są wysyłane z niniejszej Umowy. Określa algorytm certyfikatu publicznego partnera gościa do szyfrowania wiadomości. |
| Algorytm szyfrowania |Algorytm szyfrowania do użycia podczas szyfrowania komunikatu. Umożliwia skonfigurowanie certyfikatu publicznego partnera gościa do szyfrowania wiadomości. |
| Certyfikat |Certyfikat używany do szyfrowania wiadomości. Umożliwia skonfigurowanie certyfikatu prywatnego partnera gościa do szyfrowania wiadomości. |
| Włącz kompresję komunikatów |Wymaga kompresji wszystkie komunikaty, które są wysyłane z niniejszej Umowy. |
| Rozwiń nagłówki HTTP |Umieszcza nagłówek content-type protokołu HTTP na jeden wiersz. |
| Żądaj powiadomienia MDN |Wymaga MDN dla wszystkich wiadomości, które są wysyłane z niniejszej Umowy. |
| Żądaj podpisanego powiadomienia MDN |Wymaga wszystkich komunikatów Mdn, które są wysyłane do niniejszej Umowy do podpisania. |
| Żądaj asynchronicznego powiadomienia MDN |Wymaga asynchronicznych komunikatów Mdn do wysłania do niniejszej Umowy. |
| Adres URL |Podaj adres URL, gdzie wysyłać komunikatów Mdn. |
| Włącz opcję NRR |Wymaga uznawania otrzymania (NRR), atrybut komunikacji, który zawiera dowód odebrania danych jako rozwiązane. |
| Format algorytmu SHA2 |Wybierz format algorytmu do użycia w Mikrofon lub logowanie nagłówków wychodzących komunikatu AS2 lub powiadomienia MDN |

## <a name="find-your-created-agreement"></a>Znajdź utworzony umowy

1. Po zakończeniu na ustawienie swoje właściwości umowy **Dodaj** wybierz **OK** w stanie zakończyć tworzenia umowy, a następnie wróć do konta integracji.

    Nowo dodane umowy teraz pojawia się w Twojej **umów** listy.

2. Można również wyświetlić Twojej umowy w przeglądzie konta integracji. W menu konta integracji, wybierz **Przegląd**, a następnie wybierz **umów** kafelka. 

   ![Wybierz Kafelek "Umowy" Aby wyświetlić wszystkie umowy](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/as2/). 

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  
