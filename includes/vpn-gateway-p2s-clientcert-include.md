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
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Certyfikat klienta jest generowany na podstawie certyfikatu głównego i instalowany na każdym komputerze klienckim. Jeśli prawidłowy certyfikat klienta nie jest zainstalowany, a klient próbuje nawiązać połączenie z siecią wirtualną, uwierzytelnianie nie powiedzie się.

Można wygenerować unikatowy certyfikat dla każdego klienta lub można użyć tego samego certyfikatu dla wielu klientów. Zaletą generowania unikatowych certyfikatów klienta jest możliwość odwołania pojedynczego certyfikatu. W przeciwnym razie, jeśli wielu klientów korzysta z tego samego certyfikatu klienta i zajdzie potrzeba jego odwołania, będzie konieczne wygenerowanie i zainstalowanie nowych certyfikatów dla wszystkich klientów, którzy używają tego certyfikatu do uwierzytelniania.

Certyfikaty klienta można wygenerować, posługując się następującymi metodami:

- **Certyfikat przedsiębiorstwa:**

  - Jeśli używasz rozwiązania z certyfikatem przedsiębiorstwa, wygeneruj certyfikat klienta przy użyciu formatu wartości nazwy pospolitej „name@yourdomain.com”, a nie formatu „nazwa_domeny\nazwa_użytkownika”.
  - Upewnij się, że certyfikat klienta jest oparty na szablonie certyfikatu „Użytkownik”, którego pierwszym elementem na liście użycia jest „Uwierzytelnienie klienta”, a nie Logowanie karty inteligentnej itp. Certyfikat można sprawdzić przez dwukrotne kliknięcie certyfikatu klienta i wyświetlenie pozycji **Szczegóły > Ulepszone użycie klucza**.

- **Certyfikat główny z podpisem własnym:** ważne jest, aby wykonać czynności opisane jednym z poniższych artykułów dotyczących certyfikatu punkt-lokacja. W przeciwnym razie utworzone przez Ciebie certyfikaty klienta nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd klientów podczas nawiązywania połączenia. Czynności opisane w następujących artykułach umożliwiają wygenerowanie zgodnego certyfikatu klienta: 

  * [Instrukcje programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): w przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Certyfikaty generowane na podstawie certyfikatu głównego można instalować na dowolnym kliencie typu punkt-lokacja.
  * [Instrukcje MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): użyj programu MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Rozwiązanie MakeCert jest przestarzałe, ale można nadal używać go do generowania certyfikatów. Certyfikaty generowane na podstawie certyfikatu głównego można instalować na dowolnym kliencie typu punkt-lokacja.

  Gdy certyfikat klienta jest generowany na podstawie certyfikatu głównego z podpisem własnym za pomocą poprzednich instrukcji, jest on automatycznie instalowany na komputerze użytym do jego wygenerowania. Aby zainstalować certyfikat klienta na innym komputerze klienckim, należy go wyeksportować jako plik .pfx, razem z całym łańcuchem certyfikatów. Spowoduje to utworzenie pliku PFX zawierającego informacje o certyfikacie głównym, który jest wymagany do pomyślnego uwierzytelnienia klienta. Czynności wymagane do wyeksportowania certyfikatu zostały opisane w temacie [Certificates - export a client certificate (Certyfikaty — eksportowanie certyfikatu klienta)](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).