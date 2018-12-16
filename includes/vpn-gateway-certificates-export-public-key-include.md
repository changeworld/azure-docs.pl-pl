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
ms.openlocfilehash: 3c6485406c67bf84b9e0fdfb9f4683abe5062d6c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444278"
---
Po utworzeniu certyfikatu głównego z podpisem własnym, należy wyeksportować plik cer klucza publicznego certyfikatu głównego (nie klucz prywatny). Ten plik zostanie później przekazany na platformę Azure. Poniższe kroki pomogą Ci wyeksportować plik cer dla certyfikatu głównego z podpisem własnym:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Zlokalizuj certyfikat główny z podpisem własnym — zwykle znajduje się w katalogu „Certyfikaty - bieżący użytkownik\Osobisty\Certyfikat” — a następnie kliknij go prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikat w obszarze bieżący użytkownik\osobisty\certyfikat może być otwarty Menedżer certyfikatów w przypadku certyfikatów komputera lokalnego (tytuł będzie "Certyfikatów — lokalny komputer" jako przeciwieństwie do "Certyfikaty — bieżący użytkownik"). Aby otworzyć Menedżera certyfikatów w bieżącej zakres użytkownika uruchomić go za pomocą tego samego programu PowerShell, których certyfikaty zostały utworzone, wpisując ```certmgr```.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie eksportuj klucza prywatnego](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.

   ![Kodowanie Base-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Aby uzyskać **Eksport pliku**, **Przeglądaj** do lokalizacji, do którego chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Twój certyfikat jest został pomyślnie wyeksportowany.

   ![Powodzenie](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Wyeksportowany certyfikat wygląda podobnie do poniższego:

   ![Wyeksportowano](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Jeśli otworzysz wyeksportowany certyfikat za pomocą Notatnika, zostanie wyświetlony podobny do tego przykładu. Sekcja w kolorze niebieskim zawiera informacje, które zostanie przekazany na platformę Azure. Jeśli Otwórz swój certyfikat przy użyciu programu Notepad, a nie wygląda podobnie do poniższego, zwykle oznacza, że nie wyeksportowano go przy użyciu Base-64 certyfikat x.509 szyfrowany algorytmem (. Format CER). Ponadto jeśli chcesz użyć w innym edytorze tekstu, Dowiedz się, że niektóre edytory może prowadzić do niezamierzonych formatowania w tle. To jest utworzenie problemy podczas przekazywania tekst z tego certyfikatu do platformy Azure.

   ![Otwórz w Notatniku](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
