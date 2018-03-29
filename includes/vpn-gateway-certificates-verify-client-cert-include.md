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
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Jeśli występują problemy z połączeniem, sprawdź następujące elementy:

- Jeśli wyeksportowano certyfikat klienta, upewnij się, że został on wyeksportowany jako plik pfx przy użyciu wartości domyślnej „Jeśli jest to możliwe, dołącz wszystkie certyfikaty ze ścieżki certyfikacji”. Podczas eksportowania z użyciem tej wartości eksportowane są również informacje o certyfikacie głównym. W trakcie instalowania certyfikatu na komputerze klienckim certyfikat główny, który jest zawarty w pliku pfx, również zostanie zainstalowany na komputerze klienckim. Na komputerze klienckim muszą być zainstalowane informacje o certyfikacie głównym. Aby to sprawdzić, wybierz pozycję **Zarządzaj certyfikatami użytkowników** i przejdź do pozycji **Zaufane główne urzędy certyfikacji\Certyfikaty**. Sprawdź, czy certyfikat główny znajduje się na liście. Aby uwierzytelnianie działało, certyfikat główny musi być obecny.

- Jeśli używasz certyfikatu wydanego przy użyciu rozwiązania dla przedsiębiorstwa proponowanego przez urząd certyfikacji i występują problemy z uwierzytelnianiem, sprawdź kolejność uwierzytelniania w certyfikacie klienta. Kolejność elementów na liście uwierzytelniania można sprawdzić przez dwukrotne kliknięcie certyfikatu klienta i przejście do pozycji **Szczegóły > Ulepszone użycie klucza**. Upewnij się, że pierwszym elementem na liście jest „Uwierzytelnienie klienta”. Jeśli tak nie jest, certyfikat klienta należy wydać na podstawie szablonu Użytkownik mającego Uwierzytelnienie klienta jako pierwszy element na liście.

- Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów z połączeniami typu punkt-lokacja, zobacz [Troubleshoot P2S connections (Rozwiązywanie problemów dotyczących połączeń typu punkt-lokacja)](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).