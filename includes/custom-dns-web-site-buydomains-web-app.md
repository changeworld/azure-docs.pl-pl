---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531102"
---
Chcąc domenę, możesz kupić domen na [portalu zarządzania systemu Azure](https://portal.azure.com) bezpośrednio. Nazwy domen i przypisać do swojej aplikacji sieci web, wykonaj następujące kroki.

1. Otwórz w przeglądarce, [portalu zarządzania systemu Azure](https://portal.azure.com).
2. W **aplikacji sieci Web** kliknij nazwę aplikacji sieci web, wybierz pozycję **ustawienia**, a następnie wybierz pozycję **domeny niestandardowe i protokół SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. W **domeny niestandardowe i protokół SSL** bloku kliknij **kupowanie domen**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. W **zakup domen** bloku, użyj pola tekstowego, aby wprowadzić nazwę domeny, które chcesz kupić. Sugerowane dostępnych domen będą wyświetlane tylko nam wszystkim zdmuchnąć pola tekstowego. Wybierz domenę, które chcesz kupić.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Kliknij przycisk **informacje kontaktowe** i wypełnij formularz informacji kontaktowych w domenie.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Kliknij przycisk **wybierz** na **zakup domen** bloku, następnie zostaną wyświetlone informacje o zakupie **potwierdzenie zakupu** bloku. Zaakceptuj postanowienia prawne i kliknięcie **Kup**, Twoje zamówienie zostanie przesłany i możesz monitorować proces zakupu na **powiadomień**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Jeśli pomyślnie uporządkowanych domeny można Zarządzanie domeną i przypisać do swojej aplikacji sieci web. Kliknij przycisk **"..."** po prawej stronie domenę. Następnie możesz **anulowania zakupu** lub **domeny Zarządzaj**. Kliknij przycisk **domeny zarządzania**, firma Microsoft może powiązać, a następnie **poddomeny** do naszej aplikacji sieci web na **domeny Zarządzaj** bloku.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Po zakończeniu konfiguracji niestandardowej nazwy domeny zostaną wyświetlone w **powiązania nazwy hosta** części aplikacji sieci web.

W tym momencie można wprowadzić nazwę domeny niestandardowej w przeglądarce i zobacz, że jej pomyślnie spowoduje przejście do aplikacji sieci web.

