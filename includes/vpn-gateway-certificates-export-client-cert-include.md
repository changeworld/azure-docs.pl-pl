---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764017"
---
Możesz wygenerować certyfikat klienta, jest automatycznie instalowany na komputerze, którego użyto do jego wygenerowania. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, należy wyeksportować certyfikat klienta, który został wygenerowany.

1. Aby wyeksportować certyfikat klienta, otwórz okno **Zarządzaj certyfikatami użytkowników**. Certyfikaty klientów, które zostanie wygenerowany domyślnie znajdują się w katalogu "Certyfikaty - bieżący użytkownik\osobisty\certyfikat". Kliknij prawym przyciskiem myszy certyfikat klienta, który chcesz wyeksportować, kliknij pozycję **wszystkie zadania**, a następnie kliknij przycisk **wyeksportować** otworzyć **Kreatora eksportu certyfikatów**.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. W Kreatorze eksportu certyfikatów kliknij **dalej** aby kontynuować.

   ![Następne kroki](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wybierz **tak, Eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

   ![Eksportowanie klucza prywatnego](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stronie **Format pliku eksportu** pozostaw wybrane wartości domyślne. Upewnij się, że jest zaznaczona pozycja **Jeśli jest to możliwe, dołącz wszystkie certyfikaty do ścieżki certyfikacji**. To ustawienie również Eksportuje informacje o certyfikacie głównym, wymaganego do uwierzytelnienia klienta się pomyślnie. Bez tego uwierzytelnianie klienta nie powiedzie się, ponieważ klient nie ma zaufanego certyfikatu głównego. Następnie kliknij przycisk **Dalej**.

   ![format pliku eksportu](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stronie **Zabezpieczenia** należy włączyć ochronę klucza prywatnego. Jeśli wybierzesz opcję użycia hasła, zapisz lub zapamiętaj hasło ustawione dla tego certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. W obszarze **Eksport pliku** wybierz pozycję **Przeglądaj**, aby przejść do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![plik do wyeksportowania](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![zakończ](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)