---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183107"
---
Po utworzeniu certyfikatu głównego z podpisem własnym, należy wyeksportować plik cer klucza publicznego certyfikatu głównego (nie klucz prywatny). Ten plik zostanie później przekazany na platformę Azure. Poniższe kroki pomogą Ci wyeksportować plik cer dla certyfikatu głównego z podpisem własnym:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Zlokalizuj certyfikat główny z podpisem własnym — zwykle znajduje się w katalogu „Certyfikaty - bieżący użytkownik\Osobisty\Certyfikat” — a następnie kliknij go prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikat w obszarze bieżący użytkownik\osobisty\certyfikat, być może przypadkowo otwarto "Certyfikatów — lokalny komputer", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, możesz wpisać *certmgr* w oknie konsoli.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie eksportuj klucza prywatnego](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)** , a następnie kliknij pozycję **Dalej**.

   ![Kodowanie Base-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Aby uzyskać **Eksport pliku**, **Przeglądaj** do lokalizacji, do którego chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Browse](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zakończ](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Twój certyfikat jest został pomyślnie wyeksportowany.

   ![Powodzenie](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Wyeksportowany certyfikat wygląda podobnie do poniższego:

   ![Wyeksportowane](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Jeśli otworzysz wyeksportowany certyfikat za pomocą Notatnika, zostanie wyświetlony podobny do tego przykładu. Sekcja w kolorze niebieskim zawiera informacje, które zostanie przekazany na platformę Azure. Jeśli Otwórz swój certyfikat przy użyciu programu Notepad, a nie wygląda podobnie do poniższego, zwykle oznacza, że nie wyeksportowano go przy użyciu Base-64 certyfikat x.509 szyfrowany algorytmem (. Format CER). Ponadto jeśli chcesz użyć w innym edytorze tekstu, Dowiedz się, że niektóre edytory może prowadzić do niezamierzonych formatowania w tle. To jest utworzenie problemy podczas przekazywania tekst z tego certyfikatu do platformy Azure.

   ![Otwórz w Notatniku](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
