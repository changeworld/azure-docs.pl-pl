---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 8c55b9b9ce6e98e91e7c6a712e0a9dbca0964512
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985478"
---
Każdy komputer kliencki, który połączysz z siecią wirtualną przy użyciu połączenia punkt-lokacja, musi mieć zainstalowany certyfikat klienta. Należy wygenerować go na podstawie certyfikatu głównego i zainstalować na każdym komputerze klienckim. Jeśli nie zainstalujesz prawidłowego certyfikatu klienta, uwierzytelnianie zakończy się niepowodzeniem, gdy klient spróbuje nawiązać połączenie z siecią wirtualną.

Można wygenerować unikatowy certyfikat dla każdego klienta lub można użyć tego samego certyfikatu dla wielu klientów. Zaletą generowania unikatowych certyfikatów klienta jest możliwość odwołania pojedynczego certyfikatu. W przeciwnym razie, jeśli wielu klientów korzysta z tego samego certyfikatu klienta do uwierzytelniania i odwołasz go, konieczne będzie wygenerowanie i zainstalowanie nowych certyfikatów dla każdego klienta korzystającego z tego certyfikatu.

Certyfikaty klienta można wygenerować, posługując się następującymi metodami:

- **Certyfikat przedsiębiorstwa:**

  - Jeśli używasz rozwiązania z certyfikatem przedsiębiorstwa, wygeneruj certyfikat klienta przy użyciu formatu wartości nazwy pospolitej *name@yourdomain.com*. Użyj tego formatu zamiast formatu *nazwa domeny\nazwa użytkownika*.
  - Upewnij się, że certyfikat klienta jest oparty na szablonie certyfikatu użytkownika, którego pierwszym elementem na liście użytkownika jest *Uwierzytelnienie klienta*. Sprawdź certyfikat, dwukrotnie go klikając i wyświetlając obszar **Ulepszone użycie klucza** na karcie **Szczegóły**.

- **Certyfikat główny z podpisem własnym:** Wykonaj czynności opisane w jednym z następujących artykułów dotyczących certyfikatu punkt-lokacja, aby utworzone przez Ciebie certyfikaty klienta były zgodne z połączeniami punkt-lokacja. Czynności opisane w tych artykułach umożliwiają wygenerowanie zgodnego certyfikatu klienta: 

  * [Instrukcje dotyczące programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): W przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Wygenerowane certyfikaty można zainstalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące narzędzia MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Użyj narzędzia MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Mimo iż narzędzie MakeCert jest przestarzałe, przy jego użyciu można nadal generować certyfikaty. Możesz zainstalować wygenerowane certyfikaty na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące systemu Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Gdy certyfikat klienta jest generowany na podstawie certyfikatu głównego z podpisem własnym, jest on automatycznie instalowany na komputerze użytym do jego wygenerowania. Aby zainstalować certyfikat klienta na innym komputerze klienckim, wyeksportuj go jako plik pfx razem z całym łańcuchem certyfikatów. Spowoduje to utworzenie pliku pfx zawierającego informacje o certyfikacie głównym, który jest wymagany do uwierzytelnienia klienta. 

**Aby wyeksportować certyfikat**

Aby uzyskać instrukcje dotyczące eksportowania certyfikatu, zobacz [Generowanie i eksportowanie certyfikatów dla połączeń punkt-lokacja przy użyciu programu PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
