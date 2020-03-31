---
title: Architektura urządzenia migracji platformy Azure
description: Zawiera omówienie urządzenia migracji platformy Azure używanego w ocenie i migracji serwera.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389022"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura urządzenia migracji platformy Azure

W tym artykule opisano architekturę i procesy urządzenia usługi Azure Migrate. Urządzenie migracji platformy Azure to lekkie urządzenie wdrożone lokalnie w celu odnajdywanie maszyn wirtualnych i serwerów fizycznych w celu migracji na platformę Azure. 

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie migracji platformy Azure jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Używana do** 
--- | --- | ---
**Ocena maszyn wirtualnych VMware** | Migracja platformy Azure:Ocena serwera | Odnajduj maszyny wirtualne VMware.<br/><br/> Odnajduj aplikacje maszynowe i zależności.<br/><br/> Zbieranie metadanych i metadanych wydajności maszyny i wysyłanie ich na platformę Azure.
**Migracja maszyn wirtualnych VMware (bez agenta)** | Migracja platformy Azure:migracja serwera | Odkryj maszyny wirtualne VMware<br/><br/>  Replikowanie maszyn wirtualnych VMware z [migracją bez agenta](server-migrate-overview.md).
**Ocena maszyny wirtualnej funkcji Hyper-V** | Migracja platformy Azure:Ocena serwera | Odnajduj maszyny wirtualne z programem Hyper V.<br/><br/> Zbieranie metadanych i metadanych wydajności maszyny i wysyłanie ich na platformę Azure.
**Maszyna fizyczna** |  Migracja platformy Azure:Ocena serwera |  Odkryj serwery fizyczne.<br/><br/> Zbieranie metadanych i metadanych wydajności maszyny i wysyłanie ich na platformę Azure.

## <a name="appliance-components"></a>Elementy urządzenia

Urządzenie posiada szereg komponentów.

- **Aplikacja do zarządzania:** Jest to aplikacja sieci web do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceny maszyn do migracji na platformę Azure.
- **Agent odnajdywania:** Agent zbiera dane konfiguracji maszyny. Używane podczas oceny maszyn do migracji na platformę Azure. 
- **Agent oceny**: Agent zbiera dane dotyczące wydajności. Używane podczas oceny maszyn do migracji na platformę Azure.
- **Agent DRA:** Organizuje replikację maszyn wirtualnych i koordynuje komunikację między replikowanymi maszynami a platformą Azure. Używane tylko podczas replikowania maszyn wirtualnych VMware na platformie Azure przy użyciu migracji bez agenta.
- **Brama:** Wysyła replikowane dane na platformę Azure. Używane tylko podczas replikowania maszyn wirtualnych VMware na platformie Azure przy użyciu migracji bez agenta.
- **Usługa automatycznej aktualizacji:** Aktualizuje składniki urządzenia (uruchamia się co 24 godziny).



## <a name="appliance-deployment"></a>Wdrażanie urządzeń

- Urządzenie migracji platformy Azure można skonfigurować przy użyciu szablonu dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) lub [VMware](how-to-set-up-appliance-vmware.md) lub przy użyciu instalatora skryptów programu PowerShell dla [VMware/Hyper-V](deploy-appliance-script.md)oraz [dla serwerów fizycznych.](how-to-set-up-appliance-physical.md) 
- Wymagania dotyczące obsługi urządzeń i wymagania wstępne dotyczące wdrażania są podsumowane w [macierzy obsługi urządzeń](migrate-appliance.md).


## <a name="appliance-registration"></a>Rejestracja urządzenia

Podczas konfigurowania urządzenia można zarejestrować urządzenie za pomocą usługi Azure Migrate, a akcje podsumowane w tabeli występują.

