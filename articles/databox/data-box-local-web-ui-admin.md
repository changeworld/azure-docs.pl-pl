---
title: Administrowanie urządzeniem Microsoft Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika | Microsoft Docs — dane
description: W tym artykule opisano sposób administrowania urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: c0793cd4adc5359aaa2282eb2505c257315c056f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793397"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Administrowanie urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika

W tym artykule opisano niektóre zadania związane z konfigurowaniem i zarządzaniem, które można wykonywać na urządzeniu Data Box. Urządzeniem Data Box można zarządzać za pomocą interfejsu użytkownika witryny Azure Portal oraz za pomocą lokalnego internetowego interfejsu użytkownika dla urządzenia. Ten artykuł dotyczy zadań, które można wykonywać za pomocą lokalnego internetowego interfejsu użytkownika.

Lokalny internetowy interfejs użytkownika dla urządzenia Data Box jest używany na potrzeby konfiguracji początkowej urządzenia. Lokalnego internetowego interfejsu użytkownika można również używać do zamykania lub ponownego uruchamiania urządzenia Data Box, wykonywania testów diagnostycznych, aktualizacji oprogramowania, wyświetlania dzienników kopiowania i generowania pakietu dziennika dla pomocy technicznej firmy Microsoft.

W tym artykule zawarto następujące samouczki:

- Generowanie pakietu dla pomocy technicznej
- Zamykanie lub ponowne uruchamianie urządzenia
- Wyświetlanie dostępnej pojemności urządzenia
- Pomijanie weryfikacji sumy kontrolnej 

## <a name="generate-support-package"></a>Generowanie pakietu dla pomocy technicznej

Jeśli napotkasz jakiekolwiek problemy z urządzeniem, możesz utworzyć pakiet dla pomocy technicznej z dzienników systemu. Pomoc techniczna firmy Microsoft korzysta z tego pakietu podczas rozwiązywania problemów. Aby wygenerować pakiet dla pomocy technicznej, wykonaj następujące czynności:

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Kontakt z pomocą techniczną** i kliknij przycisk **Tworzenie pakietu pomocy technicznej**.

    ![Tworzenie pakietu dla pomocy technicznej 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Następuje zbieranie danych do pakietu dla pomocy technicznej. Ta operacja trwa kilka minut.

    ![Tworzenie pakietu dla pomocy technicznej 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Po zakończeniu tworzenia pakietu dla pomocy technicznej kliknij przycisk **Pobierz pakiet dla pomocy technicznej**. 

    ![Tworzenie pakietu dla pomocy technicznej 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Przejrzyj i wybierz lokalizację pobierania. Otwórz folder, aby wyświetlić jego zawartość.

    ![Tworzenie pakietu dla pomocy technicznej 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Zamykanie lub ponowne uruchamianie urządzenia

Urządzenie Data Box można zamknąć lub ponownie uruchomić przy użyciu lokalnego internetowego interfejsu użytkownika. Zaleca się, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych. Upewnij się, że kopiowanie danych nie jest w toku podczas wyłączania urządzenia.

Aby zamknąć urządzenie Data Box, wykonaj następujące czynności.

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
2. Kliknij przycisk **Zamknij**.

    ![Zamykanie urządzenia Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.

    ![Zamykanie urządzenia Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Aby ponownie włączyć wyłączone urządzenie, użyj przycisku zasilania na panelu przednim.

Aby ponownie uruchomić urządzenie Data Box, wykonaj następujące czynności.

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
2. Kliknij przycisk **Uruchom ponownie**.

    ![Ponowne uruchamianie urządzenia Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.

   Urządzenie zostaje wyłączone i uruchomione ponownie.

## <a name="view-available-capacity-of-the-device"></a>Wyświetlanie dostępnej pojemności urządzenia

Aby wyświetlić dostępną i używaną pojemność urządzenia, można użyć jego pulpitu nawigacyjnego. 

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Wyświetl pulpit nawigacyjny**.
2. W obszarze **Połącz i skopiuj** jest wyświetlane wolne i zajęte miejsce na urządzeniu.

    ![Wyświetlanie dostępnej pojemności](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Pomijanie weryfikacji sumy kontrolnej

Suma kontrolna jest generowana domyślnie dla danych podczas przygotowywania do wysłania. W niektórych rzadkich przypadkach, w zależności od typu danych (małe rozmiary plików), wydajność może zostać znacząco zmniejszona. W takich przypadkach można pominąć generowanie sumy kontrolnej. 

Zdecydowanie zaleca się, aby nie wyłączać sumy kontrolnej, chyba że jej generowanie ma znaczny wpływ na wydajność.

1. W prawym górnym rogu lokalnego internetowego interfejsu użytkownika urządzenia przejdź do pozycji Ustawienia.

    ![Wyłączanie sumy kontrolnej](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Wyłącz** weryfikację sumy kontrolnej.
3. Kliknij przycisk **Zastosuj**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób [zarządzać urządzeniem Data Box w witrynie Azure Portal](data-box-portal-admin.md).

