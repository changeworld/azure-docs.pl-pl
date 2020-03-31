---
title: Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
description: W tym artykule opisano sposób instalowania agenta inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263350"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
Ten dokument przeprowadzi Cię przez proces instalacji agenta inicjowania obsługi administracyjnej usługi Azure Active Directory (Azure AD) Connect i jak początkowo skonfigurować go w witrynie Azure portal.

>[!IMPORTANT]
>Poniższe instrukcje instalacji zakładają, że wszystkie [wymagania wstępne](how-to-prerequisites.md) zostały spełnione.

Instalowanie i konfigurowanie inicjowania obsługi administracyjnej usługi Azure AD Connect odbywa się w następujących krokach:
    
- [Instalowanie agenta](#install-the-agent)
- [Weryfikowanie instalacji agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalowanie agenta
Aby zainstalować agenta, wykonaj następujące kroki.

1. Zaloguj się na serwerze, którego będziesz używać z uprawnieniami administratora przedsiębiorstwa.
1. Przejdź do witryny Azure Portal. Po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)** > **Przejrzyj wszystkich agentów**.
1. Pobierz agenta inicjowania obsługi administracyjnej usługi Azure AD Connect z witryny Azure portal.

   ![Pobieranie agenta lokalnego](media/how-to-install/install9.png)</br>
1. Uruchom instalator inicjowania obsługi administracyjnej usługi Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Na ekranie **Pakiet agenta aprowizacji usługi Microsoft Azure AD Connect** zaakceptuj postanowienia licencyjne i wybierz pozycję **Zainstaluj**.

   ![Ekran pakietu agenta aprowizacji usługi Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. Po zakończeniu tej operacji zostanie uruchomiony kreator konfiguracji. Zaloguj się za pomocą konta administratora globalnego usługi Azure AD.
1. Na ekranie **Połącz usługa Active Directory** wybierz pozycję **Dodaj katalog**. Następnie zaloguj się za pomocą konta administratora usługi Active Directory. Ta operacja dodaje katalog lokalny. Wybierz **pozycję Dalej**.

   ![Ekran Łączenie usługi Active Directory](media/how-to-install/install3.png)</br>

1. Na ekranie **Konfiguracja pełna** wybierz pozycję **Potwierdź**. Ta operacja rejestruje i uruchamia ponownie agenta.

   ![Ekran ukończenia konfiguracji](media/how-to-install/install4.png)</br>

1. Po zakończeniu tej operacji powinien zostać wyświetlony komunikat **Konfiguracja agenta została pomyślnie zweryfikowana.** Wybierz **pozycję Wyjdź**.

   ![Przycisk Zakończ](media/how-to-install/install5.png)</br>
1. Jeśli nadal widzisz początkowy ekran **pakietu agenta aprowizującego usługi Microsoft Azure AD Connect,** wybierz przycisk **Zamknij**.

## <a name="verify-agent-installation"></a>Weryfikowanie instalacji agenta
Weryfikacja agenta odbywa się w witrynie Azure portal i na serwerze lokalnym, na który jest uruchomiony agent.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta usługi Azure portal
Aby sprawdzić, czy agent jest widoczny na platformie Azure, wykonaj następujące kroki.

1. Zaloguj się do Portalu Azure.
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Azure AD Connect**. W centrum wybierz pozycję **Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**.

   ![Portal Azure](media/how-to-install/install6.png)</br>

1.  Na ekranie **inicjowania obsługi administracyjnej usługi Azure AD (wersja zapoznawcza)** wybierz pozycję **Przejrzyj wszystkich agentów**.

    ![Przejrzyj opcję wszystkich agentów](media/how-to-install/install7.png)</br>
 
1. Na ekranie **lokalnych agentów inicjowania obsługi administracyjnej** są wyświetlane zainstalowane agenty. Sprawdź, czy dany agent istnieje i jest oznaczony *jako aktywny*.

   ![Ekran lokalnych agentów inicjowania obsługi administracyjnej](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Sprawdź port
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy twój agent może się z nią komunikować, wykonaj następujące kroki.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure za pomocą portu 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym jest zainstalowany agent.

![Weryfikacja osiągów portu](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent jest uruchomiony, wykonaj następujące kroki.

1.  Zaloguj się do serwera przy za pomocą konta administratora.
1.  Otwórz **usługi,** przechodząc do niego lub przechodząc do **strony Uruchom** > **Run** > **plik Services.msc**.
1.  W obszarze **Usługi**upewnij się, że program **Microsoft Azure AD Connect Agent Updater** i agent **aprowizowania usługi Microsoft Azure AD Connect** są tam dostępne, a ich stan jest *uruchomiony*.

    ![Ekran usług](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent został zainstalowany, ale musi być skonfigurowany i włączony, zanim rozpocznie synchronizację użytkowników. Aby skonfigurować nowego agenta, zobacz [Tworzenie nowej konfiguracji dla obsługi administracyjnej opartej na chmurze usługi Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
 
