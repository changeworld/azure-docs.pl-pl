---
title: "Konfigurowanie serwera usługi Azure MFA wysokiej dostępności | Dokumentacja firmy Microsoft"
description: "Wdrażanie wielu wystąpień serwera usługi Azure Multi-Factor Authentication w konfiguracji, które zapewniają wysoką dostępność."
services: multi-factor-authentication
keywords: "Usługa Azure MFA"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c2f9a2422ea5a47edd80948073c7f7a6cb0d0945
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurowanie serwera usługi Azure Multi-Factor Authentication wysokiej dostępności

Do osiągnięcia wysokiej dostępności z wdrożeniem usługi Azure MFA serwera, należy wdrożyć wiele serwerów usługi MFA. Ta sekcja zawiera informacje dotyczące projektu równoważeniem obciążenia do osiągnięcia celów wysokiej dostępności w możesz wdrożenia serwera MFS Azure.

## <a name="mfa-server-overview"></a>Omówienie serwera usługi MFA

Architektura usługi Azure MFA serwera obejmuje kilka składników, jak pokazano na poniższym diagramie:

 ![Architektura serwera usługi MFA](./media/mfa-server-high-availability/mfa-ha-architecture.png)

Serwer usługi MFA jest Windows Server, który ma zainstalowane oprogramowanie Azure Multi-Factor Authentication. Wystąpienie serwera usługi MFA musi być aktywowany przez usługę MFA na platformie Azure funkcji. Więcej niż jeden serwer usługi MFA może być zainstalowana na lokalnym.

Pierwszy serwer usługi MFA, na którym jest zainstalowany jest głównym serwera usługi MFA po aktywacji przez usługę Azure MFA domyślnie. Główny serwer usługi MFA ma kopię zapisu PhoneFactor.pfdata bazy danych. Kolejnych instalacji wystąpienia serwera usługi MFA są nazywane slaves. MFA slaves ma replikowane tylko do odczytu kopię bazy danych PhoneFactor.pfdata. Serwery MFA replikować informacji za pomocą zdalnego wywoływania procedur (RPC). Wszystkie serwery usługi MFA muszą zbiorczo zostać przyłączony do domeny lub autonomicznego do replikowania informacji.

Zarówno MFA i podrzędna serwerów usługi MFA komunikować się z usługi MFA, gdy jest wymagane uwierzytelnianie dwuskładnikowe. Na przykład gdy użytkownik próbuje uzyskać dostęp do aplikacji, która wymaga uwierzytelniania dwuskładnikowego, użytkownik zostanie najpierw uwierzytelniany przez dostawcę tożsamości, takie jak Active Directory (AD).

Po pomyślnym uwierzytelnieniu z usługą Active Directory serwer usługi MFA będą komunikować się z usługą MFA. Serwer usługi MFA czeka na powiadomienie z usługi MFA, aby zezwolić lub odmówić dostępu użytkownika do aplikacji.

Serwer główny MFA przejdzie do trybu offline, nadal mogą być przetwarzane uwierzytelnienia, ale nie można przetworzyć operacji, które wymagają zmiany w bazie danych usługi MFA. (Przykłady: dodanie użytkowników samoobsługi zmiany numeru PIN i zmiana informacji o użytkowniku)

## <a name="deployment"></a>Wdrożenie

Należy rozważyć następujące kwestie ważne dla równoważenia obciążenia serwera usługi Azure MFA i jej powiązane składniki.

* **Przy użyciu standardu RADIUS w celu zapewniania wysokiej dostępności**. Jeśli używasz usługi Azure MFA serwerów jako serwery RADIUS, można skonfigurować jako podstawowy cel uwierzytelniania usługi RADIUS i innymi serwerami usługi MFA Azure jako miejsca docelowe dodatkowego uwierzytelniania potencjalnie jeden serwer usługi MFA. Jednak nie może być przydatna tę metodę w celu uzyskania wysokiej dostępności, ponieważ musi czekać na limit czasu występuje w przypadku niepowodzenia uwierzytelniania w celu podstawowego uwierzytelniania, zanim mogą być uwierzytelniane względem celu dodatkowego uwierzytelniania. Jest bardziej wydajne ruchu usługi RADIUS między klientem RADIUS i serwery usługi RADIUS (w tym przypadku serwerów uwierzytelnianie wieloskładnikowe Azure działają jako serwery RADIUS) równoważenia obciążenia, aby klientów RADIUS można skonfigurować pojedynczy adres URL, który może wskazywać.
* **Konieczne ręczne podwyższenie MFA slaves**. Jeśli serwer główny usługi Azure MFA przejdzie do trybu offline, serwerów pomocniczych uwierzytelnianie wieloskładnikowe Azure dalsze przetwarzanie żądań uwierzytelniania Wieloskładnikowego. Jednak dopóki głównego serwera usługi MFA są dostępne, Administratorzy nie można dodać użytkowników lub zmodyfikować ustawienia uwierzytelniania MFA, a użytkownicy nie mogą wprowadzać zmiany przy użyciu aplikacji portal użytkowników. Promowanie podrzędna MFA roli głównego jest zawsze proces ręczny.
* **Odrębność składników**. Serwera usługi Azure MFA obejmuje kilka składników, które mogą być instalowane na tym samym wystąpieniu systemu Windows Server lub w różnych wystąpieniach. Następujące składniki: Portal użytkowników, Mobile App Web Service i adapter AD FS (agent). Ta odrębność sprawia, że można używać serwera Proxy aplikacji sieci Web portalu użytkowników i serwera sieci Web aplikacji mobilnej publikowanie w sieci obwodowej. Taka konfiguracja dodaje na ogólne bezpieczeństwo projektu, jak pokazano na poniższym diagramie. Portal użytkowników usługi MFA i serwer sieci Web aplikacji mobilnej może również wdrożyć w konfiguracji równoważenia obciążenia wysokiej dostępności.

   ![Serwer usługi MFA w sieci obwodowej](./media/mfa-server-high-availability/mfasecurity.png)

