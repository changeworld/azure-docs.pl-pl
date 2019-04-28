---
title: Rozwiązywanie problemów z bramą zarządzania danymi | Dokumentacja firmy Microsoft
description: Zawiera porady dotyczące rozwiązywania problemów związanych z bramą zarządzania danymi.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0559d89bd691323a95713d518df05e58283cef39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61253761"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Rozwiązywanie problemów z używaniem bramy zarządzania danymi
Ten artykuł zawiera informacje na temat rozwiązywania problemów z używaniem bramy zarządzania danymi.

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [może być samodzielnie hostowane środowisko integration runtime w usłudze Data Factory](../create-self-hosted-integration-runtime.md).

Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, aby uzyskać szczegółowe informacje na temat bramy. Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu przewodnik przenoszenia danych z lokalnej bazy danych programu SQL Server do usługi Microsoft Azure Blob storage przy użyciu bramy.

## <a name="failed-to-install-or-register-gateway"></a>Nie udało się zainstalować lub zarejestrować bramę
### <a name="1-problem"></a>1. Problem
Widzisz ten komunikat o błędzie podczas instalowania i rejestrowania bramy, w szczególności podczas pobierania pliku instalacyjnego bramy.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Przyczyna
Maszyny, na którym chcesz zainstalować bramę udało się pobrać najnowszy plik instalacyjny bramy z Centrum pobierania, ze względu na problem z siecią.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia serwera proxy zapory, aby zobaczyć, czy ustawienia blokować połączenia sieciowego z komputera w celu [Centrum pobierania](https://download.microsoft.com/)i zaktualizuj ustawienia odpowiednio.

Alternatywnie możesz pobrać plik instalacyjny najnowszej bramy z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) na innych komputerach, które mogą uzyskiwać dostęp do Centrum pobierania. Można następnie skopiuj plik Instalatora na komputerze-hoście bramy i uruchom go ręcznie, aby zainstalować i zaktualizować bramę.

### <a name="2-problem"></a>2. Problem
Zostanie wyświetlony ten błąd, gdy próbujesz zainstalować bramę, klikając pozycję **zainstalować bezpośrednio na tym komputerze** w witrynie Azure portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Przyczyna
Brama jest już zainstalowana na komputerze.

#### <a name="resolution"></a>Rozwiązanie
Odinstaluj istniejącą bramę na komputerze, a następnie kliknij przycisk **zainstalować bezpośrednio na tym komputerze** Połącz ponownie.

### <a name="3-problem"></a>3. Problem
Ten błąd może pojawić się podczas rejestrowania nowej bramy.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Przyczyna
Możesz zobaczyć ten komunikat dla jednego z następujących powodów:

* Format klucza bramy jest nieprawidłowy.
* Klucz brama została unieważniona.
* Klucz bramy zostało wygenerowane z poziomu portalu.  

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy używasz klucza prawego bramy z poziomu portalu. Jeśli to konieczne, ponownie wygenerować klucz i zarejestrować bramę przy użyciu klucza.

