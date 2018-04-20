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
ms.openlocfilehash: 7e19837c1d16ddeea185f340305a0c9c52ce23ff
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
Po utworzeniu certyfikatu głównego z podpisem własnym należy wyeksportować plik .cer klucza publicznego certyfikatu głównego (nie klucz prywatny). Później możesz przekazać ten plik na platformie Azure. Poniższe kroki ułatwiają wyeksportować plik cer dla certyfikatu z podpisem własnym głównego:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Zlokalizuj certyfikat główny z podpisem własnym — zwykle znajduje się w katalogu „Certyfikaty - bieżący użytkownik\Osobisty\Certyfikat” — a następnie kliknij go prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**.

  ![Eksportowanie](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. W Kreatorze kliknij pozycję **Dalej**.

  ![Eksportowanie certyfikatu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

  ![Nie eksportuj klucza prywatnego](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.

  ![Algorytmem Base-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Aby uzyskać **Eksport pliku**, **Przeglądaj** do lokalizacji, do którego chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

  ![Browse](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

  ![Zakończ](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certyfikat jest został pomyślnie wyeksportowany.

  ![Powodzenie](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Wyeksportowany certyfikat wygląda podobnie do następującej:

  ![Wyeksportowano](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Po otwarciu wyeksportowanego certyfikatu za pomocą Notatnika, zobacz podobny do tego przykładu. Sekcja na niebiesko zawiera informacje przekazywany do platformy Azure. Jeśli certyfikat jest Otwórz w Notatniku i nie wygląda podobnie do poniższego, zwykle oznacza to, że nie wyeksportowano go przy użyciu Base-64 certyfikat x.509 szyfrowany algorytmem (. Format CER). Ponadto jeśli chcesz użyć innego edytora tekstów, zrozumieć, że niektóre edytory można wprowadzić niezamierzone formatowania w tle. To jest utworzenie problemów po przekazaniu tekst z tego certyfikatu do platformy Azure.

  ![Otwórz w Notatniku](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)