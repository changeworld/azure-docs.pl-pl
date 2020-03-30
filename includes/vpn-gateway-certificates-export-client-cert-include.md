---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059968"
---
Podczas generowania certyfikatu klienta jest on automatycznie instalowany na komputerze, który został użyty do jego wygenerowania. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, musisz wyeksportować wygenerowany certyfikat klienta.

1. Aby wyeksportować certyfikat klienta, otwórz okno **Zarządzaj certyfikatami użytkowników**. Wygenerowane certyfikaty klienta są domyślnie zlokalizowane w polu "Certyfikaty — bieżący użytkownik\Osobisty\Certyfikaty". Kliknij prawym przyciskiem myszy certyfikat klienta, który chcesz wyeksportować, kliknij **wszystkie zadania**, a następnie kliknij polecenie **Eksportuj,** aby otworzyć **Kreatora eksportu certyfikatów**.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. W Kreatorze eksportu certyfikatów kliknij przycisk **Dalej,** aby kontynuować.

   ![Następne kroki](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wybierz **pozycję Tak, wyeksportuj klucz prywatny,** a następnie kliknij przycisk **Dalej**.

   ![eksportowanie klucza prywatnego](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stronie **Format pliku eksportu** pozostaw wybrane wartości domyślne. Upewnij się, że jest zaznaczona pozycja **Jeśli jest to możliwe, dołącz wszystkie certyfikaty do ścieżki certyfikacji**. To ustawienie dodatkowo eksportuje informacje o certyfikatie głównym, które są wymagane do pomyślnego uwierzytelnienia klienta. Bez niego uwierzytelnianie klienta kończy się niepowodzeniem, ponieważ klient nie ma zaufanego certyfikatu głównego. Następnie kliknij przycisk **Dalej**.

   ![eksportowanie formatu pliku](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stronie **Zabezpieczenia** należy włączyć ochronę klucza prywatnego. Jeśli wybierzesz opcję użycia hasła, zapisz lub zapamiętaj hasło ustawione dla tego certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. W obszarze **Eksport pliku** wybierz pozycję **Przeglądaj**, aby przejść do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![plik do wyeksportowania](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![zakończ](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)