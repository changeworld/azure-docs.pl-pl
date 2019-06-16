---
title: Omówienie programu Microsoft Azure StorSimple i dostawcy rozwiązań w chmurze | Dokumentacja firmy Microsoft
description: Omówienie magazynu StorSimple i dostawcy usług Kryptograficznych dla partnerów usługi StorSimple.
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
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629990"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Wdrażanie macierzy wirtualnej StorSimple dla Cloud Solution Provider Program

## <a name="overview"></a>Omówienie

Macierz wirtualną StorSimple można wdrożyć przez partnerów Cloud Solution Provider (CSP) dla swoich klientów. Partner programu CSP może utworzyć usługi Menedżer urządzeń StorSimple. Ta usługa następnie może służyć do wdrażania i zarządzania rozwiązania StorSimple Virtual Array i skojarzone udziałów, woluminów i kopii zapasowych.

W tym artykule opisano, jak partner programu CSP można dodać do istniejącego klienta klient lub nową subskrypcję, a następnie utworzyć usługi do wdrożenia rozwiązania StorSimple Virtual Array w programie CSP.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Użytkownik jest zarejestrowany w ramach programu CSP.
- Masz poprawne [Centrum partnerskiego](https://partnercenter.microsoft.com/) poświadczenia logowania. Poświadczenia umożliwiają Zaloguj się do portalu dla partnerów do dodawania nowych klientów, wyszukiwać klientów lub przejdź do konta klienta, na pulpicie nawigacyjnym partnera. Dostawca CSP może działać jako administratora usługi StorSimple w imieniu klientów w witrynie Azure portal.
                             
## <a name="add-a-customer"></a>Dodaj klienta

Jeśli dodasz klienta, jest tworzony automatycznie subskrypcji. Aby dodać klienta (i automatycznie Utwórz subskrypcję), wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń konta programu CSP. Kliknij przycisk **pulpit nawigacyjny**.

     ![Pulpit nawigacyjny Centrum partnerskiego](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W okienku po lewej stronie kliknij **klientów**. W okienku po prawej stronie kliknij **Dodaj klientów**. Wprowadź szczegóły klienta. Kliknij pozycję **Next: Subskrypcje** do utworzenia subskrypcji klienta.

    ![Dodaj klienta](./media/storsimple-partner-csp-deploy/image2.png)

3.  Wybierz **Microsoft Azure** oferty. Przewiń do dołu strony, a następnie kliknij przycisk **przeglądu**.

    ![Przejrzyj informacje o subskrypcji](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Przejrzyj informacje, a następnie kliknij przycisk **przesyłania**.

    ![Prześlij subskrypcję](./media/storsimple-partner-csp-deploy/image4.png)

5. Zapisz informacje o potwierdzenie w przyszłości.

    ![Zapisz potwierdzenia](./media/storsimple-partner-csp-deploy/image5.png)

6. Znajdź fizyczną lub przejdź do klienta właśnie dodałeś. Kliknij przycisk **nazwa firmy** na przechodzenie do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W okienku po lewej stronie wybierz **Zarządzanie usługami**. W okienku po prawej stronie w obszarze **administrowania usługami**, kliknij przycisk **portalu zarządzania Microsoft Azure** zalogowania się jako administrator platformy Azure dla klientów.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ nowy** i wyszukać, lub przejdź do **seria urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrażania usługi Menedżer urządzeń StorSimple](storsimple-virtual-array-manage-service.md).

    ![Tworzenie usługi Menedżer urządzeń StorSimple](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Dodaj subskrypcję

W niektórych przypadkach może być istniejącym klientem, a następnie należy dodać subskrypcję. Aby dodać subskrypcję do istniejącego klienta, wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń konta programu CSP. Kliknij przycisk **pulpit nawigacyjny**.

     ![Pulpit nawigacyjny Centrum partnerskiego](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W okienku po lewej stronie kliknij **klientów**. Znajdź fizyczną lub przejdź do klienta, które chcesz dodać subskrypcję. Kliknij przycisk ![ikonę znacznika wyboru Rozwiń](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonę, aby rozwinąć wiersz dla nazwy firmy klienta. W sekcji szczegółów kliknij **Dodaj subskrypcje**.

    ![Klienci](./media/storsimple-partner-csp-deploy/image10.png)

3. Sprawdź **Microsoft Azure** dla **najważniejsze oferty** w subskrypcji i kliknij przycisk **przesyłania**. Spowoduje to utworzenie nowej subskrypcji.

    ![Dodaj nową subskrypcję](./media/storsimple-partner-csp-deploy/image11.png)

6. Po utworzeniu nowej subskrypcji, kliknij **< — klienci** w okienku po lewej stronie, aby powrócić do **klientów** strony. Wyszukaj klienta, dla którego utworzono subskrypcję. Kliknij przycisk **nazwa firmy** na przechodzenie do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W okienku po lewej stronie wybierz **Zarządzanie usługami**. W okienku po prawej stronie w obszarze **administrowania usługami**, kliknij przycisk **portalu zarządzania Microsoft Azure** zalogowania się jako administrator platformy Azure dla klientów.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ nowy** i wyszukać, lub przejdź do **seria urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrażania usługi Menedżer urządzeń StorSimple](storsimple-virtual-array-manage-service.md).

    ![Tworzenie usługi Menedżer urządzeń StorSimple](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz więcej pytań dotyczących StorSimple w programie CSP, przejdź do strony [StorSimple w programie CSP: Często zadawane pytania dotyczące](storsimple-partner-csp-faq.md).
- Jeśli wszystko jest gotowe do wdrożenia usługi StorSimple, przejdź do strony [wdrażanie rozwiązania StorSimple w programie CSP](storsimple-partner-csp-deploy.md).
