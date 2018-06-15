---
title: Konfigurowanie serwera proxy sieci web dla urządzenia z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia serwera proxy sieci web dla urządzenia StorSimple przy użyciu programu Windows PowerShell dla StorSimple.
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
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874997"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Skonfiguruj serwer proxy sieci web dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak za pomocą programu Windows PowerShell dla StorSimple można skonfigurować oraz wyświetlić ustawienia serwera proxy sieci web dla urządzenia StorSimple. Ustawienia serwera proxy sieci web są używane przez urządzenia StorSimple podczas komunikowania się z chmurą. Serwer proxy sieci web służy do dodawania kolejna warstwa zabezpieczeń, filtr zawartości pamięci podręcznej w celu ułatwienia wymaganiach odnośnie do przepustowości lub nawet uzyskać pomoc dotyczącą analytics.

Wskazówki zawarte w tym samouczku dotyczą tylko urządzeń fizycznych z serii StorSimple 8000. Konfiguracja serwera proxy sieci Web nie jest obsługiwana na urządzeniu StorSimple chmury (urządzenia 8010 i 8020).

Serwer proxy sieci Web jest _opcjonalne_ konfiguracji dla urządzenia StorSimple. Możesz skonfigurować serwer proxy sieci web tylko przy użyciu programu Windows PowerShell dla urządzenia StorSimple. Konfiguracja jest procesem dwuetapowym w następujący sposób:

1. Skonfiguruj ustawienia serwera proxy sieci web za pomocą kreatora lub środowiska Windows PowerShell dla StorSimple polecenia cmdlet.
2. Następnie Włącz ustawienia serwera proxy sieci web skonfigurowany za pomocą środowiska Windows PowerShell dla StorSimple polecenia cmdlet.

Po zakończeniu konfiguracji serwera proxy sieci web można wyświetlić ustawienia serwera proxy sieci web skonfigurowany w usługi Microsoft Azure StorSimple Menedżer urządzeń i środowiska Windows PowerShell dla urządzenia StorSimple.

Po przeczytaniu tego samouczka, będą mieć możliwość:

* Skonfiguruj serwer proxy sieci web przy użyciu Kreatora instalacji i poleceń cmdlet.
* Włącz serwer proxy sieci web przy użyciu poleceń cmdlet.
* Wyświetl ustawienia serwera proxy sieci web w portalu Azure.
* Rozwiązywanie błędów podczas konfigurowania serwera proxy sieci web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Skonfiguruj serwer proxy sieci web za pomocą środowiska Windows PowerShell dla urządzenia StorSimple

Użyj jednej z następujących skonfigurować ustawienia serwera proxy sieci web:

* Kreator instalacji do przeprowadzenia kroków konfiguracji.
* Polecenia cmdlet programu Windows PowerShell dla StorSimple.

W poniższych sekcjach omówiono każdej z tych metod.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Skonfiguruj serwer proxy sieci web za pomocą Kreatora instalacji

Wykonaj kroki konfiguracji serwera proxy sieci web za pomocą Kreatora konfiguracji. Wykonaj poniższe kroki, aby skonfigurować serwer proxy sieci web na urządzeniu.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Aby skonfigurować serwer proxy sieci web za pomocą Kreatora instalacji

1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu** i podaj **hasło administratora urządzenia**. Wpisz następujące polecenie, aby uruchomić sesję kreatora instalacji:
   
    `Invoke-HcsSetupWizard`
