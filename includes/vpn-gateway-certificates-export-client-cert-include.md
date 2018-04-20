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
ms.openlocfilehash: 517ebbf739c64c0364dc21386fee86ebc740e997
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
Możesz wygenerować certyfikat klienta, jest automatycznie instalowana na komputerze, który został użyty do wygenerowania. Jeśli chcesz zainstalować certyfikat klienta na inny komputer kliencki, należy wyeksportować certyfikat klienta, który zostanie wygenerowany.

1. Aby wyeksportować certyfikat klienta, otwórz okno **Zarządzaj certyfikatami użytkowników**. Certyfikaty klienta, które zostanie wygenerowany domyślnie znajdują się w "Certyfikaty — bieżący User\Personal\Certificates". Kliknij prawym przyciskiem myszy certyfikat klienta, który chcesz wyeksportować, kliknij pozycję **wszystkie zadania**, a następnie kliknij przycisk **wyeksportować** otworzyć **Kreatora eksportu certyfikatów**.

  ![Eksportowanie](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. W Kreatorze eksportu certyfikatów kliknij **dalej** aby kontynuować.

  ![Następne kroki](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wybierz **tak, Eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

  ![Eksportowanie klucza prywatnego](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stronie **Format pliku eksportu** pozostaw wybrane wartości domyślne. Upewnij się, że jest zaznaczona pozycja **Jeśli jest to możliwe, dołącz wszystkie certyfikaty do ścieżki certyfikacji**. To ustawienie dodatkowo Eksportuje informacje certyfikat główny jest wymagany do uwierzytelniania klienta powiodło się. Bez tego uwierzytelnianie klienta nie powiedzie się, ponieważ klient nie ma zaufanego certyfikatu głównego. Następnie kliknij przycisk **Dalej**.

  ![format pliku eksportu](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stronie **Zabezpieczenia** należy włączyć ochronę klucza prywatnego. Jeśli wybierzesz opcję użycia hasła, zapisz lub zapamiętaj hasło ustawione dla tego certyfikatu. Następnie kliknij przycisk **Dalej**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. W obszarze **Eksport pliku** wybierz pozycję **Przeglądaj**, aby przejść do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

  ![plik do eksportu](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

  ![zakończ](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)