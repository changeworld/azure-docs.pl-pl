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
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059921"
---
Po utworzeniu certyfikatu głównego z podpisem własnym wyeksportuj plik .cer klucza głównego (nie klucz prywatny). Później przekażesz ten plik na platformę Azure. Poniższe kroki pomagają wyeksportować plik cer dla certyfikatu głównego z podpisem własnym:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Zlokalizuj certyfikat główny z podpisem własnym — zwykle znajduje się w katalogu „Certyfikaty - bieżący użytkownik\Osobisty\Certyfikat” — a następnie kliknij go prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikatu w obszarze Bieżący użytkownik\Osobiste\Certyfikaty, być może przypadkowo otwarto "Certyfikaty — komputer lokalny", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, wpisz *certmgr* w oknie konsoli.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie należy eksportować klucza prywatnego](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.

   ![Zakodowane w bazie-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. W przypadku **eksportu pliku** **przejdź** do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certyfikat został pomyślnie wyeksportowany.

   ![Powodzenie](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Wyeksportowany certyfikat wygląda podobnie do następującego:

   ![Eksportowane](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Jeśli wyeksportowany certyfikat zostanie otwarty przy użyciu Notatnika, zobaczysz coś podobnego do tego przykładu. Sekcja na niebiesko zawiera informacje, które są przekazywane na platformę Azure. Jeśli otworzysz certyfikat za pomocą Notatnika i nie wygląda on podobnie do tego, zazwyczaj oznacza to, że nie wyeksportowałeś go przy użyciu zakodowanego X.509 base-64(. CER). Ponadto jeśli chcesz użyć innego edytora tekstu, należy zrozumieć, że niektóre edytory mogą wprowadzać niezamierzone formatowanie w tle. Może to spowodować problemy po przekazaniu tekstu z tego certyfikatu na platformę Azure.

   ![Otwórz za pomocą Notatnika](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