2. Jeśli jest to, czy użyto Kreatora instalacji w czasie rejestracji urządzenia po raz pierwszy, należy skonfigurować wszystkie ustawienia sieciowe wymagane, aż do konfiguracji serwera proxy sieci web. Jeśli urządzenie jest już zarejestrowany, Zaakceptuj wszystkie ustawienia skonfigurowanej sieci, aż do konfiguracji serwera proxy sieci web. W Kreatorze instalacji, gdy zostanie wyświetlony monit, aby skonfigurować ustawienia serwera proxy sieci web, wpisz **tak**.
3. Dla **adres URL serwera Proxy sieci Web**, podaj adres IP lub w pełni kwalifikowaną nazwę (FQDN) serwera proxy sieci web i numer portu TCP, który chcesz urządzenia do użycia przy komunikacji z chmurą. Użyj następującego formatu:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Domyślnie jest określony numer portu TCP 8080.
4. Wybierz typ uwierzytelniania jako **NTLM**, **podstawowe**, lub **Brak**. Podstawowa jest najmniej bezpieczna uwierzytelniania dla konfiguracji serwera proxy. NT LAN Manager (NTLM) to protokół uwierzytelniania wysokiego poziomu zabezpieczeń i złożone korzystającym z systemu obsługi wiadomości trzystopniowo, (czasami cztery Jeśli wymagane jest dodatkowe integralności) do uwierzytelnienia użytkownika. Domyślne uwierzytelnianie to NTLM. Aby uzyskać więcej informacji, zobacz [podstawowe](http://hc.apache.org/httpclient-3.x/authentication.html) i [uwierzytelniania NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **W usłudze Menedżer urządzeń StorSimple wykresy monitorowania urządzenia nie działają w przypadku podstawowych lub w konfiguracji serwera proxy dla tego urządzenia jest włączone uwierzytelnianie NTLM. Monitorowania wykresy do pracy należy upewnić się, że uwierzytelnianie ma wartość NONE.**
  
5. Jeśli uwierzytelnianie jest włączone, należy podać **nazwa użytkownika serwera Proxy sieci Web** i **hasło serwera Proxy sieci Web**. Należy potwierdzić hasło.
   
    ![Skonfiguruj serwer Proxy sieci Web na StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

W przypadku rejestracji urządzenia po raz pierwszy, należy kontynuować rejestrację. Jeśli urządzenie zostało już zarejestrowane, kończy pracę kreatora. Skonfigurowane ustawienia są zapisywane.

Serwer proxy sieci Web jest teraz włączony. Można pominąć [włączyć serwer proxy sieci web](#enable-web-proxy) kroku i przejść bezpośrednio do [wyświetlić ustawienia serwera proxy sieci web w portalu Azure](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Skonfiguruj serwer proxy sieci web za pomocą środowiska Windows PowerShell dla StorSimple poleceń cmdlet

To alternatywny sposób, aby skonfigurować ustawienia serwera proxy sieci web za pomocą programu Windows PowerShell dla StorSimple poleceń cmdlet. Wykonaj poniższe kroki, aby skonfigurować serwer proxy sieci web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Aby skonfigurować serwer proxy sieci web za pomocą polecenia cmdlet
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Wprowadź i Potwierdź hasło po wyświetleniu monitu.
   
    ![Skonfiguruj serwer Proxy sieci Web na StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Serwer proxy sieci web jest teraz skonfigurowane i musi zostać włączony.

## <a name="enable-web-proxy"></a>Włącz serwer proxy sieci web

Serwer proxy sieci Web jest domyślnie wyłączona. Po skonfigurowaniu ustawień serwera proxy sieci web na urządzeniu StorSimple, użyj programu Windows PowerShell dla urządzenia StorSimple, aby włączyć ustawienia serwera proxy sieci web.

> [!NOTE]
> **Ten krok nie jest wymagane, jeśli użyto Kreatora instalacji do skonfigurowania serwera proxy sieci web. Serwer proxy sieci Web automatycznie jest domyślnie włączona po sesji kreatora instalacji.**


W programie Windows PowerShell dla urządzenia StorSimple włączyć serwer proxy sieci web na urządzeniu, należy wykonać następujące czynności:

#### <a name="to-enable-web-proxy"></a>Aby włączyć serwer proxy sieci web
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest `Password1`.
2. W wierszu polecenia wpisz:
   
    `Enable-HcsWebProxy`
   
    Konfiguracja serwera proxy sieci web ma teraz włączone na urządzeniu StorSimple.
   
    ![Skonfiguruj serwer Proxy sieci Web na StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Wyświetl ustawienia serwera proxy sieci web w portalu Azure

Ustawienia serwera proxy sieci web są skonfigurowane za pośrednictwem interfejsu programu Windows PowerShell i nie można zmienić w portalu. Można jednak przeglądać te ustawienia skonfigurowane w portalu. Wykonaj poniższe kroki, aby wyświetlić serwera proxy sieci web.

#### <a name="to-view-web-proxy-settings"></a>Aby wyświetlić ustawienia serwera proxy sieci web
1. Przejdź do **usługi Menedżer StorSimple urządzenia > urządzenia**. Wybierz i kliknij urządzenie, a następnie przejdź do **ustawienia urządzenia > sieci**.

    ![Kliknij sieć](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. W **ustawień sieciowych** bloku, kliknij przycisk **serwer proxy sieci Web** kafelka.

    ![Kliknij serwer proxy sieci web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. W **serwer proxy sieci Web** bloku, sprawdź ustawienia serwera proxy sieci web skonfigurowana na urządzeniu StorSimple.
   
    ![Wyświetl ustawienia serwera proxy sieci web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Błędy podczas konfiguracji serwera proxy sieci web

Jeśli ustawienia serwera proxy sieci web są niepoprawnie skonfigurowane, komunikaty o błędach są wyświetlane dla użytkownika w programie Windows PowerShell dla urządzenia StorSimple. W poniższej tabeli opisano niektóre z tych komunikaty o błędach, ich prawdopodobne przyczyny i zalecane działania.

| Numer porządkowy. | Błąd HRESULT kodu | Możliwe przyczyny | Zalecane działanie |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Polecenie jest uruchamiane z pasywnym kontrolera i nie może nawiązać połączenia z kontrolerem active. |Uruchom polecenie na aktywnym kontrolerze. Aby uruchomić polecenie z pasywnym kontrolera, należy naprawić połączeń pasywnych z aktywnym kontrolerze. Microsoft Support musi Uwzględnij, jeśli to połączenie zostanie przerwane. |
| 2. |0x800710dd — identyfikator operacji jest nieprawidłowy |Ustawienia serwera proxy nie są obsługiwane na urządzeniu chmury StorSimple. |Ustawienia serwera proxy nie są obsługiwane na urządzeniu chmury StorSimple. Można je skonfigurować tylko na urządzeniu fizycznym StorSimple. |
| 3. |0x80070057 — nieprawidłowy parametr |Jeden z parametrów podanych ustawień serwera proxy jest nieprawidłowy. |Identyfikator URI nie jest dostępny w poprawnym formacie. Użyj następującego formatu:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA — serwer RPC jest niedostępny |Główną przyczyną problemu jest jedną z następujących czynności:</br></br>Klaster nie jest w górę. </br></br>Usługa ścieżki danych nie jest uruchomiona.</br></br>Jest wykonywane z pasywnym kontrolera i nie może nawiązać połączenia z kontrolerem active. |Uwzględnij Microsoft Support aby upewnić się, że klaster jest uruchomiony i działa usługa ścieżki danych.</br></br>Uruchom polecenie z aktywnym kontrolerze. Jeśli chcesz uruchomić polecenie z pasywnym kontrolera, upewnij się, że pasywnym kontroler może komunikować się z aktywnym kontrolerze. Microsoft Support musi Uwzględnij, jeśli to połączenie zostanie przerwane. |
| 5. |0x800706be - wywołanie RPC nie powiodło się |Klaster jest wyłączony. |Uwzględnij Microsoft Support aby upewnić się, że klaster jest uruchomiony. |
| 6. |0x8007138f — nie można odnaleźć zasobu klastra |Nie znaleziono zasobu klastra usługi platformy. Może to nastąpić, jeśli instalacja nie jest właściwy. |Może być konieczne przeprowadzenie resetowania urządzenia do ustawień fabrycznych. Może być konieczne tworzenie zasobu platformy. Skontaktuj się z Microsoft Support następnych kroków. |
| 7. |0x8007138c - zasób klastra nie w trybie online |Zasoby klastra platformy lub ścieżki danych nie są online. |Skontaktuj się z Microsoft Support w celu zapewnienia, że zasób usługi platforma i ścieżki danych online. |

> [!NOTE]
> * Powyżej listę komunikatów o błędach nie jest kompletną.
> * Błędy związane z ustawieniami serwera proxy sieci web zostanie pominięta w portalu Azure w usłudze Menedżer StorSimple urządzenia. Jeśli wystąpi problem z serwerem proxy sieci web po zakończeniu konfiguracji, stan urządzenia zmieni się na **Offline** w klasycznym portalu. |

## <a name="next-steps"></a>Następne kroki
* Jeśli wystąpią problemy podczas wdrażania urządzenia lub konfigurowanie ustawień serwera proxy sieci web dotyczą [rozwiązywania problemów z wdrożeniem urządzenia StorSimple](storsimple-troubleshoot-deployment.md).
* Aby dowiedzieć się, jak używać usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

