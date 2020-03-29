---
title: Konfigurowanie serwera proxy sieci Web dla urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia serwera proxy storsimple za pomocą programu Windows PowerShell for StorSimple.
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
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204246"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurowanie serwera proxy sieci Web dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób konfigurowania i wyświetlania ustawień serwera proxy storsimple za pomocą programu Windows PowerShell for StorSimple. Ustawienia serwera proxy sieci Web są używane przez urządzenie StorSimple podczas komunikowania się z chmurą. Serwer proxy sieci Web służy do dodawania kolejnej warstwy zabezpieczeń, filtrowania zawartości, pamięci podręcznej w celu ułatwienia wymagań dotyczących przepustowości, a nawet pomocy w zakresie analizy.

Wskazówki zawarte w tym samouczku dotyczą tylko urządzeń fizycznych z serii StorSimple 8000. Konfiguracja serwera proxy sieci Web nie jest obsługiwana w urządzeniu StorSimple Cloud Appliance (8010 i 8020).

Serwer proxy sieci Web jest _opcjonalną_ konfiguracją urządzenia StorSimple. Serwer proxy sieci Web można skonfigurować tylko za pośrednictwem programu Windows PowerShell dla storsimple. Konfiguracja jest procesem dwuetapowym w następujący sposób:

1. Najpierw skonfiguruj ustawienia serwera proxy sieci Web za pomocą kreatora instalacji lub programu Windows PowerShell dla poleceń cmdlet StorSimple.
2. Następnie można włączyć skonfigurowane ustawienia serwera proxy sieci Web za pośrednictwem programu Windows PowerShell dla poleceń cmdlet StorSimple.

Po zakończeniu konfiguracji serwera proxy sieci Web można wyświetlić skonfigurowane ustawienia serwera proxy sieci Web zarówno w usłudze Menedżera urządzeń Usługi Microsoft Azure StorSimple, jak i w programie Windows PowerShell for StorSimple.

Po przeczytaniu tego samouczka, będzie można:

* Konfigurowanie serwera proxy sieci Web przy użyciu kreatora konfiguracji i poleceń cmdlet.
* Włącz serwer proxy sieci Web przy użyciu poleceń cmdlet.
* Wyświetlanie ustawień serwera proxy sieci Web w witrynie Azure portal.
* Rozwiązywanie problemów z błędami podczas konfiguracji serwera proxy sieci Web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurowanie serwera proxy sieci Web za pośrednictwem programu Windows PowerShell dla storsimple

Do skonfigurowania ustawień serwera proxy sieci Web należy użyć jednej z następujących czynności:

* Kreator instalacji, który poprowadzi Cię przez kroki konfiguracji.
* Polecenia cmdlet w programie Windows PowerShell dla StorSimple.

Każda z tych metod jest omówiona w poniższych sekcjach.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurowanie serwera proxy sieci Web za pomocą kreatora konfiguracji

Za pomocą kreatora konfiguracji można przejść przez kroki konfiguracji serwera proxy sieci Web. Wykonaj następujące czynności, aby skonfigurować serwer proxy sieci Web na urządzeniu.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Aby skonfigurować serwer proxy sieci Web za pomocą kreatora konfiguracji

1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnym dostępem** i podaj hasło **administratora urządzenia**. Aby rozpocząć sesję kreatora instalacji, wpisz następujące polecenie:
   
    `Invoke-HcsSetupWizard`
