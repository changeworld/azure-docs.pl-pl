---
title: Wysoka dostępność usługi Azure MFA Server — usługa Azure Active Directory
description: Wdrażanie wielu wystąpień serwera uwierzytelniania wieloskładnikowego platformy Azure w konfiguracjach zapewniających wysoką dostępność.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b2df4e87dddcfedd10682e4e3ab6c014ad7bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848191"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurowanie serwera uwierzytelniania wieloskładnikowego platformy Azure w celu zapewnienia wysokiej dostępności

Aby osiągnąć wysoką dostępność dzięki wdrożeniu usługi Azure Server MFA, należy wdrożyć wiele serwerów usługi MFA. Ta sekcja zawiera informacje na temat projektu z równoważeniem obciążenia, aby osiągnąć cele wysokiej dostępności we wdrożeniu usługi Azure MFS Server.

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="mfa-server-overview"></a>Omówienie serwera usługi MFA

Architektura usługi usługi usługi Usługi Usługi Azure MFA zawiera kilka składników, jak pokazano na poniższym diagramie:

 ![Składniki architektury serwera usługi MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Serwer usługi MFA to serwer windowsowy z zainstalowanym oprogramowaniem uwierzytelniania wieloskładnikowego azure. Wystąpienie serwera usługi MFA musi być aktywowane przez usługę usługi MFA na platformie Azure, aby działało. W środowisku lokalnym można zainstalować więcej niż jeden serwer usługi MFA.

Pierwszy serwer usługi MFA, który jest zainstalowany jest serwerem zbiorczym zbiorczym po aktywacji przez usługę Azure MFA domyślnie. Główny serwer usługi MFA ma zapisywalną kopię bazy danych PhoneFactor.pfdata. Kolejne instalacje wystąpień serwera usługi MFA są nazywane podwładnymi. Podwładni usługi MFA mają replikowaną kopię bazy danych PhoneFactor.pfdata tylko do odczytu. Serwery usługi MFA replikują informacje przy użyciu zdalnego wywołania procedury (RPC). Wszystkie usługi MFA Severs muszą być łącznie przyłączone do domeny lub autonomiczne w celu replikowania informacji.

Serwery główne i podrzędne usługi MFA komunikują się z usługą usługi MFA, gdy wymagane jest uwierzytelnianie dwuskładnikowe. Na przykład gdy użytkownik próbuje uzyskać dostęp do aplikacji, która wymaga uwierzytelniania dwuskładnikowego, użytkownik zostanie najpierw uwierzytelniony przez dostawcę tożsamości, takiego jak usługa Active Directory (AD).

Po pomyślnym uwierzytelnieniu za pomocą usługi AD serwer usługi MFA będzie komunikować się z usługą usługi MFA. Serwer usługi MFA czeka na powiadomienie z usługi MFA, aby zezwolić lub odmówić użytkownikowi dostępu do aplikacji.

Jeśli serwer główny usługi MFA przejdzie w tryb offline, uwierzytelnianie nadal mogą być przetwarzane, ale operacje, które wymagają zmian w bazie danych usługi MFA, nie mogą być przetwarzane. (Przykłady obejmują: dodanie użytkowników, samoobsługowe zmiany numeru PIN, zmianę informacji o użytkowniku lub dostęp do portalu użytkownika)

## <a name="deployment"></a>wdrażania

Należy wziąć pod uwagę następujące ważne punkty równoważenia obciążenia usługi Azure MFA Server i jego powiązanych składników.

* **Używanie standardu RADIUS do uzyskania wysokiej dostępności**. Jeśli serwery usługi Azure MFA są serwerami USŁUGI RADIUS, można potencjalnie skonfigurować jeden serwer usługi MFA jako podstawowy obiekt docelowy uwierzytelniania USŁUGI RADIUS i inne serwery usługi Azure MFA jako pomocnicze obiekty docelowe uwierzytelniania. Jednak ta metoda osiągnięcia wysokiej dostępności może nie być praktyczne, ponieważ należy poczekać na upływ czasu występuje, gdy uwierzytelnianie nie powiedzie się w podstawowym docelowym uwierzytelniania, zanim można uwierzytelnić się względem pomocniczego obiektu docelowego uwierzytelniania. Bardziej wydajne jest równoważenie obciążenia ruchu RADIUS między klientem RADIUS a serwerami RADIUS (w tym przypadku serwerami usługi Azure MFA działającymi jako serwery RADIUS), dzięki czemu można skonfigurować klientów RADIUS za pomocą jednego adresu URL, na który mogą wskazać.
* **Trzeba ręcznie promować podwładnych usługi MFA**. Jeśli główny serwer usługi Azure MFA przejdzie w tryb offline, pomocnicze serwery usługi Azure MFA nadal przetwarzają żądania usługi MFA. Jednak dopóki główny serwer usługi MFA nie będzie dostępny, administratorzy nie mogą dodawać użytkowników ani modyfikować ustawień usługi MFA, a użytkownicy nie mogą wprowadzać zmian za pomocą portalu użytkownika. Promowanie funkcji mfa podrzędnych do roli wzorca jest zawsze procesem ręcznym.
* **Rozdzielność komponentów**. Serwer usługi Azure MFA zawiera kilka składników, które można zainstalować w tym samym wystąpieniu systemu Windows Server lub w różnych wystąpieniach. Składniki te obejmują portal użytkownika, usługę sieci Web aplikacji mobilnych i kartę ADFS (agent). Ta rozdzielność umożliwia korzystanie z serwera proxy aplikacji sieci Web do publikowania portalu użytkownika i serwera sieci Web aplikacji mobilnych z sieci obwodowej. Taka konfiguracja zwiększa ogólne bezpieczeństwo projektu, jak pokazano na poniższym diagramie. Portal użytkownika usługi MFA i serwer sieci Web aplikacji mobilnych można również wdrożyć w konfiguracjach z równoważenia obciążenia usługi ha.

   ![Serwer usługi MFA z siecią obwodową](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Jednorazowe hasło (OTP) przez SMS (aka jednokierunkowe SMS) wymaga użycia lepkich sesji, jeśli ruch jest równoważące obciążenie**. Jednokierunkowy program SMS jest opcją uwierzytelniania, która powoduje, że serwer usługi MFA wysyła użytkownikom wiadomość tekstową zawierającą protokół OTP. Użytkownik wprowadza OTP w oknie monitu, aby zakończyć wyzwanie usługi MFA. Jeśli obciążenie równoważenia serwerów usługi Azure MFA, ten sam serwer, który obsługiwał żądanie początkowego uwierzytelniania musi być serwer, który odbiera komunikat OTP od użytkownika; jeśli inny serwer usługi MFA otrzyma odpowiedź OTP, wyzwanie uwierzytelniania nie powiedzie się. Aby uzyskać więcej informacji, zobacz [Hasło jednorazowe za pośrednictwem programu SMS dodane do serwera usługi Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* Wdrożenia portalu **użytkownika i usługi sieci Web aplikacji mobilnych z równoważeniem obciążenia wymagają sesji przyklejonych.** Jeśli równoważenie obciążenia portalu użytkownika usługi MFA i usługi sieci Web aplikacji mobilnych musi pozostać na tym samym serwerze.

## <a name="high-availability-deployment"></a>Wdrażanie o wysokiej dostępności

Na poniższym diagramie przedstawiono pełną implementację usługi ha równoważenia obciążenia usługi Azure MFA i jej składników, wraz z usługą ADFS w celach informacyjnych.

 ![Implementacja usługi Azure MFA Server](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Zwróć uwagę na następujące elementy odpowiednio numerowanego obszaru poprzedniego diagramu.

1. Dwa serwery usługi Azure MFA (MFA1 i MFA2) są równoważone obciążeniem (mfaapp.contoso.com) i są skonfigurowane do używania portu statycznego (4443) do replikowania bazy danych PhoneFactor.pfdata. SDK usługi sieci Web jest zainstalowany na każdym serwerze usługi MFA, aby umożliwić komunikację za pośrednictwem portu TCP 443 z serwerami ADFS. Serwery usługi MFA są wdrażane w konfiguracji bezstanowej z równoważenia obciążenia. Jeśli jednak chcesz używać protokołu OTP za pośrednictwem programu SMS, należy użyć równoważenia obciążenia stanowego.
   ![Usługa Azure MFA Server — narzędzie wielofunkcyjne serwera aplikacji](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Ponieważ RPC używa portów dynamicznych, nie zaleca się otwierania zapór do zakresu portów dynamicznych, które potencjalnie mogą korzystać z usługi RPC. Jeśli masz zaporę **między serwerami** aplikacji usługi MFA, należy skonfigurować serwer usługi MFA do komunikowania się na porcie statycznym dla ruchu replikacji między serwerami podrzędnymi i głównymi i otworzyć ten port na zaporze. Można wymusić port statyczny, tworząc wartość rejestru ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` ```Pfsvc_ncan_ip_tcp_port``` DWORD na wywołanej i ustawiając wartość na dostępny port statyczny. Połączenia są zawsze inicjowane przez podrzędne serwery usługi MFA do wzorca, port statyczny jest wymagany tylko na wzorcu, ale ponieważ można podwyższyć podrzędny jako wzorca w dowolnym momencie, należy ustawić port statyczny na wszystkich serwerach usługi MFA.

2. Dwa serwery aplikacji mobilnych portalu użytkownika/usługi MFA (MFA-UP-MAS1 i MFA-UP-MAS2) są równoważone z obciążeniem w konfiguracji **stanowej** (mfa.contoso.com). Przypomnijmy, że sesje przyklejone są wymagane do równoważenia obciążenia portalu użytkownika usługi MFA i usługi aplikacji mobilnych.
   ![Serwer usługi Azure MFA — portal użytkowników i usługa aplikacji mobilnych](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Farma serwera ADFS jest równoważona z obciążeniem i publikowana w Internecie za pośrednictwem serwerów proxy ADFS ze zrównoważonym obciążeniem w sieci obwodowej. Każdy serwer usługi ADFS używa agenta usługi ADFS do komunikowania się z serwerami usługi Azure MFA przy użyciu pojedynczego adresu URL z równoważenia obciążenia (mfaapp.contoso.com) za pośrednictwem portu TCP 443.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie i konfigurowanie serwera usługi Azure MFA](howto-mfaserver-deploy.md)
