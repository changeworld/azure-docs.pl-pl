---
title: Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB
description: Podczas tworzenia w językach i środowiskach run, które nie korzystają z Magazynu certyfikatów systemu Windows, należy wyeksportować certyfikaty TLS/SSL i zarządzać nimi. Ten wpis zawiera instrukcje krok po kroku.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: b4283ea7d500ca038d9f1cade89c772880ece199
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409064"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB do użycia w językach Java, Python i Node.js

[**Pobieranie emulatora**](https://aka.ms/cosmosdb-emulator)

Emulator usługi Azure Cosmos DB udostępnia środowisko lokalne, które emuluje usługę Usługi Azure Cosmos DB do celów programisty, w tym do korzystania z połączeń TLS. Ten wpis pokazuje, jak wyeksportować certyfikaty TLS/SSL do użytku w językach i środowiskach run, które nie integrują się z Magazynem certyfikatów systemu Windows, takim jak Java, który używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) i Pythona, który używa [otoki gniazd](https://docs.python.org/2/library/ssl.html) i Node.js, który używa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Więcej informacji na temat emulatora zawiera artykuł [Use the Azure Cosmos DB Emulator for development and testing](./local-emulator.md) (Korzystanie z emulatora usługi Azure Cosmos DB na potrzeby programowania i testowania).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wymiana certyfikatów
> * Eksportowanie certyfikatu TLS/SSL
> * Poznanie sposobu używania certyfikatu w środowiskach Java, Python i Node.js

## <a name="certification-rotation"></a>Wymiana certyfikatów

Certyfikaty w lokalnym emulatorze usługi Azure Cosmos DB są generowane podczas pierwszego uruchomienia emulatora. Istnieją dwa certyfikaty. Jeden używany do nawiązywania połączenia z lokalnym emulatorem i jeden do zarządzania wpisami tajnymi w emulatorze. Certyfikat, który chcesz wyeksportować, to certyfikat połączenia z przyjazną nazwą „DocumentDBEmulatorCertificate”.

Oba certyfikaty można wygenerować ponownie, klikając pozycję **Reset Data** (Resetuj dane) w emulatorze usługi Azure Cosmos DB uruchomionym w obszarze powiadomień systemu Windows. Jeśli certyfikaty zostaną ponownie wygenerowane i zainstalowane w magazynie certyfikatów języka Java lub użyte w innym miejscu, konieczne będzie ich zaktualizowanie, ponieważ w przeciwnym razie aplikacja nie będzie już nawiązywała połączenia z lokalnym emulatorem.

![Resetowanie danych lokalnego emulatora usługi Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Jak wyeksportować certyfikat TLS/SSL usługi Azure Cosmos DB

1. Uruchom menedżera certyfikatów systemu Windows, uruchamiając plik certlm.msc, przejdź do folderu Osobiste->Certyfikaty i otwórz certyfikat o przyjaznej nazwie **DocumentDbEmulatorCertificate**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Kliknij pozycję **Szczegóły**, a następnie przycisk **OK**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Kliknij pozycję **Kopiuj do pliku...**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Kliknij przycisk **alej**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 4](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Kliknij pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij przycisk **Dalej**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 5](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Kliknij pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie przycisk **Dalej**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 6](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Nadaj certyfikatowi nazwę. W tym przypadku wpisz nazwę **documentdbemulatorcert**, a następnie kliknij przycisk **Dalej**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 7](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Kliknij przycisk **Zakończ**.

    ![Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 8](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Sposób użycia certyfikatu w środowisku Java

Podczas uruchamiania aplikacji Java lub mongodb, które używają klienta Java łatwiej jest zainstalować certyfikat `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` w domyślnym magazynie certyfikatów Java niż przekazywanie flag. Na przykład dołączona [aplikacja demonstracyjna Java](https://localhost:8081/_explorer/index.html) zależy od domyślnego magazynu certyfikatów.

Postępuj zgodnie z instrukcjami w artykule [Dodawanie certyfikatu do magazynu certyfikatów Java CA](https://docs.microsoft.com/azure/java-add-certificate-ca-store), aby zaimportować certyfikat X.509 do domyślnego magazynu certyfikatów Java. Pamiętaj, że podczas uruchamiania narzędzia keytool będziesz pracować w katalogu %JAVA_HOME%.

Po zainstalowaniu certyfikatu TLS/SSL "CosmosDBEmulatorCertificate" aplikacja powinna mieć możliwość nawiązania połączenia i korzystania z lokalnego emulatora usługi Azure Cosmos DB. Jeśli nadal występują problemy, możesz wykonać czynności opisane w artykule [Debugowanie połączeń SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Jest bardzo prawdopodobne, że certyfikat nie został zainstalowany w magazynie %JAVA_HOME%/jre/lib/security/cacerts. Na przykład jeśli masz wiele zainstalowanych wersji języka Java, aplikacja może używać innego magazynu cacerts niż ten, który został zaktualizowany.

## <a name="how-to-use-the-certificate-in-python"></a>Sposób użycia certyfikatu w środowisku Python

Domyślnie [pakiet SDK języka Python (wersja 2.0.0 lub nowsza)](sql-api-sdk-python.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas łączenia się z lokalnym emulatorem. Jeśli jednak chcesz użyć sprawdzania poprawności TLS, możesz wykonać przykłady w dokumentacji [otoki gniazda Języka Python.](https://docs.python.org/2/library/ssl.html)

## <a name="how-to-use-the-certificate-in-nodejs"></a>Sposób użycia certyfikatu w środowisku Node.js

Domyślnie [node.js SDK(wersja 1.10.1 lub nowsza)](sql-api-sdk-node.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas łączenia się z emulatorem lokalnym. Jeśli jednak chcesz użyć sprawdzania poprawności TLS, możesz postępować zgodnie z przykładami w [dokumentacji Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Wymieniono certyfikaty
> * Wyeksportowany certyfikat TLS/SSL
> * Przedstawiono sposób używania certyfikatu w językach Java, Python i Node.js

Teraz możesz przejść do sekcji pojęć, aby uzyskać więcej informacji na temat usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)