2. Jeśli jest to pierwszy raz, kiedy używasz kreatora konfiguracji do rejestracji urządzenia, należy skonfigurować wszystkie wymagane ustawienia sieciowe, dopóki nie osiągniesz konfiguracji serwera proxy sieci Web. Jeśli urządzenie jest już zarejestrowane, zaakceptuj wszystkie skonfigurowane ustawienia sieciowe, dopóki nie osiągniesz konfiguracji serwera proxy sieci Web. W kreatorze instalacji po wyświetleniu monitu o skonfigurowanie ustawień serwera proxy sieci Web wpisz **Tak**.
3. W przypadku **adresu URL serwera proxy sieci Web**określ adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN) serwera proxy sieci Web oraz numer portu TCP, którego urządzenie ma być używane podczas komunikacji z chmurą. Użyj następującego formatu:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Domyślnie określono numer portu TCP 8080.
4. Wybierz typ uwierzytelniania jako **NTLM**, **Basic**lub **None**. Podstawowe jest najmniej bezpiecznym uwierzytelnianiem dla konfiguracji serwera proxy. NT LAN Manager (NTLM) to wysoce bezpieczny i złożony protokół uwierzytelniania, który używa trójdrożnego systemu obsługi wiadomości (czasami czterech, jeśli wymagana jest dodatkowa integralność) do uwierzytelniania użytkownika. Domyślnym uwierzytelnianiem jest NTLM. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie podstawowe](https://hc.apache.org/httpclient-3.x/authentication.html) i [NTLM](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **W usłudze StorSimple Device Manager wykresy monitorowania urządzeń nie działają, gdy uwierzytelnianie podstawowe lub NTLM jest włączone w konfiguracji serwera proxy urządzenia. Aby wykresy monitorowania działały, należy upewnić się, że uwierzytelnianie jest ustawione na BRAK.**
  
5. Jeśli uwierzytelnianie zostało włączone, podaj **nazwę użytkownika serwera proxy sieci Web** i hasło serwera proxy sieci **Web**. Należy również potwierdzić hasło.
   
    ![Konfigurowanie serwera proxy sieci Web na urządzeniu StorSimple1](./media/storsimple-configure-web-proxy/IC751830.png)

Jeśli rejestrujesz urządzenie po raz pierwszy, kontynuuj rejestrację. Jeśli urządzenie zostało już zarejestrowane, kreator zakończy pracę. Skonfigurowane ustawienia zostaną zapisane.

Serwer proxy sieci Web jest teraz włączony. Możesz pominąć krok [Włącz serwer proxy sieci Web](#enable-web-proxy) i przejść bezpośrednio do ustawienia [wyświetlania serwera proxy sieci Web w witrynie Azure portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurowanie serwera proxy sieci Web za pośrednictwem programu Windows PowerShell dla poleceń cmdlet StorSimple

Alternatywnym sposobem konfigurowania ustawień serwera proxy sieci Web jest polecenie cmdlet programu Windows PowerShell for StorSimple. Wykonaj następujące kroki, aby skonfigurować serwer proxy sieci Web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Aby skonfigurować serwer proxy sieci Web za pomocą poleceń cmdlet
1. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślnym hasłem jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Podaj i potwierdź hasło po wyświetleniu monitu.
   
    ![Konfigurowanie serwera proxy sieci Web na urządzeniu StorSimple3](./media/storsimple-configure-web-proxy/IC751831.png)

Serwer proxy sieci Web jest teraz skonfigurowany i musi być włączony.

## <a name="enable-web-proxy"></a>Włączanie serwera proxy sieci Web

Serwer proxy sieci Web jest domyślnie wyłączony. Po skonfigurowaniu ustawień serwera proxy sieci Web na urządzeniu StorSimple użyj programu Windows PowerShell for StorSimple, aby włączyć ustawienia serwera proxy sieci Web.

> [!NOTE]
> **Ten krok nie jest wymagany, jeśli do skonfigurowania serwera proxy sieci Web użyto kreatora konfiguracji. Serwer proxy sieci Web jest domyślnie włączany automatycznie po sesji kreatora konfiguracji.**


Wykonaj następujące czynności w programie Windows PowerShell for StorSimple, aby włączyć serwer proxy sieci Web na urządzeniu:

#### <a name="to-enable-web-proxy"></a>Aby włączyć serwer proxy sieci Web
1. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślnym hasłem jest `Password1`.
2. W wierszu polecenia wpisz polecenie:
   
    `Enable-HcsWebProxy`
   
    Konfiguracja serwera proxy sieci Web została włączona na urządzeniu StorSimple.
   
    ![Konfigurowanie serwera proxy sieci Web na urządzeniu StorSimple4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Wyświetlanie ustawień serwera proxy sieci Web w witrynie Azure portal

Ustawienia serwera proxy sieci Web są konfigurowane za pośrednictwem interfejsu programu Windows PowerShell i nie można ich zmienić z poziomu portalu. Można jednak wyświetlić te skonfigurowane ustawienia w portalu. Wykonaj następujące kroki, aby wyświetlić serwer proxy sieci Web.

#### <a name="to-view-web-proxy-settings"></a>Aby wyświetlić ustawienia serwera proxy w sieci Web
1. Przejdź do **usługi StorSimple Device Manager > Devices**. Wybierz i kliknij urządzenie, a następnie przejdź do **pozycji Ustawienia urządzenia > sieć**.

    ![Kliknij pozycję Sieć](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. W bloku **Ustawienia sieci** kliknij kafelek serwera proxy **sieci Web.**

    ![Kliknij serwer proxy sieci Web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. W **bloku serwera proxy sieci Web** przejrzyj skonfigurowane ustawienia serwera proxy sieci Web na urządzeniu StorSimple.
   
    ![Wyświetlanie ustawień serwera proxy w sieci Web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Błędy podczas konfiguracji serwera proxy sieci Web

Jeśli ustawienia serwera proxy sieci Web są niepoprawnie skonfigurowane, komunikaty o błędach są wyświetlane użytkownikowi w programie Windows PowerShell for StorSimple. W poniższej tabeli wyjaśniono niektóre z tych komunikatów o błędach, ich prawdopodobne przyczyny i zalecane działania.

| Nr seryjny | Kod błędu HRESULT | Możliwa przyczyna | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Polecenie jest uruchamiane z kontrolera pasywnego i nie jest w stanie komunikować się z aktywnym kontrolerem. |Uruchom polecenie na aktywnym kontrolerze. Aby uruchomić polecenie z kontrolera pasywnego, należy naprawić łączność z pasywnego do aktywnego kontrolera. Jeśli ta łączność jest uszkodzona, należy zaangażować pomoc techniczną firmy Microsoft. |
| 2. |0x800710dd — identyfikator operacji jest nieprawidłowy |Ustawienia serwera proxy nie są obsługiwane w urządzeniu StorSimple Cloud Appliance. |Ustawienia serwera proxy nie są obsługiwane w urządzeniu StorSimple Cloud Appliance. Można je skonfigurować tylko na urządzeniu fizycznym StorSimple. |
| 3. |0x80070057 - Nieprawidłowy parametr |Jeden z parametrów podanych dla ustawień serwera proxy jest nieprawidłowy. |Identyfikator URI nie jest dostarczany w poprawnym formacie. Użyj następującego formatu:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - Serwer RPC niedostępny |Główną przyczyną jest jedna z następujących przyczyn:</br></br>Klaster nie jest w górę. </br></br>Usługa Datapath nie jest uruchomiona.</br></br>Polecenie jest uruchamiane z kontrolera pasywnego i nie jest w stanie komunikować się z aktywnym kontrolerem. |Zaangażuj pomoc techniczną firmy Microsoft, aby upewnić się, że klaster jest uruchomiony i usługa datapath jest uruchomiona.</br></br>Uruchom polecenie z aktywnego kontrolera. Jeśli chcesz uruchomić polecenie z kontrolera pasywnego, należy upewnić się, że kontroler pasywny może komunikować się z aktywnym kontrolerem. Jeśli ta łączność jest uszkodzona, należy zaangażować pomoc techniczną firmy Microsoft. |
| 5. |0x800706be - połączenie RPC nie powiodło się |Klaster jest w dół. |Zaangażuj pomoc techniczną firmy Microsoft, aby upewnić się, że klaster jest w stanie. |
| 6. |0x8007138f — nie znaleziono zasobu klastra |Nie znaleziono zasobu klastra usług platformy. Może się to zdarzyć, gdy instalacja nie była właściwa. |Może być konieczne przywrócenie ustawień fabrycznych na urządzeniu. Może być konieczne utworzenie zasobu platformy. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| 7. |0x8007138c — zasób klastra nie jest w trybie online |Zasoby klastra platformy lub ścieżki danych nie są w trybie online. |Skontaktuj się z pomocą techniczną firmy Microsoft, aby upewnić się, że zasoby usługi datapath i platformy są w trybie online. |

> [!NOTE]
> * Powyższa lista komunikatów o błędach nie jest wyczerpująca.
> * Błędy związane z ustawieniami serwera proxy sieci web nie będą wyświetlane w witrynie Azure portal w usłudze Menedżera urządzeń StorSimple. Jeśli po zakończeniu konfiguracji wystąpi problem z serwerem proxy sieci Web, stan urządzenia zmieni się na **Offline** w klasycznym portalu.|

## <a name="next-steps"></a>Następne kroki
* Jeśli wystąpią jakiekolwiek problemy podczas wdrażania urządzenia lub konfigurowania ustawień serwera proxy sieci Web, zapoznaj się [z instrukcjami dotyczącymi wdrażania urządzenia StorSimple](storsimple-troubleshoot-deployment.md).
* Aby dowiedzieć się, jak korzystać z usługi StorSimple Device Manager, przejdź do [usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

