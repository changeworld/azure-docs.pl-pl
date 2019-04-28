---
author: WenJason
ms.service: databox
ms.topic: include
origin.date: 12/07/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728411"
---
1. Zaloguj się do urządzenia Data Box. Upewnij się, że jest odblokowane.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Przejdź do pozycji **Ustaw interfejsy sieciowe**. Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Przejdź do pozycji **Połącz i skopiuj** i kliknij przycisk **REST (wersja zapoznawcza)**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. W oknie dialogowym **Uzyskaj dostęp do konta magazynu i przekaż dane** skopiuj **Punkt końcowy usługi blob**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Uruchom program **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w folderze `C:\Windows\System32\Drivers\etc`.
6. Dodaj do pliku **hosts** następujący wpis: `<device IP address> <Blob service endpoint>`
7. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