### <a name="4-problem"></a>4. Problem
Gdy rejestrujesz z bramy może zobaczysz następujący komunikat o błędzie.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Zawartość lub format klucza jest nieprawidłowy](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Przyczyna
Zawartość lub format klucza bramy wejściowe są niepoprawne. Jedną z przyczyn może być to, że tylko część klucz został skopiowany z portalu lub używasz nieprawidłowy klucz.

#### <a name="resolution"></a>Rozwiązanie
Wygeneruj klucz bramy w portalu i użyj przycisku kopiowania, aby skopiować cały klucz. Następnie wklej go w tym oknie, aby zarejestrować bramę.

### <a name="5-problem"></a>5. Problem
Gdy rejestrujesz z bramy może zobaczysz następujący komunikat o błędzie.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Przyczyna
Ponownie wygenerowano klucz bramy lub brama została usunięta w witrynie Azure portal. Również zdarzyć, jeśli nie jest najpóźniejsza instalacji bramy zarządzania danymi.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia bramy zarządzania danymi jest najnowsza wersja, możesz znaleźć najnowszej wersji w programie Microsoft [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Jeśli konfiguracja jest bieżący / latest bramy nadal istnieje w portalu, to ponowne wygenerowanie klucza bramy w witrynie Azure portal użyj przycisku kopiowania, aby skopiować cały klucz i wklej go w tym oknie, aby zarejestrować bramę. W przeciwnym razie utwórz ponownie bramę i zacząć od początku.

### <a name="6-problem"></a>6. Problem
Gdy rejestrujesz z bramy może zobaczysz następujący komunikat o błędzie.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Przyczyna
Ten błąd może się zdarzyć, ponieważ brama została usunięta lub ponownie wygenerować klucz skojarzonej bramy.

#### <a name="resolution"></a>Rozwiązanie
Jeśli brama została usunięta, Utwórz ponownie bramę z poziomu portalu, kliknij przycisk **zarejestrować**, skopiuj klucz z portalu, wklej go i próbuje zarejestrować bramę.

Jeśli brama jest nadal istnieje, ale ponownie wygenerowano klucz, używania nowego klucza, aby zarejestrować bramę. Jeśli nie masz klucza, należy ponownie wygenerować klucz ponownie z portalu.

### <a name="7-problem"></a>7. Problem
Zapisując one bramy może być konieczne wprowadź ścieżkę i hasło dla certyfikatu.

![Określ certyfikat](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Przyczyna
Brama została zarejestrowana na innych komputerach, przed. Podczas wstępnej rejestracji bramy certyfikat szyfrowania został skojarzony z bramą. Certyfikat może być własnym generowanych przez bramę lub dostarczone przez użytkownika.  Ten certyfikat jest używany do szyfrowania poświadczeń magazynu danych (usługę połączoną).  

![Eksportowanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Podczas przywracania bramy na inny komputer hosta, Kreator rejestracji poprosi o podanie tego certyfikatu można odszyfrować poświadczeń wcześniej zaszyfrowany przy użyciu tego certyfikatu.  Bez tego certyfikatu nie można odszyfrować poświadczeń przez nową bramę i wykonania działania kolejnych kopii skojarzone z tej nowej bramy zakończy się niepowodzeniem.  

#### <a name="resolution"></a>Rozwiązanie
Jeśli wyeksportowano certyfikat poświadczeń z oryginalnej maszyny bramy przy użyciu **wyeksportować** znajdujący się na **ustawienia** kartę w danych Menedżera konfiguracji bramy zarządzania, należy użyć certyfikatu w tym miejscu.

Nie można pominąć ten etap, w przypadku odzyskiwania bramy. Jeśli brakuje certyfikatu, musisz usunąć bramę z portalu i ponownie utwórz nową bramę.  Ponadto należy zaktualizować wszystkie połączone usługi, które są powiązane z bramą przez ponownego wprowadzania poświadczeń.

### <a name="8-problem"></a>8. Problem
Być może zobaczysz następujący komunikat o błędzie.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Przyczyna
Ten błąd występuje, gdy brama znajduje się w środowisku, które wymaga dostępu do zasobów w Internecie lub hasło uwierzytelniania na serwerze proxy serwera proxy HTTP jest zmieniany, ale nie jest odpowiednio aktualizowana w bramie.

#### <a name="resolution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami w sekcji zagadnienia dotyczące serwera Proxy w tym artykule, a następnie skonfiguruj ustawienia serwera proxy przy użyciu danych Menedżera konfiguracji bramy zarządzania.

## <a name="gateway-is-online-with-limited-functionality"></a>Brama jest w trybie online, z ograniczoną funkcjonalnością
### <a name="1-problem"></a>1. Problem
Zostanie wyświetlony stan bramy jako online, z ograniczoną funkcjonalnością.

#### <a name="cause"></a>Przyczyna
Zostanie wyświetlony stan bramy jako online z ograniczoną funkcjonalnością dla jednego z następujących powodów:

* Bramy nie można połączyć z usługą w chmurze za pomocą usługi Azure Service Bus.
* Usługi w chmurze nie można połączyć się z bramą za pośrednictwem usługi Service Bus.

Gdy brama jest w trybie online, z ograniczoną funkcjonalnością, nie można użyć Kreatora kopiowania usługi Data Factory do tworzenia potoków danych na potrzeby kopiowania danych do lub z lokalnych magazynów danych. Obejść ten problem można użyć edytora usługi Data Factory w witrynie portal, programu Visual Studio lub programu Azure PowerShell.

#### <a name="resolution"></a>Rozwiązanie
Istnieje rozwiązanie tego problemu (online z ograniczoną funkcjonalnością) zależy od tego, czy brama nie może nawiązać połączenia z usługą w chmurze ani w inny sposób. Te rozwiązania można znaleźć w poniższych sekcjach.

### <a name="2-problem"></a>2. Problem
Zostanie wyświetlony następujący błąd.

`Error: Gateway cannot connect to cloud service through service bus`

![Bramy nie można połączyć z usługą w chmurze](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna
Bramy nie można połączyć z usługą w chmurze za pośrednictwem usługi Service Bus.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby pobrać bramę przywrócić do trybu online:

1. Zezwalaj na adres IP reguły ruchu wychodzącego na maszynie bramy i zapory w firmie. Adresy IP można znaleźć w dzienniku zdarzeń Windows (identyfikator == 401): Próbowano uzyskać dostęp do gniazda w sposób zabroniony przez jego uprawnienia dostępu XX. XX. XX. XX:9350.
1. Skonfiguruj ustawienia serwera proxy w bramie. Zobacz szczegółowe informacje w sekcji zagadnienia dotyczące serwera Proxy.
1. Włącz porty wychodzące 5671 i 9350-9354 zaporę Windows na maszynie bramy i zapory w firmie. Zobacz, portów i sekcji zapory, aby uzyskać szczegółowe informacje. Ten krok jest opcjonalny, ale firma Microsoft zaleca wziąć pod uwagę wydajności.

### <a name="3-problem"></a>3. Problem
Zostanie wyświetlony następujący błąd.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Przyczyna
Błąd przejściowy w łączności sieciowej.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby pobrać bramę przywrócić do trybu online:

1. Poczekaj kilka minut, połączenia zostaną automatycznie odzyskane po błędzie został usunięty.
1. Jeśli błąd będzie się powtarzać, uruchom ponownie usługę bramy.

## <a name="failed-to-author-linked-service"></a>Nie można utworzyć połączonej usługi
### <a name="problem"></a>Problem
Możesz zobaczyć następujący błąd podczas próby użycia Menedżera poświadczeń w portalu, wprowadź poświadczenia dla nowa połączona usługa lub zaktualizować poświadczenia dla istniejącej połączonej usługi.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Gdy zostanie wyświetlony ten błąd, na stronie ustawień z danych Menedżera konfiguracji bramy zarządzania może wyglądać poniższym zrzucie ekranu.

![Nie można nawiązać połączenia z bazą danych](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Przyczyna
Certyfikat SSL mogą zostać utracone na maszynie bramy. Komputer z bramą, nie można załadować obecnie certyfikat, który jest używany do szyfrowania SSL. Można także napotkać komunikat o błędzie w dzienniku zdarzeń, który jest podobny do następującego.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby rozwiązać ten problem:

1. Uruchom Menedżera konfiguracji bramy zarządzania danych.
2. Przejdź do karty **Ustawienia**.  
3. Kliknij przycisk **zmienić** przycisk, aby zmienić certyfikat SSL.

   ![Zmień przycisk certyfikat](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Wybierz nowy certyfikat jako certyfikatu SSL. Można użyć dowolnego certyfikatu SSL, który jest generowany przez Ciebie lub każdej organizacji.

   ![Określ certyfikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Działanie kopiowania zakończy się niepowodzeniem.
### <a name="problem"></a>Problem
Po skonfigurowaniu potoku w portalu, można zauważyć wystąpił następujący błąd "UserErrorFailedToConnectToSqlserver".

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Przyczyna
Może to mieć kilka przyczyn i środki zaradcze zmienia się odpowiednio.

#### <a name="resolution"></a>Rozwiązanie
Zezwalaj na połączenia wychodzące TCP za pośrednictwem portu TCP/1433 po stronie klienta bramy zarządzania danymi, przed nawiązaniem połączenia z usługą SQL database.

Jeśli docelowa baza danych jest bazą danych Azure SQL database, sprawdź ustawienia programu SQL Server zapory dla platformy Azure oraz.

Zobacz następującą sekcję, aby przetestować połączenie z lokalnym magazynem danych.

## <a name="data-store-connection-or-driver-related-errors"></a>Połączenie magazynu danych lub błędy związane z sterownika
Jeśli widzisz magazyn połączenia lub błędy związane z sterownik danych, wykonaj następujące czynności:

1. Uruchom Menedżera konfiguracji bramy zarządzania danych na maszynie bramy.
2. Przełącz się do **diagnostyki** kartę.
3. W **Testuj połączenie**, Dodaj wartości grupy bramy.
4. Kliknij przycisk **testu** aby zobaczyć, jeśli możesz połączyć się ze źródłem danych lokalnych z maszyny bramy przy użyciu informacji dotyczących połączenia oraz poświadczenia. Jeśli połączenie testowe nadal zakończy się niepowodzeniem po zainstalowaniu sterownika, uruchom bramę ponownie, aby wczytać najnowsze zmiany.

![Testuj połączenie na karcie diagnostyki](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Dzienniki bramy
### <a name="send-gateway-logs-to-microsoft"></a>Wyślij dzienniki bramy do firmy Microsoft
Przed kontaktem z Microsoft Support Aby uzyskać pomoc dotyczącą rozwiązywania problemów z bramą, może zostać wyświetlony o Udostępnij dzienniki bramy. Wraz z wydaniem bramy można udostępniać dzienniki wymagane bramy przy użyciu dwóch kliknięć przycisków w danych Menedżera konfiguracji bramy zarządzania.    

1. Przełącz się do **diagnostyki** kartę w danych Menedżera konfiguracji bramy zarządzania.

    ![Na karcie dane diagnostyki bramy zarządzania](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknij przycisk **Wyślij dzienniki** na wyświetlone następujące okno dialogowe.

    ![Dzienniki wysyłania bramy zarządzania danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcjonalnie) Kliknij przycisk **wyświetlanie dzienników** przejrzeć dzienniki zdarzeń podglądu.
4. (Opcjonalnie) Kliknij przycisk **zachowania** Aby zapoznać się z firmy Microsoft w sieci web services — zasady zachowania poufności informacji.
5. Gdy jesteś zadowolony z co zamierzasz przekazać, kliknij przycisk **Wyślij dzienniki** faktycznie wysyłać dzienniki z ostatnich siedmiu dni do firmy Microsoft do rozwiązywania problemów. Stan operacji wysyłania dzienników powinny Zobacz, jak pokazano na poniższym zrzucie ekranu.

    ![Dane zarządzania bramy Wyślij dzienniki stanu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po zakończeniu operacji zobaczysz okno dialogowe, jak pokazano na poniższym zrzucie ekranu.

    ![Dane zarządzania bramy Wyślij dzienniki stanu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Zapisz **identyfikator raportu** i udostępnić go innym Microsoft Support. Identyfikator raportu jest używana do lokalizowania dzienniki bramy, które przekazane do rozwiązywania problemów.  Identyfikator raportu jest również zapisywana w zdarzeniu podglądu.  Można go znaleźć, sprawdzając identyfikator zdarzenia "25" i sprawdź datę i godzinę.

    ![Dane zarządzania bramy Wyślij dzienniki identyfikator raportu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Dzienniki bramy archiwum na komputerze hosta bramy
Istnieją sytuacje, w którym masz problemy z bramy i dzienniki bramy nie można udostępniać bezpośrednio:

* Ręcznie zainstaluj bramę i zarejestrować bramę.
* Spróbuj zarejestrować bramę wygenerowano klucz w danych Menedżera konfiguracji bramy zarządzania.
* Próby wysłania dzienników i nie można połączyć usługę bramy hosta.

Dla tych scenariuszy można zapisać dzienniki bramy jako plik zip i udostępnić je w przypadku skontaktuj się z pomocą techniczną firmy Microsoft. Na przykład jeśli wystąpi błąd, gdy zarejestrujesz bramy jako pokazano na następującym zrzucie ekranu.   

![Błąd rejestracji bramy zarządzania danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kliknij przycisk **Archiwizuj dzienniki bramy** link do zarchiwizowania i zapisywanie dzienników, a następnie Udostępnij plik zip pomocy technicznej firmy Microsoft.

![Dzienniki bramy zarządzania w warstwie archiwum danych](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Zlokalizuj dzienniki bramy
Informacje szczegółowe bramy można znaleźć w dziennikach zdarzeń Windows.

1. Uruchom Windows **Podgląd zdarzeń**.
2. Zlokalizuj dzienniki w **Dzienniki aplikacji i usług** > **bramy zarządzania danymi** folderu.

   Przy rozwiązywaniu problemów związanych z bramą, wyszukać zdarzenia na poziomie błędu w przeglądarce zdarzeń.

![Brama zarządzania danymi dzienniki w Podglądzie zdarzeń](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
