---
title: Rozwiązywanie problemów z TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, co zrobić z różnymi problemami przy użyciu certyfikatów TLS/SSL z msal. Biblioteka Objective-C.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369400"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Jak: Rozwiązywanie problemów z rozwiązaniami MSAL dla komputerów iOS TLS/SSL

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z [biblioteki uwierzytelniania Firmy Microsoft (MSAL) dla systemu iOS i macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemy z siecią

**Błąd -1200**: "Wystąpił błąd SSL i nie można nawiązać bezpiecznego połączenia z serwerem".

Ten błąd oznacza, że połączenie nie jest bezpieczne. Występuje, gdy certyfikat jest nieprawidłowy. Aby uzyskać więcej informacji, w tym serwer, `NSURLErrorFailingURLErrorKey` który `userInfo` nie sprawdza TLS, zapoznaj się ze słownikiem obiektu błędu.

Ten błąd pochodzi z biblioteki sieciowej firmy Apple. Pełna lista kodów błędów NSURL znajduje się w NSURLError.h w systemach SDK macOS i iOS. Aby uzyskać więcej informacji na temat tego błędu, zobacz [Kody błędów systemu ładowania adresów URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemy z certyfikatami

Jeśli adres URL zawierający nieprawidłowy certyfikat łączy się z serwerem, który ma być używany w ramach przepływu uwierzytelniania, dobrym początkiem diagnozowania problemu jest przetestowanie adresu URL za pomocą usługi sprawdzania poprawności SSL, takiej jak [test serwera SSL](https://www.ssllabs.com/ssltest/analyze.html). Testuje serwer pod kątem szerokiej gamy scenariuszy i przeglądarek oraz sprawdza, czy nie ma wielu znanych luk w zabezpieczeniach.

Domyślnie nowa funkcja usługi Apple [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) stosuje bardziej rygorystyczne zasady zabezpieczeń do aplikacji korzystających z certyfikatów TLS/SSL. Niektóre systemy operacyjne i przeglądarki internetowe zaczęły domyślnie wymuszać niektóre z tych zasad. Ze względów bezpieczeństwa zalecamy, aby nie wyłączać ats.

Certyfikaty korzystające z skrótów SHA-1 mają znane luki w zabezpieczeniach. Większość nowoczesnych przeglądarek internetowych nie zezwala na certyfikaty z skrótami SHA-1.

## <a name="captive-portals"></a>Portale zniewolonych

Portal zniewolony przedstawia użytkownikowi stronę internetową, gdy po raz pierwszy uzyskuje dostęp do sieci Wi-Fi i nie uzyskał jeszcze dostępu do tej sieci. Przechwytuje ich ruch internetowy, dopóki użytkownik nie spełnia wymagań portalu. Błędy sieciowe, ponieważ użytkownik nie może połączyć się z zasobami sieciowymi, są oczekiwane, dopóki użytkownik nie połączy się za pośrednictwem portalu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [portalach zniewolonych](https://en.wikipedia.org/wiki/Captive_portal) i nowej funkcji [bezpieczeństwa transportu aplikacji (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) firmy Apple.
