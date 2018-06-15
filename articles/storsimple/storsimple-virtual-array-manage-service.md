---
title: Wdrażanie usługi Menedżer urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak tworzyć i usuwać usługi Menedżer StorSimple urządzenia w portalu Azure i opisano sposób zarządzania klucz rejestracji usługi.
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
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876103"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Wdrażanie usługi Menedżer StorSimple urządzenia dla tablicy wirtualnego StorSimple
## <a name="overview"></a>Omówienie

Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi musisz służy do zarządzania urządzeniami z portalu Microsoft Azure działającego w przeglądarce. Dzięki temu można monitorować wszystkie urządzenia, które są połączone z usługą Menedżera urządzeń StorSimple z jednym centralnym miejscu, w tym samym minimalizując nakładu prac administracyjnych.

Typowe zadania związane z usługą Menedżera urządzeń StorSimple są:

* Tworzenie usługi
* Usuwanie usługi
* Pobieranie klucza rejestracji usługi
* Wygeneruj ponownie klucz rejestracji usługi

W tym samouczku opisano sposób wykonywania wszystkich poprzednich zadań. Informacje zawarte w tym artykule ma zastosowanie tylko do tablic wirtualne StorSimple. Aby uzyskać więcej informacji dotyczących serii StorSimple 8000, przejdź do [wdrożyć usługę Menedżer StorSimple](storsimple-manage-service.md).

## <a name="create-a-service"></a>Tworzenie usługi

Aby utworzyć usługę, musi być:

* Subskrypcja z umową Enterprise
* Aktywne konto magazynu Microsoft Azure
* Informacji dotyczących rozliczeń, która służy do zarządzania dostępem

Można także wygenerować konta magazynu podczas tworzenia usługi.

Jednej usługi można zarządzać wieloma urządzeniami. Jednak urządzenia nie może obejmować wiele usług. Duże przedsiębiorstwa mogą być wiele wystąpień usługi do pracy z różnych subskrypcji, organizacji lub nawet miejsc wdrożenia.

> [!NOTE]
> Należy oddzielnego wystąpienia usługi Menedżer urządzenia StorSimple do zarządzania urządzeń z serii StorSimple 8000 i tablice wirtualne StorSimple.


Wykonaj poniższe kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi, upewnij się, że żadnych podłączonych urządzeń używają go. Jeśli usługa jest w użyciu, Dezaktywuj połączonych urządzeń. Operacja Dezaktywuj sever połączenia między urządzeniem i usługi, ale zachować dane urządzenie w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Dowolne urządzenie, które zostało przy użyciu usługi będzie trzeba resetowanie, zanim będzie można go używać z inną usługą do ustawień fabrycznych. W tym scenariuszu dane lokalne na urządzeniu, a także konfiguracji, zostaną utracone.
 

Wykonaj poniższe kroki, aby usunąć usługę.

#### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Przejdź do **wszystkie zasoby**. Wyszukiwanie usługi Menedżer StorSimple urządzenia. Wybierz usługi, który chcesz usunąć.
   
    ![Wybierz usługę do usunięcia](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Przejdź do pulpitu nawigacyjnego usługi, aby upewnić się, Brak urządzeń, połączone z tą usługą. Jeśli nie ma żadnych urządzeń zarejestrowanych w usłudze tej usługi, pojawi się także komunikat transparentu, w celu. Kliknij polecenie **Usuń**.
   
    ![Usuwanie usługi](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** powiadomienia o potwierdzenie. 
   
    ![Potwierdź usunięcie usługi](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Może upłynąć kilka minut, aż usługi do usunięcia. Po pomyślnym usunięciu usługi, otrzymasz powiadomienie.
   
    ![Usunięcie usługi powiodło się](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Na liście usług będą odświeżane.

 ![Zaktualizowaną listę usług](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi
Po pomyślnym utworzeniu usługi musisz zarejestrować urządzenie StorSimple w usłudze. Aby zarejestrować pierwszego urządzenia StorSimple, konieczne będzie klucz rejestracji usługi. Aby zarejestrować dodatkowych urządzeń z istniejącej usługi StorSimple, konieczne będzie zarówno klucz rejestracji i klucza szyfrowania danych usługi (który jest generowany na pierwszym urządzeniem podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia usługi StorSimple](storsimple-security.md). Możesz pobrać klucz rejestracji po zalogowaniu się do **klucze** bloku dla usługi.

Wykonaj poniższe kroki, aby pobrać klucz rejestracji usługi.

#### <a name="to-get-the-service-registration-key"></a>Aby pobrać klucz rejestracji usługi
1. W **Menedżera urządzeń StorSimple** bloku, przejdź do **zarządzania &gt;**  **klucze**.
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W **klucze** zostanie wyświetlony blok, klucz rejestracji usługi. Skopiuj klucz rejestracji za pomocą ikony kopiowania. 

Klucz rejestracji usługi należy przechowywać w bezpiecznym miejscu. Należy tego klucza, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowych urządzeń z tą usługą. Po uzyskaniu klucza rejestracji usługi, należy skonfigurować urządzenie za pomocą programu Windows PowerShell dla StorSimple interfejsu.

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Należy ponownie wygenerować klucz rejestracji usługi, jeśli jest wymagane do wykonania obrotu klucza lub zmiana listy administratorów usługi. Podczas ponownego generowania klucza, nowy klucz służy tylko do celów rejestracji kolejnych urządzeń. Przez ten proces nie dotyczy to urządzeń, które zostały już zarejestrowane.

Wykonaj poniższe kroki, aby wygenerować ponownie klucz rejestracji usługi.

#### <a name="to-regenerate-the-service-registration-key"></a>Można ponownie wygenerować klucz rejestracji usługi
1. W **Menedżera urządzeń StorSimple** bloku, przejdź do **zarządzania &gt;**  **klucze**.
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W **klucze** bloku, kliknij przycisk **ponownie wygenerować**.
   
   ![Kliknij przycisk regenerate](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. W **klucz rejestracji usługi Regenerate** bloku, przeglądanie nagrań akcji wymagany, gdy klucze są generowane. Wszystkie kolejne urządzenia, które są zarejestrowane z tą usługą użyje nowy klucz rejestracji. Kliknij przycisk **ponownie wygenerować** o potwierdzenie. Po zakończeniu rejestracji, otrzymasz powiadomienie.
   
   ![Potwierdź klucz regenerate](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Pojawi się nowy klucz rejestracji usługi.
   
    ![Potwierdź klucz regenerate](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Skopiuj ten klucz i zapisać go do rejestracji żadnych nowych urządzeń z tą usługą.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wprowadzenie](storsimple-virtual-array-deploy1-portal-prep.md) tablicą wirtualne StorSimple.
* Dowiedz się, jak [administrowania urządzenia StorSimple](storsimple-ova-web-ui-admin.md).

