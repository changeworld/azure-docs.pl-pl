---
title: Konfigurowanie serwera proxy sieci web dla urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia serwera proxy sieci web dla urządzenia StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: be5719d2c383c838ef70c6862c1055c3374e05e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362496"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurowanie serwera proxy sieci web dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak konfigurować i wyświetlać ustawienia serwera proxy sieci web dla urządzenia StorSimple za pomocą programu Windows PowerShell dla usługi StorSimple. Ustawienia serwera proxy sieci web są używane przez urządzenia StorSimple, podczas komunikowania się z chmurą. Serwer proxy sieci web służy do dodania dodatkową warstwę zabezpieczeń, filtr zawartości pamięci podręcznej w celu ułatwienia wymaganiach odnośnie do przepustowości lub nawet uzyskać pomoc dotyczącą analizy.

Wskazówki zawarte w tym samouczku dotyczy tylko urządzeń fizycznych z serii StorSimple 8000. Konfiguracja serwera proxy sieci Web nie jest obsługiwana na urządzenie StorSimple w chmurze (8010 i 8020).

Serwer proxy sieci Web jest _opcjonalne_ konfiguracji dla urządzenia StorSimple. Możesz skonfigurować serwer proxy sieci web tylko za pomocą programu Windows PowerShell dla usługi StorSimple. Konfiguracja jest procesem dwuetapowym w następujący sposób:

1. Najpierw skonfigurować ustawienia serwera proxy sieci web za pomocą Kreatora instalacji lub środowiska Windows PowerShell dla poleceń cmdlet usługi StorSimple.
2. Następnie Włącz ustawienia serwera proxy sieci web skonfigurowanych za pomocą programu Windows PowerShell dla poleceń cmdlet usługi StorSimple.

Po zakończeniu konfiguracji serwera proxy sieci web można wyświetlić ustawienia serwera proxy sieci web skonfigurowany zarówno w przypadku usługi Menedżer urządzeń programu Microsoft Azure StorSimple, jak i programu Windows PowerShell dla usługi StorSimple.

Po przeczytaniu tego samouczka, będzie mieć możliwość:

* Skonfiguruj serwer proxy sieci web za pomocą Kreatora instalacji i poleceń cmdlet.
* Włącz serwer proxy sieci web za pomocą poleceń cmdlet.
* Wyświetl ustawienia serwera proxy sieci web w witrynie Azure portal.
* Rozwiązywanie problemów z błędami podczas konfigurowania serwera proxy sieci web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurowanie serwera proxy sieci web za pomocą programu Windows PowerShell dla usługi StorSimple

Użyj jednej z następujących skonfigurować ustawienia serwera proxy sieci web:

* Kreator instalacji przeprowadzenie Cię przez kroki konfiguracji.
* Polecenia cmdlet programu Windows PowerShell dla usługi StorSimple.

W poniższych sekcjach omówiono każda z tych metod.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurowanie serwera proxy sieci web za pomocą Kreatora instalacji

Wykonaj kroki konfiguracji serwera proxy sieci web za pomocą Kreatora instalacji. Wykonaj poniższe kroki, aby skonfigurować serwer proxy sieci web na urządzeniu.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Aby skonfigurować serwer proxy sieci web za pomocą Kreatora instalacji

1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu** i podaj **hasło administratora urządzenia**. Wpisz następujące polecenie, aby uruchomić sesję kreatora instalacji:
   
    `Invoke-HcsSetupWizard`
