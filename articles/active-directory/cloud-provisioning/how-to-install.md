---
title: Instalowanie Azure AD Connect agenta aprowizacji w chmurze
description: W tym temacie opisano krok po kroku dotyczące instalowania agenta aprowizacji.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846236"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Zainstaluj Azure AD Connect agenta aprowizacji w chmurze
Ten dokument przeprowadzi Cię przez proces instalacji agenta aprowizacji Azure AD Connect i sposób jego wstępnego skonfigurowania w Azure Portal.

>[!IMPORTANT]
>W poniższych instrukcjach instalacji założono, że spełniono wszystkie [wymagania wstępne](how-to-prerequisites.md) .

Instalowanie i Konfigurowanie Azure AD Connect aprowizacji jest realizowane w następujących krokach:
    
- [Instalowanie agenta](#install-the-agent)
- [Weryfikuj instalację agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalowanie agenta

1. Zaloguj się na serwerze, który będzie używany z uprawnieniami administratora przedsiębiorstwa.
2. Przejdź do Azure Portal, wybierz pozycję Azure Active Directory po lewej stronie.
3. Kliknij pozycję **Zarządzaj aprowizacji (wersja zapoznawcza)** i wybierz pozycję **Przejrzyj wszystkich agentów**.
3. Pobierz Azure AD Connect agenta aprowizacji z Azure Portal.
![Ekran powitalny](media/how-to-install/install9.png)</br>
3. Uruchamianie Azure AD Connect aprowizacji (AADConnectProvisioningAgent. Installer)
3. Na ekranie powitalnym **Zaakceptuj** postanowienia licencyjne, a następnie kliknij przycisk **Instaluj**.</br>
![Ekran powitalny](media/how-to-install/install1.png)</br>

4. Po zakończeniu tej operacji zostanie uruchomiony Kreator konfiguracji.  Zaloguj się przy użyciu konta administratora globalnego usługi Azure AD.
5. Na ekranie **połącz Active Directory** kliknij pozycję **Dodaj katalog** , a następnie zaloguj się przy użyciu konta administratora Active Directory.  Ta operacja spowoduje dodanie katalogu lokalnego.  Kliknij przycisk **Dalej**.</br>
![Ekran powitalny](media/how-to-install/install3.png)</br>

6. Na ekranie **Konfiguracja ukończona** kliknij przycisk **Potwierdź**.  Ta operacja spowoduje zarejestrowanie i ponowne uruchomienie agenta.</br>
![Ekran powitalny](media/how-to-install/install4.png)</br>

7. Po zakończeniu tej operacji powinna zostać wyświetlona informacja o **tym, że została pomyślnie zweryfikowana.**  Możesz kliknąć przycisk **Zakończ**.</br>
![Ekran powitalny](media/how-to-install/install5.png)</br>
8. Jeśli nadal widzisz początkowy ekran powitalny, kliknij przycisk **Zamknij**.


## <a name="verify-agent-installation"></a>Weryfikuj instalację agenta
Weryfikacja agenta odbywa się w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal
Aby sprawdzić, czy Agent jest widziany przez platformę Azure, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** kliknij pozycję **Przejrzyj wszystkich agentów**.
![](media/how-to-install/install7.png) aprowizacji usługi Azure AD</br>
 
4. Na **ekranie agenci aprowizacji lokalnego** zostaną zainstalowani agenci.  Sprawdź, czy dany Agent jest tam i jest oznaczony jako **aktywny**.
![agentów aprowizacji](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować, możesz użyć następujących czynności:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdzi, czy agenci mogą komunikować się z platformą Azure przez port 443.  Otwórz przeglądarkę i przejdź do powyższego adresu URL z serwera, na którym Agent jest zainstalowany.
![Usługi](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent działa, wykonaj następujące kroki:

1.  Zaloguj się na serwerze przy użyciu konta administratora
2.  Otwórz **usługi** , przechodząc do niej lub uruchamiając/Uruchom/Services. msc.
3.  W obszarze **usługi** upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam, a stan jest **uruchomiony**.
![Usługi](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent został zainstalowany, ale przed rozpoczęciem synchronizowania użytkowników musi zostać skonfigurowany i włączony.  Aby skonfigurować nowego agenta, zobacz [Azure AD Connect aprowizacji nowej konfiguracji agenta](how-to-configure.md).



## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
 
