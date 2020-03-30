---
title: Wdrażanie usługi StorSimple Device Manager | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak utworzyć i usunąć usługę StorSimple Device Manager w witrynie Azure portal i opisano sposób zarządzania kluczem rejestracji usługi.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267458"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Wdrażanie usługi StorSimple Device Manager dla tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi można jej używać do zarządzania urządzeniami z witryny Microsoft Azure portal uruchomiony w przeglądarce. Dzięki temu można monitorować wszystkie urządzenia, które są podłączone do usługi StorSimple Device Manager z jednej, centralnej lokalizacji, minimalizując w ten sposób obciążenia administracyjne.

Typowe zadania związane z usługą StorSimple Device Manager to:

* Tworzenie usługi
* Usuwanie usługi
* Pobieranie klucza rejestracji usługi
* Ponowne generowanie klucza rejestracji usługi

W tym samouczku opisano sposób wykonywania każdego z powyższych zadań. Informacje zawarte w tym artykule mają zastosowanie tylko do tablic wirtualnych StorSimple. Aby uzyskać więcej informacji na temat serii StorSimple 8000, przejdź do [wdrażania usługi StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Tworzenie usługi

Aby utworzyć usługę, musisz mieć:

* Subskrypcja z umową Enterprise Agreement
* Aktywne konto magazynu platformy Microsoft Azure
* Informacje rozliczeniowe używane do zarządzania dostępem

Można również wybrać do generowania konta magazynu podczas tworzenia usługi.

Jedna usługa może zarządzać wieloma urządzeniami. Jednak urządzenie nie może obejmować wiele usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacjami, a nawet lokalizacjami wdrażania.

> [!NOTE]
> Do zarządzania urządzeniami z serii StorSimple 8000 i macierzami wirtualnymi StorSimple potrzebne są oddzielne wystąpienia usługi StorSimple Device Manager.


Wykonaj następujące kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi upewnij się, że żadne podłączone urządzenia jej nie używają. Jeśli usługa jest używana, należy dezaktywować podłączone urządzenia. Operacja dezaktywacji spowoduje zerwanie połączenia między urządzeniem a usługą, ale zachowa dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Każde urządzenie, które korzystało z usługi, będzie musiało zostać zresetowane do ustawień fabrycznych, zanim będzie można z niej korzystać z innej usługi. W tym scenariuszu dane lokalne na urządzeniu, a także konfiguracji zostaną utracone.
 

Wykonaj następujące kroki, aby usunąć usługę.

#### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Przejdź do sekcji **Wszystkie zasoby**. Wyszukaj usługę StorSimple Device Manager. Wybierz usługę, którą chcesz usunąć.
   
    ![Wybierz usługę do usunięcia](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Przejdź do pulpitu nawigacyjnego usługi, aby upewnić się, że do usługi nie są podłączone żadne urządzenia. Jeśli w tej usłudze nie ma żadnych urządzeń, zostanie wyświetlony również komunikat banera. Kliknij **pozycję Usuń**.
   
    ![Usuń usługę](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak** w powiadomieniu o potwierdzeniu. 
   
    ![Potwierdzanie usunięcia usługi](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Usunięcie usługi może potrwać kilka minut. Po pomyślnym usunięciu usługi zostaniesz o tym powiadomiony.
   
    ![Pomyślne usunięcie usługi](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Lista usług zostanie odświeżona.

 ![Zaktualizowana lista usług](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi
Po pomyślnym utworzeniu usługi należy zarejestrować urządzenie StorSimple w usłudze. Aby zarejestrować swoje pierwsze urządzenie StorSimple, potrzebny jest klucz rejestracji usługi. Aby zarejestrować dodatkowe urządzenia w istniejącej usłudze StorSimple, potrzebny jest zarówno klucz rejestracji, jak i klucz szyfrowania danych usługi (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [StorSimple security](storsimple-security.md). Klucz rejestracji można uzyskać, uzyskując dostęp do **kluczy** bloku dla usługi.

Wykonaj następujące kroki, aby uzyskać klucz rejestracji usługi.

#### <a name="to-get-the-service-registration-key"></a>Aby uzyskać klucz rejestracji usługi
1. W **bloku StorSimple Device Manager** przejdź do **klawiszy** **zarządzania &gt; ** .
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W **keys** bloku pojawia się klucz rejestracji usługi. Skopiuj klucz rejestracji za pomocą ikony kopiowania. 

Klucz rejestracyjny usługi należy przechowywać w bezpiecznym miejscu. Ten klucz, a także klucz szyfrowania danych usługi, będą potrzebować tego klucza, aby zarejestrować dodatkowe urządzenia w tej usłudze. Po uzyskaniu klucza rejestracji usługi należy skonfigurować urządzenie za pośrednictwem interfejsu programu Windows PowerShell for StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Ponowne generowanie klucza rejestracji usługi
Należy ponownie wygenerować klucz rejestracji usługi, jeśli wymagane jest wykonanie rotacji kluczy lub jeśli lista administratorów usługi uległa zmianie. Podczas ponownego generowania klucza, nowy klucz jest używany tylko do rejestrowania kolejnych urządzeń. Ten proces nie ma wpływu na urządzenia, które zostały już zarejestrowane.

Wykonaj następujące kroki, aby ponownie wygenerować klucz rejestracji usługi.

#### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W **bloku StorSimple Device Manager** przejdź do **klawiszy** **zarządzania &gt; ** .
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W bloku **Klawisze** kliknij pozycję **Regeneruj**.
   
   ![Kliknij przycisk wygeneruj ponownie](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. W **bloku klucz rejestracji usługi regeneracji** przejrzyj akcję wymaganą podczas ponownego generowania kluczy. Wszystkie kolejne urządzenia, które są zarejestrowane w tej usłudze użyje nowego klucza rejestracji. Kliknij przycisk **Wygeneruj,** aby potwierdzić. Zostaniesz powiadomiony po zakończeniu rejestracji.
   
   ![Potwierdź klucz regeneracji](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Pojawi się nowy klucz rejestracji usługi.
   
    ![Potwierdź klucz regeneracji](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Skopiuj ten klucz i zapisz go do rejestracji nowych urządzeń za pomocą tej usługi.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [rozpocząć pracę](storsimple-virtual-array-deploy1-portal-prep.md) z tablicą wirtualną StorSimple.
* Dowiedz się, jak [administrować urządzeniem StorSimple.](storsimple-ova-web-ui-admin.md)

