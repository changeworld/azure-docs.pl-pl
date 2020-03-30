---
title: Rozwiązywanie problemów z usługą Zarządzania danymi
description: Zawiera wskazówki dotyczące rozwiązywania problemów związanych z bramą zarządzania danymi.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065026"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Rozwiązywanie problemów z używaniem bramy zarządzania danymi
Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z korzystaniem z bramy zarządzania danymi.

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [środowisko uruchomieniowe integracji hostowanego samodzielnie w usłudze Data Factory](../create-self-hosted-integration-runtime.md).

Szczegółowe informacje na temat bramy można znaleźć w artykule [Brama zarządzania danymi.](data-factory-data-management-gateway.md) Zobacz artykuł [Przenoszenie danych między lokalnymi i chmurowymi,](data-factory-move-data-between-onprem-and-cloud.md) aby zapoznać się z instrukcją przenoszenia danych z lokalnej bazy danych programu SQL Server do magazynu obiektów Blob platformy Microsoft Azure przy użyciu bramy.

## <a name="failed-to-install-or-register-gateway"></a>Nie można zainstalować lub zarejestrować bramy
### <a name="1-problem"></a>1. Problem
Ten komunikat o błędzie jest wyświetlany podczas instalowania i rejestrowania bramy, w szczególności podczas pobierania pliku instalacyjnego bramy.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Przyczyna
Komputer, na którym próbujesz zainstalować bramę, nie może pobrać najnowszego pliku instalacyjnego bramy z centrum pobierania z powodu problemu z siecią.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia serwera proxy zapory, aby sprawdzić, czy ustawienia blokują połączenie sieciowe z komputera do [centrum pobierania,](https://download.microsoft.com/)i odpowiednio zaktualizuj ustawienia.

Alternatywnie można pobrać plik instalacyjny najnowszej bramy z [centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) na innych komputerach, które mają dostęp do centrum pobierania. Następnie można skopiować plik instalatora na komputer-host bramy i uruchomić go ręcznie, aby zainstalować i zaktualizować bramę.

### <a name="2-problem"></a>2. Problem
Ten błąd jest widoczny podczas próby zainstalowania bramy, klikając **przycisk zainstaluj bezpośrednio na tym komputerze** w witrynie Azure portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Przyczyna
Brama jest już zainstalowana na komputerze.

#### <a name="resolution"></a>Rozwiązanie
Odinstaluj istniejącą bramę na komputerze i ponownie kliknij **łącze install bezpośrednio na tym komputerze.**

### <a name="3-problem"></a>3. Problem
Ten błąd może wystąpić podczas rejestrowania nowej bramy.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Przyczyna
Ten komunikat może zostać wyświetlony z jednego z następujących powodów:

* Format klucza bramy jest nieprawidłowy.
* Klucz bramy został unieważniony.
* Klucz bramy został ponownie wygenerowany z portalu.  

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy używasz odpowiedniego klucza bramy z portalu. W razie potrzeby ponownie wygeneruj klucz i użyj klucza do zarejestrowania bramy.

### <a name="4-problem"></a>4. Problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Zawartość lub format klucza jest nieprawidłowy](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Przyczyna
Zawartość lub format klucza bramy wejściowej jest niepoprawna. Jednym z powodów może być skopiowanie tylko części klucza z portalu lub użycia nieprawidłowego klucza.

#### <a name="resolution"></a>Rozwiązanie
Wygeneruj klucz bramy w portalu i użyj przycisku kopiowania, aby skopiować cały klucz. Następnie wklej go w tym oknie, aby zarejestrować bramę.

### <a name="5-problem"></a>5. Problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Przyczyna
Klucz bramy został ponownie wygenerowany lub brama została usunięta w witrynie Azure portal. Może się to również zdarzyć, jeśli konfiguracja bramy zarządzania danymi nie jest najnowsza.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy konfiguracja bramy zarządzania danymi jest najnowszą wersją, możesz znaleźć najnowszą wersję w [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkId=271260)firmy Microsoft .

Jeśli konfiguracja jest bieżąca/ najnowsza, a brama nadal istnieje w portalu, ponownie wygeneruj klucz bramy w witrynie Azure portal i użyj przycisku kopiowania, aby skopiować cały klucz, a następnie wklej go w tym oknie, aby zarejestrować bramę. W przeciwnym razie ponownie utworzyć bramę i rozpocząć od nowa.

### <a name="6-problem"></a>6. Problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Przyczyna
Ten błąd może wystąpić, ponieważ brama została usunięta lub skojarzony klucz bramy został ponownie wygenerowany.

#### <a name="resolution"></a>Rozwiązanie
Jeśli brama została usunięta, ponownie utwórz bramę z portalu, kliknij przycisk **Zarejestruj,** skopiuj klucz z portalu, wklej go i spróbuj zarejestrować bramę.

Jeśli brama nadal istnieje, ale jej klucz został ponownie wygenerowany, użyj nowego klucza, aby zarejestrować bramę. Jeśli nie masz klucza, ponownie ponownie wygeneruj klucz z portalu.

### <a name="7-problem"></a>7. Problem
Podczas rejestrowania bramy może być konieczne wprowadzenie ścieżki i hasła certyfikatu.

![Określanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Przyczyna
Brama została wcześniej zarejestrowana na innych komputerach. Podczas początkowej rejestracji bramy z bramą został skojarzony certyfikat szyfrowania. Certyfikat może być generowany samodzielnie przez bramę lub dostarczony przez użytkownika.  Ten certyfikat jest używany do szyfrowania poświadczeń magazynu danych (połączonej usługi).  

![Eksportowanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Podczas przywracania bramy na innym komputerze-hoście kreator rejestracji prosi o odszyfrowanie poświadczeń zaszyfrowanych wcześniej za pomocą tego certyfikatu.  Bez tego certyfikatu poświadczenia nie mogą być odszyfrowywane przez nową bramę, a kolejne wykonania działania kopiowania skojarzone z tą nową bramą nie powiodą się.  

#### <a name="resolution"></a>Rozwiązanie
Jeśli certyfikat poświadczeń został wyeksportowany z oryginalnego komputera bramy przy użyciu przycisku **Eksportuj** na karcie **Ustawienia** w menedżerze konfiguracji bramy zarządzania danymi, użyj certyfikatu w tym miejscu.

Nie można pominąć tego etapu podczas odzyskiwania bramy. Jeśli brakuje certyfikatu, należy usunąć bramę z portalu i ponownie utworzyć nową bramę.  Ponadto zaktualizuj wszystkie połączone usługi, które są związane z bramą, ponownie wejdząc ich poświadczenia.

### <a name="8-problem"></a>8. Problem
Może zostać wyświetlony następujący komunikat o błędzie.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Przyczyna
Ten błąd występuje, gdy brama znajduje się w środowisku, które wymaga serwera proxy HTTP dostępu do zasobów internetowych lub hasło uwierzytelniania serwera proxy zostanie zmienione, ale nie jest odpowiednio aktualizowane w bramie.

#### <a name="resolution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami w sekcji Zagadnienia serwera proxy w tym artykule i skonfiguruj ustawienia serwera proxy za pomocą programu Menedżer konfiguracji bramy zarządzania danymi.

## <a name="gateway-is-online-with-limited-functionality"></a>Brama jest w trybie online z ograniczoną funkcjonalnością
### <a name="1-problem"></a>1. Problem
Stan bramy jest widoczny w trybie online z ograniczoną funkcjonalnością.

#### <a name="cause"></a>Przyczyna
Stan bramy jest widoczny w trybie online z ograniczoną funkcjonalnością z jednego z następujących powodów:

* Brama nie może połączyć się z usługą w chmurze za pośrednictwem usługi Azure Service Bus.
* Usługa w chmurze nie może połączyć się z bramą za pośrednictwem usługi Service Bus.

Gdy brama jest w trybie online z ograniczoną funkcjonalnością, za pomocą Kreatora kopiowania fabryki danych można utworzyć potoki danych do kopiowania danych do lub z lokalnych magazynów danych. Aby obejść ten problem, można użyć Edytora fabryki danych w portalu, programie Visual Studio lub programie Azure PowerShell.

#### <a name="resolution"></a>Rozwiązanie
Rozwiązanie tego problemu (w trybie online z ograniczoną funkcjonalnością) opiera się na tym, czy brama nie może połączyć się z usługą w chmurze lub w inny sposób. Poniższe sekcje zawierają te rozwiązania.

### <a name="2-problem"></a>2. Problem
Pojawi się następujący błąd.

`Error: Gateway cannot connect to cloud service through service bus`

![Brama nie może połączyć się z usługą w chmurze](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna
Brama nie może połączyć się z usługą w chmurze za pośrednictwem usługi Service Bus.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby przywrócić bramę do trybu online:

1. Zezwalaj na reguły wychodzące adresu IP na komputerze bramy i zaporze firmowej. Adresy IP można znaleźć w dzienniku zdarzeń systemu Windows (ID == 401): Podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu XX. Xx. Xx. XX:9350.
1. Konfigurowanie ustawień serwera proxy na bramie. Szczegółowe informacje można znaleźć w sekcji Zagadnienia dotyczące serwera proxy.
1. Włącz porty wychodzące 5671 i 9350-9354 zarówno na Zaporze systemu Windows na komputerze bramy, jak i w zaporze firmowej. Szczegółowe informacje można znaleźć w sekcji Porty i zapora. Ten krok jest opcjonalny, ale zaleca się go do uwzględnienia wydajności.

### <a name="3-problem"></a>3. Problem
Pojawi się następujący błąd.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Przyczyna
Błąd przejściowy w łączności sieciowej.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby przywrócić bramę do trybu online:

1. Poczekaj kilka minut, łączność zostanie automatycznie odzyskana, gdy błąd zniknie.
1. Jeśli błąd będzie się powtarzał, uruchom ponownie usługę bramy.

## <a name="failed-to-author-linked-service"></a>Nie można autoryzować połączonej usługi
### <a name="problem"></a>Problem
Ten błąd może wystąpić podczas próby użycia Menedżera poświadczeń w portalu do wprowadzania poświadczeń dla nowej połączonej usługi lub aktualizowania poświadczeń dla istniejącej usługi połączonej.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Po wyświetleniu tego błędu strona ustawień programu Data Management Gateway Configuration Manager może wyglądać następującego zrzutu ekranu.

![Nie można uzyskać dostępu do bazy danych](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Przyczyna
Certyfikat TLS/SSL mógł zostać utracony na komputerze bramy. Komputer bramy nie może załadować certyfikatu aktualnie używanego do szyfrowania TLS. W dzienniku zdarzeń może również zostać wyświetlony komunikat o błędzie podobny do następującego komunikatu.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby rozwiązać problem:

1. Uruchom menedżera konfiguracji bramy zarządzania danymi.
2. Przełącz się do karty **Ustawienia.**  
3. Kliknij przycisk **Zmień,** aby zmienić certyfikat TLS/SSL.

   ![Przycisk Zmień certyfikat](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Wybierz nowy certyfikat jako certyfikat TLS/SSL. Można użyć dowolnego certyfikatu TLS/SSL, który jest generowany przez Ciebie lub dowolną organizację.

   ![Określanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Działanie kopiowania kończy się niepowodzeniem
### <a name="problem"></a>Problem
Po skonfigurowaniu potoku w portalu można zauważyć następujący błąd "UserErrorFailedToConnectToSqlserver".

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Przyczyna
Może się to zdarzyć z różnych powodów, a łagodzenie różni się odpowiednio.

#### <a name="resolution"></a>Rozwiązanie
Przed nawiązaniem połączenia z bazą danych SQL zezwól na wychodzące połączenia TCP za pośrednictwem portu TCP/1433 po stronie klienta bramy zarządzania danymi.

Jeśli docelowa baza danych jest bazą danych SQL platformy Azure, sprawdź ustawienia zapory programu SQL Server również dla platformy Azure.

Zobacz poniższą sekcję, aby przetestować połączenie z lokalnym magazynem danych.

## <a name="data-store-connection-or-driver-related-errors"></a>Połączenie magazynu danych lub błędy związane ze sterownikami
Jeśli widzisz połączenie z magazynem danych lub błędy związane ze sterownikami, wykonaj następujące czynności:

1. Uruchom program Data Management Gateway Configuration Manager na komputerze bramy.
2. Przełącz się do karty **Diagnostyka.**
3. W **polu Test połączenia**dodaj wartości grupy bramy.
4. Kliknij **przycisk Testuj,** aby sprawdzić, czy można połączyć się z lokalnym źródłem danych z komputera bramy przy użyciu informacji o połączeniu i poświadczeń. Jeśli połączenie testowe nadal zakończy się niepowodzeniem po zainstalowaniu sterownika, uruchom bramę ponownie, aby wczytać najnowsze zmiany.

![Karta Testowanie połączenia w obszarze Diagnostyka](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Dzienniki bramy
### <a name="send-gateway-logs-to-microsoft"></a>Wysyłanie dzienników bramy do firmy Microsoft
Po skontaktowaniu się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy dotyczącej rozwiązywania problemów z bramą może zostać poproszony o udostępnienie dzienników bramy. Wraz z wydaniem bramy można udostępniać wymagane dzienniki bramy za pomocą dwóch kliknięć przycisków w programie Menedżer konfiguracji bramy zarządzania danymi.    

1. Przełącz się do karty **Diagnostyka** w programie Menedżer konfiguracji bramy zarządzania danymi.

    ![Karta Diagnostyka bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknij **pozycję Wyślij dzienniki,** aby wyświetlić następujące okno dialogowe.

    ![Dzienniki wysyłania bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcjonalnie) Kliknij **dzienniki widoku,** aby przejrzeć dzienniki w podglądzie zdarzeń.
4. (Opcjonalnie) Kliknij **pozycję Prywatność,** aby zapoznać się z zasadami zachowania poufności informacji dotyczącymi usług sieci Web firmy Microsoft.
5. Jeśli jesteś zadowolony z tego, co zamierzasz przekazać, kliknij przycisk **Wyślij dzienniki,** aby faktycznie wysłać dzienniki z ostatnich siedmiu dni do firmy Microsoft w celu rozwiązania problemów. Powinien zostać wyświetlony stan operacji wysyłania dzienników, jak pokazano na poniższym zrzucie ekranu.

    ![Stan dzienników wysyłania bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po zakończeniu operacji zostanie wyświetlone okno dialogowe, jak pokazano na poniższym zrzucie ekranu.

    ![Stan dzienników wysyłania bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Zapisz identyfikator **raportu** i udostępnij go pomocy technicznej firmy Microsoft. Identyfikator raportu służy do lokalizowania dzienników bramy przekazanych w celu rozwiązania problemów.  Identyfikator raportu jest również zapisywany w podglądzie zdarzeń.  Możesz go znaleźć, patrząc na identyfikator zdarzenia "25" i sprawdź datę i godzinę.

    ![Identyfikator raportu wysyłania dzienników bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiwizowanie dzienników bramy na komputerze-hoście bramy
Istnieje kilka scenariuszy, w których występują problemy z bramą i nie można bezpośrednio udostępniać dzienników bramy:

* Ręcznie zainstaluj bramę i zarejestruj bramę.
* Spróbuj zarejestrować bramę za pomocą klucza zregenerowanego w programie Menedżer konfiguracji bramy zarządzania danymi.
* Spróbuj wysłać dzienniki i nie można połączyć usługi hosta bramy.

W tych scenariuszach można zapisać dzienniki bramy jako plik zip i udostępnić je po skontaktowaniu się z pomocą techniczną firmy Microsoft. Na przykład jeśli podczas rejestrowania bramy pojawia się błąd, jak pokazano na poniższym zrzucie ekranu.   

![Błąd rejestracji bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kliknij **łącze Dzienniki bramy archiwum,** aby zarchiwizować i zapisać dzienniki, a następnie udostępnić plik zip do pomocy technicznej firmy Microsoft.

![Dzienniki archiwum bramy zarządzania danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Lokalizowanie dzienników bramy
Szczegółowe informacje dziennika bramy można znaleźć w dziennikach zdarzeń systemu Windows.

1. Uruchom **Podgląd zdarzeń**systemu Windows .
2. Lokalizowanie dzienników w folderze > **Brama zarządzania danymi** **dzienników aplikacji i usług.**

   Podczas rozwiązywania problemów związanych z bramą poszukaj zdarzeń na poziomie błędu w podglądzie zdarzeń.

![Brama zarządzania danymi loguje się w przeglądarce zdarzeń](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