* **Hasła jednorazowego (OTP) za pośrednictwem programu SMS (alias jednokierunkowe SMS) wymaga użycia trwałe sesje, jeśli ruch jest równoważeniem obciążenia**. Jednokierunkowe programu SMS to opcja uwierzytelniania, która powoduje, że serwer usługi MFA wysłać użytkownikom wiadomość tekstową zawierającą OTP. W oknie monitu, aby wykonać żądanie uwierzytelniania MFA, użytkownik wprowadza kod OTP. Po załadowaniu saldo serwerów Azure MFA, tym samym serwerze, który obsłużył żądanie uwierzytelniania początkowego musi być serwer, który odbiera komunikat OTP od użytkowników; inny serwer usługi MFA odbiera odpowiedź uwierzytelniania OTP, żądanie uwierzytelnienia nie powiedzie się. Aby uzyskać więcej informacji, zobacz [jedno hasło czas za pośrednictwem programu SMS dodane do serwera usługi Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Trwałe sesje wymagają równoważeniem obciążenia wdrożeń z portalem użytkowników a Mobile App Web Service**. Jeśli użytkownik są równoważenia obciążenia portalu użytkownika usługi MFA i usługi Mobile App Web Service, każdej sesji musi pozostać na tym samym serwerze.

## <a name="high-availability-deployment"></a>Wdrożenie wysokiej dostępności

Na poniższym diagramie przedstawiono zakończenia HA równoważeniem obciążenia wdrożenia usługi Azure MFA i jego składników, wraz z usług AD FS dla odwołania.

 ![Azure MFA serwera HA implementacji](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Należy pamiętać, następujące elementy dla obszaru odpowiednio numerowane na diagramie.

1. Dwa serwery uwierzytelnianie wieloskładnikowe Azure (MFA1 i MFA2) są objęte równoważeniem obciążenia (mfaapp.contoso.com) i są skonfigurowane do używania portu statycznego (4443), aby replikować PhoneFactor.pfdata bazy danych. Zestaw SDK usług sieci Web jest zainstalowany na każdym z serwera usługi MFA, aby umożliwić komunikację za pośrednictwem portu TCP 443 na serwerach usług AD FS. Serwery uwierzytelniania MFA są wdrażane w bezstanowej konfiguracji usługi równoważenia obciążenia. Jednak jeśli chcesz użyć uwierzytelniania OTP za pośrednictwem programu SMS, musi użyć równoważenia obciążenia stanowe.
   ![Azure MFA Serwer - App wysokiej dostępności](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Ponieważ RPC używa portów dynamicznych, nie zaleca się otwarcie zapory do zakresu portów dynamicznych, które mogą za pomocą usługi RPC. Jeśli Zapora jest **między** MFA serwerów aplikacji, należy skonfigurować serwer usługi MFA do komunikacji na portu statycznego dla ruchu replikacji między podrzędny i serwery główne i otworzyć ten port w zaporze. Możesz wymusić portu statycznego, tworząc wartości DWORD rejestru na ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` o nazwie ```Pfsvc_ncan_ip_tcp_port``` i ustawiając wartość dostępny port statyczny. Połączenia są zawsze inicjowane przez podrzędny serwerów usługi MFA do poziomu głównego, portu statycznego jest wymagana tylko na głównym, ale ponieważ podrzędna jako wzorca w dowolnym momencie możesz podwyższyć poziom, należy ustawić portu statycznego na wszystkich serwerach usługi MFA.

2. Dwa serwery aplikacji mobilnej usługi MFA w portalu/użytkownika (uwierzytelnianie wieloskładnikowe-UP-MAS1 i MFA-UP-MAS2) jest równoważone w **stateful** konfiguracji (mfa.contoso.com). Odwołaj się, że trwałe sesje są wymagania dotyczące równoważenia obciążenia portalu użytkownika uwierzytelniania Wieloskładnikowego oraz Mobile App Service.
   ![Serwer usługi Azure MFA — portalem użytkowników a usługą aplikacji mobilnych wysokiej dostępności](./media/mfa-server-high-availability/mfaportal.png)
3. Farma serwerów usług AD FS jest równoważeniu obciążenia i publikowany w Internecie przy użyciu równoważenia obciążenia serwerów proxy usług AD FS w sieci obwodowej. Każdy serwer usług AD FS używa agenta usług AD FS do komunikacji z serwerami usługi MFA Azure przy użyciu jednej z równoważeniem obciążenia adresu URL (mfaapp.contoso.com) za pośrednictwem portu TCP 443.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie i konfigurowanie serwera usługi Azure MFA](multi-factor-authentication-get-started-server.md)
