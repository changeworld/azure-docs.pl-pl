---
title: Konfigurowanie serwera usługi Azure MFA wysokiej dostępności — usługi Azure Active Directory
description: Wdrażanie wielu wystąpień serwera Azure Multi-Factor Authentication w konfiguracjach, które zapewniają wysoką dostępność.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ddf0885ce7615e06b78eccbd6424e63cc6103c2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547009"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurowanie serwera usługi Azure Multi-Factor Authentication wysokiej dostępności

Uzyskanie wysokiej dostępności z wdrożeniem usługi Azure MFA Server, należy wdrożyć wiele serwerów usługi MFA. Ta sekcja zawiera informacje na temat projektu ze zrównoważonym obciążeniem, aby osiągnąć swoje cele wysokiej dostępności w możesz wdrożenia serwera MFS Azure.

## <a name="mfa-server-overview"></a>Serwer MFA — omówienie

Architektura usługi serwera Azure MFA obejmuje kilka składników, jak pokazano na poniższym diagramie:

 ![Składniki architektury serwera MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Serwer MFA jest systemu Windows Server, który ma zainstalowane oprogramowanie Azure Multi-Factor Authentication. Wystąpienie serwera MFA musi zostać aktywowany przez usługę MFA na platformie Azure do funkcji. Więcej niż jeden serwer usługi MFA może być zainstalowane w środowisku lokalnym.

Pierwszy serwer usługi MFA, na którym jest zainstalowany jest główną aplikacją serwer usługi MFA podczas aktywacji przez usługę Azure MFA domyślnie. Główny serwer MFA ma zapisywalnego kopię PhoneFactor.pfdata bazy danych. Instalacje kolejnych wystąpień serwera MFA są określane jako elementów podrzędnych. Uwierzytelnianie wieloskładnikowe podwładnych ma replikowane tylko do odczytu kopię bazy danych PhoneFactor.pfdata. Serwery usługi MFA replikować informacji za pomocą zdalnego wywołania procedury (RPC). Wszystkie serwery usługi MFA, musisz zbiorczo być przyłączone do domeny lub autonomiczny do replikowania informacji.

Zarówno węzła głównego usługi MFA, jak i serwery podrzędne MFA komunikować się z usługą MFA, gdy wymagane jest uwierzytelnianie dwuskładnikowe. Na przykład gdy użytkownik próbuje uzyskać dostęp do aplikacji, która wymaga uwierzytelniania dwuskładnikowego, użytkownik zostanie najpierw uwierzytelnieni przez dostawcę tożsamości, takie jak Active Directory (AD).

Po pomyślnym uwierzytelnieniu z usługą Active Directory serwer MFA będą komunikować się z usługą MFA. Serwer MFA czeka na powiadomienie z usługi MFA, aby udzielić lub odmówić dostępu użytkownika do aplikacji.

Jeśli głównym serwerem MFA przejdzie do trybu offline, uwierzytelnień mogą nadal być przetwarzane, ale nie można przetworzyć operacji, które wymagają zmian w bazie danych usługi MFA. (Przykłady: dodanie użytkowników, Samoobsługowe numeru PIN zmian, zmiany informacji o użytkowniku lub dostępu do aplikacji portal użytkowników)

## <a name="deployment"></a>Wdrożenie

Należy wziąć pod uwagę następujące kwestie istotne dla równoważenia obciążenia serwera Azure MFA i jej składniki pokrewne.

* **Przy użyciu standardu RADIUS w celu osiągnięcia wysokiej dostępności**. Jeśli używasz serwery usługi Azure MFA jako serwery usługi RADIUS, potencjalnie można skonfigurować jeden serwer usługi MFA jako podstawowy cel Uwierzytelnianie serwera RADIUS i inne serwery usługi Azure MFA jako elementy docelowe dodatkowego uwierzytelniania. Jednak nie może być przydatna tę metodę w celu uzyskania wysokiej dostępności, ponieważ musi czekać na limit czasu występuje w przypadku niepowodzenia uwierzytelniania w elemencie docelowym uwierzytelniania podstawowego, zanim mogą być uwierzytelniani względem obiektu docelowego uwierzytelniania pomocniczego. Jest bardziej wydajne w celu równoważenia ruchu usługi RADIUS między klientem RADIUS i serwery usługi RADIUS (w tym przypadku serwery usługi Azure MFA działających jako serwery usługi RADIUS), aby klienci usługi RADIUS można skonfigurować za pomocą pojedynczego adresu URL, który może wskazywać.
* **Trzeba ręcznie podwyższyć poziom elementów podrzędnych MFA**. Jeśli serwer główny Azure MFA przejdzie do trybu offline, pomocnicze serwery usługi Azure MFA w dalszym ciągu przetwarzać żądań uwierzytelniania Wieloskładnikowego. Jednak dopóki głównym serwerem MFA jest dostępna, Administratorzy mogą dodawać użytkowników lub nie zmodyfikować ustawień usługi MFA i użytkownicy nie mogą wprowadzać zmiany przy użyciu portalu użytkowników. Podwyższanie poziomu usługi MFA w stosunku do roli wzorca jest zawsze procesem wykonywanym ręcznie.
* **Odrębność składniki**. Serwer usługi Azure MFA obejmuje kilka składników, które mogą być instalowane na tym samym wystąpieniu systemu Windows Server lub w innych wystąpieniach. Te składniki obejmują portalu użytkowników, Mobile App Web Service i adaptera AD FS (agent). Ta odrębność sprawia, że można publikować portalu użytkowników i serwera sieci Web aplikacji mobilnej z sieci obwodowej za pomocą serwera Proxy aplikacji sieci Web. Taka konfiguracja dodaje się do ogólnego stanu zabezpieczeń z projektem, jak pokazano na poniższym diagramie. Ponadto można wdrażać portalu użytkowników usługi MFA i serwera sieci Web aplikacji mobilnej w konfiguracji równoważenia obciążenia o wysokiej dostępności.

   ![Serwer MFA z sieci obwodowej](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Hasła jednorazowego (OTP) za pośrednictwem wiadomości SMS (zwane również jednokierunkowa wiadomość SMS) wymaga użycia trwałych sesji, czy ruch ze zrównoważonym obciążeniem**. Jednokierunkowa wiadomość SMS jest opcja uwierzytelniania, który powoduje, że serwer MFA wysłać użytkownikom wiadomość tekstową zawierającą kod OTP. Użytkownik musi wprowadzić kod OTP w oknie monitu, aby wezwania usługi MFA. Jeśli załadujesz saldo serwery usługi Azure MFA, tym samym serwerze, który obsłużył żądanie uwierzytelniania początkowego musi być serwer, który odbiera komunikat OTP od użytkowników; inny serwer MFA odbiera odpowiedź uwierzytelniania OTP, żądanie uwierzytelnienia nie powiedzie się. Aby uzyskać więcej informacji, zobacz [jednego hasła czasu za pośrednictwem wiadomości SMS dodane do serwera Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Ze zrównoważonym obciążeniem wdrażanie portalu użytkowników i Mobile App Web Service wymaga trwałych sesji**. Jeśli użytkownik jest równoważenie obciążenia portalu użytkowników usługi MFA i Mobile App Web Service, każda sesja musi pozostać na tym samym serwerze.

## <a name="high-availability-deployment"></a>Wdrażanie wysokiej dostępności

Na poniższym diagramie przedstawiono pełną implementację równoważenia obciążenia o wysokiej dostępności usługi Azure MFA i jego składników, wraz z usług AD FS dla odwołania.

 ![Usługa Azure MFA serwera zaświadczanie o kondycji wdrażania](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Należy zauważyć następujące elementy dla odpowiednio ponumerowany obszar na diagramie.

1. Dwa serwery usługi Azure MFA (MFA1 i MFA2) są objęte równoważeniem obciążenia (mfaapp.contoso.com) i są skonfigurowane do korzystania z portu statycznego (4443) do replikacji bazy danych PhoneFactor.pfdata. Zestaw SDK usługi sieci Web jest zainstalowany na każdym serwerze usługi MFA w celu umożliwienia komunikacji za pośrednictwem portu TCP 443 z serwerami usług AD FS. Serwery usługi MFA są wdrażane w ramach bezstanowej konfiguracji ze zrównoważonym obciążeniem. Jednak jeśli chcesz użyć uwierzytelniania OTP za pośrednictwem wiadomości SMS, należy użyć równoważenia obciążenia stanowe.
   ![Serwer Azure MFA — serwer aplikacji o wysokiej dostępności](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Ponieważ RPC używa portów dynamicznych, nie zaleca się otwarcie zapory do zakresu portów dynamicznych, które mogą za pomocą usługi RPC. Jeśli Zapora jest **między** MFA serwerów aplikacji, należy skonfigurować serwer usługi MFA do komunikowania się na portu statycznego dla ruchu replikacji między serwerami podrzędnego i głównego i otworzyć ten port zapory. Można wymusić port statyczny, tworząc wartość rejestru DWORD w ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` o nazwie ```Pfsvc_ncan_ip_tcp_port``` i ustawiając wartość do dostępnego portu statycznego. Połączenia są zawsze inicjowane przez podrzędne serwery uwierzytelniania Wieloskładnikowego do poziomu głównego, portu statycznego jest wymagana tylko we wzorcu, ale ponieważ możesz podwyższyć poziom podrzędny jako główną w dowolnym momencie, należy ustawić portu statycznego na wszystkich serwerach usługi MFA.

2. Dwa serwery aplikacji mobilnej portalu/usługi MFA użytkownika (uwierzytelnianie wieloskładnikowe-UP-MAS1 i MFA-UP-MAS2) jest równoważone w **stanowa** konfiguracji (mfa.contoso.com). Pamiętaj, że trwałych sesji są wymagane w przypadku równoważenia obciążenia portalu użytkowników usługi MFA i usługą aplikacji mobilnej.
   ![Serwer usługi Azure MFA — Portal użytkowników i usługa Mobile App wysokiej dostępności](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Farma serwerów usług AD FS jest równoważeniu obciążenia i są publikowane w Internecie za pośrednictwem ze zrównoważonym obciążeniem serwerów proxy usług AD FS w sieci obwodowej. Każdy serwer usług AD FS używa agenta usług AD FS do komunikacji z serwerami usługi MFA Azure za pomocą jednego ze zrównoważonym obciążeniem adresu URL (mfaapp.contoso.com) za pośrednictwem portu TCP 443.

## <a name="next-steps"></a>Kolejne kroki

* [Instalowanie i konfigurowanie serwera usługi Azure MFA](howto-mfaserver-deploy.md)
