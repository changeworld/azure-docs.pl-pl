---
title: Wdrażanie programu StorSimple Virtual Array dla dostawcy rozwiązań w chmurze
description: Omówienie StorSimple i CSP dla partnerów StorSimple.
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
ms.openlocfilehash: 7f1927a67127766c72be463c283225135b2a2aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77466913"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Wdrażanie programu StorSimple Virtual Array dla dostawcy rozwiązań w chmurze

## <a name="overview"></a>Omówienie

StorSimple Virtual Array może być wdrażany przez partnerów dostawcy rozwiązań w chmurze (CSP) dla swoich klientów. Partner dostawcy usług kryptograficznych można utworzyć usługę Menedżera urządzeń StorSimple. Ta usługa może następnie służyć do wdrażania i zarządzania StorSimple Virtual Array i skojarzonych udziałów, woluminów i kopii zapasowych.

W tym artykule opisano, jak partner dostawcy usług kryptograficznych może dodać klienta lub nową subskrypcję do istniejącego klienta, a następnie utworzyć usługę do wdrażania tablicy wirtualnej StorSimple w dostawcy usług kryptograficznych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Jesteś zarejestrowany w programie CSP.
- Masz prawidłowe dane logowania [Centrum partnerów.](https://partnercenter.microsoft.com/) Poświadczenia umożliwiają zalogowanie się do portalu partnerów w celu dodania nowych klientów, wyszukiwania klientów lub przechodzenia do konta klienta z pulpitu nawigacyjnego partnera. Dostawcy usług kryptograficznych może działać jako administrator StorSimple w imieniu klienta w witrynie Azure portal.
                             
## <a name="add-a-customer"></a>Dodawanie klienta

Jeśli dodasz klienta, subskrypcja zostanie utworzona automatycznie. Aby dodać klienta (i automatycznie utworzyć subskrypcję), wykonaj następujące kroki w portalu partnerów.

1. Przejdź do [Centrum partnerów](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń CSP. Kliknij **pozycję Pulpit nawigacyjny**.

     ![Pulpit nawigacyjny w Centrum partnerów](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W lewym okienku kliknij pozycję **Klienci**. W prawym okienku kliknij pozycję **Dodaj klientów**. Wprowadź szczegóły odbiorcy. Kliknij **przycisk Dalej: Subskrypcje,** aby utworzyć subskrypcję klienta.

    ![Dodaj klienta](./media/storsimple-partner-csp-deploy/image2.png)

3.  Wybierz ofertę **platformy Microsoft Azure.** Przewiń do dołu strony i kliknij przycisk **Przejrzyj**.

    ![Przeglądanie informacji o subskrypcji](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Przejrzyj informacje i kliknij przycisk **Prześlij**.

    ![Prześlij subskrypcję](./media/storsimple-partner-csp-deploy/image4.png)

5. Zapisz informacje o potwierdzeniu do wykorzystania w przyszłości.

    ![Zapisz potwierdzenie](./media/storsimple-partner-csp-deploy/image5.png)

6. Znajdź lub przejdź do klienta, którego właśnie dodałeś. Kliknij **nazwę firmy,** aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W lewym okienku wybierz pozycję **Zarządzanie usługami**. W prawym okienku w obszarze **Administrowanie usługami**kliknij pozycję **Portal zarządzania platformy Microsoft Azure,** aby zalogować się jako administrator platformy Azure dla klienta.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ Nowy** i wyszukaj lub przejdź do **serii urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [usługi Wdrażanie usługi StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Utwórz usługę StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Dodawanie subskrypcji

W niektórych przypadkach może mieć istniejącego klienta i trzeba dodać subskrypcję. Aby dodać subskrypcję do istniejącego klienta, wykonaj następujące kroki w portalu partnera.

1. Przejdź do [Centrum partnerów](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń CSP. Kliknij **pozycję Pulpit nawigacyjny**.

     ![Pulpit nawigacyjny w Centrum partnerów](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W lewym okienku kliknij pozycję **Klienci**. Znajdź klienta, do którego chcesz dodać subskrypcję, możesz znaleźć lub przejść do niego. Kliknij ![ikonę](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikony rozwiń, aby rozwinąć wiersz nazwy firmy dla klienta. W szczegółach kliknij pozycję **Dodaj subskrypcje**.

    ![Klienci](./media/storsimple-partner-csp-deploy/image10.png)

3. Sprawdź, czy **na platformie Microsoft Azure** nie ma najlepszych **ofert** w subskrypcji i kliknij przycisk **Prześlij**. Spowoduje to utworzenie nowej subskrypcji.

    ![Dodaj nową subskrypcję](./media/storsimple-partner-csp-deploy/image11.png)

6. Po utworzeniu nowej subskrypcji kliknij **przycisk< — klienci** w lewym okienku, aby powrócić do strony **Klienci.** Wyszukaj klienta, dla którego właśnie utworzono subskrypcję. Kliknij **nazwę firmy,** aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W lewym okienku wybierz pozycję **Zarządzanie usługami**. W prawym okienku w obszarze **Administrowanie usługami**kliknij pozycję **Portal zarządzania platformy Microsoft Azure,** aby zalogować się jako administrator platformy Azure dla klienta.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć Menedżera urządzeń StorSimple, kliknij przycisk **+ Nowy** i wyszukaj lub przejdź do **serii urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [usługi Wdrażanie usługi StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Utwórz usługę StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli masz więcej pytań dotyczących StorSimple w CSP, przejdź do [StorSimple w CSP: Często zadawane pytania](storsimple-partner-csp-faq.md).
- Jeśli jesteś gotowy do wdrożenia StorSimple, przejdź do [wdrażania StorSimple w dostawcy usług kryptograficznych](storsimple-partner-csp-deploy.md).
