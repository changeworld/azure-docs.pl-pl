---
title: Rozwiązywanie problemów z brama zarządzania danymi | Dokumentacja firmy Microsoft
description: Zawiera wskazówki dotyczące rozwiązywania problemów związanych z bramą zarządzania danymi.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 66e6725dd4e23eeaf0c8d0dcff6e5d26e3743218
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624288"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Rozwiązywanie problemów z używaniem bramy zarządzania danymi
Ten artykuł zawiera informacje na temat rozwiązywania problemów przy użyciu bramy zarządzania danymi.

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [hosta samodzielnego środowiska uruchomieniowego integracji z fabryki danych w wersji 2](../create-self-hosted-integration-runtime.md).

Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby uzyskać szczegółowe informacje na temat bramy. Zobacz [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu przewodnik przenoszenia danych z lokalną bazą danych programu SQL Server do magazynu obiektów Blob Microsoft Azure przy użyciu bramy.

## <a name="failed-to-install-or-register-gateway"></a>Nie można zainstalować lub zarejestruj bramę
### <a name="1-problem"></a>1. Problem
Widzisz ten komunikat o błędzie podczas instalowania i rejestrowanie bramy, w szczególności podczas pobierania pliku instalacji bramy.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Przyczyna
Komputer, na którym chcesz zainstalować bramę nie udało się pobrać najnowszy plik instalacyjny bramy z Centrum pobierania z powodu problemu z siecią.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia serwera proxy zapory, aby zobaczyć, czy ustawienia Blokuj połączenia sieciowego z komputera w celu [Centrum pobierania](https://download.microsoft.com/)i zaktualizuj ustawienia odpowiednio.

Alternatywnie można pobrać plik instalacyjny najnowszą bramę z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) na innych komputerach, które mogą uzyskiwać dostęp do Centrum pobierania. Można następnie skopiuj plik Instalatora na komputerze hosta bramy i uruchom go ręcznie, aby zainstalować i zaktualizować bramę.

### <a name="2-problem"></a>2. Problem
Zostanie wyświetlony ten błąd, gdy próbuje zainstalować bramę, klikając **Zainstaluj bezpośrednio na tym komputerze** w portalu Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Przyczyna
Brama jest już zainstalowana na tym komputerze.

#### <a name="resolution"></a>Rozwiązanie
Odinstaluj istniejącą bramę na komputerze, a następnie kliknij przycisk **Zainstaluj bezpośrednio na tym komputerze** połączyć ponownie.

### <a name="3-problem"></a>3. Problem
Ten błąd może być widoczna podczas rejestrowania nowej bramy.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Przyczyna
Ten komunikat zostanie wyświetlony dla jednego z następujących powodów:

* Format klucz bramy jest nieprawidłowy.
* Klucz bramy został unieważniony.
* Wygenerowano ponownie klucz bramy z portalu.  

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy używasz klucz prawo bramy z portalu. W razie potrzeby ponownie wygenerować klucz i użyć klucza do zarejestrowania bramy.

### <a name="4-problem"></a>4. Problem
Można napotkać komunikat o błędzie, jeśli rejestrujesz bramy.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Zawartość i format klucza jest nieprawidłowy](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Przyczyna
Zawartość lub format klucz bramy wejściowy jest nieprawidłowy. Jednego z powodów może być to, że tylko część klucz został skopiowany z portalu lub jest używany nieprawidłowy klucz.

#### <a name="resolution"></a>Rozwiązanie
Wygeneruj klucz bramy w portalu, a następnie użyj przycisku kopiowania, aby skopiować cały klucz. Następnie wklej go w tym oknie, aby zarejestrować bramę.

### <a name="5-problem"></a>5. Problem
Można napotkać komunikat o błędzie, jeśli rejestrujesz bramy.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Przyczyna
Wygenerowano ponownie klucz bramy lub bramy został usunięty w portalu Azure. Możliwe również, jeśli instalacja bramy zarządzania danymi nie jest najnowszą.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia bramy zarządzania danymi jest najnowsza wersja, możesz znaleźć najnowszej wersji w programie Microsoft [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Jeśli Instalator jest bieżący / najnowsze i brama nadal istnieje w portalu, ponownie wygenerować klucz bramy w portalu Azure, użyj przycisku kopiowania, aby skopiować cały klucz i wklej go w tym oknie, aby zarejestrować bramę. W przeciwnym razie ponownie bramę i zacząć od nowa.

### <a name="6-problem"></a>6. Problem
Można napotkać komunikat o błędzie, jeśli rejestrujesz bramy.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Przyczyna
Ten błąd może się zdarzyć, ponieważ brama została usunięta lub ponownie wygenerować klucz skojarzone bramy.

#### <a name="resolution"></a>Rozwiązanie
Jeśli brama została usunięta, ponownie utwórz bramę z portalu, kliknij pozycję **zarejestrować**, skopiuj klucz z portalu, wklej go, a następnie spróbuj zarejestrować bramę.

Jeśli brama nadal istnieje, ale wygenerowano jego klucza, należy użyć nowego klucza do rejestrowania bramy. Jeśli nie masz klucza, należy ponownie wygenerować klucz ponownie z portalu.

### <a name="7-problem"></a>7. Problem
W przypadku rejestrowania bramy, może być konieczne wprowadź ścieżkę i hasło dla certyfikatu.

![Określ certyfikat](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Przyczyna
Brama została zarejestrowana na innych komputerach przed. Podczas początkowej rejestracji bramy certyfikat szyfrowania został skojarzony z bramą. Certyfikat można własnym generowanych przez bramę lub przez użytkownika.  Ten certyfikat służy do szyfrowania poświadczeń dostępu do magazynu danych (połączonej usługi).  

![Eksportowanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Podczas przywracania bramy na inny komputer hosta, Kreator rejestracji żąda tego certyfikatu do odszyfrowania zaszyfrowanych wcześniej za pomocą tego certyfikatu poświadczeń.  Bez tego certyfikatu nie można odszyfrować poświadczeń przez nową bramę i wykonaniami działania kopiowania kolejnych skojarzone z tym nową bramę zakończy się niepowodzeniem.  

#### <a name="resolution"></a>Rozwiązanie
Jeśli certyfikat poświadczeń zostały wyeksportowane z oryginalnego komputera bramy przy użyciu **wyeksportować** znajdującego się na **ustawienia** karcie w danych Menedżera konfiguracji bramy zarządzania, w tym miejscu użyć certyfikatu.

Nie można pominąć ten etap, podczas przywracania bramy. Jeśli brakuje certyfikatu, należy usunąć bramę z portalu i ponownie utwórz nową bramę.  Ponadto należy zaktualizować wszystkie połączone usługi, powiązane z bramą, ponownego wprowadzania poświadczeń.

### <a name="8-problem"></a>8. Problem
Może pojawić następujący komunikat o błędzie.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Przyczyna
Ten błąd wystąpi, gdy brama jest w środowisku, które wymaga serwera proxy HTTP uzyskać dostęp do zasobów w Internecie lub serwer proxy uwierzytelniania hasła jest zmieniany, ale nie jest odpowiednio aktualizowany w bramy.

#### <a name="resolution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami [zagadnienia dotyczące serwera Proxy](#proxy-server-considerations) sekcji tego artykułu, a następnie skonfiguruj ustawienia serwera proxy z danych Menedżera konfiguracji bramy zarządzania.

## <a name="gateway-is-online-with-limited-functionality"></a>Brama jest w trybie online z ograniczoną funkcjonalnością
### <a name="1-problem"></a>1. Problem
Możesz wyświetlić stan bramy jako online z ograniczoną funkcjonalnością.

#### <a name="cause"></a>Przyczyna
Możesz zobaczyć stan bramy jako online z ograniczoną funkcjonalnością dla jednego z następujących powodów:

* Brama nie można połączyć z usługą w chmurze za pomocą usługi Azure Service Bus.
* Usługi w chmurze nie można połączyć się z bramą za pośrednictwem usługi Service Bus.

Gdy brama jest dostępna z ograniczoną funkcjonalnością, nie można utworzyć potoki danych kopiowania danych do lub z lokalnych magazynów danych za pomocą Kreatora kopiowania fabryki danych. Jako rozwiązanie alternatywne można Edytor fabryki danych w portalu, Visual Studio lub Azure PowerShell.

#### <a name="resolution"></a>Rozwiązanie
Rozwiązanie tego problemu (online z ograniczoną funkcjonalnością) jest na podstawie tego, czy brama nie może nawiązać połączenia usługi w chmurze lub w inny sposób. Poniższe sekcje zawierają te rozwiązania.

### <a name="2-problem"></a>2. Problem
Zostanie wyświetlony następujący błąd.

`Error: Gateway cannot connect to cloud service through service bus`

![Brama nie można połączyć z usługą w chmurze](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna
Brama nie może połączyć się do usługi w chmurze za pośrednictwem usługi Service Bus.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj te kroki, aby przywrócić bramę do trybu online:

1. Zezwalaj na adres IP reguły wychodzące na komputerze bramy i firmowej zapory. Adresy IP można znaleźć w dzienniku zdarzeń systemu Windows (identyfikator == 401): nastąpiła próba uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu XX. XX. XX. XX:9350.
* Skonfiguruj ustawienia serwera proxy w bramie. Zobacz [zagadnienia dotyczące serwera Proxy](#proxy-server-considerations) sekcji, aby uzyskać szczegółowe informacje.
* Włącz portów wychodzących 5671 i 9350-9354 zapory systemu Windows na komputerze bramy i firmowej zapory. Zobacz [portów i zapory](#ports-and-firewall) sekcji, aby uzyskać szczegółowe informacje. Ten krok jest opcjonalny, ale firma Microsoft zaleca ze względów wydajności.

### <a name="3-problem"></a>3. Problem
Zostanie wyświetlony następujący błąd.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Przyczyna
Błąd przejściowy w łączności sieciowej.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj te kroki, aby przywrócić bramę do trybu online:

1. Zaczekaj kilka minut, łączność zostanie automatycznie odzyskana po błędzie został usunięty.
* Jeśli błąd będzie się powtarzać, uruchom ponownie usługę bramy.

## <a name="failed-to-author-linked-service"></a>Nie można utworzyć połączonej usługi
### <a name="problem"></a>Problem
Ten błąd może być widoczna podczas próby użycia Menedżera poświadczeń w portalu, wprowadź poświadczenia dla nowej usługi połączonej lub zaktualizować poświadczenia istniejącą połączoną usługę.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Gdy zostanie wyświetlony ten błąd, strony ustawień z danych Menedżera konfiguracji bramy zarządzania może wyglądać Poniższy zrzut ekranu.

![Nie można nawiązać połączenia bazy danych](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Przyczyna
Certyfikat SSL mogą zostać utracone na komputerze bramy. Komputer z bramą, nie można załadować obecnie certyfikat używany do szyfrowania SSL. Może być również wyświetlany jest komunikat o błędzie w dzienniku zdarzeń, który jest podobny do następującego.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki w celu rozwiązania problemu:

1. Uruchom Menedżera konfiguracji bramy zarządzania danymi.
2. Przejdź do karty **Ustawienia**.  
3. Kliknij przycisk **zmienić** przycisk, aby zmienić certyfikat SSL.

   ![Zmień przycisk certyfikat](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Wybierz nowego certyfikatu jako certyfikatu SSL. Możesz użyć dowolnego certyfikatu SSL, który jest generowany przez użytkownika lub każdej organizacji.

   ![Określ certyfikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Aktywność kopiowania nie powiedzie się.
### <a name="problem"></a>Problem
Po skonfigurowaniu potoku w portalu można zauważyć następujący błąd "UserErrorFailedToConnectToSqlserver".

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Przyczyna
Może to mieć kilka przyczyn, i środki zaradcze różni odpowiednio.

#### <a name="resolution"></a>Rozwiązanie
Zezwalaj na połączenia wychodzące TCP za pośrednictwem portu TCP/1433 po stronie klienta bramy zarządzania danymi przed nawiązaniem połączenia z bazą danych SQL.

Docelowa baza danych w przypadku bazy danych Azure SQL, sprawdzanie programu SQL Server ustawień zapory dla platformy Azure oraz.

Zobacz sekcję poniżej, aby przetestować połączenie z lokalnym magazynem danych.

## <a name="data-store-connection-or-driver-related-errors"></a>Połączenia magazynu danych lub błędy związane z sterownika
Jeśli widzisz dane przechowywane połączenia lub błędy związane z sterowników, wykonaj następujące czynności:

1. Uruchom Menedżera konfiguracji bramy zarządzania danymi na maszynie bramy.
2. Przełącz się do **diagnostyki** kartę.
3. W **Testuj połączenie**, Dodaj grupę bramę.
4. Kliknij przycisk **testu** aby zobaczyć, jeśli możesz połączyć się ze źródłem danych lokalnych na maszynie bramy przy użyciu poświadczeń i informacji o połączeniu. Jeśli połączenie testowe nadal zakończy się niepowodzeniem po zainstalowaniu sterownika, uruchom bramę ponownie, aby wczytać najnowsze zmiany.

![Testuj połączenie na karcie diagnostyki](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Dzienniki bramy
### <a name="send-gateway-logs-to-microsoft"></a>Wyślij dzienniki bramy do firmy Microsoft
Gdy skontaktować się z Microsoft Support Aby uzyskać pomoc dotyczącą rozwiązywania problemów bramy, może zostać poproszona o udostępniać dzienniki bramy. Wraz z wydaniem bramy można udostępniać dzienniki wymagane bramy z dwóch kliknięcia przycisków w danych Menedżera konfiguracji bramy zarządzania.    

1. Przełącz się do **diagnostyki** kartę w danych Menedżera konfiguracji bramy zarządzania.

    ![Karta zarządzania diagnostyki bramy danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknij przycisk **Wyślij dzienniki** Aby wyświetlić następujące okno dialogowe.

    ![Dzienniki zarządzania bramy wysyłanie danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcjonalnie) Kliknij przycisk **Sprawdź dzienniki** Aby przejrzeć dzienniki zdarzeń podglądu.
4. (Opcjonalnie) Kliknij przycisk **prywatności** Aby przejrzeć poufności usług sieci web firmy Microsoft.
5. Po zakończeniu masz zamiar kliknij pozycję Przekaż **Wyślij dzienniki** faktycznie wysyłać dzienniki z ostatnich siedmiu dni do firmy Microsoft do rozwiązywania problemów. Stan operacji wysyłania dzienników powinny być widoczne, jak pokazano na poniższym zrzucie ekranu.

    ![Dane zarządzania bramy wysyłania dzienników stanu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po zakończeniu operacji zostanie wyświetlone okno dialogowe, jak pokazano na poniższym zrzucie ekranu.

    ![Dane zarządzania bramy wysyłania dzienników stanu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Zapisz **identyfikator raportu** i udostępniać je Support firmy Microsoft. Identyfikator raportu jest używana do lokalizowania dzienniki bramy, które zostało przez Ciebie przekazane do rozwiązywania problemów.  Identyfikator raportu jest także zapisane w zdarzeniu podglądu.  Można go znaleźć, sprawdzając identyfikator zdarzenia "25" i sprawdź datę i godzinę.

    ![Dane zarządzania bramy wysyłania dzienników identyfikator raportu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Dzienniki bramy archiwum na komputerze hosta bramy
Istnieją sytuacje, w którym masz problemy bramy i dzienniki bramy nie mogą współużytkować bezpośrednio:

* Ręcznie zainstaluj bramę i zarejestruj bramę.
* Spróbuj zarejestrować bramę za pomocą ponownie wygenerowanego klucza w danych Menedżera konfiguracji bramy zarządzania.
* Próby wysłania dzienników przez użytkownika i nie może zostać połączona usługa hosta bramy.

W tych sytuacjach można zapisać dzienniki bramy jako plik zip i udostępnić go w przypadku skontaktuj się z pomocą techniczną firmy Microsoft. Na przykład jeśli wystąpi błąd podczas rejestrowania bramy jako pokazano na poniższym zrzucie ekranu.   

![Błąd zarządzania rejestracji bramy danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kliknij przycisk **archiwum dzienniki bramy** łącze archiwizacji i zapisywanie dzienników, a następnie udostępnić plik zip pomocy technicznej firmy Microsoft.

![Dzienniki zarządzania bramy archiwum danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Zlokalizuj dzienniki bramy
Brama szczegółowe informacje w dzienniku można znaleźć w dzienniku zdarzeń systemu Windows.

1. Uruchom system Windows **Podgląd zdarzeń**.
2. Zlokalizuj dzienniki w **Dzienniki aplikacji i usług** > **brama zarządzania danymi** folderu.

 Przy rozwiązywaniu problemów związanych z bramą, poszukaj zdarzenia na poziomie błąd zdarzeń podglądu.

![Brama zarządzania danymi dzienniki w Podglądzie zdarzeń](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
