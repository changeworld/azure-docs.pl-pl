---
title: Wdrażanie usługi Menedżer urządzeń StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak tworzyć i usuwać usługi Menedżer urządzeń StorSimple w witrynie Azure portal, a w tym artykule opisano sposób zarządzania klucz rejestracji usługi.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 9f6e5b606caa661429a3c4d4a53e2021d57730aa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116959"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Wdrażanie usługi Menedżer urządzeń StorSimple dla macierzy wirtualnej StorSimple
## <a name="overview"></a>Omówienie

Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i nawiązanie połączenia z wieloma urządzeniami StorSimple. Po utworzeniu usługi, można użyć go do zarządzania urządzeniami z poziomu portalu Microsoft Azure, uruchomiony w przeglądarce. Dzięki temu można monitorować wszystkie urządzenia, które są podłączone do usługi Menedżer urządzeń StorSimple z jednym, centralnym miejscu, minimalizując w ten sposób nakładu prac administracyjnych.

Typowe zadania związane z usługą Menedżera urządzeń StorSimple są:

* Tworzenie usługi
* Usuwanie usługi
* Pobieranie klucza rejestracji usługi
* Wygeneruj ponownie klucz rejestracji usługi

W tym samouczku opisano sposób wykonywania wszystkich poprzednich zadań. Informacje zawarte w tym artykule dotyczy tylko macierze wirtualne StorSimple. Aby uzyskać więcej informacji na temat serii StorSimple 8000, przejdź do [wdrażanie usługi StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Tworzenie usługi

Aby utworzyć usługę, musisz mieć:

* Subskrypcji z umową Enterprise Agreement
* Aktywne konto magazynu Microsoft Azure
* Rozliczeń informacje, które są używane do zarządzania dostępem

Możesz również wygenerować konta magazynu podczas tworzenia usługi.

Pojedyncza usługa umożliwia zarządzanie wieloma urządzeniami. Jednak urządzenia nie mogą obejmować wiele usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacji lub nawet lokalizacji wdrożenia.

> [!NOTE]
> Należy osobnych wystąpień usługi Menedżer urządzeń StorSimple do zarządzania urządzeń StorSimple serii 8000 oraz macierzy wirtualnych StorSimple.


Wykonaj poniższe kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Usuwanie usługi

Zanim usuniesz to usługa, upewnij się, że brak połączonych urządzeń jest używany. Jeśli usługa jest w użyciu, należy wyłączyć połączonych urządzeń. Operacja Dezaktywuj sever połączenia między urządzeniem a usługą, ale zachować dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Każde urządzenie, które zostało przy użyciu usługi musi być Resetowanie do ustawień fabrycznych, zanim będzie można używać z inną usługą. W tym scenariuszu dane lokalne na urządzeniu, a także konfigurację, zostaną utracone.
 

Wykonaj poniższe kroki, aby usunąć usługę.

#### <a name="to-delete-a-service"></a>Aby usunąć usługi

1. Przejdź do sekcji **Wszystkie zasoby**. Wyszukiwanie usługi Menedżer urządzeń StorSimple. Wybierz usługę, którą chcesz usunąć.
   
    ![Wybierz usługę do usunięcia](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Przejdź do pulpitu nawigacyjnego usługi, aby upewnić się, Brak z usługą jest połączonych urządzeń. Jeśli nie istnieją żadne urządzenia zarejestrowane w tej usłudze, pojawi się także komunikat transparentu efekt. Kliknij polecenie **Usuń**.
   
    ![Usuwanie usługi](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** w powiadomieniu o potwierdzenie. 
   
    ![Potwierdź usunięcie usługi](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Może upłynąć kilka minut, aż usługi do usunięcia. Po pomyślnym usunięciu usługę, otrzymasz powiadomienie.
   
    ![Usuwanie usługi pomyślne](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Na liście usług zostanie odświeżona.

 ![Zaktualizowana lista usług](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi
Po pomyślnym utworzeniu usługi musisz zarejestrować urządzenia StorSimple w usłudze. Aby zarejestrować pierwszego urządzenia StorSimple, potrzebujesz klucza rejestracji usługi. W celu rejestracji dodatkowych urządzeń za pomocą istniejącej usługi StorSimple, konieczne będzie klucz rejestracji i klucza szyfrowania danych usługi, (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia usługi StorSimple](storsimple-security.md). Możesz pobrać klucz rejestracji, uzyskując dostęp do **klucze** bloku dla Twojej usługi.

Wykonaj poniższe kroki, aby pobrać klucz rejestracji usługi.

#### <a name="to-get-the-service-registration-key"></a>Aby pobrać klucz rejestracji usługi
1. W **Menedżer urządzeń StorSimple** przejdź do bloku **zarządzania &gt;**  **klucze**.
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W **klucze** zostanie wyświetlony blok, klucz rejestracji usługi. Skopiuj klucz rejestracji za pomocą ikony kopiowania. 

Klucz rejestracji usługi należy przechowywać w bezpiecznym miejscu. Konieczne będzie ten klucz, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowych urządzeń z tą usługą. Po uzyskaniu klucza rejestracji usługi, należy skonfigurować urządzenie za pomocą programu Windows PowerShell dla usługi StorSimple interfejsu.

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Konieczne będzie ponowne generowanie klucza rejestracji usługi, jeśli jest wymagane do wykonania wymiany kluczy, lub jeśli zmieniono listy administratorów usługi. Podczas ponownego generowania klucza, nowy klucz jest używany tylko do celów rejestracji kolejnych urządzeń. Nie przez ten proces dotyczy to urządzeń, które zostały już zarejestrowane.

Wykonaj poniższe kroki, aby ponownie wygenerować klucz rejestracji usługi.

#### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W **Menedżer urządzeń StorSimple** przejdź do bloku **zarządzania &gt;**  **klucze**.
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W **klucze** bloku kliknij **ponownie wygenerować**.
   
   ![Kliknij przycisk Wygeneruj ponownie klucz](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. W **klucz rejestracji usługi ponowne generowanie** bloku, przeglądanie akcji wymagany, gdy klucze są generowane. Wszystkie kolejne urządzenia, które są zarejestrowane w tej usłudze użyje nowy klucz rejestracji. Kliknij przycisk **ponownie wygenerować** o potwierdzenie. Otrzymasz powiadomienie po zakończeniu rejestracji.
   
   ![Potwierdź ponowne generowanie klucza](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Pojawi się nowy klucz rejestracji usługi.
   
    ![Potwierdź ponowne generowanie klucza](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Skopiuj ten klucz i zapisać go do rejestrowania nowych urządzeń z tą usługą.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [wprowadzenie](storsimple-virtual-array-deploy1-portal-prep.md) za pomocą rozwiązania StorSimple Virtual Array.
* Dowiedz się, jak [administrowania urządzeniem StorSimple](storsimple-ova-web-ui-admin.md).