2. Jeśli korzystasz z Kreatora instalacji programu rejestracji urządzeń po raz pierwszy należy skonfigurować wszystkie wymagane ustawienia sieci, aż do konfiguracji serwera proxy sieci web. Jeśli urządzenie jest już zarejestrowany, Zaakceptuj wszystkie ustawienia skonfigurowanej sieci, aż do konfiguracji serwera proxy sieci web. W Kreatorze instalacji, gdy zostanie wyświetlony monit, aby skonfigurować ustawienia serwera proxy sieci web, wpisz **tak**.
3. Aby uzyskać **adres URL serwera Proxy sieci Web**, określ adres IP lub w pełni kwalifikowana nazwa domeny (FQDN) serwera proxy sieci web i numeru portu TCP, który chcesz urządzenia w celu używania do komunikacji z chmury. Użyj następującego formatu:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Domyślnie jest określony numer portu TCP 8080.
4. Wybierz typ uwierzytelniania jako **NTLM**, **podstawowe**, lub **Brak**. W warstwie podstawowa jest najniższy poziom zabezpieczeń uwierzytelniania w konfiguracji serwera proxy. NT LAN Manager (NTLM) jest protokołem uwierzytelniania bardzo bezpieczny i złożonych, korzystającym z systemu obsługi komunikatów trzystopniowo, (czasami cztery Jeśli wymagane jest dodatkowe integralności) do uwierzytelnienia użytkownika. Domyślne uwierzytelnianie to NTLM. Aby uzyskać więcej informacji, zobacz [podstawowe](http://hc.apache.org/httpclient-3.x/authentication.html) i [uwierzytelniania NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **W usłudze Menedżer urządzeń StorSimple wykresy monitorowania urządzenia nie będą działać podstawowe lub uwierzytelnianie NTLM jest włączone w konfiguracji serwera proxy dla tego urządzenia. Wykresy monitorowania do pracy należy upewnić się, że uwierzytelnianie jest ustawione na wartość NONE.**
  
5. Jeśli włączono uwierzytelnianie, należy podać **nazwa użytkownika serwera Proxy sieci Web** i **hasło serwera Proxy sieci Web**. Należy również Potwierdź hasło.
   
    ![Konfigurowanie serwera Proxy sieci Web na urządzenia StorSimple 1](./media/storsimple-configure-web-proxy/IC751830.png)

W przypadku rejestracji urządzenia po raz pierwszy, przejdź do rejestracji. Jeśli urządzenie zostało już zarejestrowane, kończy pracę kreatora. Skonfigurowane ustawienia są zapisywane.

Serwer proxy sieci Web jest teraz włączony. Możesz pominąć [Włącz serwer proxy sieci web](#enable-web-proxy) krok i przejdź bezpośrednio do [Wyświetl ustawienia serwera proxy sieci web w witrynie Azure portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurowanie serwera proxy sieci web za pomocą programu Windows PowerShell dla poleceń cmdlet usługi StorSimple

To alternatywny sposób, aby skonfigurować ustawienia serwera proxy sieci web za pomocą programu Windows PowerShell dla poleceń cmdlet usługi StorSimple. Wykonaj poniższe kroki, aby skonfigurować serwer proxy sieci web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Aby skonfigurować serwer proxy sieci web za pomocą poleceń cmdlet
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Wprowadź i Potwierdź hasło po wyświetleniu monitu.
   
    ![Konfigurowanie serwera Proxy sieci Web na StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Serwer proxy sieci web jest teraz skonfigurowane i musi być włączona.

## <a name="enable-web-proxy"></a>Włącz serwer proxy sieci web

Serwer proxy sieci Web jest domyślnie wyłączona. Po skonfigurowaniu ustawień serwera proxy sieci web na urządzeniu StorSimple, należy użyć programu Windows PowerShell dla usługi StorSimple, aby włączyć ustawienia serwera proxy sieci web.

> [!NOTE]
> **Ten krok nie jest wymagane, jeśli użyto Kreatora instalacji do skonfigurowania serwera proxy sieci web. Serwer proxy sieci Web automatycznie jest domyślnie włączona po sesji kreatora instalacji.**


W programie Windows PowerShell dla usługi StorSimple włączyć serwer proxy sieci web na urządzeniu, należy wykonać następujące czynności:

#### <a name="to-enable-web-proxy"></a>Aby włączyć serwer proxy sieci web
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Enable-HcsWebProxy`
   
    Teraz włączono konfiguracji serwera proxy sieci web na urządzeniu StorSimple.
   
    ![Konfigurowanie serwera Proxy sieci Web na StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Wyświetl ustawienia serwera proxy sieci web w witrynie Azure portal

Ustawienia serwera proxy sieci web można skonfigurować za pomocą interfejsu programu Windows PowerShell i nie można zmienić z poziomu portalu. Mogą jednak wyświetlać te ustawienia skonfigurowane w portalu. Wykonaj poniższe kroki, aby wyświetlić serwera proxy sieci web.

#### <a name="to-view-web-proxy-settings"></a>Aby wyświetlić ustawienia serwera proxy sieci web
1. Przejdź do **usługi Menedżer urządzeń StorSimple > urządzenia**. Wybierz i kliknij urządzenie, a następnie przejdź do **ustawienia urządzenia > sieć**.

    ![Kliknij sieć](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. W **ustawienia sieciowe** bloku kliknij **serwera proxy sieci Web** kafelka.

    ![Kliknij serwer proxy sieci web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. W **serwera proxy sieci Web** bloku, przejrzyj ustawienia serwera proxy sieci web skonfigurowany na urządzeniu StorSimple.
   
    ![Wyświetl ustawienia serwera proxy sieci web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Błędy podczas konfiguracji serwera proxy sieci web

Jeśli ustawienia serwera proxy sieci web są nieprawidłowo skonfigurowane, komunikaty o błędach są wyświetlane użytkownikowi w programie Windows PowerShell dla usługi StorSimple. W poniższej tabeli opisano niektóre z tych komunikatów o błędach, ich prawdopodobne przyczyny i zalecane działania.

| Nr seryjny. | Błąd HRESULT kodu | Możliwe przyczyny | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Polecenie jest wykonywane z kontrolera pasywnego i nie może komunikować się z aktywnym kontrolerze. |Uruchom polecenie na aktywnym kontrolerze. Aby uruchomić polecenie z kontrolera pasywnego, należy naprawić połączeń pasywnych z aktywnym kontrolerem. Jeśli to połączenie zostanie przerwane, musi angażowanie Microsoft Support. |
| 2. |0x800710dd — identyfikator operacji jest nieprawidłowy |Ustawienia serwera proxy nie są obsługiwane na urządzeniu StorSimple w chmurze. |Ustawienia serwera proxy nie są obsługiwane na urządzeniu StorSimple w chmurze. Te można skonfigurować tylko na urządzeniu fizycznym StorSimple. |
| 3. |0x80070057 — nieprawidłowy parametr |Jeden z parametrów podany dla ustawienia serwera proxy jest nieprawidłowy. |Nie podano identyfikatora URI w poprawnym formacie. Użyj następującego formatu: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA — serwer RPC jest niedostępny |Główną przyczyną jest jedną z następujących czynności:</br></br>Klaster nie jest się. </br></br>Usługa ścieżki danych nie jest uruchomiona.</br></br>Polecenie jest wykonywane z kontrolera pasywnego i nie może komunikować się z aktywnym kontrolerze. |Skontaktuj się z Microsoft Support aby upewnić się, że klaster działa i jest uruchomiona usługa ścieżki danych.</br></br>Uruchom polecenie z aktywnym kontrolerem. Jeśli chcesz uruchomić polecenie z kontrolera pasywnego, upewnij się, że kontrolera pasywnego może komunikować się z aktywnym kontrolerem. Jeśli to połączenie zostanie przerwane, musi angażowanie Microsoft Support. |
| 5. |0x800706be - wywołania RPC nie powiodło się |Klaster nie działa. |Skontaktuj się z Microsoft Support aby upewnić się, że klaster działa. |
| 6. |0x8007138f — nie można odnaleźć zasobu klastra |Nie można odnaleźć zasobu klastra usługi platformy. Może to nastąpić, jeśli instalacja nie jest właściwe. |Może być konieczne wykonanie do ustawień fabrycznych na urządzeniu. Może być konieczne utworzenie zasobu platformy. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| 7. |0x8007138c — zasób klastra nie online |Zasoby klastra platformy lub ścieżki danych nie są w trybie online. |Skontaktuj się z Microsoft Support w celu zapewnienia, że zasób usługi ścieżki danych i platformy online. |

> [!NOTE]
> * Powyższej listy komunikaty o błędach nie jest wyczerpująca.
> * Błędy związane z ustawieniami serwera proxy sieci web, nie pojawi się w witrynie Azure portal w usłudze Menedżer urządzeń StorSimple. Jeśli występuje problem z serwera proxy sieci web, po zakończeniu konfiguracji, jego stan zmieni się na **Offline** w portalu klasycznym. |

## <a name="next-steps"></a>Następne kroki
* Jeśli napotkasz jakiekolwiek problemy podczas wdrażania urządzenia lub konfigurowanie ustawień serwera proxy sieci web, zapoznaj się [Rozwiązywanie problemów z wdrożenia urządzenia StorSimple](storsimple-troubleshoot-deployment.md).
* Aby dowiedzieć się, jak używać usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

