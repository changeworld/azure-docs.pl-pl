---
title: Omówienie programu Microsoft Azure StorSimple i dostawcy rozwiązań w chmurze | Dokumentacja firmy Microsoft
description: Omówienie StorSimple i dostawcy usług Kryptograficznych dla partnerów usługi StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875655"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Wdrażanie tablicy wirtualnego StorSimple programu dostawcy rozwiązania chmury

## <a name="overview"></a>Omówienie

Tablica wirtualnego StorSimple można wdrożyć przez partnerów Cloud Solution Provider (CSP) dla klientów. Partner dostawcy usług Kryptograficznych można utworzyć usługi Menedżer StorSimple urządzenia. Ta usługa może następnie używane do wdrażania i zarządzania tablicy wirtualnego StorSimple oraz skojarzone udziałów, woluminów i kopii zapasowych.

W tym artykule opisano sposób partnera dostawcy usług Kryptograficznych można dodać klienta lub nową subskrypcję do istniejącego klienta, a następnie utworzyć usługi do wdrożenia tablicą wirtualnego StorSimple w dostawcy usług Kryptograficznych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Zarejestrowane w programie dostawcy usług Kryptograficznych.
- Masz poprawne [Centrum partnerskiego](http://partnercenter.microsoft.com/) poświadczenia logowania. Poświadczenia umożliwiają Zaloguj się do portalu dla partnerów do dodawania nowych klientów, wyszukiwać klientów lub przejdź do konta klienta z poziomu pulpitu nawigacyjnego partnera. Dostawca usług Kryptograficznych może działać jako StorSimple administrator w imieniu klienta w portalu Azure.
                             
## <a name="add-a-customer"></a>Dodaj klienta

Jeśli dodasz klienta subskrypcji jest tworzony automatycznie. Aby dodać klienta (i automatycznie utworzyć subskrypcję), wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](http://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń konta dostawcy usług Kryptograficznych. Kliknij przycisk **pulpitu nawigacyjnego**.

     ![Pulpit nawigacyjny w Centrum partnerskiego](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W lewym okienku kliknij **klientów**. W prawym okienku kliknij **dodać klienci**. Wprowadź szczegóły klienta. Kliknij przycisk **dalej: subskrypcji** można utworzyć subskrypcji klienta.

    ![Dodaj klienta](./media/storsimple-partner-csp-deploy/image2.png)

3.  Wybierz **Microsoft Azure** oferty. Przewiń do dołu strony, a następnie kliknij przycisk **przeglądu**.

    ![Przejrzyj informacje o subskrypcji](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Przejrzyj informacje i kliknij przycisk **przesyłania**.

    ![Przedstawia subskrypcji](./media/storsimple-partner-csp-deploy/image4.png)

5. Zapisz informacje o potwierdzenie w przyszłości.

    ![Zapisz potwierdzenia](./media/storsimple-partner-csp-deploy/image5.png)

6. Znajdź fizyczną lub przejdź do klienta zostanie dodany. Kliknij przycisk **nazwa firmy** aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W lewym okienku wybierz **Zarządzanie usługami**. W prawym okienku w obszarze **administrowania usługami**, kliknij przycisk **portalu zarządzania Microsoft Azure** do logowania się jako administrator systemu Azure dla klienta.

    ![Zaloguj się do portalu Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ nowy** i wyszukiwania lub przejdź do **wirtualnego serii urządzenia StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrożyć usługę Menedżer StorSimple urządzenia](storsimple-virtual-array-manage-service.md).

    ![Utwórz usługę Menedżer StorSimple urządzenia](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Dodaj subskrypcję

W niektórych przypadkach może być istniejący klient i należy dodać subskrypcję. Aby dodać subskrypcję do istniejącego klienta, wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](http://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń konta dostawcy usług Kryptograficznych. Kliknij przycisk **pulpitu nawigacyjnego**.

     ![Pulpit nawigacyjny w Centrum partnerskiego](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W lewym okienku kliknij **klientów**. Znajdź fizyczną lub przejdź do klienta chcesz dodać subskrypcję. Kliknij przycisk ![Ikona znacznika wyboru Rozwiń](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonę, aby rozwinąć wiersz dla nazwy firmy klienta. W sekcji szczegółów kliknij **dodawać subskrypcje**.

    ![Klienci](./media/storsimple-partner-csp-deploy/image10.png)

3. Sprawdź **Microsoft Azure** dla **Top oferty** w subskrypcji i kliknij przycisk **przesyłania**. Spowoduje to utworzenie nowej subskrypcji.

    ![Dodaj nową subskrypcję](./media/storsimple-partner-csp-deploy/image11.png)

6. Po utworzeniu nowej subskrypcji, kliknij przycisk **<--klientów** w okienku po lewej, aby powrócić do **klientów** strony. Wyszukiwanie odbiorcy, dla którego utworzono subskrypcji. Kliknij przycisk **nazwa firmy** aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W lewym okienku wybierz **Zarządzanie usługami**. W prawym okienku w obszarze **administrowania usługami**, kliknij przycisk **portalu zarządzania Microsoft Azure** do logowania się jako administrator systemu Azure dla klienta.

    ![Zaloguj się do portalu Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ nowy** i wyszukiwania lub przejdź do **wirtualnego serii urządzenia StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrożyć usługę Menedżer StorSimple urządzenia](storsimple-virtual-array-manage-service.md).

    ![Utwórz usługę Menedżer StorSimple urządzenia](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli masz więcej pytań dotyczących StorSimple w dostawcy usług Kryptograficznych, przejdź do [StorSimple w dostawcy usług Kryptograficznych: często zadawane pytania](storsimple-partner-csp-faq.md).
- Jeśli wszystko jest gotowe do wdrożenia programu StorSimple, przejdź do [wdrażanie programu StorSimple w dostawcy usług Kryptograficznych](storsimple-partner-csp-deploy.md).