**Akcja** | **Szczegóły** | **Uprawnienia**
--- | --- | ---
**Rejestrowanie dostawców źródeł** | Dostawcy tych zasobów są zarejestrowani w subskrypcji, którą wybierzesz podczas instalacji urządzenia: Microsoft.OffAzure, Microsoft.Migrate i Microsoft.KeyVault.<br/><br/> Rejestrowanie dostawcy zasobów konfiguruje subskrypcję do pracy z dostawcą zasobów. | Aby zarejestrować dostawców zasobów, potrzebujesz roli współautora lub właściciela w ramach subskrypcji.
**Tworzenie komunikacji z aplikacjami usługi Azure AD** | Usługa Azure Migrate tworzy aplikację usługi Azure Active Directory (Azure AD) do komunikacji (uwierzytelnianie i autoryzacja) między agentami działającymi na urządzeniu a ich odpowiednimi usługami uruchomionymi na platformie Azure.<br/><br/> Ta aplikacja nie ma uprawnień do wykonywania wywołań usługi Azure Resource Manager lub dostępu RBAC na dowolnym zasobie. | Aby utworzyć aplikację, potrzebujesz [tych uprawnień](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) do migracji platformy Azure.
**Tworzenie magazynu aplikacji usługi Azure AD - klucz** | Ta aplikacja jest tworzona tylko dla bezagentowej migracji maszyn wirtualnych VMware na platformę Azure.<br/><br/> Jest on używany wyłącznie do uzyskiwania dostępu do magazynu kluczy utworzonego w subskrypcji użytkownika dla migracji bez agenta.<br/><br/> Ma dostęp RBAC w magazynie kluczy platformy Azure (utworzony w dzierżawie klienta), gdy odnajdowanie jest inicjowane z urządzenia. | Aby utworzyć aplikację, potrzebujesz [tych uprawnień](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) do migracji platformy Azure.



## <a name="collected-data"></a>Zebrane dane

Dane zebrane przez klienta dla wszystkich scenariuszy wdrażania są podsumowane w [macierzy obsługi urządzeń](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Proces odnajdywania i zbierania

![Architektura](./media/migrate-appliance-architecture/architecture.png)

Urządzenie komunikuje się z serwerami vCenter i hostami/klastrem funkcji Hyper-V przy użyciu następującego procesu.

1. **Rozpocznij odnajdowanie:**
    - Po uruchomieniu odnajdywania na urządzeniu Hyper-V komunikuje się z hostami funkcji Hyper-V na portach WinRM 5985 (HTTP) i 5986 (HTTPS).
    - Po uruchomieniu odnajdywania na urządzeniu VMware domyślnie komunikuje się z serwerem vCenter na porcie TCP 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można go skonfigurować w aplikacji sieci web urządzenia.
2. **Zbieranie metadanych i danych dotyczących wydajności:**
    - Urządzenie używa sesji wspólnego modelu informacyjnego (CIM) do zbierania danych maszyny Wirtualnej funkcji Hyper-V z hosta funkcji Hyper-V na portach 5985 i 5986.
    - Urządzenie domyślnie komunikuje się z portem 443 w celu zbierania danych VMware VM Z serwera vCenter Server.
3. **Wyślij dane:** Urządzenie wysyła zebrane dane do oceny serwera migracji usługi Azure i migracji serwera migracji platformy Azure za pomocą portu SSL 443. Urządzenie może łączyć się z platformą Azure przez Internet lub można użyć usługi ExpressRoute z komunikacją równorzędność publiczną/Microsoft.
    - W przypadku danych dotyczących wydajności urządzenie zbiera dane dotyczące wykorzystania w czasie rzeczywistym.
        - Dane dotyczące wydajności są zbierane co 20 sekund dla VMware i co 30 sekund dla funkcji Hyper-V dla każdej metryki wydajności.
        - Zebrane dane są rzutowane w celu utworzenia pojedynczego punktu danych na 10 minut.
        - Szczytowa wartość wykorzystania jest wybierana ze wszystkich punktów danych 20/30 sekund i wysyłana na platformę Azure w celu obliczenia oceny.
        - Na podstawie wartości percentyla określonej we właściwościach oceny (50./90./95/99.), punkty dziesięć minut są sortowane w porządku rosnącym, a odpowiednia wartość percentyla jest używana do obliczania oceny
    - W przypadku migracji serwera urządzenie rozpoczyna zbieranie danych maszyny Wirtualnej i replikuje je na platformę Azure.
4. **Oceniaj i migruj:** Można teraz tworzyć oceny z metadanych zebranych przez urządzenie przy użyciu oceny serwera migracji usługi Azure. Ponadto można również rozpocząć migrację maszyn wirtualnych VMware przy użyciu migracji serwera migracji usługi Azure Migrate Server w celu zorganizowania replikacji maszyn wirtualnych bez agenta.





## <a name="appliance-upgrades"></a>Modernizacje urządzeń

Urządzenie jest uaktualniane, jak agenci migracji platformy Azure uruchomione na urządzeniu są aktualizowane. Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. To ustawienie domyślne można zmienić, aby ręcznie zaktualizować agentów.

Możesz wyłączyć automatyczną aktualizację w rejestrze, ustawiając klucz HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" na 0 (DWORD).

 

## <a name="next-steps"></a>Następne kroki

[Przejrzyj](migrate-appliance.md) macierz obsługi urządzeń.

