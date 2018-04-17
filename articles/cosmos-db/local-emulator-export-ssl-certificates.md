---
title: Wyeksportowanie certyfikatów emulatora DB rozwiązania Cosmos Azure | Dokumentacja firmy Microsoft
description: Podczas tworzenia w językach i środowisk uruchomieniowych, który nie należy używać magazynu certyfikatów systemu Windows należy wyeksportować i zarządzanie certyfikatami SSL. Ten wpis zawiera instrukcje krok po kroku.
services: cosmos-db
documentationcenter: ''
keywords: Emulator usługi Azure rozwiązania Cosmos bazy danych
author: voellm
manager: kfile
editor: ''
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87d453cd544b3e913209f50e4e08b77282efab39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Wyeksportowanie certyfikatów emulatora DB rozwiązania Cosmos Azure do użycia z programem Java, Python i Node.js

[**Pobierz Emulator**](https://aka.ms/cosmosdb-emulator)

Emulator DB rozwiązania Cosmos Azure zapewnia środowisko lokalne, które emuluje usługę Azure DB rozwiązania Cosmos do celów programistycznych, łącznie z jej użycie połączenia SSL. Ten wpis pokazano, jak można wyeksportować certyfikaty SSL dla języków i środowisk uruchomieniowych, który nie jest integrowana z magazynu certyfikatów systemu Windows, takich jak Java, który używa własnego [magazyn certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) i języka Python, który używa [gniazda otoki](https://docs.python.org/2/library/ssl.html) i Node.js, który używa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Więcej o emulatora w [użyć emulatora DB rozwiązania Cosmos Azure do programowania i testowania](./local-emulator.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Obracanie certyfikatów
> * Eksportowanie certyfikatu protokołu SSL
> * Poznanie certyfikatu w języku Java, Python i Node.js

## <a name="certification-rotation"></a>Obracanie certyfikacji

Certyfikaty w lokalnym emulatorze DB rozwiązania Cosmos Azure są generowane podczas pierwszego uruchomienia emulator. Istnieją dwa certyfikaty. Jeden używane do łączenia z lokalnym emulatorze i jeden do zarządzania kluczy tajnych w emulatorze. Certyfikat, który chcesz wyeksportować jest certyfikatu połączenia z przyjazną nazwą "DocumentDBEmulatorCertificate".

Oba certyfikaty mogą zostać wygenerowane ponownie, klikając **Resetuj dane** w sposób przedstawiony poniżej z emulatora DB rozwiązania Cosmos Azure działa w na pasku zadań systemu Windows. Ponowne generowanie certyfikatów i ich instalacji do magazynu certyfikatów Java lub używać ich w innym miejscu należy zaktualizować je, w przeciwnym razie aplikacji już nie będzie łączyć się emulatora lokalnego.

![Resetuj Azure DB rozwiązania Cosmos emulatora lokalne dane](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Jak wyeksportować certyfikat protokołu SSL DB rozwiązania Cosmos Azure

1. Uruchom Menedżera certyfikatów systemu Windows przez systemem certlm.msc i przejdź do folderu certyfikatów osobiste-> i otwórz certyfikat o przyjaznej nazwie **DocumentDbEmulatorCertificate**.

    ![Krok 1 eksportu Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Polecenie **szczegóły** następnie **OK**.

    ![Krok 2 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Kliknij przycisk **Kopiuj do pliku...** .

    ![Krok 3 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Kliknij przycisk **Dalej**.

    ![Krok 4 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Kliknij przycisk **nie eksportuj klucza prywatnego**, następnie kliknij przycisk **dalej**.

    ![Krok 5 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Polecenie **certyfikat x.509 szyfrowany algorytmem Base-64 (. CER)** , a następnie **dalej**.

    ![Krok 6 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Nadaj nazwę certyfikatu. W takim przypadku **documentdbemulatorcert** , a następnie kliknij przycisk **dalej**.

    ![Krok 7 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Kliknij przycisk **Zakończ**.

    ![Krok 8 wyeksportować Azure emulator lokalnej bazy danych rozwiązania Cosmos](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Sposób użycia certyfikatu w języku Java

Podczas uruchamiania aplikacji Java lub aplikacji bazy danych MongoDB, które używają klienta Java łatwiej Zainstaluj certyfikat w domyślnym magazynie certyfikatów Java niż przekazywanie "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword = "<password>" flag. Na przykład dołączonej [aplikacji Java pokaz](https://localhost:8081/_explorer/index.html) zależy od domyślnym magazynie certyfikatów.

Postępuj zgodnie z instrukcjami [Dodawanie certyfikatu do magazynu certyfikatów urzędu certyfikacji Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) można zaimportować certyfikatu X.509 w domyślnym magazynie certyfikatów Java. Zwróć Pamiętaj można pracować w katalogu % JAVA_HOME % podczas uruchamiania keytool.

Po "CosmosDBEmulatorCertificate" SSL jest instalowany certyfikat aplikacji powinno być możliwe do nawiązywania połączeń i użyć lokalnego emulatora DB rozwiązania Cosmos Azure. Jeśli nadal występują problemy można wykonać [debugowania połączeń SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) artykułu. Jest bardzo prawdopodobne, certyfikat nie został zainstalowany w magazynie %JAVA_HOME%/jre/lib/security/cacerts. Na przykład, jeśli masz wiele zainstalowanych wersji programu Java aplikacji może używać magazynu cacerts innego niż zaktualizowane.

## <a name="how-to-use-the-certificate-in-python"></a>Sposób użycia certyfikatu w języku Python

Domyślnie [Python SDK(version 2.0.0 or higher)](sql-api-sdk-python.md) interfejsu API SQL nie będą spróbuj i użyć certyfikatu SSL, podczas nawiązywania połączenia z lokalnym emulatora. Jeśli jednak chcesz używać protokołu SSL weryfikacji można wykonać w przykładach w [Python gniazda otoki](https://docs.python.org/2/library/ssl.html) dokumentacji.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Sposób użycia certyfikatu w środowisku Node.js

Domyślnie [Node.js SDK(version 1.10.1 or higher)](sql-api-sdk-node.md) interfejsu API SQL nie będą spróbuj i użyć certyfikatu SSL, podczas nawiązywania połączenia z lokalnym emulatora. Jeśli jednak chcesz używać protokołu SSL weryfikacji można wykonać w przykładach w [dokumentacji Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Obrócony certyfikatów
> * Wyeksportowany certyfikat SSL
> * Przedstawiono sposób używania certyfikatu w języku Java, Python i Node.js

Możesz teraz przejść do tworzenia wyzwalacza HTTP funkcji Azure z bazy danych Azure rozwiązania Cosmos samouczek powiązania wejściowego.

> [!div class="nextstepaction"]
> [Tworzenie funkcji platformy Azure przy użyciu danych wejściowych z bazy danych usługi Azure rozwiązania Cosmos](tutorial-functions-http-trigger.md) 
